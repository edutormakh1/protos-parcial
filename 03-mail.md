# Mail: SMTP, POP3 y MIME

> Cheatsheet de PROTOS (72.07, ITBA) para el parcial práctico. Repaso del tema y, al final, el paso a paso de todos los ejercicios de parciales que caen acá.

---

## Cheatsheet

### La sesión SMTP a mano

SMTP es TCP 25. `nc -C` manda CRLF, obligatorio: sin eso el punto final no cierra el mensaje.

```
nc -C localhost 25
EHLO localhost
MAIL FROM:<remitente@dominio>
RCPT TO:<destinatario@pdc.lab>
DATA
From: Nombre Apellido <remitente@dominio>
To: <destinatario@pdc.lab>
Subject: =?UTF-8?B?...?=
Date: Sun, 25 Oct 2026 22:30:00 -0300
X-Parcial: 2024/2
MIME-Version: 1.0
Content-Type: text/plain; charset="UTF-8"
Content-Transfer-Encoding: base64

wqFIb2xhIG11bmRvIQ==
.
QUIT
```

> **Sobre vs headers**
>
> `MAIL FROM` y `RCPT TO` son el **sobre**: con eso el servidor decide a quién entrega. `From:`/`To:` son headers (lo que muestra el cliente). La entrega la manda el `RCPT TO`. La **línea en blanco** separa headers del cuerpo, y el **punto solo** cierra el DATA.

### Codificar lo que tiene tildes

Se codifica **distinto** según dónde vaya. Preparalo **antes** de conectarte (adentro de netcat no corrés comandos):

```
echo -n '¡Buenos días!' | base64  # Subject → =?UTF-8?B?<lo que salga>?=
wqFCdWVub3MgZMOtYXMh

echo -n '¡Hola mundo!' | base64  # cuerpo → tal cual, con Content-Transfer-Encoding: base64
wqFIb2xhIG11bmRvIQ==
```

- **Header** (Subject): encoded-word `=?UTF-8?B?<base64>?=`.
- **Cuerpo**: base64 pelado + `Content-Transfer-Encoding: base64`.
- El `-n` no es opcional (si no, codifica un salto de línea de más). Si el subject no tiene tildes, va en texto normal.

> **2024 ej4: una sola parte base64**
>
> El punto (f) pide que lo lea un cliente que **no** soporta quoted-printable. Un solo `text/plain` en base64 cumple y es lo más seguro. El multipart (una parte quoted-printable + otra base64) funciona pero es más largo y roza lo que la consigna excluye.

### Respuestas del servidor SMTP

| Código | Cuándo / qué |
|---|---|
| 220 | al conectar: listo |
| 250 | EHLO / MAIL FROM / RCPT TO / fin del DATA: OK |
| 354 | después de DATA: escribí el mail y cerrá con un punto |
| 550 | RCPT TO: destinatario no existe o no se acepta |

### Cuerpo con varias partes (multipart)

Si piden texto y HTML, o un adjunto. El `boundary` lo inventás vos, se declara en el `Content-Type` y no puede aparecer en el contenido. El cierre lleva `--` al final.

```
Content-Type: multipart/alternative; boundary="SEP123"

--SEP123
Content-Type: text/plain; charset="UTF-8"
Content-Transfer-Encoding: base64

wqFIb2xhIG11bmRvIQ==
--SEP123
Content-Type: text/html; charset="UTF-8"
Content-Transfer-Encoding: base64

PHA+wqFIb2xhIG11bmRvITwvcD4=
--SEP123--  # cierre: dos guiones al final
```

`alternative` = el mismo contenido en formatos distintos (texto y HTML). `mixed` = cosas distintas (texto + adjunto). Quoted-printable (la otra codificación de cuerpo): cada byte no-ASCII como `=HH`, ej. `¡` es `=C2=A1`.

### Leer por POP3 y demostrar que funciona

```
nc -C localhost 110
USER alumno
PASS <contraseña>
LIST  # número y tamaño de cada mail
RETR 1  # descargar el mensaje 1 completo
DELE 1  # marcar para borrar (se aplica al QUIT)
QUIT
```

En el examen el usuario es la dirección completa (`USER tulegajo@mail.sebikul.com`, misma cadena de contraseña). Demostrar que funciona NO es solo el "250 queued": `sudo grep status=sent /var/log/mail.log` + leerlo por POP3.

