# SSH y túneles

> Cheatsheet de PROTOS (72.07, ITBA) para el parcial práctico. Repaso del tema y, al final, el paso a paso de todos los ejercicios de parciales que caen acá.

---

## Cheatsheet

### Conexión y claves

```
ssh usuario@host  # conexión básica (-p otro puerto)
chmod 600 ./clave  # ssh rechaza una clave que otros pueden leer
ssh -i ./clave usuario@host  # entrar con esa clave
scp -i ./clave usuario@host:/ruta/archivo .  # copiar un archivo
```

### Los tres túneles

> **La puerta va del lado del que llama**
>
> Las letras **no** dicen hacia dónde van los datos (en los tres van para los dos lados, como una llamada: uno llama, el otro atiende, después hablan los dos). Dicen **en qué máquina se abre la puerta**, el puerto donde alguien se conecta.
> 
> | Túnel | La puerta está en | Lleva a |
> |---|---|---|
> | -L | **tu** máquina | un destino fijo visto desde el servidor |
> | -R | el **servidor** | un destino fijo visto desde tu máquina |
> | -D | **tu** máquina | cualquier destino (proxy SOCKS) |
> 
> Para elegir: ¿de qué lado está el que **llama** (`nc host puerto`, `curl`)? La puerta va de ese lado; el que **atiende** (`nc -l`) va del otro.

### Seguir un archivo remoto en vivo (2022/2026), con -L

Que `tail -f archivo.log` en tu host muestre en vivo `/tmp/pdc` de pampero. Con `-L`, **pampero atiende** y **tu host llama**. 4 comandos, 3 terminales:

```
# term 1 (tu host): abrir el túnel
ssh -L 5522:localhost:5522 usuario@pampero.itba.edu.ar

# ya en pampero (misma terminal): atender y mandar
tail -f /tmp/pdc | nc -l localhost 5522

# term 2 (tu host): llamar y guardar
nc localhost 5522 > archivo.log

# term 3 (tu host): mirar
tail -f archivo.log
```

- `-l` = **listen**: ese `nc` es el servidor (atiende). Sin `-l` es el cliente. El que atiende arranca primero.
- Orden: 1) túnel, 2) `nc -l` en pampero, 3) `nc` en tu host.
- No "termina": queda andando; cada línea nueva en pampero aparece en tu `tail -f`. Cortás con `Ctrl+C` y `exit`.

> **Si no anda**
>
> **localhost vs 127.0.0.1:** si `localhost` resuelve a IPv6, el túnel va por `::1` y `nc` escucha en IPv4 → rechazado. Usá `127.0.0.1` en los dos lados. **Otro nc:** si `nc -l localhost 5522` da error de uso, es el tradicional: `nc -l -p 5522`.

### Con -R (la otra solución válida)

```
nc -l localhost 8080 > archivo.log  # en TU host, escuchás (antes que pampero)
ssh -R 5522:localhost:8080 usuario@pampero
tail -f /tmp/pdc | nc localhost 5522  # en pampero, mandás
```

Puerto remoto > 1024; si piden usar el legajo, ese es. La consigna pide túnel, no dice cuál: las dos valen.

### -L: llegar a algo interno del servidor

```
ssh -L <puerto_local>:<host_destino>:<puerto_destino> usuario@servidor

ssh -L 1234:localhost:5432 usuario@servidor  # la DB del servidor, en tu puerto 1234
psql -h localhost -p 1234  # desde tu PC, como si fuera local
```

### -D: usar el servidor como proxy SOCKS

```
ssh -D 1080 usuario@servidor
curl -x socks5h://localhost:1080 http://loquesea  # navegás con la IP del servidor
```

Para practicar `curl -x` en casa: `ssh -N -D 1080 localhost` y ya tenés un SOCKS en el 1080.

### Claves y warnings

```
ssh-keygen -t ed25519  # generar par de claves (privada + .pub)
ssh-copy-id usuario@host  # copiar tu pública a authorized_keys del servidor
ssh-keygen -R <host>  # arreglar "REMOTE HOST IDENTIFICATION HAS CHANGED"
```

ProxyCommand (el acceso del integrador): `ssh -o ProxyCommand="docker run --rm -i cloudflare/cloudflared access ssh --hostname %h" usuario@host`.

---

## Ejercicios paso a paso

### Ejercicio 1: Seguir en vivo un archivo remoto con un túnel SSH reverso

_[2022 2C]_

**Enunciado**

> Ejercicio 2
>
> Realice los cambios necesarios para que desde una terminal de su host al ejecutar el comando tail -f archivo.log pueda seguir los cambios (en vivo) del archivo /tmp/pdc del host pampero.itba.edu.ar.
>
> Nota: Cualquier solución que no involucre túneles obtendrá la mitad del puntaje posible.
>
> Si necesita asignar puertos para túneles use su número de legajo para evitar colisiones.
>
> Pista: tail -f, netcat y los pipes de UNIX (|) y las redirecciones de archivo ( > ) pueden canalizar la información.

