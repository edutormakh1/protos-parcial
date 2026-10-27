# HTTP, curl y nginx

> Cheatsheet de PROTOS (72.07, ITBA) para el parcial práctico. Repaso del tema y, al final, el paso a paso de todos los ejercicios de parciales que caen acá.

---

## Cheatsheet

### curl — los flags que caen

```
curl -v <url>  # SIEMPRE: request, response y el código
curl -H "Accept-Language: es" <url>  # agregar un header
curl -u user:pass <url>  # Basic Auth (401 lo pide)
curl -X POST -H "Content-Type: application/json" -d '{"rta":"x"}' <url>  # POST con JSON
curl -x socks5h://proxy:1080 <url>  # a través de un proxy SOCKS5
curl -H "Host: foo.lab" http://IP/  # elegir el virtual host a mano
curl -L <url>  # seguir un redirect; -o archivo: guardar
```

### curl: caché condicional y rangos

```
curl -i <url>  # ver los headers de la respuesta (ETag, Last-Modified)
curl -i -H 'If-None-Match: "abc123"' <url>  # si no cambió → 304 Not Modified
curl -i -H "If-Modified-Since: Thu, 13 Mar 2025 19:51:16 GMT" <url>  # ídem por fecha
curl -r 0-999 <url>  # Range: primeros 1000 bytes → 206 Partial Content
```

### HTTP a mano con netcat

HTTP es texto. En HTTP/1.1 el header `Host` es obligatorio y la línea vacía cierra los headers.

```
nc -C ejemplo.com 80
GET / HTTP/1.1
Host: ejemplo.com
Connection: close
# (línea vacía: Enter)
```

> **Las dos reglas de curl**
>
> **1.** Los flags van **antes** de la URL. **2.** Con proxy casi siempre `socks5h` (con h): el host del ejercicio solo existe del lado del proxy, así que el DNS lo resuelve el proxy. Y `-v` siempre: el código y headers como `WWW-Authenticate` o `Allow` te dicen qué falta.

### Códigos de estado = qué agregar

| Código | Qué hacer |
|---|---|
| 401 | falta auth → `-u user:pass` |
| 403 | autenticado pero sin permiso, o falta un header |
| 404 | ruta equivocada |
| 405 | método no permitido → mirá `Allow`, cambiá `-X` |
| 415 | falta o está mal el `Content-Type` del cuerpo |
| 406 | `Accept`/`Accept-Language` no matchean |
| 301 / 302 | seguí el `Location` (o `-L`) |
| 502 / 504 | el backend del `proxy_pass` no responde |

### nginx — reverse proxy (2024 ej1, 2026 ej2/ej3)

Un sitio vive en `/etc/nginx/sites-available/NOMBRE` y se activa con un symlink a `sites-enabled/`. nginx solo lee lo que está en `sites-enabled`.

```
# /etc/nginx/sites-available/foo
server {
    listen 80;
    server_name foo.example.org;
    location / {
        proxy_pass http://protos.sebikul.com;
    }
}
```

```
ln -s ../sites-available/foo /etc/nginx/sites-enabled/foo  # activar
echo "127.0.0.1 foo.example.org" >> /etc/hosts  # que el nombre resuelva a tu nginx
nginx -t && systemctl reload nginx  # validar y recargar
curl -v http://foo.example.org/
```

### nginx: default server, servir archivos y redirigir un dominio

```
server {
    listen 80 default_server;  # el que responde si ningún server_name coincide
    server_name _;
    root /var/www/foo;
    index index.html;
    location / { try_files $uri $uri/ =404; }
}

server {
    listen 80;
    server_name viejo.pdc.lab;
    return 302 http://nuevo.pdc.lab$request_uri;  # redirige conservando el path
}
```

```
mkdir -p /var/www/foo && echo "Bienvenido a foo" > /var/www/foo/index.html
curl -v -H "Host: foo" http://localhost/  # probar un server_name sin tocar /etc/hosts
```