### Simular el servidor en tu máquina

```
sudo apt install postfix dovecot-pop3d  # postfix: "Internet Site", dominio pdc.lab
postconf -e 'home_mailbox = Maildir/'  # cada mail en un archivo
systemctl restart postfix
```

Dovecot lee el mismo buzón (`dovecot --version`): 2.3 → `mail_location = maildir:~/Maildir`; 2.4 → `mail_driver = maildir` + `mail_path = ~/Maildir`. Después `sudo adduser alumno`. Si POP3 da `Plaintext authentication disallowed`: `disable_plaintext_auth = no` en `10-auth.conf`.

### MX y STARTTLS (lo justo)

Para mandar a `usuario@dominio`, el que envía mira el **MX** del dominio (`dig MX dominio`) y se conecta a su 25. **STARTTLS** arranca en texto plano y sube a TLS sin cambiar de puerto (587). SPF es un TXT con las IPs autorizadas a mandar.

---

## Ejercicios paso a paso

### Ejercicio 1: Encontrar y corregir los errores de una sesión SMTP

_[Probable 2C 2026]_

**Enunciado**

> La siguiente sesión SMTP fue capturada de un alumno que intentaba enviar un mail multipart desde la VM del laboratorio, contra el Postfix local (dominio pdc.lab, usuario rodri). El mail nunca llegó a destino. Se transcribe tal cual la tipeó el alumno, con las líneas numeradas; las respuestas del servidor no se incluyen.
>
>  1  alumno@vm:~$ nc localhost 25
>  2  MAIL FROM: alumno@itba.edu.ar
>  3  RCPT FROM: <rodri@pdc.lab>
>  4  EHLO localhost
>  5  DATA
>  6  Subject: Entrega del práctico de mañana
>  7  From: Un Alumno <alumno@itba.edu.ar>
>  8  To: rodri <rodri@pdc.lab>
>  9  Content-Type: multipart/alternative
> 10  --limite123
> 11  Content-Type: text/plain
> 12  Content-Transfer-Encoding: base64
> 13
> 14  Hola Rodri, te paso el práctico resuelto.
> 15
> 16  --limite_123
> 17  Content-Type: text/html; charset="UTF-8"
> 18  Content-Transfer-Encoding: 7bit
> 19
> 20  <html><body><p>Hola Rodri, te paso el práctico resuelto.</p></body></html>
> 21
> 22  --limite_123
> 23  .
>
> (las líneas 13, 15, 19 y 21 están vacías)
>
> 1. Encontrar y corregir los errores. Hay 8 errores que rompen (la sesión no se completa, o el mail llega mal formado y el cliente no lo puede mostrar) y 5 detalles menores (el mail llega, pero incumple las RFC o se ve mal). Indicar para cada uno el número de línea, qué está mal y cómo se corrige.
>
> 2. Demostrar que la versión corregida funciona enviándola contra el Postfix de la VM y verificando la recepción.

**Pista:** Leé la sesión dos veces: una mirando solo el diálogo SMTP (quién saluda, quién habla primero, cómo se escriben las direcciones) y otra mirando solo el mensaje que va adentro del DATA, donde el servidor te va a contestar 250 Ok aunque esté todo mal.

**Resolución paso a paso**

**1. Encarar la lectura en dos pasadas, no de corrido**

Si leés la sesión línea por línea de arriba abajo mezclás dos protocolos distintos y se te escapan errores. Las líneas 1 a 5 (más el punto de la línea 23 y el QUIT que falta) son el diálogo SMTP: ahí el que se queja es el servidor, con códigos 4xx/5xx, y el mail no sale. Las líneas 6 a 22 son el contenido del DATA: ahí el servidor te dice 250 Ok igual, porque a Postfix el cuerpo le da lo mismo, y el error solo se ve después, cuando abrís el mail y el cliente te muestra el boundary crudo o un bloque de base64 ilegible. Separar las dos pasadas también te ordena la respuesta: primero los errores de diálogo, después los de mensaje.

```
Pasada 1 — diálogo SMTP (líneas 1-5, 23):
  comando nc, orden de EHLO / MAIL FROM / RCPT TO, sintaxis de las direcciones, cierre con . y QUIT

Pasada 2 — mensaje RFC 822 + MIME (líneas 6-22):
  headers, línea en blanco separadora, Content-Type del multipart,
  boundary declarado vs. boundary usado, cierre del multipart,
  Content-Transfer-Encoding de cada parte
```