**Pista:** El dato nace en pampero y tiene que terminar en tu disco: ¿quién inicia la conexión del flujo, y por eso el túnel es -L o -R?

**Resolución paso a paso**

**1. Elegí el puerto del túnel con tu número de legajo**

El enunciado lo pide explícitamente porque pampero es compartido: si todos usan el 8080, el segundo que abre el túnel recibe 'remote port forwarding failed for listen port' y el túnel no se establece. Derivar el puerto del legajo garantiza que no choques con nadie.

```
# legajo 65522 → puerto remoto 5522 (o 55522 si querés usarlo entero)
PUERTO_REMOTO=5522
PUERTO_LOCAL=8080
```

**2. Dejá un netcat local escuchando y volcando a archivo.log**

Esto tiene que estar arriba antes de que pampero intente conectarse, sino la conexión del nc remoto se rechaza. El '>' redirige todo lo que entre por el socket al archivo, que es lo que después vas a seguir con tail. Dejalo corriendo en su propia terminal.

```
nc -l localhost 8080 > archivo.log
```

_Verificación:_ ss -tlnp | grep 8080 → nc escuchando en 127.0.0.1:8080

**3. Abrí el túnel reverso desde tu máquina hacia pampero**

-R abre un puerto de escucha en pampero (5522) que desemboca en tu localhost:8080. La regla para elegir no es hacia dónde viajan los datos: TCP es bidireccional, y una vez conectados los datos van para cualquier lado. Lo que decide es dónde está el que escucha (nc -l). Acá el nc -l está en tu host, así que el nc de pampero tiene que poder llegar a tu host: hace falta un puerto en pampero que desemboque en tu máquina, y eso es -R. Si en cambio pusieras el nc -l en pampero, serías vos el que se conecta hacia allá, y eso es -L (ver el último paso).

```
ssh -R 5522:localhost:8080 rohernandez@pampero.itba.edu.ar
```

_Verificación:_ Quedás con una shell en pampero; ahí ss -tlnp | grep 5522 muestra el sshd escuchando

**4. En pampero, canalizá el archivo por netcat hacia el túnel**

tail -f deja el archivo abierto y va escupiendo cada línea nueva a medida que se escribe; el pipe se la pasa a nc, que la manda por el túnel. Como es un flujo continuo, el comando no termina: eso es lo que hace que el seguimiento sea en vivo.

```
tail -f /tmp/pdc | nc localhost 5522
```

_Verificación:_ No tiene que volver al prompt; en tu máquina archivo.log empieza a crecer

**5. Seguí el archivo local en una tercera terminal**

Es lo que literalmente pide la consigna. Ahora tenés tres procesos vivos en paralelo (nc local, túnel ssh, tail|nc remoto) y la cadena completa: pampero → nc → túnel → nc local → archivo.log → tail.

```
tail -f archivo.log
```

_Verificación:_ Cada línea que se agregue a /tmp/pdc en pampero aparece acá en un par de segundos

**6. Alternativa igual de válida: el mismo ejercicio con -L**

Invertís quién escucha. El nc -l pasa a pampero, junto con el tail, y vos te conectás hacia allá. -L abre un puerto en TU máquina que desemboca en localhost:5522 de pampero. Los datos siguen viajando de pampero a tu host: tu nc es el cliente, pero recibe lo que el servidor le manda. Orden: primero el nc -l en pampero, después tu nc.

```
# terminal 1 (tu host): túnel local
ssh -L 5522:localhost:5522 usuario@pampero.itba.edu.ar

# en esa sesión, ya en pampero: el que escucha es pampero
tail -f /tmp/pdc | nc -l localhost 5522

# terminal 2 (tu host): te conectás al túnel y guardás
nc localhost 5522 > archivo.log

# terminal 3 (tu host)
tail -f archivo.log
```

_Verificación:_ Lo mismo que con -R: cada línea que se agrega al archivo en pampero aparece en tu tail -f archivo.log.

**Errores típicos**

- Mezclar las dos soluciones: con -R el nc -l va en tu host; con -L el nc -l va en pampero. Un -L con el nc -l en tu host no funciona, porque del lado al que apunta el túnel no hay nadie escuchando.
- Levantar el tail|nc en pampero antes de que el nc local esté escuchando: la conexión se rechaza y no fluye nada.
- Usar un puerto genérico como el 8080 en pampero: choca con el de otro alumno y el forwarding falla.
- Resolverlo con scp en un loop o con ssh 'tail -f' directo: anda, pero el enunciado aclara que sin túnel se pierde la mitad del puntaje.
- Usar '>>' o cerrar el nc local entre pruebas: el archivo se trunca o el túnel queda sin el otro extremo.

_Fuente: 2022_2c_practico.md_

---

### Ejercicio 2: Túnel SSH reverso para seguir en vivo /tmp/parcial-20261

_[2026 1C]_

**Enunciado**