> **Headers: conservar vs agregar (el error más común)**
>
> nginx **ya le pasa al backend todos los headers del cliente**, tal cual (lo probé con un nginx real). Solo redefine `Host` y `Connection`, y descarta los vacíos o con guion bajo.
> 
> | Caso | proxy_set_header |
> |---|---|
> | Conservar un header del cliente, ej. `X-Parcial-Protos` (2024 ej1) | **no hace falta**: ya se reenvía. Ponerlo no está mal, es redundante |
> | Agregar uno que el cliente NO manda, ej. `Metadata` (2026 ej2) | **sí**, valor literal: `proxy_set_header Metadata true;` |
> 
> Para usar el valor que mandó el cliente: `$http_nombre` (minúsculas, guiones → guiones bajos). `proxy_pass` no cambia la URL del browser; un `return 301` sí.

### Recorrer un nginx a partir de su config (el ejercicio "de Tomás")

Te dan la config de un nginx andando y tenés que pedirle recursos con curl. No se configura nada: se **lee** para saber qué pedir. `sudo nginx -T` imprime la config completa. Elige el bloque por **puerto** (`listen`) y después por **nombre** (el `server_name` que matchea el header `Host`).

| Directiva | Qué te dice para pedirle |
|---|---|
| `server_name secreto.lab;` | mandá `Host: secreto.lab` |
| `root /var/www/x;` | el path se **pega** al root: `/a/b` → `/var/www/x/a/b` |
| `location /f/ { alias /srv/; }` | con alias el prefijo se **reemplaza**: `/f/k` → `/srv/k` |
| `autoindex on;` | pedir la carpeta devuelve el listado |
| `auth_basic` | pide usuario y contraseña → `-u user:pass` |

```
curl -v -H "Host: secreto.lab" http://IP:8080/  # elegir el server_name
curl -H "Host: secreto.lab" http://IP:8080/carpeta/  # ver el listado (autoindex)
curl -H "Host: secreto.lab" http://IP:8080/archivo -o archivo  # bajar un archivo
```

La segunda parte sigue el final del integrador: el servidor expone una clave SSH y el nombre de usuario; los bajás, `chmod 600` la clave y entrás con `ssh -i./clave usuario@host` (cheat de SSH).

---

## Ejercicios paso a paso

### Ejercicio 1: Reverse proxy nginx preservando un header del cliente

_[2024 2C]_

**Enunciado**

> Ejercicio I
>
> Realizar los cambios necesarios para que al ingresar a http://foo.example.org/ utilizando un HTTP User Agent (Firefox, Google Chrome, Curl) se sirva el contenido de http://protos.sebikul.com/. Conserve el valor del header X-Parcial-Protos.
>
> Requisito.  No se debe ver modificada la URL ingresada en el browser.
>             Si se utiliza curl no se permiten otras opciones. Se recomienda verificación utilizando curl. $ curl http://foo.example.org/
>
> Nota.  Las representaciones que retorna protos.sebikul.com pueden cambiar sin previo aviso. Se puede llegar a pedir cualquier recurso dentro de protos.sebikul.com.

**Pista:** Si la URL no puede cambiar en el browser, ¿sirve un redirect 301, o nginx tiene que traer el contenido él mismo?

**Resolución paso a paso**

**1. Escalá a root e instalá nginx**

Todo lo que sigue toca /etc/nginx y /etc/hosts, que son de root. Si cada comando lo hacés con sudo suelto te vas a comer un 'Permission denied' en medio del redirect de un echo >> y vas a perder minutos.

```
sudo apt install nginx-core
sudo -i
```

_Verificación:_ systemctl status nginx → tiene que decir active (running)

**2. Hacé que foo.example.org apunte a tu nginx**

El browser y curl tienen que resolver foo.example.org a tu propia máquina, sino le pegan a cualquier otro lado o directamente no resuelve. Como el dominio no es tuyo en el DNS real, lo forzás por /etc/hosts, que se consulta antes que el DNS.

```
echo "127.0.0.1 foo.example.org" >> /etc/hosts
```

_Verificación:_ getent hosts foo.example.org → tiene que devolver 127.0.0.1

**3. Creá el virtual host con proxy_pass**

proxy_pass hace que nginx vaya a buscar el contenido al origen y lo devuelva como si fuera propio: el cliente nunca se entera y la URL en la barra no cambia. Un return 301 cumpliría funcionalmente pero rompe el requisito de que la URL no se modifique.

```
# /etc/nginx/sites-available/foo
server {
    listen 80;
    server_name foo.example.org;

    location / {
        proxy_pass http://protos.sebikul.com;
    }
}
```

**4. Conservá el header X-Parcial-Protos (lo hace nginx solo)**