_Verificación:_ Antes de escribir nada, hacé una lista de dos columnas: «errores de diálogo» y «errores de mensaje». Si en una columna te quedan 0 errores, volvé a leer: el ejercicio tiene errores de los dos tipos.

**2. Transporte de la sesión: nc sin -C, o sea sin CRLF (línea 1)**

SMTP termina cada línea con CRLF (\r\n). nc sin -C manda solo LF (\n), así que el servidor nunca ve el fin de línea que espera. El síntoma típico es que te conectás, ves el 220 de bienvenida y después todo lo que tipeás queda sin respuesta: el servidor sigue esperando el CR y no te contesta ni 250 ni un error. Y lo peor es que el DATA también queda mal, porque el fin de mensaje es exactamente CRLF . CRLF: el punto de la línea 23 no cierra nada y la sesión se cuelga hasta que corta por timeout. Es el error más fácil de arreglar y el que más gente deja pasar, porque toda la sintaxis de abajo puede estar perfecta y no anda igual.

```
nc -C localhost 25

# equivalente, mandando el mail desde un archivo:
cat mail.txt | nc -C localhost 25

# si el nc de la VM no tiene -C:
openssl s_client -crlf -connect localhost:25 -starttls smtp

# para ver los bytes que salen:
cat -A mail.txt   # cada línea tiene que terminar en ^M$
```

_Verificación:_ Conectate con nc -C localhost 25, tipeá EHLO localhost y tiene que contestarte 250 con la lista de extensiones (PIPELINING, SIZE, STARTTLS...). Si no contesta nada, seguís sin CRLF.

**3. Diálogo SMTP: falta el EHLO al principio, MAIL FROM sin <>, no hay RCPT TO, falta el QUIT (líneas 2, 3, 4, 23)**

Son cuatro cosas y cada una tiene su síntoma propio. (a) El EHLO está en la línea 4, después del MAIL FROM: el saludo tiene que ser lo primero, así que el MAIL FROM de la línea 2 se come un «503 5.5.1 Error: send HELO/EHLO first» y la transacción arranca muerta. (b) La dirección de la línea 2 va sin angle brackets: la RFC 5321 pide el reverse-path entre <>, y aunque Postfix suele ser permisivo, un MTA estricto te devuelve «501 5.5.4 Syntax: MAIL FROM: <address>»; en el parcial se corrige igual, porque lo que se evalúa es la sintaxis. (c) La línea 3 dice RCPT FROM, que no existe como comando: el servidor contesta «502 5.5.2 Error: command not recognized» y te quedás sin ningún destinatario, así que el DATA de la línea 5 muere con «554 5.5.1 Error: no valid recipients». Eso solo ya garantiza que el mail no sale. (d) Después del punto de la línea 23 no hay QUIT: el servidor igual encola el mail, por eso cuenta como detalle menor y no como error que rompe, pero la conexión queda abierta hasta el timeout y nunca ves el «221 2.0.0 Bye» que te confirma el cierre limpio.

```
EHLO localhost
MAIL FROM: <alumno@itba.edu.ar>
RCPT TO: <rodri@pdc.lab>
DATA
... headers + cuerpo ...
.
QUIT
```

_Verificación:_ Cada comando tiene que devolver 250 (y el DATA un 354 «End data with <CR><LF>.<CR><LF>»). Después del punto tiene que aparecer «250 2.0.0 Ok: queued as XXXX» y después del QUIT un «221 2.0.0 Bye». Si el RCPT TO te da «550 5.1.1 User unknown», el usuario no existe en el sistema: verificalo con id rodri.

**4. Estructura del mensaje: falta la línea en blanco, falta MIME-Version, falta Date, Subject con acentos sin codificar (líneas 6, 9, 10)**

