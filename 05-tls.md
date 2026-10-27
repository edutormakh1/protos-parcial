# TLS: HTTPS con certificado

> Cheatsheet de PROTOS (72.07, ITBA) para el parcial práctico. Repaso del tema y, al final, el paso a paso de todos los ejercicios de parciales que caen acá.

---

## Cheatsheet

### Certificado self-signed

```
openssl req -x509 -newkey rsa:2048 -nodes \
  -keyout /etc/ssl/private/foo.key \
  -out /etc/ssl/certs/foo.crt \
  -days 365 -subj "/CN=foo.pdc.lab" \
  -addext "subjectAltName=DNS:foo.pdc.lab"
```

- `-nodes`: sin passphrase, así nginx arranca solo.
- `-addext subjectAltName`: **obligatorio**. Chrome ignora el CN y valida solo el SAN; sin SAN rechaza el cert aunque confíes. El SAN = el nombre exacto de la URL.

### nginx como terminador TLS

```
server {
    listen 443 ssl;
    server_name foo.pdc.lab;
    ssl_certificate     /etc/ssl/certs/foo.crt;
    ssl_certificate_key /etc/ssl/private/foo.key;
    location / {
        proxy_pass http://localhost:8080;  # el backend sigue siendo HTTP
    }
}
```

```
nginx -t && systemctl reload nginx
curl -k https://foo.pdc.lab  # -k ignora la validación (para probar)
curl --cacert /etc/ssl/certs/foo.crt https://foo.pdc.lab  # confiar en ESE cert
```

Para que el **curl pelado** funcione (como pide la consigna): copiás el `.crt` a `/usr/local/share/ca-certificates/` (con extensión `.crt`) y `sudo update-ca-certificates` (dice `1 added`). Verificás el SAN: `openssl x509 -in foo.crt -noout -text | grep -A1 Alternative`. TLS vs SSL (cátedra): difieren en cómo empieza la conexión segura; SSL es el predecesor, hoy el real es TLS.

---

## Ejercicios paso a paso

### Ejercicio 1: Exponer por HTTPS un backend HTTP con nginx y certificado self-signed

_[2025 2C]_

**Enunciado**

> Ejercicio IV
>
> El servidor en parcial.protos.foo esta corriendo un servidor HTTP inseguro. Usted, como un administrador responsable, desea exponerlo al publico a traves de HTTPS. Configure lo necesario para que al acceder a https://seguro.protos.foo con un HTTP User Agent (Firefox, Google Chrome, Curl), se sirva el contenido devuelto de parcial.protos.foo
>
> Requisito.  No se debe ver modificada la URL ingresada en el browser.
>             Si se utiliza curl no se permiten otras opciones. Se recomienda verificación utilizando curl. $ curl https://seguro.protos.foo).

**Pista:** Si curl no puede llevar -k, ¿de dónde saca la confianza en un certificado que firmaste vos mismo?

**Resolución paso a paso**

**1. Resolvé los dos nombres antes de tocar nginx**

seguro.protos.foo tiene que apuntar a tu máquina (el nginx) y parcial.protos.foo al backend. Esto va primero porque nginx resuelve el upstream del proxy_pass en el momento de arrancar: si el nombre no resuelve, nginx -t falla con 'host not found in upstream' y no levanta el servicio.

```
ip addr show

# tu máquina, la que corre nginx
echo "192.168.x.y  seguro.protos.foo"  >> /etc/hosts
# solo si el DNS del laboratorio no lo resuelve ya
echo "192.168.x.z  parcial.protos.foo" >> /etc/hosts
```

_Verificación:_ getent hosts seguro.protos.foo parcial.protos.foo → las dos IPs; curl http://parcial.protos.foo tiene que responder

**2. Generá el par clave privada + certificado self-signed**

HTTPS necesita dos archivos: la clave privada (nunca sale del servidor, descifra y firma) y el certificado (lleva la clave pública y la identidad). Normalmente lo firma una CA; en el parcial lo firmás vos. -nodes evita que la clave quede cifrada con passphrase, sino nginx se cuelga pidiéndola al arrancar. El CN tiene que ser exactamente el hostname que va a usar el cliente. El -addext con subjectAltName no es opcional: Chrome (que la consigna nombra) ignora el CN y valida solo contra el SAN, así que sin SAN rechaza el certificado aunque el sistema confíe en él.