nginx ya le pasa al backend todos los headers que manda el cliente: solo redefine Host y Connection, y descarta los vacíos o con guion bajo en el nombre. X-Parcial-Protos tiene guiones, así que llega al origen sin que hagas nada. La línea proxy_set_header con $http_x_parcial_protos (el valor que mandó el cliente, en minúsculas y con guiones bajos) es redundante: no cambia el resultado, pero tampoco está mal, y en el examen deja por escrito que te ocupaste del requisito. Si la ponés, explicá que es explícita, no que sin ella se perdería.

```
location / {
    proxy_pass http://protos.sebikul.com;
    # opcional: nginx ya lo reenvía solo
    proxy_set_header X-Parcial-Protos $http_x_parcial_protos;
}
```

_Verificación:_ curl -v -H "X-Parcial-Protos: 65522" http://foo.example.org/ con y sin la línea: el origen recibe el header en los dos casos

**5. Habilitá el sitio y recargá nginx**

El archivo en sites-available no se lee: nginx solo incluye lo que está en sites-enabled. Y nginx -t antes del reload te avisa de errores de sintaxis con el servicio todavía arriba, en vez de dejarte el nginx caído.

```
ln -s /etc/nginx/sites-available/foo /etc/nginx/sites-enabled/foo
nginx -t && systemctl reload nginx
```

_Verificación:_ nginx -t → 'syntax is ok' y 'test is successful'

**6. Verificá con curl sin opciones extra**

La consigna prohíbe flags adicionales, así que el curl pelado tiene que andar. Si tenés que agregar -H 'Host: ...' es señal de que el server_name o el /etc/hosts están mal.

```
curl http://foo.example.org/
curl -v http://foo.example.org/ -H "X-Parcial-Protos: 65522"
```

_Verificación:_ Tiene que devolver el contenido de protos.sebikul.com, y el backend tiene que ver el valor del header

**Errores típicos**

- Usar return 301 http://protos.sebikul.com en vez de proxy_pass: la URL cambia en el browser y se incumple el requisito explícito.
- Creer que sin proxy_set_header el header se pierde: nginx ya lo reenvía. Lo que sí lo rompe es ponerle guion bajo al nombre (X_Parcial_Protos), porque nginx descarta esos headers por defecto.
- Escribir mal la variable ($http_X-Parcial-Protos): tiene que ser todo minúscula y con guiones bajos, $http_x_parcial_protos.
- Dejar el archivo solo en sites-available sin el symlink a sites-enabled: nginx nunca lo lee y seguís viendo la página default.
- No agregar foo.example.org a /etc/hosts: curl falla con 'Could not resolve host' antes de llegar a nginx.
- Agregar proxy_set_header Host $host: el origen recibiría Host: foo.example.org en vez de protos.sebikul.com y podría no saber qué sitio servir. Por defecto nginx ya manda el Host correcto.

_Fuente: 2024_2c_practico.md_

---

### Ejercicio 2: GET con curl a través de un proxy SOCKS5 con Basic Auth

_[2024 2C]_

**Enunciado**

> Ejercicio II
>
> Obtenga la representación textual (media type: text/plain) en el lenguaje español (es) del recurso <http://ejercicio2.sebikul.com:8080/foo/> usando curl.
>
> Dato  la red no rutea paquetes hacia el host ejercicio2.sebikul.com, pero este es accesible mediante un proxy <socks5h://proxy.sebikul.com:1080>.
>
> Dato  A su poder ha llegado un request y una respuesta sobre el mismo servidor con información que le podría ser útil.
>
> > GET /abc/ HTTP/1.0
> > Host: ejercicio2.proto.leak.com.ar
> > Accept-Encoding: gzip, deflate, br
> > Accept-Language: en,it;q=0.9,es-AR;q=0.8,es;q=0.7
> > Authorization: Basic dXN1YXJpbzpwYXNzd29yZA==
> > Connection: keep-alive
> > Pragma: no-cache
>
> < HTTP/1.1 200 OK
> < Server: nginx/1.1.19
> < Date: Wed, 19 May 2021 02:18:51 GMT
> < Content-Length: 1084
> < Last-Modified: Sun, 22 Apr 2018 23:52:26 GMT
> <
> < esta no es la solucion (estaba en el enunciado como ejemplo)
>
> Resolución parcial. Obtenga
>   - 1.0 puntos si no llega a la solución pero logra reproducir el request HTTP que se observa en la trama del archivo PCAP utilizando el proxy socks.
>   - Si no sabe como hacer un request HTTP utilizando un proxy SOCKv5 puede obtener ayuda sobre como hacerlo, pero con una penalización de 0.5 puntos.
>
> [El Authorization del request filtrado se lee con dificultad en la captura; el base64 transcripto decodifica a usuario:password, que es lo que usa la solución.]