La RFC 822 dice headers, línea en blanco, cuerpo, y esa línea en blanco falta entre la 9 y la 10. El síntoma es muy concreto: el «--limite123» de la línea 10 se lee como si fuera otro header y no como el comienzo del cuerpo, así que el parser se come todo el multipart como headers inválidos y te llega un mail vacío o con el cuerpo entero pegado arriba. Los otros tres son menores, pero igual se ven. Sin MIME-Version: 1.0 el cliente tiene derecho a ignorar todo el MIME y mostrarte el cuerpo crudo con los boundaries a la vista, incluso si el multipart está bien armado. Sin Date, el MTA te lo agrega al recibirlo, pero el mensaje que vos emitiste no es válido y en el mail recibido vas a ver la fecha de entrega, no la de envío. Y el Subject de la línea 6 lleva «práctico» y «mañana» con acentos sin codificar: los headers son ASCII de 7 bits, así que el resultado es el clásico asunto con «pr?ctico» o «prÃ¡ctico» según cómo lo interprete el cliente. La codificación es RFC 2047: =?charset?B?base64?= o =?charset?Q?quoted-printable?=.

```
MIME-Version: 1.0
Date: Tue, 21 Oct 2026 19:30:00 -0300
Subject: =?UTF-8?B?RW50cmVnYSBkZWwgcHLDoWN0aWNvIGRlIG1hw7FhbmE=?=
From: Un Alumno <alumno@itba.edu.ar>
To: rodri <rodri@pdc.lab>
Content-Type: multipart/alternative; boundary="limite123"
                      <-- ESTA línea en blanco es la que faltaba
--limite123

# cómo se saca ese base64 del Subject:
echo -n 'Entrega del práctico de mañana' | base64
#   -> RW50cmVnYSBkZWwgcHLDoWN0aWNvIGRlIG1hw7FhbmE=

# y la fecha en formato RFC 2822:
date -R
```