```
openssl req -x509 -newkey rsa:4096 \
  -keyout /etc/ssl/private/seguro.key \
  -out /etc/ssl/certs/seguro.crt \
  -days 365 -nodes \
  -subj "/CN=seguro.protos.foo" \
  -addext "subjectAltName=DNS:seguro.protos.foo"
```

_Verificación:_ openssl x509 -in /etc/ssl/certs/seguro.crt -noout -text | grep -A1 Alternative → tiene que mostrar DNS:seguro.protos.foo

**3. Configurá nginx para escuchar en 443 con TLS y hacer proxy**

listen 443 ssl más las dos directivas ssl_* es lo que termina la conexión TLS en nginx. proxy_pass con http:// adentro es lo correcto: el tramo nginx→backend va sin cifrar porque el backend solo habla HTTP, el cifrado es solo del cliente a vos. Un redirect en vez de proxy_pass cambiaría la URL y rompe el requisito.

```
# /etc/nginx/sites-available/seguro
server {
    listen 443 ssl;
    server_name seguro.protos.foo;

    ssl_certificate     /etc/ssl/certs/seguro.crt;
    ssl_certificate_key /etc/ssl/private/seguro.key;

    location / {
        proxy_pass http://parcial.protos.foo;
    }
}
```

**4. Habilitá el sitio y recargá**

El symlink a sites-enabled es lo que hace que nginx lea el archivo. nginx -t antes del reload te atrapa la ruta del certificado mal escrita o el upstream que no resuelve, con el servicio todavía andando.

```
ln -s /etc/nginx/sites-available/seguro /etc/nginx/sites-enabled/
nginx -t && systemctl reload nginx
```

_Verificación:_ nginx -t → 'test is successful'; ss -tlnp | grep 443 → nginx escuchando

**5. Instalá el certificado en el trust store del sistema**

Acá está el truco del ejercicio. Un certificado self-signed no lo firma ninguna CA conocida, así que curl aborta con 'self-signed certificate' — y la consigna prohíbe usar -k. update-ca-certificates toma los .crt de /usr/local/share/ca-certificates/ y los agrega a la lista de CAs en que confía el sistema, que es de donde curl la lee.

```
cp /etc/ssl/certs/seguro.crt /usr/local/share/ca-certificates/seguro.crt
update-ca-certificates
```

_Verificación:_ Tiene que imprimir '1 added'; el cert aparece en /etc/ssl/certs/ca-certificates.crt

**6. Verificá con el curl pelado que pide la consigna**

Es exactamente el comando con el que te van a corregir: sin -k, sin --resolve, sin nada. Si necesitás agregar algo, falta uno de los pasos anteriores.

```
curl https://seguro.protos.foo
# para ver el handshake en detalle si algo falla:
curl -v https://seguro.protos.foo
openssl s_client -connect seguro.protos.foo:443 -servername seguro.protos.foo
```

_Verificación:_ El contenido de parcial.protos.foo, sin error de TLS y sin flags extra

**Errores típicos**

- Usar -k para 'que ande': la consigna prohíbe opciones adicionales en curl, el cert tiene que estar en el trust store.
- Poner el CN del certificado distinto del hostname (parcial.protos.foo en vez de seguro.protos.foo): curl falla con 'subject name does not match'.
- Arrancar nginx antes de que parcial.protos.foo resuelva: nginx -t falla con 'host not found in upstream'.
- Copiar el .crt al trust store y olvidarse de correr update-ca-certificates: el archivo está pero el sistema no lo indexó.
- Usar return 301 https://parcial.protos.foo en vez de proxy_pass: la URL cambia en el browser.
- Generar el certificado sin -addext subjectAltName: curl puede andar con el CN, pero Chrome lo rechaza.

_Fuente: 2025_2c_practico.md_

---