**Pista:** El request filtrado trae un Authorization: Basic — ¿qué hay realmente adentro de ese base64?

**Resolución paso a paso**

**1. Decodificá las credenciales del request filtrado**

El Authorization: Basic no está cifrado, es solo base64 de usuario:contraseña. Sin decodificarlo no sabés con qué par autenticarte, y el servidor te va a devolver 401 por más que el proxy funcione.

```
echo 'dXN1YXJpbzpwYXNzd29yZA==' | base64 -d
# → usuario:password
```

_Verificación:_ La salida tiene que tener la forma usuario:contraseña, con un solo ':'

**2. Armá el curl apuntando al proxy SOCKS5**

La red no rutea hacia ejercicio2.sebikul.com, así que un curl directo se queda colgado hasta el timeout. El flag -x manda la conexión por el proxy; el esquema socks5h hace que el DNS lo resuelva el proxy, que es lo que querés cuando el nombre solo existe del otro lado.

```
curl -x socks5h://proxy.sebikul.com:1080 http://ejercicio2.sebikul.com:8080/foo/
```

_Verificación:_ Debería contestar algo (aunque sea un 401), no quedarse colgado

**3. Agregá la autenticación con -u**

El recurso está protegido con Basic Auth. Con -u curl arma solo el header Authorization: Basic <base64>, así no tenés que recalcular el base64 a mano y equivocarte en un caracter.

```
curl -x socks5h://proxy.sebikul.com:1080 -u usuario:password http://ejercicio2.sebikul.com:8080/foo/
```

_Verificación:_ curl -v tiene que mostrar '> Authorization: Basic dXN1YXJpbzpwYXNzd29yZA==' y la respuesta ya no ser 401

**4. Pedí la representación en texto plano y en español**

El recurso tiene varias representaciones y el servidor elige por negociación de contenido. Si no mandás los Accept, te devuelve la representación por defecto (probablemente HTML y en inglés) y la respuesta no es la que pide la consigna.

```
curl -x socks5h://proxy.sebikul.com:1080 -u usuario:password \
  -H "Accept: text/plain" \
  -H "Accept-Language: es" \
  http://ejercicio2.sebikul.com:8080/foo/
```

_Verificación:_ En curl -v la respuesta tiene que traer Content-Type: text/plain y el cuerpo en español

**5. Verificá el request completo con -v**

Es la única forma de confirmar que los tres pedidos (proxy, auth, negociación) viajaron juntos en el mismo request. Muchas veces el comando 'anda' pero uno de los headers quedó afuera por una comilla mal cerrada.

```
curl -v -x socks5h://proxy.sebikul.com:1080 -u usuario:password -H "Accept: text/plain" -H "Accept-Language: es" http://ejercicio2.sebikul.com:8080/foo/
```

_Verificación:_ Líneas '>' con Authorization, Accept y Accept-Language, y un HTTP/1.1 200 OK de vuelta

**Errores típicos**

- Usar socks5:// en vez de socks5h:// cuando el hostname solo resuelve del lado del proxy: tu máquina no lo resuelve y curl falla con 'Could not resolve host'.
- Copiar el Accept-Language entero del request filtrado (en,it;q=0.9,...) en vez de pedir es: el servidor prioriza inglés y devuelve la representación equivocada.
- Olvidarse del puerto :8080 en la URL y pegarle al 80 por defecto.
- Mandar Accept: text/html o no mandar Accept: devuelve la representación por defecto en vez de text/plain.
- Reusar el Host: ejercicio2.proto.leak.com.ar del request filtrado: ese era un ejemplo, el host real es ejercicio2.sebikul.com.

_Fuente: 2024_2c_practico.md_

---

### Ejercicio 3: Reverse proxy nginx que inyecta el header Metadata: true

_[2026 1C]_

**Enunciado**