_Verificación:_ En el mail recibido (cat ~/Maildir/new/*) tiene que haber una línea completamente vacía entre el último header y el primer «--limite123», y el Subject tiene que mostrarse con los acentos bien. Ojo con el final del Subject codificado: el «=» del padding de base64 va adentro y después cierra con «?=», no con un solo «=».

**5. MIME multipart: boundary no declarado, boundary usado distinto, cierre sin los -- finales, base64 declarado sobre texto plano (líneas 9, 10, 11, 12, 14, 16, 18, 22)**

Acá están los errores que hacen que el mail llegue pero sea ilegible. (a) La línea 9 declara multipart/alternative sin el parámetro boundary: sin eso el cliente no tiene con qué separar las partes, no hay default posible, y de nuevo termina mostrando el cuerpo completo en bruto con los «--limite123» adentro del texto. (b) El delimitador de la línea 10 es limite123 pero los de las líneas 16 y 22 son limite_123: aun arreglando la declaración, solo la primera parte matchea y el resto queda como contenido de esa primera parte, así que te llega un único bloque de texto con el HTML pegado al final. El boundary declarado y todos los delimitadores tienen que ser idénticos, carácter por carácter. (c) La línea 22 es el cierre del multipart y no termina en --: sin el «--limite123--» el cliente nunca sabe dónde cierra la última parte, la trata como truncada y algunos clientes la descartan entera. (d) La línea 12 declara Content-Transfer-Encoding: base64 pero la línea 14 es texto plano legible: el cliente intenta decodificar «Hola Rodri, te paso el práctico resuelto.» como base64, encuentra caracteres inválidos y te muestra basura o nada. Hay dos salidas: codificar el cuerpo en base64 de verdad, o declarar el encoding que corresponde — y como esa parte tiene acentos, lo prolijo es base64 o quoted-printable, nunca 7bit (que es justamente el otro error, la línea 18 declara 7bit sobre un HTML con «práctico»). De paso, la línea 11 declara text/plain sin charset: el default de MIME es us-ascii, así que los acentos se ven mal incluso con el encoding correcto; va charset="UTF-8" como en la parte HTML de la línea 17.

```
Content-Type: multipart/alternative; boundary="limite123"

--limite123
Content-Type: text/plain; charset="UTF-8"
Content-Transfer-Encoding: base64

SG9sYSBSb2RyaSwgdGUgcGFzbyBlbCBwcsOhY3RpY28gcmVzdWVsdG8uCg==

--limite123
Content-Type: text/html; charset="UTF-8"
Content-Transfer-Encoding: quoted-printable

<html><body><p>Hola Rodri, te paso el pr=C3=A1ctico resuelto.</p></body></html>

--limite123--

# para generar el base64 de la parte text/plain:
echo 'Hola Rodri, te paso el práctico resuelto.' | base64
#   -> SG9sYSBSb2RyaSwgdGUgcGFzbyBlbCBwcsOhY3RpY28gcmVzdWVsdG8uCg==

# y para verificar que decodifica bien:
echo 'SG9sYSBSb2RyaSwgdGUgcGFzbyBlbCBwcsOhY3RpY28gcmVzdWVsdG8uCg==' | base64 -d
```

_Verificación:_ Contá los delimitadores: en un multipart de dos partes tienen que haber exactamente 3 líneas que empiecen con --limite123, y solo la última termina en --. Cada parte tiene además su propia línea en blanco entre sus headers y su contenido. Y si declarás base64, pasá el bloque por base64 -d: tiene que salir el texto original.

**6. La sesión corregida completa, lista para copiar**

Conviene escribirla entera de una sola vez en vez de ir parchando la original: así te asegurás de que el orden del diálogo y la estructura del mensaje queden coherentes entre sí, y es lo que te van a pedir mostrar en el parcial. Guardala en un archivo y mandala con cat | nc -C, así si algo sale mal corregís el archivo y repetís sin tener que retipear 25 líneas en la terminal.

```
alumno@vm:~$ nc -C localhost 25
EHLO localhost
MAIL FROM: <alumno@itba.edu.ar>
RCPT TO: <rodri@pdc.lab>
DATA
MIME-Version: 1.0
Date: Tue, 21 Oct 2026 19:30:00 -0300
Subject: =?UTF-8?B?RW50cmVnYSBkZWwgcHLDoWN0aWNvIGRlIG1hw7FhbmE=?=
From: Un Alumno <alumno@itba.edu.ar>
To: rodri <rodri@pdc.lab>
Content-Type: multipart/alternative; boundary="limite123"

--limite123
Content-Type: text/plain; charset="UTF-8"
Content-Transfer-Encoding: base64

SG9sYSBSb2RyaSwgdGUgcGFzbyBlbCBwcsOhY3RpY28gcmVzdWVsdG8uCg==

--limite123
Content-Type: text/html; charset="UTF-8"
Content-Transfer-Encoding: quoted-printable

<html><body><p>Hola Rodri, te paso el pr=C3=A1ctico resuelto.</p></body></html>

--limite123--
.
QUIT
```

_Verificación:_ Releela contra la lista de 13 errores y marcá uno por uno dónde quedó corregido: los 6 headers (MIME-Version, Date, Subject, From, To, Content-Type), la línea en blanco separadora, los 3 delimitadores con el último cerrado en --, el punto solo y el QUIT.

**7. Verificar contra el Postfix de la VM: enviar, mirar el log y leer el mail**

El punto 2 de la consigna pide demostrar que funciona, y «me contestó 250 Ok» no alcanza: el 250 solo dice que Postfix aceptó el mensaje, no que se entregó ni que el MIME esté bien. Las tres evidencias que valen son el status=sent en /var/log/mail.log, el archivo en ~/Maildir/new/ y el RETR por POP3 mostrando las dos partes con el cierre --limite123--. Si el mail no aparece en ~/Maildir, casi siempre es que falta home_mailbox=Maildir/ en /etc/postfix/main.cf y los mails están cayendo en /var/spool/mail/rodri, donde dovecot no los busca.

```
# 1. mandar el mail desde archivo (ojo: también va con -C)
cat mail.txt | nc -C localhost 25

# 2. ver que postfix lo entregó
sudo tail -f /var/log/mail.log
# buscar: to=<rodri@pdc.lab>, ... status=sent (delivered to maildir)

# 3. leerlo en el filesystem
ls -l ~/Maildir/new/
cat ~/Maildir/new/*

# 4. leerlo por POP3
nc -C localhost 110
USER rodri
PASS <contraseña del sistema>
LIST
RETR 1
QUIT

# 5. decodificar la parte base64 para probar que se lee bien
echo 'SG9sYSBSb2RyaSwgdGUgcGFzbyBlbCBwcsOhY3RpY28gcmVzdWVsdG8uCg==' | base64 -d
```

_Verificación:_ En mail.log tiene que decir status=sent. El RETR tiene que devolver el mensaje con MIME-Version, Date, el Subject codificado, la línea en blanco, las dos partes y el cierre --limite123--. Si POP3 no lista nada, revisá mail_location (o mail_driver/mail_path en las versiones nuevas) en /etc/dovecot/conf.d/10-mail.conf apuntando a maildir:~/Maildir y reiniciá dovecot.

**Errores típicos**

- Corregir toda la sintaxis del diálogo y del MIME pero seguir mandando con nc sin -C: el mail no sale igual y el error más barato del ejercicio queda sin ver. El -C se menciona en la línea 1 justamente para que lo encuentres.
- Agregar el boundary al Content-Type de la línea 9 y olvidarse de que los delimitadores del cuerpo (líneas 10, 16 y 22) tienen que ser el mismo string, o dejar el cierre de la línea 22 sin los dos guiones finales. El multipart queda roto igual y el cliente muestra el cuerpo crudo.
- Arreglar el RCPT FROM a RCPT TO pero dejar el EHLO en la línea 4: el orden importa, el MAIL FROM antes del saludo se come un 503 y la transacción no arranca. Hay que moverlo arriba, no solo corregir el comando.
- Dejar Content-Transfer-Encoding: base64 sobre el texto plano de la línea 14 «porque el header está bien escrito». El header y el contenido tienen que ser coherentes: o codificás el cuerpo, o cambiás el encoding declarado.
- Mezclar los dos tipos de error en la respuesta y no decir cuál es cuál. La consigna distingue 8 que rompen de 5 detalles menores, así que conviene entregarlos en dos listas separadas y con el número de línea de cada uno.
- Cerrar la demostración con el «250 Ok: queued as...» como única prueba. Eso solo dice que Postfix aceptó el mensaje; hay que mostrar status=sent en /var/log/mail.log y el mail leído desde ~/Maildir o por POP3.

_Fuente: reconstruido a partir de indicios sobre el próximo parcial + formato del ej. 3 de 2025_

---

### Ejercicio 2: Enviar un mail MIME por SMTP a mano y verificarlo por POP3

_[2024 2C]_

**Enunciado**

> Ejercicio IV
>
> Envíe un correo electrónico cumpliendo todos los estándares y buenas prácticas que cumpla con los siguientes requisitos:
>   a. Contenga el header X-Parcial con el valor 2024/2
>   b. Contenga un título (subject): ¡Buenos días!
>   c. Contenga como remitente su nombre, apellido y cuenta de correo del ITBA
>   d. Contenga el texto:
>          ¡Hola mundo!
>   e. El correo debe entregarse a la dirección {legajo}@mail.sebikul.com donde {legajo} es número de legajo. Por ejemplo si su legajo 40123 entonces deberá entregar el correo a la dirección 40123@mail.sebikul.com
>   f. Debe poder ser leido por MUAs que no soporten la codificacion quoted printable.
>
> Debe verificar su recepción utilizando el protocolo POP3. Para ello dispone de un servidor POP3 en el host mail.sebikul.com. El nombre de usuario es {legajo}@mail.sebikul.com, donde {legajo} debe reemplazarse por su número de legajo, y la password será el mismo valor que el usuario.
> Por ejemplo, si su legajo fuese 0123, el nombre usuario POP3 sería 0123@mail.sebikul.com y la contraseña 0123@mail.sebikul.com.

**Pista:** El punto (f) pide que lo lea un MUA sin quoted-printable: ¿cómo mandás el mismo texto dos veces en codificaciones distintas sin duplicar el mail?

**Resolución paso a paso**

**1. Prepará las codificaciones antes de abrir la conexión**

Una vez que estás adentro de DATA no podés correr comandos ni volver atrás: si te equivocás en un base64 tenés que abortar y empezar de nuevo. El Subject lleva ¡ e í, que no son ASCII, así que va como encoded-word (RFC 2047). El cuerpo también tiene ¡, así que hay que codificarlo, y el punto (f) prohíbe quoted-printable: queda base64. Usá echo -n (o printf) para no codificar un salto de línea de más.

```
# Subject como encoded-word (=?charset?B?<base64>?=)
echo -n '¡Buenos días!' | base64
# → wqFCdWVub3MgZMOtYXMh   →  =?UTF-8?B?wqFCdWVub3MgZMOtYXMh?=

# Cuerpo en base64 (el punto f descarta quoted-printable)
echo -n '¡Hola mundo!' | base64
# → wqFIb2xhIG11bmRvIQ==
```

_Verificación:_ echo '<base64>' | base64 -d tiene que devolverte el texto original con los acentos bien

**2. Conectate al servidor SMTP con netcat en modo CRLF**

SMTP exige terminar cada línea con CRLF (\r\n). Sin -C netcat manda solo LF y el servidor responde '500 Syntax error' o se queda esperando para siempre. El puerto 25 lo podés escribir como 'smtp' porque está en /etc/services.

```
nc -C mail.sebikul.com smtp
```

_Verificación:_ Tiene que aparecer un banner '220 ... ESMTP ...'

**3. Hacé el diálogo SMTP: EHLO, MAIL FROM, RCPT TO, DATA**

Estos son los datos del sobre (envelope), que es lo que el servidor usa realmente para enrutar. Son independientes de los headers From:/To: que escribís después: el servidor entrega según el RCPT TO, no según el To:.

```
EHLO localhost
MAIL FROM: <rohernandez@itba.edu.ar>
RCPT TO: <65522@mail.sebikul.com>
DATA
```

_Verificación:_ 250 a cada comando, y 354 ('Start mail input') después del DATA

**4. Escribí los headers del mensaje**

Acá cumplís los puntos a, b y c: el X-Parcial, el Subject codificado y el From con nombre, apellido y cuenta del ITBA. Date y Message-ID son las buenas prácticas que pide el enunciado. Los tres últimos headers describen el cuerpo: MIME-Version habilita MIME, Content-Type dice que es texto en UTF-8 y Content-Transfer-Encoding: base64 dice cómo viene codificado (punto f).

```
MIME-Version: 1.0
Date: Sun, 24 May 2026 11:24:55 -0300
Message-ID: <parcial-65522@itba.edu.ar>
X-Parcial: 2024/2
Subject: =?UTF-8?B?wqFCdWVub3MgZMOtYXMh?=
From: Rodrigo Alejandro Hernandez <rohernandez@itba.edu.ar>
To: <65522@mail.sebikul.com>
Content-Type: text/plain; charset="UTF-8"
Content-Transfer-Encoding: base64
```

**5. Escribí el cuerpo en base64, después de una línea vacía**

La línea vacía separa los headers del cuerpo y es obligatoria. Después va el texto en base64, que cualquier cliente de mail decodifica: eso cumple el punto (f). La solución del repo arma un multipart/alternative con una parte en quoted-printable y otra en base64. Funciona en la práctica, pero es más largo, tiene más lugares donde equivocarse (el boundary, el cierre con --) y depende de que el cliente elija la parte en base64. Un solo text/plain en base64 cumple todo sin ambigüedad.

```

wqFIb2xhIG11bmRvIQ==
```

_Verificación:_ echo wqFIb2xhIG11bmRvIQ== | base64 -d → ¡Hola mundo!

**6. Cerrá el DATA con un punto solo y salí**

El punto en una línea sola es el delimitador de fin de mensaje en SMTP. Hasta que no lo mandás, el servidor sigue acumulando líneas y el mail no se entrega.

```
.
QUIT
```

_Verificación:_ 250 con un queue id (ej. '250 2.0.0 Ok: queued as ABC123') y después 221 Bye

**7. Verificá la recepción por POP3**

Es parte de la consigna, no un extra. LIST te confirma que el mensaje llegó y RETR te deja ver cómo quedaron los headers y el multipart realmente: ahí descubrís si el Subject quedó mal codificado o si el boundary se rompió.

```
nc -C mail.sebikul.com pop3

USER 65522@mail.sebikul.com
PASS 65522@mail.sebikul.com
LIST
RETR 1
QUIT
```

_Verificación:_ LIST devuelve al menos '1 <bytes>' y RETR 1 muestra el mail con el X-Parcial, el Subject codificado y el cuerpo en base64

**Errores típicos**

- Usar nc sin -C: SMTP necesita CRLF y el servidor rechaza los comandos o nunca responde.
- Poner el Subject con '¡' y 'í' en crudo en vez de encoded-word RFC 2047: el header queda fuera de estándar y se ve con caracteres rotos.
- Confundir el usuario POP3: es {legajo}@mail.sebikul.com, con la misma cadena completa como contraseña, no solo el legajo.
- Mandar el cuerpo en quoted-printable: es justo lo que prohíbe el punto (f).
- Codificar con echo sin -n: se codifica también el salto de línea y el base64 cambia.

_Fuente: 2024_2c_practico.md_

---