> EJERCICIO IV
> Realice los cambios necesarios para que desde una terminal de su host al ejecutar el comando tail -f archivo.log pueda seguir los cambios (en vivo) del archivo /tmp/parcial-20261 del host pampero.itba.edu.ar
>
> Notas: Cualquier solución que no involucre túneles obtendrá la mitad del puntaje posible.
> Si necesita asignar puertos para túneles use su número de legajo para evitar colisiones.
>
> Pista: tail -f, netcat y los pipes de UNIX ( | ) y las redirecciones de archivo ( > ) pueden canalizar la información

**Pista:** pampero es el que tiene el dato y el que tiene que iniciar la conexión hacia vos: ¿eso es -L o -R?

**Resolución paso a paso**

**1. Elegí el puerto remoto a partir de tu legajo**

pampero lo usa toda la cursada al mismo tiempo. Si pedís un puerto genérico ya tomado, ssh te contesta 'Warning: remote port forwarding failed for listen port' y el túnel queda sin el extremo remoto, pero la sesión igual se abre, así que es fácil no darse cuenta.

```
# legajo 65522 → puerto remoto 55522
PUERTO_REMOTO=55522
PUERTO_LOCAL=8080
```

**2. Dejá un netcat escuchando en tu máquina y volcando a archivo.log**

Tiene que estar arriba antes de que pampero se conecte, sino el nc remoto recibe un connection refused. El '>' manda todo lo que entre por el socket al archivo, que es el que después vas a seguir. Dejalo en su propia terminal, no lo mandes a background.

```
nc -l localhost 8080 > archivo.log
```

_Verificación:_ ss -tlnp | grep 8080 → nc escuchando en 127.0.0.1:8080

**3. Abrí el túnel reverso contra pampero**

-R abre un puerto de escucha en pampero (55522) que desemboca en tu localhost:8080. La regla para elegir no es hacia dónde viajan los datos: TCP es bidireccional, y una vez conectados los datos van para cualquier lado. Lo que decide es dónde está el que escucha (nc -l). Acá el nc -l está en tu host, así que el nc de pampero tiene que poder llegar a tu host: hace falta un puerto en pampero que desemboque en tu máquina, y eso es -R. Si en cambio pusieras el nc -l en pampero, serías vos el que se conecta hacia allá, y eso es -L (ver el último paso).

```
ssh -R 55522:localhost:8080 rohernandez@pampero.itba.edu.ar
```

_Verificación:_ Quedás con shell en pampero y no aparece ningún warning de 'remote port forwarding failed'

**4. En pampero, canalizá el archivo por el túnel con tail y nc**

tail -f mantiene el archivo abierto y emite cada línea nueva apenas se escribe; el pipe se la entrega a nc, que la manda por el túnel hacia tu nc local. Por eso el comando nunca vuelve al prompt: es un flujo continuo, no una copia puntual.

```
tail -f /tmp/parcial-20261 | nc localhost 55522
```

_Verificación:_ No vuelve al prompt, y en tu máquina archivo.log empieza a crecer

**5. Seguí el archivo local en una tercera terminal**

Es el comando textual que pide la consigna. En este punto tenés tres procesos vivos: el nc local, la sesión ssh con el túnel, y el tail|nc en pampero. Si cortás cualquiera de los tres, el flujo se corta.

```
tail -f archivo.log
```

_Verificación:_ Cada línea nueva de /tmp/parcial-20261 aparece en tu terminal casi en el momento

**6. Alternativa igual de válida: el mismo ejercicio con -L**

Invertís quién escucha. El nc -l pasa a pampero, junto con el tail, y vos te conectás hacia allá. -L abre un puerto en TU máquina que desemboca en localhost:55522 de pampero. Los datos siguen viajando de pampero a tu host: tu nc es el cliente, pero recibe lo que el servidor le manda. Orden: primero el nc -l en pampero, después tu nc.

```
# terminal 1 (tu host): túnel local
ssh -L 55522:localhost:55522 usuario@pampero.itba.edu.ar

# en esa sesión, ya en pampero: el que escucha es pampero
tail -f /tmp/parcial-20261 | nc -l localhost 55522

# terminal 2 (tu host): te conectás al túnel y guardás
nc localhost 55522 > archivo.log

# terminal 3 (tu host)
tail -f archivo.log
```

_Verificación:_ Lo mismo que con -R: cada línea que se agrega al archivo en pampero aparece en tu tail -f archivo.log.

**Errores típicos**

- Mezclar las dos soluciones: con -R el nc -l va en tu host; con -L el nc -l va en pampero. Un -L con el nc -l en tu host no funciona, porque del lado al que apunta el túnel no hay nadie escuchando.
- Arrancar el tail|nc en pampero antes de levantar el nc local: connection refused y no fluye nada.
- Elegir un puerto que ya esté ocupado en pampero: el forwarding falla con un warning fácil de pasar por alto y el túnel queda inútil.
- Resolverlo con un ssh 'tail -f' directo o con scp en loop: funciona pero el enunciado descuenta la mitad del puntaje por no usar túnel.
- Cerrar la sesión ssh creyendo que el túnel queda solo: al cerrarla se cae el forwarding y el flujo se corta.

_Fuente: Enunciado real: parcial práctico 2026 1C (PDF). Solución de referencia: Parcial 2026 1C.md del repo_

---