> Ejercicio II
> En las nubes públicas, es común encontrarse con endpoints de metadata que proveen información estructurada solo en formato JSON sobre la instancia en la cual se está corriendo. Normalmente se encuentran montadas sobre direcciones IP no routeables, y requieren la presencia del header Metadata: true para evitar ataques de SSRF (Server Side Request Forgery).
> Usted encontró un endpoint de metadata mal configurado disponible públicamente en http://metadata.protos.foo.
>
> Requisito: No se permiten otras opciones de curl al hacer pedidos a http://metadata.local.
>
> [Nota: en el PDF del enunciado falta la oración con la consigna concreta. Por el requisito y la solución del repo, lo pedido es que un curl http://metadata.local pelado (sin -H ni ninguna otra opción) devuelva la metadata de metadata.protos.foo. Como curl no puede mandar el header, alguien en el medio tiene que agregarlo: nginx.]

**Pista:** Si curl no puede llevar opciones, el header Metadata: true no lo puede mandar el cliente. ¿Quién más está en el camino del request y puede agregarlo?

**Resolución paso a paso**

**1. Escalá a root**

Todo lo que sigue escribe en /etc/nginx y /etc/hosts. Con sudo suelto el redirect de 'echo >> /etc/hosts' se ejecuta como tu usuario y falla con Permission denied, que es un clásico que cuesta cinco minutos de confusión.

```
sudo -i
```

_Verificación:_ whoami → root

**2. Hacé que metadata.local resuelva a tu máquina**

El TLD .local no existe en el DNS público: si no lo agregás a /etc/hosts, curl muere con 'Could not resolve host' sin llegar nunca a nginx. Como apunta a tu propio nginx, 127.0.0.1 alcanza.

```
echo "127.0.0.1 metadata.local" >> /etc/hosts
```

_Verificación:_ getent hosts metadata.local → 127.0.0.1

**3. Creá el virtual host con proxy_pass y los headers fijos**

proxy_pass trae el contenido del origen sin que cambie la URL. proxy_set_header Metadata true va con valor literal, no con una variable $http_*: el cliente nunca manda ese header (no puede, por el requisito), es nginx el que lo inyecta. El Accept: application/json es opcional: la consigna dice que el endpoint solo habla JSON, así que con el Accept: */* que manda curl ya alcanza; la solución del repo lo agrega por las dudas y no molesta.

```
# /etc/nginx/sites-available/metadata.local
server {
    listen 80;
    server_name metadata.local;

    location / {
        proxy_pass http://metadata.protos.foo;
        proxy_set_header Metadata true;
        proxy_set_header Accept application/json;
    }
}
```

**4. Habilitá el sitio con el symlink**

nginx solo incluye lo que está en sites-enabled; el archivo en sites-available es inerte. Usar ruta relativa al crear el link desde sites-enabled mantiene el symlink válido aunque muevas el árbol de config.

```
cd /etc/nginx/sites-enabled
ln -s ../sites-available/metadata.local ./metadata.local
ls -l /etc/nginx/sites-enabled/
```

_Verificación:_ El symlink tiene que aparecer apuntando a ../sites-available/metadata.local, sin flecha rota

**5. Validá y recargá nginx**

nginx -t detecta la llave sin cerrar o el upstream que no resuelve con el servicio todavía arriba. reload cambia la config sin cortar las conexiones en curso, a diferencia de restart.

```
nginx -t && systemctl reload nginx
```

_Verificación:_ 'syntax is ok' y 'test is successful'

**6. Verificá con curl**

El curl pelado es exactamente lo que pide el requisito y comprueba de punta a punta: resolución, virtual host, proxy y header. Para comparar, pegale directo al origen con y sin el header: así ves que lo que cambia la respuesta es Metadata: true.

```
curl http://metadata.local

# comparación, solo para entender (NO es la entrega):
curl http://metadata.protos.foo                      # sin header → error/403
curl -H "Metadata: true" http://metadata.protos.foo  # con header → JSON
```

_Verificación:_ curl http://metadata.local devuelve el mismo JSON que el pedido directo con -H "Metadata: true"

**Errores típicos**

- Resolverlo con curl -H "Metadata: true": viola el requisito, no se permiten otras opciones de curl.
- Escribir proxy_set_header Metadata $http_metadata: el cliente no manda ese header, hay que poner el valor literal true.
- No agregar metadata.local a /etc/hosts: curl no resuelve y nunca llega a nginx.
- Dejar el archivo en sites-available sin el symlink a sites-enabled: seguís viendo el sitio default de nginx.
- Usar return/rewrite en lugar de proxy_pass: devolvés un redirect y el cliente termina pidiéndole al origen sin el header.

_Fuente: Enunciado real: parcial práctico 2026 1C (PDF). Solución de referencia: Parcial 2026 1C.md del repo_

---

### Ejercicio 4: Navegar con curl un servidor HTTP detrás de un proxy SOCKS5

_[2026 1C]_

**Enunciado**

> Ejercicio III
> El administrador de su empresa necesita ayuda navegando el servidor HTTP en ejercicio3.protos.foo:3126 usando curl.
>
> Dato: la red no rutea paquetes hacia el host ejercicio3.protos.foo, pero este es accesible mediante un proxy <socks5h://proxy.protos.foo:1080>
>
> Notas: si no sabe cómo hacer un request HTTP utilizando un proxy SOCKSv5 puede obtener ayuda sobre cómo hacerlo, pero con una penalización de 0.5 puntos.
>
> [Nota: la consigna es abierta a propósito: el servidor te va guiando con sus respuestas. Según la solución del repo, el recorrido terminaba en un POST a /debugger con cuerpo JSON {"fix": "yes"}, Basic Auth admin:admin y los headers X-legajo y Accept-Language: es. Los pasos intermedios de abajo están reconstruidos a partir de ese comando final.]

**Pista:** El servidor te va a decir qué le falta, con el texto de la respuesta y con el código de estado. ¿Qué te pide un 401? ¿Y un 405? ¿Y un 415?

**Resolución paso a paso**

**1. Confirmá que sin proxy no llegás**

Verificar que el curl directo no llega confirma el diagnóstico del enunciado antes de sumar flags. --max-time evita que te quedes un minuto mirando un timeout.

```
curl -v --max-time 5 http://ejercicio3.protos.foo:3126/
```

_Verificación:_ Falla por timeout o por 'Could not resolve host': eso justifica el proxy

**2. Entrá por el proxy SOCKS5 con resolución remota y leé la raíz**

Este es el paso que vale 0.5 si pedís ayuda, así que tiene que salirte solo. -x enruta por el proxy. El esquema socks5h (con h) hace que el DNS lo resuelva el proxy; con socks5 pelado lo resolvería tu máquina, que no conoce el nombre. Arrancás por / porque 'navegar' significa empezar por el índice y leer qué te dice.

```
curl -v -x socks5h://proxy.protos.foo:1080 http://ejercicio3.protos.foo:3126/
```

_Verificación:_ Hay respuesta HTTP. Leé el cuerpo: ahí el servidor te dice a qué recurso ir o qué hacer

**3. Seguí las indicaciones leyendo cada código de estado**

Cada error del servidor es una pista de qué agregar. Usá -v siempre: las líneas '<' te muestran el código y headers como WWW-Authenticate o Allow. Esta tabla es la que hay que tener en la cabeza: 404 → ruta equivocada; 405 Method Not Allowed → cambiá el método (mirá el header Allow); 401 Unauthorized → falta autenticación, -u usuario:clave; 415 Unsupported Media Type → falta o está mal el Content-Type del cuerpo; 406 Not Acceptable → tus Accept / Accept-Language no coinciden con lo que hay; 400 → el cuerpo o un header obligatorio está mal.

```
# ejemplo del recorrido, agregando de a una cosa:
curl -v -x socks5h://proxy.protos.foo:1080 http://ejercicio3.protos.foo:3126/debugger
curl -v -x socks5h://proxy.protos.foo:1080 -X POST http://ejercicio3.protos.foo:3126/debugger
curl -v -x socks5h://proxy.protos.foo:1080 -X POST -u admin:admin \
  -H "Content-Type: application/json" -d '{"fix": "yes"}' \
  http://ejercicio3.protos.foo:3126/debugger
```

_Verificación:_ Cada respuesta cambia de código o de mensaje: si repetís un request y te da lo mismo, no agregaste lo que pedía

**4. Armá el comando final con todo lo pedido**

Lo que se entrega es el comando completo. -d manda el cuerpo e implica POST; el -X POST explícito deja claro el método. Sin Content-Type: application/json curl manda application/x-www-form-urlencoded y el backend no parsea el JSON. -u arma solo el Authorization: Basic con el base64 de admin:admin. X-legajo es un header propio con tu legajo y Accept-Language: es pide la versión en español.

```
curl -v -X POST \
  -x socks5h://proxy.protos.foo:1080 \
  -u "admin:admin" \
  -H "X-legajo: <tu legajo>" \
  -H "Accept-Language: es" \
  -H "Content-Type: application/json" \
  -d '{"fix": "yes"}' \
  http://ejercicio3.protos.foo:3126/debugger
```

_Verificación:_ En -v: líneas '>' con POST /debugger, Authorization, X-legajo, Accept-Language y Content-Type, y de vuelta un 200 con el mensaje final

**Errores típicos**

- Usar socks5:// en vez de socks5h://: tu máquina intenta resolver un nombre que solo existe del lado del proxy y falla.
- No usar -v: te perdés el código de estado y headers como WWW-Authenticate o Allow, que son justamente las pistas.
- Olvidar el Content-Type: application/json: curl manda form-urlencoded por defecto y el backend no parsea el cuerpo.
- Usar comillas dobles alrededor del JSON en bash: las comillas internas se rompen. Van comillas simples afuera.
- Olvidarse del puerto :3126 y pegarle al 80.
- Pedir la ayuda del proxy por miedo: son 0.5 puntos; -x socks5h://host:puerto tiene que salirte de memoria.

_Fuente: Enunciado real: parcial práctico 2026 1C (PDF). Solución de referencia: Parcial 2026 1C.md del repo_

---


### Ejercicio 5: extraer una clave SSH servida por nginx y entrar

El host 192.168.0.238 corre nginx con dos sitios: el del puerto 80 sirve `/var/www` como archivos estáticos (`server_name google.com`), y el del puerto 81 es un reverse proxy a `127.0.0.1:8000` (`server_name secrethost`). El mismo host tiene OpenSSH y solo deja entrar al usuario `pepito` con clave privada. La clave está en `/var/www/sec/mykey`, pero tiene passphrase. Hay que extraer la clave, conseguir la passphrase y hacer login.

**La idea:** el sitio del puerto 80 sirve `/var/www` con `try_files`, así que la clave guardada en `/var/www/sec/mykey` queda expuesta como un archivo más en `/sec/mykey`. El backend `127.0.0.1:8000` solo escucha en localhost y no se alcanza desde la red, pero el sitio del puerto 81 lo expone con `proxy_pass`, y por ahí sale la passphrase.

**1. Bajar la clave privada (puerto 80, `root /var/www`)**

```
curl http://192.168.0.238/sec/mykey -H "Host: google.com" -o mykey
cat mykey   # tiene que empezar con -----BEGIN OPENSSH PRIVATE KEY-----
```

El `-H "Host: google.com"` es lo que hace que nginx elija ese `server` (matchea `server_name`). Sin el header correcto cae en el default server y puede dar 404.

**2. Permisos estrictos (si no, ssh rechaza la clave)**

```
chmod 600 mykey
```

**3. Conseguir la passphrase por el proxy (puerto 81 → `127.0.0.1:8000`)**

```
curl http://192.168.0.238:81/ -H "Host: secrethost"
```

Ese contenido no es accesible directo desde afuera (el 8000 es solo localhost); el proxy del 81 es la única puerta. Ahí tiene que venir la passphrase.

**4. Login por SSH como pepito**

```
ssh -i ./mykey pepito@192.168.0.238
# en "Enter passphrase for key 'mykey':" poné la del paso 3
```

_Verificación:_ el `cat` del paso 1 muestra el bloque `BEGIN OPENSSH PRIVATE KEY`; el curl del paso 3 devuelve la passphrase; el ssh abre shell como `pepito`.

**Errores típicos**

- Olvidar el `-H "Host: ..."`: nginx enruta por `server_name` contra el header Host, no por la IP. Sin el Host correcto no pegás al server que querés.
- Usar `ssh -i mykey` sin `./`: con un nombre sin ruta, ssh puede buscarlo en `~/.ssh`. Poné `./mykey` o la ruta absoluta.
- Saltear el `chmod 600`: ssh aborta con "UNPROTECTED PRIVATE KEY FILE".
- Pegarle al 8000 directo desde afuera: no escucha en la red, solo en 127.0.0.1. Hay que pasar por el proxy del 81.

_Fuente: enunciado de parcial (nginx + SSH). Demuestra routing por server_name, archivos estáticos con try_files y reverse proxy a un backend solo-localhost._
