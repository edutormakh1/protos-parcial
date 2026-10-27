Parte 2 de 2: los ejercicios de los parciales 2007 a 2025 resueltos y agrupados por tipo de ejercicio. Las recetas por tema están en la parte 1.

# Ejercicios resueltos por tipo

Los parciales 2007 a 2025 tomaron 71 ejercicios. Quedaron 60 resueltos sin repetir, agrupados por tipo de ejercicio; dentro de cada tipo van del más nuevo al más viejo. De los otros 11, 9 son iguales a uno ya resuelto y 2 no están en las fuentes.

- El título de cada ejercicio dice qué pide, sin nombrar el parcial. De qué parcial salió figura en la línea **Tomado en:**, debajo del título: a esa línea apuntan el índice y las referencias entre ejercicios.

- Cada ejercicio trae **Idea** (qué hay que lograr y el truco, en dos líneas), **Buscar**, **Enunciado** literal, **Solución**, **Demostrar** y **Ojo**, y se resuelve leyendo solo ese ejercicio.

- Donde dice sudo nano RUTA: abrís el archivo, pegás con Ctrl+Shift+V, guardás con Ctrl+O y Enter, y salís con Ctrl+X. Si dice "borrar lo que tenga", el archivo tiene que quedar solo con lo que pegás; para vaciarlo en nano: Alt+\\ (ir al principio), Alt+A (marcar), Alt+/ (ir al final) y Ctrl+K (cortar). Si dice "agregar al final", antes de pegar andá al final con Alt+/ (nano abre con el cursor al principio).

- Un ejercicio que se tomó igual en varios parciales está resuelto una sola vez. La línea **También tomado en:** nombra los otros parciales y dice qué cambia en cada uno. Si querés ir a un ejercicio por su parcial, buscá por ejemplo en: Parcial 2015-2C (sin el punto del medio): te lleva a las líneas **Tomado en:** de ese parcial y a las **También tomado en:** donde figura primero (si figura segundo, no sale: usá el nombre con el punto). Para ir directo a uno, copiá un nombre con el punto, como Parcial 2015-2C · Ejercicio 2, y pegalo en la búsqueda (Ctrl+F; en Word en español, Ctrl+B).

- La tabla Qué se toma cuenta, por familia, cuántos quedaron resueltos y cuántos se tomaron.

- El Índice por parcial dice en qué tipo quedó cada ejercicio de cada parcial, o a cuál es igual.

- Contexto de cada parcial guarda las notas que valían para todos los ejercicios de un mismo parcial.

## Arranque en la VM

**Buscar:** arranque, al empezar el examen, VM limpia, instalar, instalar todo, apt install, command not found, Unable to locate package, has no installation candidate, sudoers, is not in the sudoers file, su -, bridge, puente, Adaptador puente, NAT, 10.0.2.15, IFACE, enp0s3, portapapeles, Ctrl+Shift+V, pegar, cache lock, Could not get lock, unattended-upgr, Temporary failure resolving, Media change, cdrom, Job for isc-dhcp-server.service failed, conflicting server name, /etc/hosts, unable to resolve host, comillas, comillas curvas, comillas inteligentes, guion largo, invalid input, video, grabar, grabación, grabacion, servidor apagado, orden, puntaje parcial, sudo: command not found, whoami, bookworm, trixie, VERSION_CODENAME, sources.list, deb cdrom, debian.sources, Malformed entry, openssh-server, nmcli, Guest Additions, Portapapeles compartido, Impr Pant, Ctrl+Shift+Alt+R, Ctrl+H, Sustitución automática, xn--compressed, expected quoted string, systemctl is-active, inactive, invalid option, espejo de red, netinst

Lo que conviene hacer apenas arranca el examen, en este orden. Si todavía no sabés si pegar en la VM anda, hacé primero el paso 3, porque los pasos 1 y 2 se pegan. Mientras corre el apt install del paso 4, leé las consignas (paso 9).

> **1.** Probá sudo -v (pide tu clave). En Debian, si dice is not in the sudoers file o sudo: command not found, tu usuario no tiene sudo: pasa si pusiste clave de root al instalar. Mirá tu usuario con whoami y corré estas líneas de a una, con USUARIO cambiado por ese nombre, porque su - pide la clave de root:

su -

apt install -y sudo

usermod -aG sudo USUARIO

exit

su - USUARIO

sudo -v

Va su - con guion: sin él, usermod da command not found. Si apt install -y sudo falla, hacé los pasos 2 y 5 en esa terminal de root, sin sudo adelante, y volvé a correrlo. su - USUARIO deja sudo andando en esa terminal; para las terminales nuevas, cerrá la sesión y volvé a entrar.

> **2.** Red: mirá tu IP, tu placa y tu gateway, y que haya DNS. Va antes del apt, porque sin red no instala.

ip -br a

ip route

getent hosts deb.debian.org archive.ubuntu.com

IFACE es la placa con la IP del laboratorio (enp0s3 en VirtualBox, ens33 en VMware; no lo). La IP tiene que ser de la misma red que la de tu máquina física. Si es 10.0.2.15, estás en NAT: pasá el adaptador a Adaptador puente (VirtualBox: Configuración \> Red \> Adaptador 1 \> Conectado a: Adaptador puente; VMware: Network Adapter \> Bridged) y reconectá la red con sudo nmcli networking off && sudo nmcli networking on. Si getent no imprime nada, no hay DNS.

Tu gateway es la IP que sigue a default via en la salida de ip route (en default via 192.168.0.1 dev enp0s3, es 192.168.0.1). Probá que responda con ping -c 2 GATEWAY, con GATEWAY cambiado por esa IP.

> **3.** Portapapeles: copiá un bloque de este documento y pegalo en la terminal de la VM con Ctrl+Shift+V. Si no pega, abrí el documento dentro de la VM (Google Docs en Firefox, o el .docx en LibreOffice si la VM lo trae: Ubuntu 24.04 con la instalación por defecto no lo trae), o activá en VirtualBox Dispositivos \> Portapapeles compartido \> Bidireccional (necesita Guest Additions). Si en un minuto no anda, trabajá con el documento adentro de la VM. Del PDF copiá solo como último recurso, porque parte las líneas largas.
>
> **4.** Instalá todo lo que usa este documento con un solo comando. No hace preguntas.

sudo apt update

sudo DEBIAN_FRONTEND=noninteractive apt install -y nginx curl wget openssl ca-certificates bind9 bind9-utils bind9-dnsutils isc-dhcp-server isc-dhcp-client nmap ncat traceroute iputils-tracepath iptables tcpdump tshark wireshark openssh-client openssh-server netcat-openbsd telnet gnupg python3 file

- isc-dhcp-server no arranca hasta que lo configurás. En Debian, al instalar sale Job for isc-dhcp-server.service failed (y invoke-rc.d: initscript isc-dhcp-server, action "start" failed); en Ubuntu no avisa y queda en failed. Es esperable: no es un error de instalación.

- nginx y named quedan prendidos. Si un ejercicio pide arrancar el video con el servicio apagado, paralo antes de grabar: sudo systemctl stop SERVICIO (nginx, named o isc-dhcp-server).

- Si al tipear un comando Ubuntu te sugiere inetutils-traceroute o snap install nmap, no los uses: instalá traceroute y nmap con este apt, que son los que usa el documento (traceroute -n y -T, sudo nmap; el de inetutils responde invalid option -- 'n'). Si ya habías instalado inetutils-traceroute, instalar traceroute no alcanza, porque queda el que ya estaba: sacalo con sudo apt remove -y inetutils-traceroute.

> **5.** Si apt falla:

- E: Unable to locate package NOMBRE o has no installation candidate: un solo nombre que falte frena toda la instalación. En Ubuntu, habilitá universe con sudo add-apt-repository -y universe && sudo apt update y repetí. Si sigue, sacá ese nombre del comando y repetí. En Debian, si lo dice para casi todos los paquetes (o para sudo en el paso 1), no es un nombre: falta el repositorio; hacé lo del último punto.

- Waiting for cache lock: Could not get lock /var/lib/dpkg/lock-frontend. It is held by process N (unattended-upgr): la VM se está actualizando sola. Esperá a que termine; no borres el lock.

- Temporary failure resolving o Could not connect: probá ping -c 2 8.8.8.8. Si en bridge no hay salida a internet, pasá el adaptador a NAT, reconectá la red (paso 2), instalá y volvé a Adaptador puente (y reconectá otra vez). Hacelo antes de agregar rutas: al reconectar se pierden.

- Debian: si apt pide insertar un disco (Media change: please insert the disc labeled ...) o no encuentra casi ningún paquete, la VM se instaló sin espejo de red. Mirá tu versión con grep VERSION_CODENAME /etc/os-release (bookworm es Debian 12, trixie es Debian 13; un Debian instalado hoy es trixie). Hay que hacer sudo nano /etc/apt/sources.list (si estás como root por el paso 1, sin sudo), poner \# adelante de la línea deb cdrom:... (si no lo tiene ya) y agregar al final lo siguiente, con CODENAME cambiado por tu versión:

deb http://deb.debian.org/debian CODENAME main

Si sources.list no tiene la línea deb cdrom:, buscá dónde está con grep -rn cdrom /etc/apt/sources.list\*. Si sale en /etc/apt/sources.list.d/debian.sources, abrilo con sudo nano y poné \# adelante de todas las líneas del bloque que tiene URIs: cdrom:..., desde Types: hasta la línea en blanco. Si comentás solo la de URIs:, o dejás Types: sin comentar, apt da Malformed entry.

Después repetí el sudo apt update y el install.

> **6.** Lo que quedó de las prácticas, si es la misma VM con la que practicaste:

ls -l /etc/nginx/sites-enabled/

sudo nginx -t

cat /etc/hosts

systemctl is-active nginx named isc-dhcp-server

- nginx: borrá de sites-enabled los sitios de las prácticas (sudo rm /etc/nginx/sites-enabled/NOMBRE). Si nginx -t dice conflicting server name ... ignored, dos sitios tienen el mismo server_name y uno se ignora.

- /etc/hosts: con sudo nano /etc/hosts, borrá las líneas de dominios de las prácticas (foo.example.org, foo.leak.com.ar, los de protos.foo, etc.), porque le ganan al DNS real. Dejá las de localhost, la de 127.0.1.1 con el nombre de la VM (sin ella sudo avisa unable to resolve host) y las de IPv6.

- systemctl is-active imprime una línea por servicio, en el orden del comando: active es prendido; inactive o failed, apagado. Si el video tiene que arrancar con uno apagado, paralo (paso 4).

- Túneles o nc de un intento anterior: pkill -x ssh; pkill -x nc. Reglas de iptables: sudo iptables -F; sudo iptables -t nat -F. DHCP: buscá Para dejar todo como estaba.

> **7.** Comillas curvas en tu copia (conviene hacerlo antes del examen): si escribiste comandos a mano en tu copia del documento, Word y Google Docs pudieron cambiar ' por ’, " por ” y -- por — o –. Primero apagá el reemplazo automático. Google Docs: Herramientas \> Preferencias \> destildá "Usar comillas inteligentes" y, en la pestaña Sustituciones, "Sustitución automática". Word: Archivo \> Opciones \> Revisión \> Opciones de Autocorrección \> Autoformato mientras escribe \> destildá las comillas tipográficas y los guiones. Después, con Ctrl+H, reemplazá ‘ y ’ por ', y “ y ” por " en todo el documento (copiá la comilla curva de tu texto y pegala en Buscar): fuera de este punto, el documento original no tiene ninguna. Los guiones largos (— y –) buscalos uno por uno y cambialos por -- solo dentro de los comandos, porque los títulos Resueltos — ... y varias frases llevan guion largo. Síntomas: base64: invalid input, curl: (6) Could not resolve host: xn--compressed-yu6e (el —compressed; con – dice xn--compressed-nu6e) y expected quoted string near '“' en named-checkconf.
>
> **8.** Video: grabá con la herramienta de GNOME (Ubuntu y Debian): Impr Pant (o Ctrl+Shift+Alt+R) \> ícono de la cámara de video \> pantalla completa \> botón de grabar. Se corta con el botón rojo de la barra de arriba. Probalo 5 segundos al empezar y buscá el archivo; este comando lista los videos (.webm o .mp4) de tu carpeta personal de los últimos 30 minutos:

find ~ -mmin -30 \\ -name '\*.webm' -o -name '\*.mp4' \\

Agrandá la letra de la terminal con Ctrl y la tecla +. Si el enunciado pide arrancar con el servidor apagado, mostralo con systemctl is-active SERVICIO (dice inactive, o failed si falló la última vez que arrancó, como isc-dhcp-server recién instalado: las dos quieren decir apagado), después sudo systemctl start SERVICIO o el paso del ejercicio que lo arranca, y recién ahí la demostración.

> **9.** Orden: leé las cuatro consignas mientras corre el apt install y empezá por los dos ejercicios que mejor sabés (en 2025-2Q fueron 4 de 2,5 puntos, con puntaje parcial). Si uno se traba, dejá en el video el comando, el error y lo que probaste, y pasá al siguiente.

*Probado en Ubuntu 24.04 (WSL): los 25 paquetes del apt install existen en Ubuntu 24.04 (apt-cache policy) y en Debian 12 y 13 (bookworm y trixie, en qa.debian.org/madison); ubuntu-desktop-minimal (la instalación por defecto) no trae LibreOffice y ubuntu-desktop sí (apt-cache depends); con apt 2.7 y un debian.sources de prueba, comentar solo URIs: da Malformed entry y comentar el bloque entero anda; isc-dhcp-server sin configurar falla (Not configured to listen on any interfaces!) y avisa solo con el init.d de Debian; nginx -t muestra conflicting server name; los síntomas de comillas curvas y guion largo; la línea default via de ip route, getent, find y pkill -x nc; systemctl is-active con un servicio que falla al arrancar (failed, y sigue así después de systemctl stop); inetutils-traceroute con -n y -T (invalid option); con los dos traceroute instalados queda el primero (update-alternatives en una carpeta propia). Sin probar: el apt install completo (sin root), el arreglo de sudo en Debian, el caso del DVD y del netinst sin espejo de Debian, nmcli, los menús de VirtualBox, Word, Google Docs y GNOME, y las sugerencias de inetutils-traceroute y snap.*

## Atajos: qué buscar

**Buscar:** atajos, qué buscar, que buscar, cómo buscar, como buscar, Ctrl+F, Ctrl+B, Panel de navegación, panel de navegacion, esquema, MAYÚSCULAS, mayusculas, reemplazar, marcador, anotá, Alt+R, Alt+C, Case Sensitive

- Buscá una frase corta y rara del enunciado: la línea **Buscar:** de cada ejercicio las trae literales. DHCP, curl o nginx aparecen cientos de veces.

- Word: Ctrl+B (Word en español) o Ctrl+F (Word en inglés) abre el Panel de navegación. La pestaña Títulos muestra familia \> tipo \> ejercicio, y Resultados lista todas las coincidencias. Google Docs: Ver \> Mostrar esquema (en la versión con pestañas, Ver \> Mostrar pestañas y esquemas, o el ícono de arriba a la izquierda del documento).

- Lo que va en MAYÚSCULAS (LEGAJO, USUARIO, IP_SERVIDOR, IFACE, IP_DE_FOO) se reemplaza a mano por el dato del enunciado o por lo que anotaste en un paso anterior (cuando un paso dice "corré comando y anotá ...", el dato sale en esa salida, y "(en el ejemplo, ...)" muestra cómo se ve). Dentro de nano, Alt+R lo cambia en todo el archivo: Alt+R, Alt+C hasta que el renglón de búsqueda muestre \[Case Sensitive\] (en una VM en castellano, su equivalente entre corchetes), el marcador, Enter, el dato, Enter y A (todos). Sin el Alt+C, nano no distingue mayúsculas: en la receta de nginx, si buscás NOMBRE (en vez de X-NOMBRE), también cambia el nombre de \$http_x_nombre (queda \$http_x_Parcial-Protos, que está mal), y nginx -t no lo detecta.

Para los parciales más nuevos, estos términos aparecen pocas veces: la primera coincidencia es esta tabla y el ejercicio llega enseguida (hoy, en las cuatro siguientes como mucho; en la fila del proxy que conserva un header, antes pasan la Receta rápida, que también sirve, y un Ojo del ejercicio HTTPS).

| **Si el enunciado pide** | **Buscá** | **Te lleva a** |
|----|----|----|
| proxy reverso por HTTPS | update-ca-certificates | nginx: proxy reverso HTTPS de seguro.protos.foo a parcial.protos.foo |
| proxy reverso que conserva un header | proxy_pass_header | nginx: proxy reverso de foo.example.org a protos.sebikul.com conservando X-Parcial-Protos |
| gateway, servicios del servidor y routers | routers que intervienen | Gateway 192.168.50.250: descubrir el servidor de 10.0.0.0/24, sus servicios, HTTP y routers |
| arreglar una zona de BIND | named-checkzone | BIND: arreglar la zona rota it.itba.edu.ar y el SOA |
| crear una zona en BIND | allow-transfer | BIND: servir la zona demiloo.com.ar (A, CNAME www, MX copiados, TTL por registro) |
| servidor DHCP | dhcpd.conf | DHCP: IP fija por MAC, DNS de Google y rango 10-200 |
| curl por un proxy SOCKS | socks5h | curl por proxy SOCKS5: text/plain en español de ejercicio2.sebikul.com:8080/foo/ |
| mail por SMTP a mano, leído por POP3 | MUAs | Mail por SMTP a mano: header X-Parcial, asunto y cuerpo con ¡, base64, y lectura por POP3 |
| tail -f de un archivo remoto por un túnel | seguir los cambios | Seguir /tmp/pdc de pampero con tail -f archivo.log por un túnel SSH |

*Probado en nano 7.2 (WSL, con una terminal simulada): Alt+/ va al final; Alt+R con A cambia todas las apariciones, también las que están en minúsculas, salvo con Alt+C (Search \[Case Sensitive\]); con el \$http_x_nombre cambiado, nginx -t dice syntax is ok. Sin probar: los menús de Word y Google Docs (en Google Docs el nombre del menú cambia según la versión) y nano en castellano.*

## Qué se toma

Cantidad de ejercicios por familia y parciales donde apareció. Los que se tomaron idénticos en varios parciales están resueltos una sola vez: Resueltos cuenta los títulos que hay en el documento y Tomados cuenta todas las veces que se tomó. "x2" quiere decir dos ejercicios de esa familia en el mismo parcial.

| **Familia** | **Resueltos** | **Tomados** | **Parciales** |
|----|----|----|----|
| nginx | 8 | 11 | 2025-2Q, 2024-2C, 2022-2C, 2021-1C, Parcial o Recuperatorio 2021-1C x2, 2020-1C, 2016-1C (primer parcial), 2015-2C, 2013-2C, 2012-1C |
| curl | 5 | 7 | 2024-2C, 2021-1C, Recuperatorio 2020-1C, 2016-2C, 2016-1C (primer parcial), 2015 (primer parcial), 2014-2C |
| DNS | 4 | 5 | 2025-2Q, 2022-2C, 2015-2C, 2013-2C, 2011 |
| Mail | 4 | 5 | 2024-2C, Recuperatorio 2020-1C, 2016-2C, 2015 (primer parcial), 2012-1C |
| SSH | 12 | 14 | 2022-2C, 2021-1C, Parcial o Recuperatorio 2021-1C x2, 2020-1C x2, Recuperatorio 2020-1C x2, 2016-1C (segundo parcial), 2014-1C, 2011 x2, sin fecha x2 |
| Red | 7 | 7 | 2025-2Q, 2024-2C, 2020-1C x2, 2015 (segundo parcial) x2, 2011 |
| DHCP | 1 | 1 | 2025-2Q |
| iptables | 6 | 6 | 2016-1C (segundo parcial), 2015 (segundo parcial), 2014-1C, 2011 x2, sin fecha |
| Wireshark | 4 | 4 | 2021-1C, 2020-1C, Recuperatorio 2020-1C, 2012-1C |
| En papel | 9 | 9 | 2016 (segundo parcial) x2, 2015 (segundo parcial) x3, 2007 x4 |

- Suman 60 resueltos y 69 tomados. Los 9 de diferencia son iguales a otro ya resuelto: figuran en su línea **También tomado en:** y en el Índice por parcial como Igual a ....

- Los otros 2 de los 71 son consignas que no están en las fuentes: Parcial 2022-2C · Ejercicio 3 y Parcial 2021-1C · Ejercicio 4.

- De 2020 en adelante aparecen nginx, curl, DNS, Mail, SSH, Red, DHCP y Wireshark. En estos resueltos, iptables y En papel no aparecen después de 2016.

## Índice por parcial

La columna Dónde está dice familia: tipo. La familia es el título Resueltos — ... de la sección y el tipo es el subtítulo que le sigue. iptables figura como Firewall (iptables) y Wireshark como Capturas (Wireshark). Para saltar al ejercicio buscá en: y el parcial, sin el punto del medio, por ejemplo en: Parcial 2020-1C: te lleva a las líneas Tomado en y También tomado en de ese parcial, y entre ellas está el ejercicio. Si la fila dice Igual a ..., copiá ese nombre de la columna Dónde está (trae el punto) y pegalo en la búsqueda (Ctrl+F; en Word en español, Ctrl+B). Por ejemplo, Parcial 2020-1C · Ejercicio IV no sale con en: Parcial 2020-1C, porque figura segundo en su línea También tomado en.

Si dice Igual a ..., ese ejercicio no tiene título propio: está resuelto en el que nombra, que lo lista en su línea **También tomado en:** con lo que cambia. Entre paréntesis va la familia: tipo de ese otro.

| **Parcial** | **Ejercicio** | **Dónde está** |
|----|----|----|
| Parcial 2025-2Q | Ejercicio 1: DHCP: IP fija por MAC, DNS de Google y rango 10-200 | DHCP: Configurar un servidor DHCP |
| Parcial 2025-2Q | Ejercicio 2: gateway 192.168.50.250, servidor de 10.0.0.0/24, servicios, HTTP y routers | Red: Llegar a una red detrás de un gateway y descubrir sus servicios |
| Parcial 2025-2Q | Ejercicio 3: BIND: arreglar la zona rota it.itba.edu.ar y el SOA | DNS: Arreglar una zona rota |
| Parcial 2025-2Q | Ejercicio 4: proxy reverso HTTPS de seguro.protos.foo a parcial.protos.foo | nginx: Proxy reverso: un dominio sirve el contenido de otro |
| Parcial 2024-2C | Ejercicio 1: proxy reverso de foo.example.org a protos.sebikul.com con X-Parcial-Protos | nginx: Proxy reverso: un dominio sirve el contenido de otro |
| Parcial 2024-2C | Ejercicio 2: curl por proxy SOCKS5: text/plain en español de ejercicio2.sebikul.com:8080/foo/ | curl: Negociación de contenido: text/plain en español |
| Parcial 2024-2C | Ejercicio 3: ruta por el gateway 192.168.1.3, servicios de 10.0.0.1, HTTP y routers | Red: Llegar a una red detrás de un gateway y descubrir sus servicios |
| Parcial 2024-2C | Ejercicio 4: mail por SMTP a mano con X-Parcial y ¡ en base64, leído por POP3 | Mail: Enviar un mail a mano por SMTP y leerlo por POP3 |
| Parcial 2022-2C | Ejercicio 1: proxy reverso de foo.example.org a foo.leak.com.ar | nginx: Proxy reverso: un dominio sirve el contenido de otro |
| Parcial 2022-2C | Ejercicio 2: seguir /tmp/pdc de pampero con tail -f archivo.log por un túnel SSH | SSH: Seguir un archivo remoto con tail -f por un túnel |
| Parcial 2022-2C | Ejercicio 3: no está en las fuentes | Consignas que faltan: Ejercicios que no están en las fuentes |
| Parcial 2022-2C | Ejercicio 4: BIND: zona demiloo.com.ar (A, CNAME www, MX copiados, TTL) | DNS: Crear una zona en BIND |
| Parcial 2021-1C | Ejercicio 1: proxy de foo.example.org a foo.leak.com.ar con compresión transparente | nginx: Proxy reverso: un dominio sirve el contenido de otro |
| Parcial 2021-1C | Ejercicio 2: llave con passphrase = MAC de bd1, SSH en el puerto 2222 | SSH: Entrar con una llave privada: passphrase o nombre de archivo por deducir |
| Parcial 2021-1C | Ejercicio 3: curl text/plain en español a ejercicio2.proto.leak.com.ar con un request capturado | curl: Negociación de contenido: text/plain en español |
| Parcial 2021-1C | Ejercicio 4: no está en las fuentes | Consignas que faltan: Ejercicios que no están en las fuentes |
| Parcial 2021-1C | Ejercicio 5: análisis de botica.pcap.gz, preguntas A a E | Wireshark: Responder preguntas sobre una captura |
| Parcial o Recuperatorio 2021-1C | Ejercicio I: virtual hosts: itba.example.org, www.example.org y catch-all "What?" | nginx: Virtual hosts: cada dominio con su contenido |
| Parcial o Recuperatorio 2021-1C | Ejercicio II: proxy foo.example.org → foo.leak.com.ar (sin compresión) | Igual a Parcial 2022-2C · Ejercicio 1 (nginx: Proxy reverso: un dominio sirve el contenido de otro) |
| Parcial o Recuperatorio 2021-1C | Ejercicio III: llave con passphrase = MAC de en3 de pampero, puerto 2222 | SSH: Entrar con una llave privada: passphrase o nombre de archivo por deducir |
| Parcial o Recuperatorio 2021-1C | Ejercicio IV: salida a internet sin NAT (túnel SOCKS dinámico) | SSH: Salir a internet o llegar a un servicio interno con túneles |
| Parcial 2020-1C | Ejercicio I: MAC de esmerald.it.itba.edu.ar desde ens3 de pampero | Red: MAC y puertos abiertos de un host |
| Parcial 2020-1C | Ejercicio II: todos los puertos TCP abiertos del gateway de pampero | Red: MAC y puertos abiertos de un host |
| Parcial 2020-1C | Ejercicio III: seguir /tmp/bar de pampero con tail -f usando túneles | Igual a Parcial 2022-2C · Ejercicio 2 (SSH: Seguir un archivo remoto con tail -f por un túnel) |
| Parcial 2020-1C | Ejercicio IV: proxy foo.example.org → foo.leak.com.ar | Igual a Parcial 2022-2C · Ejercicio 1 (nginx: Proxy reverso: un dominio sirve el contenido de otro) |
| Parcial 2020-1C | Ejercicio V: llave-pdc.pem, passphrase = IP asignada por DHCP en dhcp-parcial.pcap.gz, puerto 9090 | SSH: Entrar con una llave privada: passphrase o nombre de archivo por deducir |
| Parcial 2020-1C | Ejercicio VI: mostrar la imagen que viaja dentro de parcial-correo.pcap.gz | Wireshark: Responder preguntas sobre una captura |
| Recuperatorio 2020-1C | Ejercicio I: IPs de los SMTP entrantes (MX) y SPF de proto.leak.com.ar | Mail: MX y SPF de un dominio |
| Recuperatorio 2020-1C | Ejercicio II: parcial-dns-y-otros: IP de ejercicio.leak.com.ar y cuerpo del mail a jcodagno | Wireshark: Responder preguntas sobre una captura |
| Recuperatorio 2020-1C | Ejercicio III: llave-pdc-recu.pem, passphrase = MAC del gateway de pampero, puerto 9090 | SSH: Entrar con una llave privada: passphrase o nombre de archivo por deducir |
| Recuperatorio 2020-1C | Ejercicio IV: salida a internet sin NAT desde parcial (túnel dinámico) | Igual a Parcial o Recuperatorio 2021-1C · Ejercicio IV (SSH: Salir a internet o llegar a un servicio interno con túneles) |
| Recuperatorio 2020-1C | Ejercicio V: curl text/plain en español de ejercicio4.proto.leak.com.ar/foo/ | Igual a Parcial 2021-1C · Ejercicio 3 (curl: Negociación de contenido: text/plain en español) |
| Parcial 2016-2C | Ejercicio 1: mail con X-Parcial: 2016/2, Subject de un registro TXT, imagen adjunta y POP3 | Mail: Enviar un mail a mano por SMTP y leerlo por POP3 |
| Parcial 2016-2C | Ejercicio 2: cadena de curl: Host en base64, 301, 401, 403 y cookie | curl: Seguir un recurso: Host en base64, redirecciones, autenticación y cookies |
| Parcial 2016-1C (primer parcial) | Ejercicio 1: parcial.protos: redirección a /api/, /api/health de api.protos:9090, /api/foo sin autenticación | nginx: Proxy reverso: un dominio sirve el contenido de otro |
| Parcial 2016-1C (primer parcial) | Ejercicio 2: curl a /secreto/543 a partir de un stream TCP de Wireshark | curl: Seguir un recurso: Host en base64, redirecciones, autenticación y cookies |
| Parcial 2016-1C (segundo parcial) | Ejercicio 1: ruta a ubuntu por foo, llave /{pkname}.pem con la MAC de foo, ssh como pdc | SSH: Entrar con una llave privada: passphrase o nombre de archivo por deducir |
| Parcial 2016-1C (segundo parcial) | Ejercicio 2: iptables: root sin HTTP (80/tcp) y bloquear el 9090 de localhost | iptables: Filtrar, redirigir y restringir con iptables |
| Parcial 2016 (segundo parcial) | Ejercicio 1: cómo navegar sitios .ar desde una red que los filtra | En papel: Preguntas teóricas |
| Parcial 2016 (segundo parcial) | Ejercicio 2: subnetting 200.16.0.0 a 200.16.7.255 (110/118/126 PCs), R1, R2, R3 y rutas estáticas | En papel: Subnetting y tablas de ruteo |
| Parcial 2015-2C | Ejercicio 1: nginx: itba.example.org, www.example.org y "What?" en cualquier otro dominio | Igual a Parcial o Recuperatorio 2021-1C · Ejercicio I (nginx: Virtual hosts: cada dominio con su contenido) |
| Parcial 2015-2C | Ejercicio 2: BIND: zona demiloo.com.ar (A, CNAME www, MX copiados, TTL) | Igual a Parcial 2022-2C · Ejercicio 4 (DNS: Crear una zona en BIND) |
| Parcial 2015 (segundo parcial) | Ejercicio 1: subnetting 188.44.55.0/24 (60/12/90 hosts con direcciones obligadas) | En papel: Subnetting y tablas de ruteo |
| Parcial 2015 (segundo parcial) | Ejercicio 2: DHCP (discover/offer/request/ack), tabla de ruteo del Host A y config del router | En papel: Subnetting y tablas de ruteo |
| Parcial 2015 (segundo parcial) | Ejercicio 3: CSMA/CD: detección y resolución de colisiones | En papel: Preguntas teóricas |
| Parcial 2015 (segundo parcial) | Ejercicio 1 (otra tanda): servidor HTTP en 192.168.1.1 detrás de otra máquina, sin perder internet | Red: Llegar a una red detrás de un gateway y descubrir sus servicios |
| Parcial 2015 (segundo parcial) | Ejercicio 2 (otra tanda): iptables: root sin HTTP y redirección 9090 → 9091 | iptables: Filtrar, redirigir y restringir con iptables |
| Parcial 2015 (segundo parcial) | Ejercicio 3 (otra tanda): screenshot de Wireshark: puerto 22 de 192.168.123.1 detrás de un firewall | Red: Llegar a una red detrás de un gateway y descubrir sus servicios |
| Parcial 2015 (primer parcial) | Ejercicio 1: enviar un mail con adjunto (X-Parcial: 2015/1) | Igual a Parcial 2016-2C · Ejercicio 1 (Mail: Enviar un mail a mano por SMTP y leerlo por POP3) |
| Parcial 2015 (primer parcial) | Ejercicio 2: seguir el recurso /secreto/143 | curl: Seguir un recurso: Host en base64, redirecciones, autenticación y cookies |
| Parcial 2014-2C | Ejercicio 1: curl a /secreto/143 con Host en base64, Cookie JSESSIONID y Accept application/json | Igual a Parcial 2015 (primer parcial) · Ejercicio 2 (curl: Seguir un recurso: Host en base64, redirecciones, autenticación y cookies) |
| Parcial 2014-1C | Ejercicio 1: ruta a ubuntu, MAC, llave por HTTP, ssh y túnel reverso al recurso web | SSH: Entrar con una llave privada: passphrase o nombre de archivo por deducir |
| Parcial 2014-1C | Ejercicio 2: iptables: bloquear HTTP a un usuario, permitir a root y redirigir 9090 → 9091 | iptables: Filtrar, redirigir y restringir con iptables |
| Parcial 2013-2C | Ejercicio 1: nginx delante del origin 10.6.0.A (Demiloo): X-Pais, www redirige, assets cacheados, gzip | nginx: Proxy reverso: un dominio sirve el contenido de otro |
| Parcial 2013-2C | Ejercicio 2: BIND: zonas demiloo.com.br y demiloo.com.ar, MX de proto.leak.com.ar y SPF | DNS: Crear una zona en BIND |
| Parcial 2012-1C | Ejercicio 1: bajar capture.pcap.gpg de www.parcialpdc.com (labu180) por www.soyalumnopdc.com | nginx: Proxy reverso: un dominio sirve el contenido de otro |
| Parcial 2012-1C | Ejercicio 2: gpg -d capture.pcap.gpg; passphrase = IP del servidor de correo con mayor prioridad del ITBA | Mail: MX y SPF de un dominio |
| Parcial 2012-1C | Ejercicio 3: capture.pcap: IP de cliente y servidor, netstat -tn, recursos HTTP, quién cierra | Wireshark: Responder preguntas sobre una captura |
| Parcial 2011 | Ejercicio 1: interfaz 192.168.10.x/25, ping 10.6.0.127 (host G) y ping hostG | Red: Llegar a una red detrás de un gateway y descubrir sus servicios |
| Parcial 2011 | Ejercicio 2: servicio echo por TCP solo desde un host de la red 10.6.0.0/24 | iptables: Filtrar, redirigir y restringir con iptables |
| Parcial 2011 | Ejercicio 3: dig: servidores SMTP de gmail.com y nameservers al resolver www.google.com | DNS: Consultas con dig |
| Parcial 2011 | Ejercicio 4: iptables: sin ICMP Echo Request y ssh también por el puerto 2222, salvo para G | iptables: Filtrar, redirigir y restringir con iptables |
| Parcial 2011 | Ejercicio 5: Apache en el loopback de hal.popegolabs.com (www.popego.com), túneles encadenados y proxy | SSH: Salir a internet o llegar a un servicio interno con túneles |
| Parcial 2011 | Ejercicio 6: tail -f /var/log/remoteapp.log para ver /var/log/app.log de hal.popegolabs.com | SSH: Seguir un archivo remoto con tail -f por un túnel |
| Parcial sin fecha | Ejercicio 1: ruta a 192.168.122.0/24, llave ospf.pem por HTTP y ssh como pdc | SSH: Entrar con una llave privada: passphrase o nombre de archivo por deducir |
| Parcial sin fecha | Ejercicio 2: que bar navegue por tu máquina: proxy SOCKS con ssh -D y túnel reverso -R | SSH: Salir a internet o llegar a un servicio interno con túneles |
| Parcial sin fecha | Ejercicio 3: iptables: bloquear lo que entra al puerto 9091 y el puerto 80 saliente | iptables: Filtrar, redirigir y restringir con iptables |
| Parcial 2007 | Ejercicio 1: evitar ads, banners y pop-ups: que ad-up.com y adcomplete.com no resuelvan a su IP | En papel: Preguntas teóricas |
| Parcial 2007 | Ejercicio 2: ITBA Online manda DNI y clave en texto plano: que no puedan capturar la clave | En papel: Preguntas teóricas |
| Parcial 2007 | Ejercicio 3: verdadero o falso: única IP pública y NAT, VPN con IP-en-IP, firewall y UDP entrante | En papel: Preguntas teóricas |
| Parcial 2007 | Ejercicio 4: navegar sitios .ar filtrados, con proxy 192.168.0.3 y puertos hasta 1024 bloqueados | En papel: Preguntas teóricas |

## Contexto de cada parcial

Notas que valen para todos los ejercicios de un mismo parcial. Los parciales que no figuran acá no tienen nota común. Cada nota dice la familia, o familia: tipo, donde quedó cada ejercicio.

Las fuentes que nombran estas notas y los ejercicios:

- "Merge" o "PDF Merge": MergeConsignasPracticasParcailes.pdf, el PDF que junta las consignas de varios parciales (2020-1C, Recuperatorio 2020-1C, Parcial o Recuperatorio 2021-1C, 2016-1C segundo parcial, 2015-2C, 2013-2C y 2012-1C).

- "MasCompilado" o "el apunte de MasCompilado": MasCompiladoPractico.pdf, un apunte de alumnos con resoluciones. Tiene errores, que cada ejercicio marca. Sus bloques llevan nombre propio, como "RECU 1C 2021" y "2C 2020".

- "59235": los archivos sueltos del Parcial 2021-1C (59235_E01.md, 59235_E02.md, 59235_E03.md y 59235_E05.md). Hay uno por ejercicio, con la consigna y la resolución de un alumno. El 59235_E04 no está.

Si era primer o segundo parcial y el título no lo dice: 2016-2C, 2015-2C y 2013-2C eran primer parcial; 2011, sin fecha y 2007 eran segundo parcial.

**Parcial 2025-2Q.**

Tomado el 31/10/2025. Cuatro ejercicios de 2,5 puntos: Ej. 1 servidor DHCP (DHCP), Ej. 2 gateway + descubrimiento (Red), Ej. 3 zona BIND rota (DNS), Ej. 4 proxy HTTPS (nginx).

**Parcial 2024-2C.**

Cuatro ejercicios: Ej. 1 proxy reverso que conserva X-Parcial-Protos (nginx), Ej. 2 curl por proxy SOCKS con negociación de contenido (curl), Ej. 3 ruta + nmap + traceroute (Red), Ej. 4 mail por SMTP con X-Parcial leído por POP3 (Mail).

**Parcial 2022-2C.**

Las fuentes traen tres de los cuatro ejercicios: Ej. 1 proxy reverso (nginx), Ej. 2 seguir un archivo remoto por un túnel (SSH) y Ej. 4 zona demiloo.com.ar (DNS). El Ejercicio 3 no está: figura como Parcial 2022-2C · Ejercicio 3 en Consignas que faltan: Ejercicios que no están en las fuentes.

**Parcial 2021-1C.**

**Buscar:** Parcial 2021-1C, 59235, foo.example.org, foo.leak.com.ar, compresion transparente, bd1, puerto 2222, ejercicio2.proto.leak.com.ar, botica.pcap

> *Es el parcial de los archivos 59235, con el formato clásico. Cuatro ejercicios en las fuentes (E01, E02, E03, E05): Ej. 1 proxy con compresión (nginx), Ej. 2 llave con passphrase (SSH), Ej. 3 text/plain en español (curl), Ej. 5 botica.pcap.gz (Wireshark). El Ejercicio 4 no figura: está anotado como Parcial 2021-1C · Ejercicio 4 en Consignas que faltan: Ejercicios que no están en las fuentes.*

**Parcial o Recuperatorio 2021-1C.**

**Buscar:** Parcial 2021-1C, Recuperatorio 2021-1C, Recu 1C 2021, RECU 1C 2021, itba.example.org, www.example.org, en3, única interfaz, unica interfaz, gateway no realiza NAT, socket pasivo en el cliente

> *Es la variante del PDF Merge: un 2021-1C distinto del Parcial 2021-1C de los archivos 59235. Sus cuatro ejercicios son Ej. I virtual hosts (nginx), Ej. II proxy (nginx, resuelto en Parcial 2022-2C · Ejercicio 1), Ej. III llave con MAC de la interfaz **en3** de pampero (SSH) y Ej. IV salida a internet sin NAT (SSH).*

El bloque "RECU 1C 2021" del MasCompilado trae otros cuatro ejercicios, todos resueltos en este documento:

- EX1, virtual hosts itba.example.org y www.example.org: es el Parcial o Recuperatorio 2021-1C · Ejercicio I (nginx: Virtual hosts: cada dominio con su contenido).

- EX2, "ídem ex 6", la imagen del correo de parcial-correo.pcap.gz: es el Parcial 2020-1C · Ejercicio VI (Wireshark: Responder preguntas sobre una captura).

- EX3, "ídem ex 5", la llave llave-pdc.pem con passphrase = IP asignada por DHCP: es el Parcial 2020-1C · Ejercicio V (SSH: Entrar con una llave privada: passphrase o nombre de archivo por deducir).

- EX4, tail -f archivo.log "pero ahora con el -L porque quiero el socket pasivo en el cliente": es la solución principal (-L) del Parcial 2022-2C · Ejercicio 2 (SSH: Seguir un archivo remoto con tail -f por un túnel), que también resuelve el Parcial 2020-1C · Ejercicio III. Terminal 1: ssh -L LEGAJO:127.0.0.1:LEGAJO USUARIO@pampero.itba.edu.ar 'tail -n +1 -f /tmp/bar \| nc -l 127.0.0.1 LEGAJO'. Terminal 2: nc 127.0.0.1 LEGAJO \> archivo.log. Terminal 3: tail -f archivo.log.

**Parcial 2020-1C.**

**Buscar:** Parcial 2020-1C, esmerald, pampero, /tmp/bar, foo.example.org, dhcp-parcial.pcap.gz, parcial-correo.pcap.gz

Seis ejercicios: Ej. I MAC de esmerald y Ej. II puertos del gateway de pampero (Red), Ej. III tail -f por túneles (SSH, resuelto en Parcial 2022-2C · Ejercicio 2), Ej. IV proxy (nginx, resuelto en Parcial 2022-2C · Ejercicio 1), Ej. V llave con passphrase sacada de dhcp-parcial.pcap.gz (SSH), Ej. VI imagen de parcial-correo.pcap.gz (Wireshark).

**Recuperatorio 2020-1C.**

**Buscar:** Recuperatorio 2020-1C, proto.leak.com.ar, SPF, parcial-dns-y-otros, llave-pdc-recu.pem, ejercicio4.proto.leak.com.ar

> *El bloque que MasCompilado llama "2C 2020" es, en contenido, el Recuperatorio 2020-1C: Ej. I MX/SPF (Mail), Ej. II captura (Wireshark), Ej. III MAC del gateway (SSH), Ej. IV túnel (SSH, resuelto en Parcial o Recuperatorio 2021-1C · Ejercicio IV), Ej. V text/plain (curl, resuelto en Parcial 2021-1C · Ejercicio 3). Las diferencias con el apunte se marcan en cada ejercicio.*

**Parcial 2016-1C (segundo parcial) y Parcial 2016 (segundo parcial).**

Son dos hojas distintas. Parcial 2016-1C (segundo parcial) es de máquina: Ej. 1 (SSH: Entrar con una llave privada: passphrase o nombre de archivo por deducir) y Ej. 2 (iptables: Filtrar, redirigir y restringir con iptables). Parcial 2016 (segundo parcial), sin cuatrimestre en el título, es en papel: Ej. 1 (En papel: Preguntas teóricas) y Ej. 2 (En papel: Subnetting y tablas de ruteo). El Parcial 2016-1C (primer parcial), de nginx y curl, es otro más.

**Parcial 2015 (segundo parcial).**

Tiene dos tandas con la misma numeración. La primera: Ej. 1 y Ej. 2 (En papel: Subnetting y tablas de ruteo) y Ej. 3 (En papel: Preguntas teóricas). La segunda lleva "(otra tanda)" en el título: Ej. 1 y Ej. 3 (Red: Llegar a una red detrás de un gateway y descubrir sus servicios) y Ej. 2 (iptables: Filtrar, redirigir y restringir con iptables).

**Parcial 2012-1C.**

Los tres ejercicios van encadenados sobre el mismo archivo:

- Parcial 2012-1C · Ejercicio 1 baja capture.pcap.gpg (nginx: Proxy reverso: un dominio sirve el contenido de otro).

- Parcial 2012-1C · Ejercicio 2 lo desencripta con gpg; la passphrase sale de una consulta MX con dig (Mail: MX y SPF de un dominio).

- Parcial 2012-1C · Ejercicio 3 responde las preguntas sobre capture.pcap (Wireshark: Responder preguntas sobre una captura).

**Parcial 2011.**

Segundo parcial, seis ejercicios. Dónde quedó cada uno:

- Ej. 1: Red: Llegar a una red detrás de un gateway y descubrir sus servicios.

- Ej. 2 y Ej. 4: iptables: Filtrar, redirigir y restringir con iptables.

- Ej. 3: DNS: Consultas con dig.

- Ej. 5: SSH: Salir a internet o llegar a un servicio interno con túneles.

- Ej. 6: SSH: Seguir un archivo remoto con tail -f por un túnel.

Contexto común del enunciado: "El objetivo es conformar un esquema logico como se muestra a continuacion: La red 10.6.0.0/24 es la red del laboratorio. La red 192.168.10.0/25 es la red de los alumnos de Protocolos de Comunicacion. El host G no encamina paquetes ni hace NAT. Usted dispone de una cuenta SSH en el host G y hal.popegolabs.com (en Internet), con usuario user y contrasena pdcpass. Se sabe que existe un servidor proxy HTTP que escucha en el puerto 8080 de proxy.itba.edu.ar, el cual es accesible desde pampero.itba.edu.ar"

Dibujo del enunciado, en texto. G tiene 192.168.20.1 del lado de los alumnos y 10.6.0.127 del lado del laboratorio:

\[alumnos 192.168.10.0/25\]--switch--(192.168.20.1) G (10.6.0.127)--switch--\[laboratorio 10.6.0.0/24\]--router--Internet

**Parcial sin fecha.**

Era un segundo parcial. Las fuentes no traen la consigna del Parcial sin fecha: solo la resolución de un alumno. El enunciado de sus tres ejercicios está reconstruido a partir de esa resolución: Ej. 1 (SSH: Entrar con una llave privada: passphrase o nombre de archivo por deducir), Ej. 2 (SSH: Salir a internet o llegar a un servicio interno con túneles) y Ej. 3 (iptables: Filtrar, redirigir y restringir con iptables).

# Resueltos — nginx (servidor HTTP)

## Proxy reverso: un dominio sirve el contenido de otro

En todos, un nginx en tu máquina atiende un dominio y con proxy_pass devuelve el contenido de otro servidor, sin que cambie la URL. De un parcial a otro cambian los dos nombres y el agregado: HTTPS, un header a conservar, compresión, redirecciones, autenticación o caché.

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| nginx: proxy reverso HTTPS de seguro.protos.foo a parcial.protos.foo | https://seguro.protos.foo sirve el contenido de http://parcial.protos.foo; curl sin opciones | HTTPS: certificado autofirmado con SAN y confiado en el sistema, porque no se puede usar -k |
| nginx: proxy reverso de foo.example.org a protos.sebikul.com conservando X-Parcial-Protos | http://foo.example.org/ sirve http://protos.sebikul.com/, cualquier recurso; curl sin opciones | Conservar el header X-Parcial-Protos, en el pedido y en la respuesta |
| nginx: proxy reverso de foo.example.org a foo.leak.com.ar | http://foo.example.org/ sirve http://foo.leak.com.ar/, cualquier recurso; curl sin opciones | El caso base: proxy solo, sin agregados. También tomado en Parcial o Recuperatorio 2021-1C · Ejercicio II y Parcial 2020-1C · Ejercicio IV, que además piden arrancar el video con nginx apagado |
| Proxy inverso con compresión transparente (foo.example.org sirve foo.leak.com.ar) | http://foo.example.org/ sirve http://foo.leak.com.ar/; curl solo con las opciones que negocian la compresión | Compresión transparente (gzip) si el cliente la pide: sin demostrarla vale la mitad. El video arranca con nginx apagado |
| nginx: redirección temporal a /api/, /api/health igual que api.protos:9090 (IP en base64 en un TXT), /api/foo sin requerir autenticación | http://parcial.protos/ redirige a /api/; /api/health y /api/foo salen de http://api.protos:9090 | Redirección temporal (302); la IP de api.protos está en base64 en el TXT de foo.leak.com.ar (se le pregunta a dns0.zoneedit.com); nginx pone el Authorization Basic de USER y PASS en /api/foo; curl solo con las opciones que lista el enunciado |
| nginx delante de un origin en 10.6.0.A: X-Pais según el dominio, www redirige permanente, assets cacheados una semana, gzip | nginx en el puerto 80 delante del origin 10.6.0.A; dominios demiloo.com.ar, demiloo.com.br y los dos con www | Header X-Pais (AR o BR) según el dominio; www redirige permanente (301); cualquier otro hostname va al sitio de Argentina; /assets/ con caché de una semana; gzip |
| Bajar /capture.pcap.gpg del virtual host www.parcialpdc.com usando la URL http://www.soyalumnopdc.com/capture.pcap.gpg | http://www.soyalumnopdc.com/capture.pcap.gpg baja /capture.pcap.gpg del virtual host www.parcialpdc.com de labu180.unix.lab | El servidor de atrás aloja varios virtual hosts: el proxy le manda Host: www.parcialpdc.com. Se pide desde un browser |

### Receta rápida: proxy reverso HTTP o HTTPS que conserva un header

Para cuando piden las dos cosas juntas: el HTTPS de Parcial 2025-2Q · Ejercicio 4 y el header de Parcial 2024-2C · Ejercicio 1 (son los dos ejercicios que siguen). Reemplazá NUEVO por el nombre que se pide, ORIGEN por el servidor de atrás y X-NOMBRE por el header, y armá \$http_x_nombre con la regla del comentario. Los errores y las variantes están en el Ojo de esos dos ejercicios.

**Buscar:** receta rápida, HTTPS y header, HTTPS más un header, conservar un header con HTTPS, proxy reverso HTTPS, \$http\_, nombre del header, variable del header, cabecera, encabezado, TLS, proxy_pass_header

> **1.** Instalá nginx y hacé que NUEVO apunte a tu máquina.

sudo apt install -y nginx openssl ca-certificates

Hay que hacer sudo nano /etc/hosts y agregar al final lo siguiente:

127.0.0.1 NUEVO

> **2.** Solo si es HTTPS: certificado con el nombre en el SAN y confiado en el sistema (curl va sin -k).

sudo mkdir -p /etc/nginx/ssl

sudo openssl req -x509 -newkey rsa:2048 -nodes -days 365 \\

-keyout /etc/nginx/ssl/NUEVO.key -out /etc/nginx/ssl/NUEVO.crt \\

-subj "/CN=NUEVO" -addext "subjectAltName=DNS:NUEVO"

sudo cp /etc/nginx/ssl/NUEVO.crt /usr/local/share/ca-certificates/NUEVO.crt

sudo update-ca-certificates

> **3.** Sitio de nginx. Si es HTTP, cambiá listen 443 ssl; por listen 80; y borrá las dos líneas ssl\_.

Hay que hacer sudo nano /etc/nginx/sites-available/NUEVO, borrar lo que tenga y pegar lo siguiente:

server {

listen 443 ssl;

server_name NUEVO;

ssl_certificate /etc/nginx/ssl/NUEVO.crt;

ssl_certificate_key /etc/nginx/ssl/NUEVO.key;

location / {

proxy_pass http://ORIGEN;

proxy_set_header Host ORIGEN;

\# \$http\_ + nombre del header en minusculas, - pasa a \_ (X-Parcial-Protos -\> \$http_x_parcial_protos)

proxy_set_header X-NOMBRE \$http_x_nombre;

proxy_pass_header X-NOMBRE;

}

}

> **4.** Activá el sitio, validá y reiniciá.

sudo ln -sf /etc/nginx/sites-available/NUEVO /etc/nginx/sites-enabled/

sudo nginx -t && sudo systemctl restart nginx

**Demostrar:** con HTTP, cambiá https por http en todos los curl.

curl https://NUEVO/

curl -i https://NUEVO/

El primero muestra lo mismo que curl http://ORIGEN/. El segundo muestra arriba, antes del contenido, los headers de la respuesta: tiene que estar la línea X-NOMBRE (si el origen lo manda). El header en el pedido: capturá en una terminal y, en otra, mandá el pedido con el header. El grep deja solo las líneas Host: y X-NOMBRE:, y el -l hace que tcpdump las vaya mostrando al momento. Con HTTPS solo se ve el tramo de nginx al origen (el del cliente va cifrado), que es el que muestra el valor conservado. Cortá el tcpdump con Ctrl+C.

sudo tcpdump -l -n -A -i any tcp port 80 \| grep -iE 'host:\|X-NOMBRE'

curl -H 'X-NOMBRE: pincha' https://NUEVO/

*Probado fuera del lab con nginx 1.24, OpenSSL 3.0.13 y curl de Ubuntu 24.04: nginx -t en HTTPS y en HTTP, y el header en los dos sentidos contra un origen simulado. El resto son los mismos comandos de los dos ejercicios que siguen, probados en el lab.*

### nginx: proxy reverso HTTPS de seguro.protos.foo a parcial.protos.foo

**Tomado en:** Parcial 2025-2Q · Ejercicio 4.

> **Idea:** La idea es armar un nginx que atienda https://seguro.protos.foo y, con proxy_pass, devuelva lo que sirve http://parcial.protos.foo sin que cambie la URL. El truco es que curl va sin -k: el certificado autofirmado tiene que llevar el nombre en el SAN y quedar confiado en el sistema con update-ca-certificates.

**Buscar:** HTTPS, proxy reverso, reverse proxy, nginx, proxy_pass, seguro.protos.foo, parcial.protos.foo, servidor HTTP inseguro, exponerlo al público, exponerlo al publico, a través de HTTPS, a traves de HTTPS, certificado autofirmado, self-signed, openssl req -x509, subjectAltName, SAN, update-ca-certificates, /usr/local/share/ca-certificates, listen 443 ssl, ssl_certificate, ssl_certificate_key, /etc/hosts, curl https://seguro.protos.foo, No se debe ver modificada la URL, no se permiten otras opciones, sin -k, User Agent, SSL certificate problem, Ejercicio IV, TLS, SSL/TLS, Firefox, Chrome, Chromium, navegador, HSTS, thisisunsafe, importar certificado, Autoridades, CA propia, CA:TRUE, basicConstraints, MOZILLA_PKIX_ERROR_CA_CERT_USED_AS_END_ENTITY, conservar un header, \$http\_, add_header, always, proxy_hide_header, usuario y clave, contraseña, autenticación básica, autenticacion basica, auth_basic, auth_basic_user_file, htpasswd, .htpasswd, openssl passwd, apr1, 401, location /app/, chrome://certificate-manager, certificate-manager, openssl x509 -req, -CAcreateserial, san.ext, extendedKeyUsage, serverAuth, ca.crt, No es seguro, No seguro, apache2-utils, htpasswd -bc, Authorization Required, Permission denied, chmod 644, Ver certificados, chrome://settings/certificates, Certificados locales, snap, Settings, Privacy & Security, View Certificates, Authorities, Trust this CA to identify websites, Local certificates, Installed by you, Not secure, Trust this certificate for identifying websites, Trusted Certificates, Certificados de confianza

**Enunciado:** El servidor en parcial.protos.foo está corriendo un servidor HTTP inseguro. Usted, como un administrador responsable, desea exponerlo al público a través de HTTPS. Configure lo necesario para que al acceder a https://seguro.protos.foo con un HTTP User Agent (Firefox, Google Chrome, Curl), se sirva el contenido devuelto de parcial.protos.foo. Requisito. No se debe ver modificada la URL ingresada en el browser. Si se utiliza curl no se permiten otras opciones. Se recomienda verificación utilizando curl. \$ curl https://seguro.protos.foo

**Solución:**

> **1.** Instalá nginx si falta y mirá qué devuelve el servidor inseguro (para comparar al final).

sudo apt install -y nginx openssl ca-certificates

curl -i http://parcial.protos.foo

> **2.** Hacé que seguro.protos.foo apunte a tu máquina.

Hay que hacer sudo nano /etc/hosts y agregar al final lo siguiente:

127.0.0.1 seguro.protos.foo

> **3.** Creá el certificado autofirmado con el nombre en el SAN.

sudo mkdir -p /etc/nginx/ssl

sudo openssl req -x509 -newkey rsa:2048 -nodes -days 365 \\

-keyout /etc/nginx/ssl/seguro.protos.foo.key \\

-out /etc/nginx/ssl/seguro.protos.foo.crt \\

-subj "/CN=seguro.protos.foo" \\

-addext "subjectAltName=DNS:seguro.protos.foo"

> **4.** Hacé que el sistema confíe en ese certificado. Como curl va sin opciones, no podés usar -k.

sudo cp /etc/nginx/ssl/seguro.protos.foo.crt /usr/local/share/ca-certificates/seguro.protos.foo.crt

sudo update-ca-certificates

1 added, 0 removed; done.

> **5.** Sitio de nginx, /etc/nginx/sites-available/seguro.protos.foo.

Hay que hacer sudo nano /etc/nginx/sites-available/seguro.protos.foo, borrar lo que tenga y pegar lo siguiente:

server {

listen 443 ssl;

server_name seguro.protos.foo;

ssl_certificate /etc/nginx/ssl/seguro.protos.foo.crt;

ssl_certificate_key /etc/nginx/ssl/seguro.protos.foo.key;

location / {

proxy_pass http://parcial.protos.foo;

proxy_set_header Host parcial.protos.foo;

proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;

proxy_set_header X-Forwarded-Proto https;

}

}

> **6.** Activá el sitio, validá y reiniciá.

sudo ln -sf /etc/nginx/sites-available/seguro.protos.foo /etc/nginx/sites-enabled/

sudo nginx -t && sudo systemctl restart nginx

\# viejo: sudo service nginx restart

**Demostrar:**

curl https://seguro.protos.foo

curl -v https://seguro.protos.foo

El primero muestra el mismo contenido que curl http://parcial.protos.foo, sin errores ni opciones. El segundo muestra además cómo se conectó: entre las líneas que empiezan con \* y con \< tienen que estar estas (el resto se puede ignorar):

\* subject: CN=seguro.protos.foo

\* issuer: CN=seguro.protos.foo

\* SSL certificate verify ok.

\< HTTP/1.1 200 OK

**Ojo:**

- curl: (60) SSL certificate problem: self-signed certificate: falta el paso 4. El archivo en /usr/local/share/ca-certificates/ tiene que terminar en .crt.

- curl: (60) SSL: no alternative certificate subject name matches target host name 'seguro.protos.foo': el certificado se generó con otro nombre. Rehacé el paso 3, el 4 y reiniciá nginx.

- curl: (35) ... wrong version number: el listen 443 quedó sin la palabra ssl.

- curl: (6) Could not resolve host: seguro.protos.foo: falta el paso 2. El nombre no existe en el DNS público (consultado el 11/10/2026: NXDOMAIN), por eso va en /etc/hosts.

- curl: (7) Failed to connect to seguro.protos.foo port 443: nginx no está corriendo. sudo nginx -t y sudo systemctl status nginx.

- nginx: \[emerg\] host not found in upstream "parcial.protos.foo": nginx resuelve el nombre al arrancar y tu VM no lo resuelve. Verificá dig +short parcial.protos.foo (el 11/10/2026 devolvía 192.168.0.172, una IP privada: solo se llega desde la red del laboratorio, con la VM en Bridge).

- nginx: \[emerg\] cannot load certificate "/etc/nginx/ssl/...": BIO_new_file() failed: ruta mal escrita o falta el paso 3.

- nginx: \[emerg\] bind() to 0.0.0.0:443 failed (98: Address already in use): otro servicio usa el 443. Corré sudo ss -ltnp, buscá la línea con :443 (al final dice qué programa es) y paralo (por ejemplo sudo systemctl stop apache2).

- No pongas proxy_set_header Host \$host; copiado de tutoriales: manda Host: seguro.protos.foo al servidor de atrás, que no conoce ese nombre y puede devolver otro sitio o 404.

- Si además piden conservar un header: dentro del location, agregá las dos líneas de X-Parcial-Protos del ejercicio siguiente, con el nombre nuevo (todo junto está en la "Receta rápida" de arriba). La variable es \$http\_ más el nombre en minúsculas, con \_ en lugar de -. Si dudás de cómo se escribe, no pongas el proxy_set_header: nginx ya reenvía el header solo, y una variable mal escrita lo rompe sin avisar.

- Si piden otra cosa, dentro del location: agregar un header en la respuesta, add_header X-Nombre "valor" always;; mandar uno fijo al origen, proxy_set_header X-Nombre "valor";; sacar uno que manda el origen, proxy_hide_header X-Nombre; (si pusiste proxy_pass_header X-Nombre;, borralo: lo anula). Detalles en el Ojo del ejercicio siguiente. El origen bajo una ruta va en otro location del mismo server: location /app/ { proxy_pass http://parcial.protos.foo/; }, donde la / final le saca /app a la ruta (detalles en el Ojo de Parcial 2022-2C · Ejercicio 1). Usuario y clave: el ítem que sigue.

- Usuario y clave delante del proxy (autenticación básica). Sacá el hash de la clave sin instalar nada:

openssl passwd -apr1 CLAVE

Hay que hacer sudo nano /etc/nginx/.htpasswd y agregar al final lo siguiente, con la salida del comando anterior (empieza con \$apr1\$) en lugar de HASH:

USUARIO:HASH

Después agregá auth_basic "Restringido"; y auth_basic_user_file /etc/nginx/.htpasswd; dentro del mismo location / del proxy_pass, y reiniciá. Se demuestra sin opciones: curl https://seguro.protos.foo da 401 Authorization Required y curl https://USUARIO:CLAVE@seguro.protos.foo da el contenido. Si da 500 y /var/log/nginx/error.log dice Permission denied: sudo chmod 644 /etc/nginx/.htpasswd.

En lugar del openssl passwd y el nano, el archivo se puede crear con htpasswd, que viene en apache2-utils: sudo apt install -y apache2-utils y sudo htpasswd -bc /etc/nginx/.htpasswd USUARIO CLAVE.

- "No se debe ver modificada la URL": no sirve un return 301 http://parcial.protos.foo. Tiene que ser proxy_pass. Las redirecciones del servidor de atrás (Location: http://parcial.protos.foo/...) nginx las reescribe solo a https://seguro.protos.foo/....

- Después de systemctl reload nginx el primer curl puede agarrar la configuración vieja. Usá restart.

- Firefox y Chrome no usan el almacén del sistema y, como .foo tiene HSTS precargado, no muestran el botón para aceptar la excepción. La verificación que pide el enunciado es con curl. Si igual piden mostrarlo en un navegador, lo más rápido es Chrome o Chromium: con la página del aviso en foco, tipeá a ciegas thisisunsafe (no hay campo de texto). Carga aunque haya HSTS, pero la barra sigue diciendo "No seguro", "No es seguro" o "Not secure", según el idioma. Firefox no tiene atajo.

- Para que el navegador confíe de verdad (en Firefox es la única forma), no alcanza con importar el certificado del paso 3: OpenSSL 3 lo genera con CA:TRUE, y Firefox no acepta un certificado de CA como certificado del sitio aunque lo importes como autoridad (MOZILLA_PKIX_ERROR_CA_CERT_USED_AS_END_ENTITY). Rehacé el paso 3 con una CA propia que firme el certificado del sitio. nginx no cambia (usa los mismos .crt y .key) y curl sigue andando, porque se confía la CA:

sudo openssl req -x509 -newkey rsa:2048 -nodes -days 365 \\

-keyout /etc/nginx/ssl/ca.key -out /etc/nginx/ssl/ca.crt -subj "/CN=CA Parcial Protos"

sudo openssl req -newkey rsa:2048 -nodes \\

-keyout /etc/nginx/ssl/seguro.protos.foo.key \\

-out /etc/nginx/ssl/seguro.protos.foo.csr -subj "/CN=seguro.protos.foo"

Hay que hacer sudo nano /etc/nginx/ssl/san.ext, borrar lo que tenga y pegar lo siguiente:

subjectAltName=DNS:seguro.protos.foo

basicConstraints=CA:FALSE

extendedKeyUsage=serverAuth

sudo openssl x509 -req -in /etc/nginx/ssl/seguro.protos.foo.csr \\

-CA /etc/nginx/ssl/ca.crt -CAkey /etc/nginx/ssl/ca.key -CAcreateserial \\

-days 365 -extfile /etc/nginx/ssl/san.ext -out /etc/nginx/ssl/seguro.protos.foo.crt

sudo cp /etc/nginx/ssl/ca.crt /usr/local/share/ca-certificates/ca-parcial-protos.crt

sudo update-ca-certificates

sudo nginx -t && sudo systemctl restart nginx

cp /etc/nginx/ssl/ca.crt ~/

Después importá ~/ca.crt en el navegador (va en tu home porque los navegadores instalados como snap no leen /etc). Firefox: Ajustes (Settings), Privacidad y seguridad (Privacy & Security), Certificados (Certificates), Ver certificados (View Certificates), pestaña Autoridades (Authorities), Importar (Import), tildá "Confiar en esta CA para identificar sitios web" (Trust this CA to identify websites) y recargá. Chrome o Chromium: chrome://certificate-manager, Certificados locales (Local certificates), Installed by you, y el botón Importar (Import) de Certificados de confianza (Trusted Certificates), no el de Intermediate ni el de Distrusted; después reabrí el navegador. En versiones viejas es chrome://settings/certificates, pestaña Autoridades (Authorities), Importar, y tildá la confianza para identificar sitios web (Trust this certificate for identifying websites). Con la CA, el curl -v de Demostrar muestra issuer: CN=CA Parcial Protos, y para sacar la confianza al terminar borrá también /usr/local/share/ca-certificates/ca-parcial-protos.crt.

- Opcional, mandar el puerto 80 a HTTPS. Hay que hacer sudo nano /etc/nginx/sites-available/seguro.protos.foo y agregar al final lo siguiente (después sudo nginx -t && sudo systemctl restart nginx):

server {

listen 80;

server_name seguro.protos.foo;

return 301 https://\$host\$request_uri;

}

- Para sacar la confianza al terminar: sudo rm /usr/local/share/ca-certificates/seguro.protos.foo.crt y sudo update-ca-certificates --fresh.

- Se probó contra un servidor local que imita a parcial.protos.foo (el real solo existe en la red del laboratorio).

*Probado en Ubuntu 24.04 (lab). Los agregados del Ojo (header, autenticación básica con openssl passwd -apr1 y CA propia) se probaron fuera del lab con nginx 1.24, OpenSSL 3.0.13 y curl de Ubuntu 24.04. Sin probar: todo lo de Chrome y Firefox (no hay entorno gráfico) y htpasswd (no estaba instalado).*

### nginx: proxy reverso de foo.example.org a protos.sebikul.com conservando X-Parcial-Protos

**Tomado en:** Parcial 2024-2C · Ejercicio 1.

> **Idea:** La idea es armar un nginx que atienda http://foo.example.org/ y, con proxy_pass, sirva cualquier recurso de http://protos.sebikul.com/ sin cambiar la URL, conservando el header X-Parcial-Protos en el pedido y en la respuesta. El truco es mandarle al origen Host: protos.sebikul.com fijo, no \$host, y apuntar foo.example.org a tu máquina en /etc/hosts.

**Buscar:** proxy reverso, reverse proxy, nginx, proxy_pass, foo.example.org, protos.sebikul.com, X-Parcial-Protos, conserve el valor del header, conservar header, proxy_set_header, proxy_pass_header, \$http_x_parcial_protos, Host, /etc/hosts, server_name, sites-available, sites-enabled, No se debe ver modificada la URL, no se permiten otras opciones, curl http://foo.example.org/, cualquier recurso, User Agent, Ejercicio I, cabecera, encabezado, \$http\_, add_header, always, proxy_hide_header, valor fijo, header fijo, header en la respuesta, quitar el header, reemplazar el header, header duplicado

**Enunciado:** Realizar los cambios necesarios para que al ingresar a http://foo.example.org/ utilizando un HTTP User Agent (Firefox, Google Chrome, Curl) se sirva el contenido de http://protos.sebikul.com/. Conserve el valor del header X-Parcial-Protos. Requisito. No se debe ver modificada la URL ingresada en el browser. Si se utiliza curl no se permiten otras opciones. Se recomienda verificación utilizando curl. \$ curl http://foo.example.org/). Nota. Las representaciones que retorna protos.sebikul.com pueden cambiar sin previo aviso. Se puede llegar a pedir cualquier recurso dentro protos.sebikul.com.

**Solución:**

> **1.** Instalá nginx si falta y mirá qué devuelve el original, con headers.

sudo apt install -y nginx

curl -i http://protos.sebikul.com/

> **2.** Hacé que foo.example.org apunte a tu máquina.

Hay que hacer sudo nano /etc/hosts y agregar al final lo siguiente:

127.0.0.1 foo.example.org

> **3.** Sitio de nginx, /etc/nginx/sites-available/foo.example.org.

Hay que hacer sudo nano /etc/nginx/sites-available/foo.example.org, borrar lo que tenga y pegar lo siguiente:

server {

listen 80;

server_name foo.example.org;

location / {

proxy_pass http://protos.sebikul.com;

proxy_set_header Host protos.sebikul.com;

\# \$http\_ + nombre del header en minusculas, - pasa a \_ (X-Parcial-Protos -\> \$http_x_parcial_protos)

proxy_set_header X-Parcial-Protos \$http_x_parcial_protos; \# pedido: reenvia el valor que mando el cliente

proxy_pass_header X-Parcial-Protos; \# respuesta: deja pasar el del servidor

}

}

> **4.** Activá el sitio, validá y reiniciá.

sudo ln -sf /etc/nginx/sites-available/foo.example.org /etc/nginx/sites-enabled/

sudo nginx -t && sudo systemctl restart nginx

\# viejo: sudo service nginx restart

**Demostrar:**

El comando del enunciado: tiene que mostrar lo mismo que curl http://protos.sebikul.com/.

curl http://foo.example.org/

El header en la respuesta (si el servidor lo manda, llega igual). -i muestra arriba, antes del contenido, los headers: mirá la primera línea y la de X-Parcial-Protos.

curl -i http://foo.example.org/

Salida esperada, recortada a las dos líneas que importan (además salen Server, Date, Content-Type, otros headers y el contenido):

HTTP/1.1 200 OK

X-Parcial-Protos: valor-secreto-del-servidor

El header en el pedido: capturá en una terminal y, en otra, mandá el pedido con el header. El grep deja solo las líneas Host: y X-Parcial-Protos:, y el -l hace que tcpdump las vaya mostrando al momento.

sudo tcpdump -l -n -A -i any tcp port 80 \| grep -iE 'host:\|x-parcial'

curl -H 'X-Parcial-Protos: pincha' http://foo.example.org/

Host: foo.example.org

X-Parcial-Protos: pincha

Host: protos.sebikul.com

X-Parcial-Protos: pincha

X-Parcial-Protos: valor-secreto-del-servidor

X-Parcial-Protos: valor-secreto-del-servidor

El segundo par es el pedido que nginx le manda a protos.sebikul.com: mismo valor del header y Host correcto. Las dos últimas líneas son el header de la respuesta, del servidor a nginx y de nginx a curl (solo aparecen si el servidor lo manda). Cortá el tcpdump con Ctrl+C. En Wireshark, el filtro es http.host == "protos.sebikul.com" y el header se ve en el panel Hypertext Transfer Protocol.

**Ojo:**

- "Conserve el valor" se puede leer en los dos sentidos (el header que manda el cliente hacia el servidor, o el que devuelve el servidor hacia el cliente). La configuración cubre los dos y la demostración muestra los dos.

- nginx ya reenvía por defecto los headers del cliente y los de la respuesta. Las dos líneas de X-Parcial-Protos lo dejan explícito; no rompen nada si el header no viene (un valor vacío no se envía).

- Si piden tocar el header en vez de conservarlo, en el mismo location: un valor fijo hacia el origen, proxy_set_header X-Parcial-Protos "VALOR"; en lugar de la línea del pedido; un valor fijo en la respuesta, add_header X-Parcial-Protos "VALOR" always; (sin always no sale en un 404, 500 ni 502); reemplazar el que manda el origen, proxy_hide_header X-Parcial-Protos; en lugar de la línea proxy_pass_header, más ese add_header (sin el proxy_hide_header salen dos); quitarlo, proxy_set_header X-Parcial-Protos ""; hacia el origen (en lugar de la línea del pedido) o proxy_hide_header X-Parcial-Protos; en la respuesta (en lugar de la línea proxy_pass_header). Si queda proxy_pass_header X-Parcial-Protos;, anula el proxy_hide_header y el header del origen sigue saliendo.

- Un add_header dentro de un location anula todos los add_header del server: si hay en los dos lugares, repetí en el location los del server.

- Para mostrar el always, pedí una ruta que el origen no tenga: curl -i http://foo.example.org/no-existe muestra arriba el 404 y el header. Sirve solo si el origen devuelve 404 para esa ruta.

- No pongas proxy_set_header Host \$host;: manda Host: foo.example.org al servidor real, que no conoce ese nombre. En la prueba eso devolvió el sitio equivocado. El apunte del alumno tenía la línea de Host comentada: anda porque nginx usa por defecto el nombre del proxy_pass, pero es más seguro dejarla escrita.

- "Cualquier recurso": location / con proxy_pass sin ruta pasa la URI tal cual. curl http://foo.example.org/dir/archivo pide /dir/archivo al servidor real.

- "No se debe ver modificada la URL": tiene que ser proxy_pass, no una redirección. Las respuestas Location: http://protos.sebikul.com/... nginx las reescribe solo a http://foo.example.org/....

- No alcanza con poner en /etc/hosts la IP de protos.sebikul.com al lado de foo.example.org: el pedido llegaría con Host: foo.example.org y el servidor no lo reconoce.

- Sale Welcome to nginx!: el pedido cayó en el sitio default. Revisá server_name, el link en sites-enabled y que reiniciaste.

- curl: (6) Could not resolve host: foo.example.org: falta el paso 2.

- nginx: \[emerg\] host not found in upstream "protos.sebikul.com": la VM no resuelve ese nombre (el 11/10/2026 no tenía registro A público; en el parcial lo publica la cátedra). Probá dig +short protos.sebikul.com.

- Si curl -i http://protos.sebikul.com/ devuelve 301 hacia https, cambiá dos líneas: proxy_pass https://protos.sebikul.com; y agregá proxy_ssl_server_name on;.

- Si el HTML trae links absolutos a protos.sebikul.com y querés que no se vean, agregá dentro del location: proxy_set_header Accept-Encoding "";, sub_filter 'http://protos.sebikul.com' 'http://foo.example.org'; y sub_filter_once off;.

- Se probó contra un servidor local que imita a protos.sebikul.com y agrega el header.

*Probado en Ubuntu 24.04 (lab). Los agregados del Ojo para tocar el header (add_header ... always, proxy_hide_header, que proxy_pass_header lo anula, proxy_set_header ... "" y la herencia de add_header) se probaron fuera del lab con nginx 1.24 contra un origen simulado.*

### nginx: proxy reverso de foo.example.org a foo.leak.com.ar

**Tomado en:** Parcial 2022-2C · Ejercicio 1.

**También tomado en:** Parcial o Recuperatorio 2021-1C · Ejercicio II y Parcial 2020-1C · Ejercicio IV (mismo enunciado, más el pedido de arrancar el video con el servidor apagado).

> **Idea:** La idea es armar un nginx que atienda http://foo.example.org/ y, con proxy_pass, sirva cualquier recurso de http://foo.leak.com.ar/ sin que cambie la URL. La trampa es que no alcanza con apuntar foo.example.org a la IP de foo.leak.com.ar en /etc/hosts: el origen no reconoce ese Host, así que nginx tiene que mandarle Host: foo.leak.com.ar, no \$host.

**Buscar:** proxy reverso, reverse proxy, nginx, proxy_pass, foo.example.org, foo.leak.com.ar, /etc/hosts, server_name, sites-available, sites-enabled, No se debe ver modificada la URL, no se permiten otras opciones, curl http://foo.example.org/, cualquier recurso, representaciones, User Agent, Firefox, Google Chrome, proxy inverso, Realizar los cambios necesarios para que al ingresar a, HTTP User Agent, se sirva el contenido de, URL ingresada en el browser, Si se utiliza curl no se permiten otras opciones, verificación utilizando curl, verificacion utilizando curl, pueden cambiar sin previo aviso, cualquier recurso dentro foo.leak.com.ar, el servidor no está corriendo, el servidor no esta corriendo, sin compresión, sin compresion, bajo una ruta, prefijo, subruta, location /app/, barra final, proxy_pass con barra

**Enunciado:** Realizar los cambios necesarios para que al ingresar a http://foo.example.org/ utilizando un HTTP User Agent (Firefox, Google Chrome, Curl) se sirva el contenido de http://foo.leak.com.ar/. Requisito. No se debe ver modificada la URL ingresada en el browser. Si se utiliza curl no se permiten otras opciones. Se recomienda verificación utilizando curl. \$ curl http://foo.example.org/). Nota. Las representaciones que retorna foo.leak.com.ar pueden cambiar sin previo aviso. Se puede llegar a pedir cualquier recurso dentro foo.leak.com.ar. (El paréntesis de cierre suelto después del comando curl está así en el original.)

**Enunciado en Parcial o Recuperatorio 2021-1C · Ejercicio II y en Parcial 2020-1C · Ejercicio IV:** el mismo texto y al final agrega "Video. Si para la resolución del video tuviese que configurar un servidor inicie el video mostrando que el servidor no está corriendo, para luego levantarlo y realizar la demostración."

**Solución:**

> **1.** Instalá nginx si falta y mirá qué devuelve el original.

sudo apt install -y nginx

curl -i http://foo.leak.com.ar/

> **2.** Hacé que foo.example.org apunte a tu máquina.

Hay que hacer sudo nano /etc/hosts y agregar al final lo siguiente:

127.0.0.1 foo.example.org

> **3.** Sitio de nginx, /etc/nginx/sites-available/foo.example.org.

Hay que hacer sudo nano /etc/nginx/sites-available/foo.example.org, borrar lo que tenga y pegar lo siguiente:

server {

listen 80;

server_name foo.example.org;

location / {

proxy_pass http://foo.leak.com.ar;

proxy_set_header Host foo.leak.com.ar;

}

}

> **4.** Activá el sitio, validá y reiniciá.

sudo ln -sf /etc/nginx/sites-available/foo.example.org /etc/nginx/sites-enabled/

sudo nginx -t && sudo systemctl restart nginx

\# viejo: sudo service nginx restart

**Demostrar:**

curl http://foo.example.org/

curl http://foo.leak.com.ar/

curl -i http://foo.example.org/RECURSO

Los dos primeros muestran lo mismo. El tercero es para probar "cualquier recurso": RECURSO es una ruta que exista en foo.leak.com.ar.

**Ojo:**

- No alcanza con poner en /etc/hosts la IP de foo.leak.com.ar al lado de foo.example.org: el pedido llegaría con Host: foo.example.org y el servidor real no reconoce ese nombre. Tiene que ser un proxy reverso.

- No pongas proxy_set_header Host \$host;: manda Host: foo.example.org al servidor real. En la prueba eso devolvió el sitio equivocado.

- "No se debe ver modificada la URL": proxy_pass, no return 301. Las respuestas Location: http://foo.leak.com.ar/... nginx las reescribe solo a http://foo.example.org/....

- "Cualquier recurso": location / con proxy_pass sin ruta pasa la URI tal cual.

- Si piden el origen bajo una ruta (por ejemplo, que http://foo.example.org/app/ sirva http://foo.leak.com.ar/), la barra final de proxy_pass decide qué le llega al origen. Con location /app/ (y el mismo proxy_set_header Host) y un pedido a /app/x: proxy_pass http://foo.leak.com.ar; manda /app/x; proxy_pass http://foo.leak.com.ar/; manda /x; proxy_pass http://foo.leak.com.ar/otra/; manda /otra/x. Un pedido a /app, sin la barra, nginx lo redirige solo a /app/ con un 301.

- Sale Welcome to nginx! o curl ... not found: el nombre resolvió pero no hay virtual host para él y el pedido cayó en el sitio default. Revisá server_name, el link en sites-enabled y que reiniciaste.

- curl: (6) Could not resolve host: foo.example.org: falta el paso 2.

- 502 Bad Gateway: nginx resuelve el nombre pero el origen no responde. No se arregla con /etc/hosts: probá el curl -i http://foo.leak.com.ar/ del paso 1.

- Si curl -i http://foo.leak.com.ar/ devuelve 301 hacia https, cambiá a proxy_pass https://foo.leak.com.ar; y agregá proxy_ssl_server_name on;.

- Si el HTML trae links absolutos a foo.leak.com.ar y querés que no se vean, agregá dentro del location: proxy_set_header Accept-Encoding "";, sub_filter 'http://foo.leak.com.ar' 'http://foo.example.org'; y sub_filter_once off;.

- El apunte del alumno solo decía "agregar el proxy_pass": faltaban el /etc/hosts y el server_name.

- Se probó contra un servidor local que imita a foo.leak.com.ar.

- nginx: \[emerg\] host not found in upstream "foo.leak.com.ar": la VM no resuelve ese nombre y nginx no arranca (lo resuelve al cargar la configuración). El 11/10/2026 foo.leak.com.ar daba NXDOMAIN: si lo vuelven a tomar va a ser con otro dominio, cambiá el nombre en las dos líneas del location. Si el dominio es el del enunciado y no resuelve, averiguá su IP con dig +short foo.leak.com.ar @8.8.8.8 (o con el DNS o la IP que dé el enunciado), anotala y agregala a /etc/hosts; recién ahí sudo nginx -t y reiniciá.

Hay que hacer sudo nano /etc/hosts y agregar al final lo siguiente, cambiando IP_DE_FOO_LEAK por la IP que anotaste:

IP_DE_FOO_LEAK foo.leak.com.ar

**Variantes:**

- El video arranca con el servidor apagado (Parcial o Recuperatorio 2021-1C · Ejercicio II y Parcial 2020-1C · Ejercicio IV): empezá con nginx apagado y mostralo con sudo systemctl status nginx; después levantalo (paso 4) y hacé la demostración. Ojo: el apt install del paso 1 deja nginx corriendo; si lo acabás de instalar, paralo antes de grabar con sudo systemctl stop nginx. Los comandos para mostrarlo apagado están en el paso 1 de Parcial 2021-1C · Ejercicio 1, en este mismo tipo.

*Probado en Ubuntu 24.04 (lab). La barra final de proxy_pass con location /app/ se probó fuera del lab con nginx 1.24. Sin probar: el sudo systemctl stop nginx de Variantes.*

### Proxy inverso con compresión transparente (foo.example.org sirve foo.leak.com.ar)

**Tomado en:** Parcial 2021-1C · Ejercicio 1.

> **Idea:** La idea es armar un nginx que atienda http://foo.example.org/ y, con proxy_pass y gzip activado, sirva el contenido de http://foo.leak.com.ar/ comprimido solo si el cliente lo pide. La clave es demostrarlo con curl -i --compressed mostrando el Content-Encoding: gzip (sin eso vale la mitad), arrancando el video con nginx apagado.

**Buscar:** foo.example.org, foo.leak.com.ar, proxy_pass, proxy inverso, proxy reverso, comprimido de forma transparente, compresion transparente, gzip, Content-Encoding, Accept-Encoding, --compressed, HTTP User Agent, no se debe ver modificada la URL, curl, Realizar los cambios necesarios para que al ingresar a, se sirva el contenido de, si el cliente así lo pidiese, si el cliente asi lo pidiese, compresión transparente, URL ingresada en el browser, sólamente se permiten las opciones necesarias para negociar la compresión, solamente se permiten las opciones necesarias para negociar la compresion, verificación utilizando curl, verificacion utilizando curl, pueden cambiar sin previo aviso, el servidor no está corriendo, el servidor no esta corriendo, mitad del puntaje posible, no sepan demostrar el funcionamiento, nginx apagado, systemctl stop, inactive (dead), apt install nginx

**Enunciado:** "Realizar los cambios necesarios para que al ingresar a http://foo.example.org/ utilizando un HTTP User Agent (Firefox, Google Chrome, Curl) se sirva el contenido de http://foo.leak.com.ar/. El contenido se debe servir comprimido de forma transparente si el cliente así lo pidiese. Requisito. No se debe ver modificada la URL ingresada en el browser. Si se utiliza curl sólamente se permiten las opciones necesarias para negociar la compresión. Se recomienda verificación utilizando curl. \$ curl … http://foo.example.org/). Nota. Las representaciones que retorna foo.leak.com.ar pueden cambiar sin previo aviso. Video. Si para la resolución del video tuviese que configurar un servidor inicie el video mostrando que el servidor no está corriendo, para luego levantarlo y realizar la demostración. Advertencia. Se asignará la mitad del puntaje posible a quienes no configuren y/o no sepan demostrar el funcionamiento de la compresión transparente."

**Solución:**

> **1.** El video pide empezar con nginx APAGADO. Antes de grabar, instalalo si falta y paralo (el apt install lo deja corriendo):

sudo apt install -y nginx

sudo systemctl stop nginx

Ya grabando, mostralo apagado: systemctl status tiene que decir Active: inactive (dead) y en ps ax \| grep nginx solo tiene que aparecer la línea del propio grep (grep nginx, o grep --color=auto nginx en Ubuntu), ningún proceso de nginx. Lo arranca el restart del paso 5.

sudo systemctl status nginx \# forma vieja: service nginx status

ps ax \| grep nginx

> **2.** Que foo.example.org resuelva a tu máquina:

Hay que hacer sudo nano /etc/hosts y agregar al final lo siguiente:

127.0.0.1 foo.example.org

> **3.** nginx tiene que poder resolver foo.leak.com.ar (lo resuelve al cargar la config). Probá con dig +short foo.leak.com.ar: si da una IP, salteá este paso. Si no da nada, averiguá la IP con dig +short foo.leak.com.ar @8.8.8.8 (o con el DNS o la IP que dé el enunciado), anotala y agregala a /etc/hosts.

Hay que hacer sudo nano /etc/hosts y agregar al final lo siguiente, cambiando IP_DE_FOO_LEAK por la IP que anotaste:

IP_DE_FOO_LEAK foo.leak.com.ar

> **4.** Archivo del virtual host proxy, /etc/nginx/sites-available/foo.example.org:

Hay que hacer sudo nano /etc/nginx/sites-available/foo.example.org, borrar lo que tenga y pegar lo siguiente:

server {

listen 80;

server_name foo.example.org;

\# compresion transparente: nginx comprime la respuesta solo si el cliente manda Accept-Encoding: gzip

gzip on;

gzip_types \*;

gzip_min_length 1;

gzip_proxied any;

gzip_http_version 1.0;

gzip_vary on;

location / {

proxy_pass http://foo.leak.com.ar;

proxy_set_header Host foo.leak.com.ar;

}

}

> **5.** Activá el sitio, validá y reiniciá:

sudo ln -sf /etc/nginx/sites-available/foo.example.org /etc/nginx/sites-enabled/

sudo nginx -t && sudo systemctl restart nginx

\# forma vieja: sudo service nginx restart

**Demostrar:**

Lo mínimo para el video (solo --compressed, la opción que negocia la compresión, y -i para que se vean los headers):

\# 1) la URL no cambia y se ve el contenido de foo.leak.com.ar

curl http://foo.example.org/

\# 2) SIN pedir compresion: no hay Content-Encoding y viene Content-Length

curl -i http://foo.example.org/

\# 3) PIDIENDO compresion: aparece Content-Encoding: gzip y curl lo descomprime solo

curl -i --compressed http://foo.example.org/

Extra, solo si el corrector pide ver los bytes o el tamaño (suma -s, -o, -w y -v, que no negocian nada; -H 'Accept-Encoding: gzip' es el --compressed hecho a mano):

\# 4) lo que viaja son bytes gzip de verdad

curl -s -H 'Accept-Encoding: gzip' http://foo.example.org/ \| file -

curl -s -H 'Accept-Encoding: gzip' http://foo.example.org/ \| gunzip

\# 5) tamaño que viaja sin y con compresion

curl -s -o /dev/null -w 'sin gzip: %{size_download} bytes\n' http://foo.example.org/

curl -s -o /dev/null -w 'con gzip: %{size_download} bytes\n' -H 'Accept-Encoding: gzip' http://foo.example.org/

\# 6) la negociacion completa: \> es lo que manda curl, \< lo que contesta nginx

curl -sv --compressed -o /dev/null http://foo.example.org/

Salida esperada, en orden y recortada (en la 6, mirá \> Accept-Encoding, lo que pide curl, y \< Content-Encoding: gzip, lo que contesta nginx):

hola mundo!

HTTP/1.1 200 OK

Content-Type: text/plain

Content-Length: 12

Vary: Accept-Encoding

hola mundo!

HTTP/1.1 200 OK

Content-Type: text/plain

Transfer-Encoding: chunked

Vary: Accept-Encoding

Content-Encoding: gzip

hola mundo!

/dev/stdin: gzip compressed data, max speed, from Unix

hola mundo!

sin gzip: 12 bytes

con gzip: 32 bytes

\> GET / HTTP/1.1

\> Accept-Encoding: deflate, gzip, br, zstd

\< HTTP/1.1 200 OK

\< Vary: Accept-Encoding

\< Content-Encoding: gzip

--compressed manda Accept-Encoding y descomprime solo: es la única opción que negocia la compresión (-i, -s, -v, -o, -w solo cambian lo que se muestra). Equivalente manual: -H 'Accept-Encoding: gzip'. Con 12 bytes el gzip pesa más (32) que el original: es normal en respuestas tan chicas; con un recurso grande baja (en la prueba, 6800 bytes a 116).

**Ojo:**

- nginx: \[emerg\] host not found in upstream "foo.leak.com.ar" → nginx NO arranca (lo tira ya en nginx -t): no resuelve el origen al cargar la config. Agregá su IP a /etc/hosts (paso 3) y recién ahí nginx -t y reiniciá.

- 502 Bad Gateway → resolvió el nombre pero NO alcanza foo.leak.com.ar (el origen está caído o inalcanzable). Verificá con curl http://foo.leak.com.ar/ que el origen responda.

- nginx: \[warn\] duplicate MIME type "text/html" → sacá text/html de gzip_types (ya se comprime por defecto). Es solo warning, no rompe.

- No aparece Content-Encoding: gzip → respuesta de menos de 20 bytes (hola mundo! tiene 11, o 12 con el salto de línea): falta gzip_min_length 1. Tipo distinto de text/html: falta gzip_types \*. Cliente HTTP/1.0: falta gzip_http_version 1.0. Pedido que llega con header Via: falta gzip_proxied any. O no mandaste Accept-Encoding: usá --compressed.

- Warning: Binary output can mess up your terminal → curl recibió gzip y no lo descomprime porque no pusiste --compressed. Es la prueba de que comprime; usá --compressed o \| gunzip.

- Sale Welcome to nginx! → el pedido cayó en el sitio default. Revisá server_name, el link en sites-enabled y que reiniciaste.

- curl: (6) Could not resolve host: foo.example.org → falta la línea en /etc/hosts (paso 2).

**Variantes:**

- Sin compresión: es Parcial 2022-2C · Ejercicio 1, en este mismo tipo (también tomado en Parcial 2020-1C · Ejercicio IV y Parcial o Recuperatorio 2021-1C · Ejercicio II; el apunte MasCompilado lo copia como ejercicio 5 de su bloque "2C 2020"). Borrá las seis líneas gzip\*, dejá solo el location. OJO: Recuperatorio 2020-1C · Ejercicio V NO es proxy, es el curl text/plain; está en Resueltos — curl, 'Negociación de contenido: text/plain en español'.

*Probado en Ubuntu 24.04 (lab), en un contenedor limpio y con foo.leak.com.ar simulado que devuelve hola mundo! (12 bytes con el salto de línea; sin el salto son 11 y viajan 31 comprimidos): Content-Encoding: gzip aparece solo si el cliente lo pide, también con cliente HTTP/1.0 y con pedido que trae Via. Sin probar: el systemctl stop del paso 1 y su salida inactive (dead).*

### nginx: redirección temporal a /api/, /api/health igual que api.protos:9090 (IP en base64 en un TXT), /api/foo sin requerir autenticación

**Tomado en:** Parcial 2016-1C (primer parcial) · Ejercicio 1.

> **Idea:** La idea es armar un nginx para parcial.protos que redirija / a /api/ con un 302 y, con proxy_pass a api.protos:9090, sirva /api/health y /api/foo, poniendo nginx el header Authorization Basic de USER y PASS en /api/foo. El truco es sacar la IP de api.protos del TXT de foo.leak.com.ar decodificando base64, y usar location = / para no redirigir todo.

**Buscar:** nginx, parcial.protos, redirección temporal, redireccion temporal, 302, return 302, location =, proxy_pass, proxy_set_header Authorization, proxy_set_header Host, api.protos, api.protos:9090, /api/health, /api/foo, /api/, 9090, misma salida, sin requerir autenticación, sin requerir autenticacion, le pide autenticarse, USER, PASS, Basic VVNFUjpQQVNT, base64, registro de tipo TXT, dig TXT, foo.leak.com.ar, dns0.zoneedit.com, cuya autoridad, User Agent, únicamente las opciones, unicamente las opciones, -v -i --head -X -s -0 --compressed -d --data-binary, sites-available, sites-enabled, /etc/hosts

**Enunciado:** (leído de una foto de la hoja) "Realizar los cambios necesarios en su computadora para que, utilizando como User Agent al comando curl, y empleando únicamente las opciones -v, -i, --head, -X, -s, -0, --compressed, -d y --data-binary, pueda verificar que: 1. Al acceder a http://parcial.protos/ ocurra una redirección temporal a http://parcial.protos/api/ 2. Cuando acceda a http://parcial.protos/api/health obtenga la misma salida que http://api.protos:9090/api/health. Puede encontrar la dirección IP en el registro de api.protos codificada con base64 en el registro de tipo TXT llamado foo.leak.com.ar (cuya autoridad es dns0.zoneedit.com). 3. Cuando acceda a http://parcial.protos/api/foo obtenga la misma salida que http://api.protos:9090/api/foo pero sin requerir autenticación. Si http://api.protos:9090/api/foo le pide autenticarse, puede utilizar como nombre de usuario a USER y como contraseña a PASS"

**Solución:**

> **1.** Para el punto 2 necesitás la IP de api.protos: está en base64 en el registro TXT de foo.leak.com.ar. Pedí el TXT:

dig +short TXT foo.leak.com.ar @dns0.zoneedit.com

"MTAuMS4zNC4xOTE="

Copiá el texto de adentro de las comillas (sin ellas) y decodificalo: en el comando de abajo, cambiá TEXTO_DEL_TXT por ese texto. Si queda TEXTO_DEL_TXT, sale basura y base64: invalid input. Anotá la IP que sale. Sale pegada al prompt, porque el texto codificado no trae salto de línea.

echo 'TEXTO_DEL_TXT' \| base64 -d

Con el texto del ejemplo, echo 'MTAuMS4zNC4xOTE=' \| base64 -d da:

10.1.34.191

> **2.** Nombres: api.protos a la IP que anotaste en el paso 1 y parcial.protos a tu máquina.

Hay que hacer sudo nano /etc/hosts y agregar al final lo siguiente, cambiando IP_API por la IP que anotaste:

IP_API api.protos

127.0.0.1 parcial.protos

Con la IP del ejemplo queda:

10.1.34.191 api.protos

127.0.0.1 parcial.protos

> **3.** Probá el origen directo y calculá el valor del Authorization: USER:PASS en base64 (el -n es para que echo no agregue un salto de línea, que cambiaría el resultado).

curl -i http://api.protos:9090/api/health

curl -i http://api.protos:9090/api/foo

curl -i http://USER:PASS@api.protos:9090/api/foo

echo -n 'USER:PASS' \| base64

HTTP/1.1 200 OK (health)

HTTP/1.1 401 Unauthorized (foo sin credenciales)

HTTP/1.1 200 OK (foo con USER:PASS en la URL)

VVNFUjpQQVNT

> **4.** Archivo /etc/nginx/sites-available/parcial.protos:

Hay que hacer sudo nano /etc/nginx/sites-available/parcial.protos, borrar lo que tenga y pegar lo siguiente (VVNFUjpQQVNT es lo que dio el base64 del paso 3; si te dio otra cosa, poné la tuya):

server {

listen 80;

server_name parcial.protos;

\# 1) redireccion TEMPORAL (302) solo de / a /api/

location = / {

return 302 http://parcial.protos/api/;

}

\# 2) todo /api/ sale del origen api.protos:9090 con la misma ruta

\# (el Host que viaja es api.protos:9090, igual que con curl directo)

location /api/ {

proxy_pass http://api.protos:9090;

}

\# 3) /api/foo: las credenciales las pone nginx, no el cliente

location = /api/foo {

proxy_set_header Authorization "Basic VVNFUjpQQVNT";

proxy_pass http://api.protos:9090;

}

}

> **5.** Habilitá el sitio y aplicá.

sudo ln -sf /etc/nginx/sites-available/parcial.protos /etc/nginx/sites-enabled/

sudo nginx -t && sudo systemctl restart nginx

\# forma vieja:

\# sudo service nginx restart

> **6.** Variante sin tocar /etc/hosts para api.protos: los dos location del paso 4 por IP, mandando el Host a mano.

Hay que hacer sudo nano /etc/nginx/sites-available/parcial.protos y cambiar los bloques location /api/ y location = /api/foo por lo siguiente, cambiando IP_API (en las dos líneas proxy_pass) por la IP que anotaste en el paso 1. Si queda IP_API, nginx -t falla con host not found in upstream "IP_API". Después validá y reiniciá como en el paso 5.

location /api/ {

proxy_set_header Host api.protos;

proxy_pass http://IP_API:9090;

}

location = /api/foo {

proxy_set_header Host api.protos;

proxy_set_header Authorization "Basic VVNFUjpQQVNT";

proxy_pass http://IP_API:9090;

}

**Demostrar:** solo con opciones permitidas.

curl -i http://parcial.protos/

curl -i http://parcial.protos/api/health

curl -i http://parcial.protos/api/foo

HTTP/1.1 302 Moved Temporarily

Location: http://parcial.protos/api/

HTTP/1.1 200 OK

(cuerpo de /api/health)

HTTP/1.1 200 OK

(cuerpo de /api/foo, sin 401)

Para mostrar "la misma salida", pedí lo mismo al origen y compará a ojo: curl -i http://api.protos:9090/api/health.

**Ojo:**

- El enunciado solo deja -v, -i, --head, -X, -s, -0, --compressed, -d y --data-binary. Nada de -H, -u, -L, -o ni -w en la demostración. Por eso parcial.protos va en /etc/hosts (no se puede -H 'Host: ...') y las credenciales del paso 3 van en la URL. -u USER:PASS o -H sirven solo para explorar el origen.

- location = / y no location /: location / redirige cualquier ruta que no tenga su propio location, no solo /. La resolución que circula tiene solo ese location /: ahí /api/ se redirige a sí misma y curl -L termina en curl: (47) Maximum (50) redirects followed.

- Redirección temporal es 302. 301 es permanente.

- 401 Unauthorized en /api/foo: base64 mal calculado. Con echo 'USER:PASS' \| base64 (sin -n) se cuela el salto de línea y da VVNFUjpQQVNTCg==. Usá echo -n. Adelante va la palabra Basic y un espacio.

- El enunciado dice usuario USER y contraseña PASS; la resolución que circula entró con USER:pass (clave en minúscula) y pegó un token inventado. Usá la combinación que te dé 200 en el paso 3 y calculá el base64 de esa: echo -n 'USER:pass' \| base64 da VVNFUjpwYXNz.

- 404 o la página de otro sitio en /api/health (variante por IP): falta proxy_set_header Host api.protos;. El origen es un virtual host.

- host not found in upstream "api.protos" al hacer nginx -t: falta api.protos en /etc/hosts o quedó escrito IP_API en lugar de la IP que anotaste (paso 2).

- base64: invalid input: quedó TEXTO_DEL_TXT sin cambiar, o copiaste también las comillas o un espacio. Pegá solo el texto de adentro. Si el TXT viene en varios pedazos entre comillas ("abc" "def"), juntalos sin comillas ni espacios (abcdef).

- 502 Bad Gateway: IP o puerto del origen mal, o no hay ruta hasta esa red.

- curl http://localhost/ puede salir por IPv6 (::1) y caer en el sitio default de nginx: probá siempre con http://parcial.protos/.

*Probado en Ubuntu 24.04 (lab), con api.protos:9090 y el registro TXT simulados. Sin probar: la lectura del TXT real (el dig contra el dns0.zoneedit.com real hoy devuelve un CNAME; ese TXT ya no existe).*

### nginx delante de un origin en 10.6.0.A: X-Pais según el dominio, www redirige permanente, assets cacheados una semana, gzip

**Tomado en:** Parcial 2013-2C · Ejercicio 1.

> **Idea:** La idea es poner un nginx en el puerto 80 delante del origin 10.6.0.A que mande el header X-Pais (AR o BR) según el dominio, redirija los www con 301 conservando el path, mande cualquier otro hostname al sitio de Argentina, marque solo /assets/ como cacheable una semana y comprima con gzip. La trampa es que X-Pais va con proxy_set_header, no con add_header.

**Buscar:** Demiloo, demiloo.com.ar, demiloo.com.br, www.demiloo.com.ar, www.demiloo.com.br, X-Pais, X-País, AR, BR, 10.6.0.A, servidor origen, origin server, origin, Tomcat, Jetty, default virtual host, REGLAS, \[REGLAS\], redirija permanentemente, redirigido permanentemente, redirección permanente, redireccion permanente, 301, otro hostname, http://127.0.0.1/, sitio de Argentina, assets, /assets/, /assets/images/foo.ico, header de caching, cacheados por cualquier caché, cacheados por cualquier cache, una semana, 604800, Cache-Control, max-age, public, no-store, comprima de forma transparente, compresión, compresion, gzip, Content-Encoding, Accept-Encoding, Vary, reverse proxy, proxy inverso, proxy_pass, proxy_set_header, upstream, map, default_server, server_name, return 301, sites-available, nginx

**Enunciado:** "Usted terminó de desarrollar un nuevo producto de internet llamado Demiloo y se encuentra listo para ser desplegado en los servidores de producción. Usted ya tiene registrado ante las entidades registrantes los siguientes dominios: demiloo.com.br (Brasil), demiloo.com.ar (Argentina). El esquema de despliegue está conformado por: Un servidor HTTP Nginx que recibe todos los requests de los usuarios (correrá en su computadora en el puerto TCP 80; enviará las pedidos a un servidor origen teniendo en cuentas algunas reglas que se detallan más abajo (ver \[REGLAS\])). Un servidor origen HTTP donde ya se encuentra desplegada la aplicación (podría ser por ejemplo un servidor Tomcat o Jetty): correrá en el host identificado con la dirección IP 10.6.0.A (A está definido en el pizarrón); Usted no podrá cambiar la configuración de este servidor ya que sólo tiene acceso via HTTP; Éste servidor tiene únicamente un default virtual host definido; La única aplicación se encuentra desplegada en / (Ej: Es accesible usando http://10.6.0.A/). Los assets de la aplicación (contenido estático como ser archivos javascripts, imágenes y archivos CSS) se encuentran todos bajo el recurso /assets/. Por ejemplo /assets/images/foo.ico. La aplicación no envía ningún header de caching. La aplicación tiene algún comportamiento diferente según se acceda al sitio de Argentina o Brasil. Para que la aplicación pueda proveer este comportamiento, ésta lee el valor del header X-Pais, donde los valores posibles son AR (para Argentina) y BR (para brasil). Por ejemplo X-Pais: AR, X-Pais: BR."

"\[REGLAS\] Instale un Nginx y realizar los cambios para que:

> **1.** El sitio debe ser accesible utilizando los dominios (recuerde el header X-Pais) demiloo.com.br (Ej: http://demiloo.com.br/users/) y demiloo.com.ar (Ej: http://demiloo.com.ar/users/).
>
> **2.** El sitio debe ser accesible utilizando los dominios www.demiloo.com.ar, www.demiloo.com.br, pero por razón de imágen no se desea que los usuarios se queden navegando en eso dominios, por lo que se desea que cuando se acceda usando estos dominios se redirija permanentemente a los dominios del punto anterior. Entonces, por ejemplo si un usuario ingresa a http://www.demiloo.com.ar/users/ será redirigido permanentemente a http://demiloo.com.ar/users/
>
> **3.** Cualquier request que llegue a su servidor HTTP que esté destinado a otro hostname (por ejemplo http://127.0.0.1/ tendrá que se redirigido al sitio de Argentina )
>
> **4.** Únicamente los assets de la aplicación sean cacheados por cualquier caché por el tiempo de una semana.
>
> **5.** Se desea que se comprima de forma transparente los recursos que sirve."

**Solución:**

> **1.** Instalá nginx y mirá que el origin responda. A es el número del pizarrón.

sudo apt update && sudo apt install -y nginx

curl -i http://10.6.0.A/

> **2.** Creá el sitio con un solo pegado. Ruta: /etc/nginx/sites-available/demiloo.

Hay que hacer sudo nano /etc/nginx/sites-available/demiloo, borrar lo que tenga y pegar lo siguiente, cambiando la A de 10.6.0.A (en el upstream, arriba de todo) por el número del pizarrón. Por ejemplo, con 50 la línea queda server 10.6.0.50:80;. Si te olvidás, nginx -t falla con host not found in upstream "10.6.0.A:80".

\# Origin: la A se cambia por el numero del pizarron (unico lugar)

upstream origen {

server 10.6.0.A:80;

}

\# Regla 1: el pais sale del dominio pedido

map \$host \$pais {

default AR;

demiloo.com.br BR;

}

\# Regla 3: cualquier otro hostname (127.0.0.1, la IP, lo que sea) -\> sitio de Argentina

server {

listen 80 default_server;

server_name \_;

return 302 http://demiloo.com.ar\$request_uri;

}

\# Regla 2: www -\> sin www, redireccion permanente (301) conservando el path

server {

listen 80;

server_name www.demiloo.com.ar;

return 301 http://demiloo.com.ar\$request_uri;

}

server {

listen 80;

server_name www.demiloo.com.br;

return 301 http://demiloo.com.br\$request_uri;

}

\# Regla 1: los dos dominios van al origin con el header X-Pais

server {

listen 80;

server_name demiloo.com.ar demiloo.com.br;

\# Regla 5: compresion transparente (solo si el cliente manda Accept-Encoding: gzip)

gzip on;

gzip_types \*;

gzip_min_length 1;

gzip_proxied any;

gzip_http_version 1.0;

gzip_vary on;

\# el origin recibe el nombre que pidio el usuario (ver Ojo)

proxy_set_header Host \$host;

proxy_set_header X-Pais \$pais;

location / {

proxy_pass http://origen;

add_header Cache-Control "no-store";

}

\# Regla 4: solo los assets se cachean, una semana = 604800 segundos

location /assets/ {

proxy_pass http://origen;

add_header Cache-Control "public, max-age=604800";

}

}

> **3.** Activá el sitio, sacá el default y reiniciá.

sudo ln -sf /etc/nginx/sites-available/demiloo /etc/nginx/sites-enabled/demiloo

sudo rm -f /etc/nginx/sites-enabled/default

sudo nginx -t && sudo systemctl restart nginx

\# forma vieja: sudo service nginx restart

> **4.** Para probar con los nombres (curl o browser) sin depender de un DNS:

Hay que hacer sudo nano /etc/hosts y agregar al final lo siguiente:

127.0.0.1 demiloo.com.ar demiloo.com.br www.demiloo.com.ar www.demiloo.com.br

**Demostrar:** un curl por regla. curl -I muestra solo los headers de la respuesta; en cada uno mirá la primera línea y el header que dice el comentario.

\# Regla 1: los dos dominios responden con la app del origin

curl -i http://demiloo.com.ar/users/

curl -i http://demiloo.com.br/users/

\# Regla 2: 301 y Location

curl -I http://www.demiloo.com.ar/users/

curl -I http://www.demiloo.com.br/users/

\# Regla 3: 302 y Location

curl -I http://127.0.0.1/

\# Regla 4: Cache-Control (assets una semana, el resto sin cache)

curl -I http://demiloo.com.ar/assets/images/foo.ico

curl -I http://demiloo.com.ar/users/

\# Regla 5: Content-Encoding: gzip, solo si el cliente lo pide

curl -I -H 'Accept-Encoding: gzip' http://demiloo.com.br/assets/app.css

curl -s --compressed -o /dev/null -w 'bajados %{size_download} bytes\n' http://demiloo.com.br/assets/app.css

curl -s -o /dev/null -w 'bajados %{size_download} bytes\n' http://demiloo.com.br/assets/app.css

Salida esperada desde la regla 2, en el mismo orden y recortada a las líneas que importan (curl -I muestra además Server, Date, Content-Type y otros):

HTTP/1.1 301 Moved Permanently

Location: http://demiloo.com.ar/users/

HTTP/1.1 301 Moved Permanently

Location: http://demiloo.com.br/users/

HTTP/1.1 302 Moved Temporarily

Location: http://demiloo.com.ar/

HTTP/1.1 200 OK

Cache-Control: public, max-age=604800

HTTP/1.1 200 OK

Cache-Control: no-store

HTTP/1.1 200 OK

Vary: Accept-Encoding

Content-Encoding: gzip

bajados 117 bytes

bajados 6400 bytes

Los tamaños (117 y 6400) son de la prueba: lo que importa es que el comprimido sea menor. Sin tocar /etc/hosts también se puede probar: curl -i -H 'Host: demiloo.com.br' http://127.0.0.1/users/.

Para mostrar el X-Pais que nginx le manda al origin (regla 1): dejá esto corriendo en otra terminal, con tu número en lugar de 50, y repetí los dos curl de la regla 1. El grep deja solo las líneas Host: y X-Pais:, y el -l hace que tcpdump las vaya mostrando al momento. Se corta con Ctrl+C.

sudo tcpdump -l -A -i any host 10.6.0.50 and tcp port 80 \| grep -iE 'host:\|x-pais'

Host: demiloo.com.ar

X-Pais: AR

Host: demiloo.com.br

X-Pais: BR

**Ojo:**

- El header hacia el origin se pone con proxy_set_header X-Pais .... add_header X-Pais ... (apunte de alumno) lo agrega a la respuesta que recibe el cliente y la aplicación nunca lo ve.

- No va if (\$http_x_pais = "AR") (otro apunte): el usuario no manda X-Pais; lo decide nginx según el dominio. Los valores son AR y BR, no argentina ni brasil.

- return 301 http://demiloo.com.ar; sin \$request_uri pierde el path: /users/ terminaría en /. El enunciado pide www.demiloo.com.ar/users/ -\> demiloo.com.ar/users/.

- Regla 3: dice "redirigido", no "permanentemente", por eso va 302. Si lo querés permanente como en los apuntes, cambiá 302 por 301 en el primer server. Si el corrector lo entiende como "atender como Argentina sin redirigir": borrá ese primer server y poné listen 80 default_server; en el último (el map ya da AR por defecto).

- Regla 4: "únicamente" los assets. Como la aplicación no manda headers de caché, al resto se le pone no-store. expires 1w; solo agrega max-age y Expires, sin public. proxy_set_header Cache-Control ... (apunte) está mal: eso viaja hacia el origin, no hacia el cliente. No hace falta proxy_cache: se piden headers para las cachés, no que nginx guarde nada.

- Regla 5: nginx comprime solo si el pedido trae Accept-Encoding: gzip; con curl hay que agregar --compressed o -H 'Accept-Encoding: gzip'. Cada línea del bloque tapa un caso en el que no saldría Content-Encoding: gzip: sin gzip_types \* solo comprime text/html (un .ico o una fuente quedan sin comprimir); sin gzip_min_length 1 no comprime respuestas de menos de 20 bytes; sin gzip_http_version 1.0 no comprime pedidos HTTP/1.0 (curl -0); gzip_proxied any hace falta si el pedido llega a través de un proxy (trae header Via).

- Acá sí va proxy_set_header Host \$host;: el origin tiene un único virtual host por defecto (atiende cualquier Host), así que no se rompe nada y la aplicación recibe el dominio que pidió el usuario (demiloo.com.ar o demiloo.com.br). Sin esa línea le llega Host: origen, el nombre del upstream. En los proxies a foo.leak.com.ar de este mismo tipo (por ejemplo Parcial 2022-2C · Ejercicio 1) es al revés: ese servidor elige el sitio por el Host y con \$host recibe un nombre que no conoce; ahí no va.

- "gzip" directive is duplicate in /etc/nginx/sites-enabled/demiloo: pusiste gzip on; fuera de un server. En Ubuntu ya está en /etc/nginx/nginx.conf a nivel http; adentro del server sí se puede repetir.

- duplicate MIME type "text/html" (es un aviso, nginx arranca igual): cambiaste el \* de gzip_types por una lista que incluye text/html. Sacalo de la lista, siempre se comprime.

- a duplicate default server for 0.0.0.0:80: quedó activo /etc/nginx/sites-enabled/default. Borralo (paso 3).

- host not found in upstream "10.6.0.A:80": no reemplazaste la A (paso 2).

- 502 Bad Gateway: nginx no llega al origin. Probá curl -i http://10.6.0.A/ y mirá sudo tail /var/log/nginx/error.log.

- nginx.service is not active, cannot reload.: usá sudo systemctl restart nginx.

*Probado en Ubuntu 24.04 (lab). Los curl -I de Demostrar se probaron fuera del lab con nginx 1.24 y curl 8.5 (con -I, que manda HEAD, también sale Content-Encoding: gzip), y la config con la A sin cambiar da host not found in upstream "10.6.0.A:80" en nginx -t. Sin probar: el apt install (el lab ya trae nginx y no permite instalar) y el origin real 10.6.0.A (se usó uno simulado en 10.6.0.50 que devuelve el Host y el X-Pais recibidos).*

### Bajar /capture.pcap.gpg del virtual host www.parcialpdc.com usando la URL http://www.soyalumnopdc.com/capture.pcap.gpg

**Tomado en:** Parcial 2012-1C · Ejercicio 1.

> **Idea:** La idea es que http://www.soyalumnopdc.com/capture.pcap.gpg baje desde un browser el recurso del virtual host www.parcialpdc.com de labu180.unix.lab, apuntando www.soyalumnopdc.com en /etc/hosts a un nginx propio. El truco es que labu180 elige el virtual host por el Host: apuntar el nombre directo a labu180 da 404, así que nginx hace proxy_pass mandando Host: www.parcialpdc.com.

**Buscar:** labu180.unix.lab, labu180, virtual host, virtual hosts, virtualhost, www.parcialpdc.com, parcialpdc, www.soyalumnopdc.com, soyalumnopdc, capture.pcap.gpg, ingresando la siguiente URL en un browser, descargar el recurso, header Host, proxy_pass, proxy_set_header Host, /etc/hosts, server_name, nginx, reverse proxy, proxy inverso

**Enunciado:** "Se sabe que existe en la red del laboratorio el host labu180.unix.lab que ejecuta un servidor HTTP el cual aloja varios virtual hosts. Dentro del virtual host www.parcialpdc.com se encuentra el recurso /capture.pcap.gpg. Se quiere que desde su host ingresando la siguiente URL en un browser http://www.soyalumnopdc.com/capture.pcap.gpg descargar el recurso mencionado anteriormente. Realizar los cambios necesarios para que esto suceda"

**Solución:**

> **1.** Idea: el servidor elige el virtual host por el header Host. El browser va a mandar Host: www.soyalumnopdc.com, que labu180 no conoce. Entonces el nombre www.soyalumnopdc.com tiene que apuntar a un nginx tuyo que reenvíe el pedido a labu180 cambiando el Host por www.parcialpdc.com.
>
> **2.** Comprobá que el recurso existe pidiéndolo con el Host correcto.

getent hosts labu180.unix.lab

curl -I -H 'Host: www.parcialpdc.com' http://labu180.unix.lab/capture.pcap.gpg

> **3.** Creá el sitio. Ruta: /etc/nginx/sites-available/www.soyalumnopdc.com.

Hay que hacer sudo nano /etc/nginx/sites-available/www.soyalumnopdc.com, borrar lo que tenga y pegar lo siguiente:

server {

listen 80;

server_name www.soyalumnopdc.com;

location / {

proxy_pass http://labu180.unix.lab;

proxy_set_header Host www.parcialpdc.com;

}

}

sudo ln -sf /etc/nginx/sites-available/www.soyalumnopdc.com /etc/nginx/sites-enabled/www.soyalumnopdc.com

sudo nginx -t && sudo systemctl restart nginx

\# forma vieja: sudo service nginx restart

> **4.** Hacé que el nombre apunte a tu propia máquina.

Hay que hacer sudo nano /etc/hosts y agregar al final lo siguiente:

127.0.0.1 www.soyalumnopdc.com

> **5.** Abrí http://www.soyalumnopdc.com/capture.pcap.gpg en el browser, o bajalo por consola:

curl -O http://www.soyalumnopdc.com/capture.pcap.gpg

\# equivalente: wget http://www.soyalumnopdc.com/capture.pcap.gpg

**Demostrar:**

curl -sI http://www.soyalumnopdc.com/capture.pcap.gpg

ls -l capture.pcap.gpg

HTTP/1.1 200 OK

Server: nginx/1.24.0 (Ubuntu)

Content-Length: 4360

**Ojo:**

- Solo /etc/hosts apuntando www.soyalumnopdc.com a la IP de labu180 no alcanza: llega Host: www.soyalumnopdc.com, labu180 atiende con su virtual host por defecto y devuelve 404 (probado).

- host not found in upstream "labu180.unix.lab" en nginx -t: tu máquina no resuelve ese nombre. Poné la IP en proxy_pass http://IP_LABU180; o agregá IP_LABU180 labu180.unix.lab en /etc/hosts.

- Si el browser tiene un proxy configurado, el proxy resuelve www.soyalumnopdc.com por su cuenta y no llega a tu nginx: agregá el nombre en las excepciones ("Sin proxy para", en inglés "No proxy for") o desactivá el proxy.

- Si solo necesitás el archivo para seguir con Parcial 2012-1C · Ejercicio 2, que lo desencripta con gpg (Resueltos — Mail, 'MX y SPF de un dominio'), sin cumplir lo de la URL: curl -H 'Host: www.parcialpdc.com' -o capture.pcap.gpg http://labu180.unix.lab/capture.pcap.gpg.

*Probado en Ubuntu 24.04 (lab). Sin probar: el labu180 real y el browser (se usó un nginx con tres virtual hosts en otro namespace y se bajó con curl y wget).*

## Virtual hosts: cada dominio con su contenido

Un nginx que devuelve un texto distinto según el dominio pedido y "What?" para cualquier otro. Se tomó dos veces, con distinta redacción del enunciado. Está resuelto con archivos y, como variante, con return 200.

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| Virtual hosts: itba.example.org, www.example.org y catch-all "What?" | itba.example.org devuelve "Ad Lucem Serenitate Tendo", www.example.org devuelve "El RFC 2606 le da un estado especial a example.org." y cualquier otro dominio "What?"; demostrar con curl | Resuelto con archivos (root e index.html) y, como variante, con return 200 y default_type text/plain, sin archivos. Tiene que andar también en Chrome o Firefox. El video arranca con el servidor apagado. También tomado en Parcial 2015-2C · Ejercicio 1 (otra redacción, sin lo de Chrome o Firefox ni lo del video) |

### Virtual hosts: itba.example.org, www.example.org y catch-all "What?"

**Tomado en:** Parcial o Recuperatorio 2021-1C · Ejercicio I.

**También tomado en:** Parcial 2015-2C · Ejercicio 1 (otra redacción del enunciado: no pide que ande en Chrome o Firefox ni que el video arranque con el servidor apagado).

> **Idea:** La idea es armar un nginx con un server por dominio: itba.example.org devuelve "Ad Lucem Serenitate Tendo", www.example.org "El RFC 2606 le da un estado especial a example.org." y cualquier otro dominio "What?". El truco es que el catch-all lleve default_server, que se borre el sitio default de Ubuntu y que los nombres vayan en /etc/hosts; el video arranca con nginx apagado.

**Buscar:** itba.example.org, www.example.org, Ad Lucem Serenitate Tendo, RFC 2606, What?, virtual host, name based, server_name, default_server, hosting, cualquier otro dominio, Configurar un servidor HTTP para que cumpla con las siguientes condiciones, Provea hosting para el sitio, Cuando un agente pida el recurso /, se obtendrá la representación, se obtendra la representacion, El RFC 2606 le da un estado especial a example.org, cualquier otro dominio que apunte al servidor, dominioque, user-agent al que se le ingrese URLs, curl http://www.example.org/, Google Chrome o Mozilla Firefox, deberá demostrar la correcta configuración utilizando curl, debera demostrar la correcta configuracion utilizando curl, el servidor no está corriendo, el servidor no esta corriendo, nginx, virtual hosts, Proveerá hosting, Proveera hosting, recurso / de, user agent al que se le ingrese URLs, default_type, text/plain, return 200, /etc/hosts, sites-available, sites-enabled, nginx apagado, systemctl stop, inactive (dead), apt install nginx

**Enunciado:** "Configurar un servidor HTTP para que cumpla con las siguientes condiciones: a. Provea hosting para el sitio itba.example.org y para el sitio www.example.org. b. Cuando un agente pida el recurso / del host itba.example.org se obtendrá la representación **Ad Lucem Serenitate Tendo** c. Cuando un agente pida el recurso / del host www.example.org se obtendrá la representación **El RFC 2606 le da un estado especial a example.org.** d. Cuando un agente pida un recurso / en cualquier otro dominioque apunte al servidor se obtendrá la representación: **What?** Realizar los cambios necesarios para que las pruebas puedan ser realizadas desde un user-agent al que se le ingrese URLs (ej curl http://www.example.org/). Tiene que funcionar por ejemplo desde Google Chrome o Mozilla Firefox, pero deberá demostrar la correcta configuración utilizando curl Video. Si para la resolución del video tuviese que configurar un servidor inicie el video mostrando que el servidor no está corriendo, para luego levantarlo y realizar la demostración." ("dominioque", sin espacio, está así en el original.)

**Enunciado en Parcial 2015-2C · Ejercicio 1:** "Configurar un servidor HTTP para que cumpla con las siguientes condiciones a. Proveerá hosting para el sitio itba.example.org y para www.example.org. b. Cuando un agente pida el recurso / de itba.example.org se obtendrá la representación Ad Lucem Serenitate Tendo c. Cuando un agente pida el recurso / de www.example.org se obtendrá la representación El RFC 2606 le da un estado especial a example.org. d. Cuando un agente pida un recurso en cualquier otro dominio se obtendrá la representación: What? Realizar los cambios necesarios para que las pruebas puedan ser realizadas desde un user agent al que se le ingrese URLs (http://www.example.org/). Deberá demostrar la correcta configuración utilizando curl."

**Solución:**

> **0.** El video de 2021-1C arranca con nginx apagado. Antes de grabar, instalalo si falta (sin él no existe /etc/nginx/sites-available) y paralo, porque el apt install lo deja corriendo:

sudo apt install -y nginx

sudo systemctl stop nginx

Ya grabando, mostralo apagado: tiene que decir Active: inactive (dead). Lo arranca el restart del paso 4.

sudo systemctl status nginx

> **1.** Que los dos nombres resuelvan a tu máquina (es obligatorio: el enunciado pide probar ingresando URLs, no con -H 'Host: ...'):

Hay que hacer sudo nano /etc/hosts y agregar al final lo siguiente:

127.0.0.1 itba.example.org

127.0.0.1 www.example.org

> **2.** Contenido de cada sitio:

sudo mkdir -p /var/www/itba /var/www/www /var/www/otros

Hay que hacer sudo nano -L /var/www/itba/index.html, borrar lo que tenga y pegar lo siguiente (-L hace que nano no agregue un salto de línea al final):

Ad Lucem Serenitate Tendo

Hay que hacer sudo nano -L /var/www/www/index.html, borrar lo que tenga y pegar lo siguiente (-L hace que nano no agregue un salto de línea al final):

El RFC 2606 le da un estado especial a example.org.

Hay que hacer sudo nano -L /var/www/otros/index.html, borrar lo que tenga y pegar lo siguiente (-L hace que nano no agregue un salto de línea al final):

What?

> **3.** Config con tres server: el catch-all es default_server. Archivo /etc/nginx/sites-available/example.org:

Hay que hacer sudo nano /etc/nginx/sites-available/example.org, borrar lo que tenga y pegar lo siguiente:

server {

listen 80 default_server; \# atiende cualquier Host que no matchee los de abajo

server_name \_;

root /var/www/otros; \# -\> What?

location / {

try_files /index.html =404; \# cualquier ruta devuelve What?

}

}

server {

listen 80;

server_name itba.example.org;

root /var/www/itba; \# -\> Ad Lucem Serenitate Tendo

}

server {

listen 80;

server_name www.example.org;

root /var/www/www; \# -\> El RFC 2606...

}

> **4.** Activá el sitio, sacá el default de Ubuntu (si queda hay dos default_server y nginx -t falla), validá y reiniciá:

sudo ln -sf /etc/nginx/sites-available/example.org /etc/nginx/sites-enabled/

sudo rm -f /etc/nginx/sites-enabled/default

sudo nginx -t && sudo systemctl restart nginx

\# forma vieja: sudo service nginx restart

**Demostrar:**

curl http://itba.example.org/ \# Ad Lucem Serenitate Tendo

curl http://www.example.org/ \# El RFC 2606 le da un estado especial a example.org.

curl -H 'Host: cualquier.cosa' http://127.0.0.1/ \# What?

curl -H 'Host: x.y' http://127.0.0.1/cualquier/ruta \# What? (cualquier recurso de otro dominio)

Las dos primeras URLs también se pueden pegar en Firefox o Chrome de la misma VM (sin probar: el lab no tiene entorno gráfico). Los dos últimos curl simulan cualquier otro dominio que apunte al servidor; curl http://127.0.0.1/ da lo mismo que el tercero.

**Ojo:**

- a duplicate default server for 0.0.0.0:80 en nginx -t → sigue habilitado /etc/nginx/sites-enabled/default. Borrá ese link (paso 4).

- Un dominio inventado devuelve el contenido de itba en vez de "What?" → te falta default_server, o el server catch-all no es el que matchea. Con default_server nginx lo usa para todo Host no listado.

- Otro dominio devuelve 404 Not Found en una ruta que no es / → falta el location / { try_files /index.html =404; } del server default.

- curl: (6) Could not resolve host → faltan las líneas de /etc/hosts.

- Sale Welcome to nginx! → sigue respondiendo el default de Ubuntu. No borraste el link (paso 4), o nginx -t falló y no se aplicó nada.

- Todos los dominios devuelven What? → los server_name no coinciden con la URL o el archivo no está linkeado en sites-enabled.

- Con la variante return 200, Firefox o Chrome bajan un archivo en vez de mostrar el texto → falta default_type text/plain. Sin él, return 200 "texto" sale como Content-Type: application/octet-stream. curl no lo nota.

**Variantes:**

- Si piden además que index index.html; o try_files \$uri \$uri/ =404;, agregalos dentro de cada location /.

- Sin archivos, con return 200 (así se resolvió Parcial 2015-2C · Ejercicio 1): reemplaza los pasos 2 y 3; el 0, el 1 y el 4 quedan igual. Tres server en un archivo: dos por nombre y uno default_server para el resto. El default_type text/plain hace que el browser muestre el texto en vez de bajarlo como archivo. Si piden solo el recurso / exacto y el resto 404, en esta config va location = / { ... } en vez de location / { ... }. La config:

Hay que hacer sudo nano /etc/nginx/sites-available/example.org, borrar lo que tenga y pegar lo siguiente:

server {

listen 80;

server_name itba.example.org;

default_type text/plain;

charset utf-8;

location / {

return 200 "Ad Lucem Serenitate Tendo\n";

}

}

server {

listen 80;

server_name www.example.org;

default_type text/plain;

charset utf-8;

location / {

return 200 "El RFC 2606 le da un estado especial a example.org.\n";

}

}

server {

listen 80 default_server;

listen \[::\]:80 default_server;

server_name \_;

default_type text/plain;

charset utf-8;

location / {

return 200 "What?\n";

}

}

La resolución que circula de Parcial 2015-2C usa un solo server con if (\$http_host = ...) y toca el default para el "What?": funciona, pero es más frágil. Un server por nombre es lo directo.

*Probado en Ubuntu 24.04 (lab): los cuatro curls devuelven las representaciones exactas; también la variante con return 200. Sin probar: Firefox y Chrome (el lab no tiene entorno gráfico; en la variante se comprobó el Content-Type con curl -i), y el paso 0 (systemctl stop y su salida inactive (dead)).*

# Resueltos — curl (cliente HTTP)

## Negociación de contenido: text/plain en español

Todos piden lo mismo: traer con curl la representación text/plain en español (es) de un recurso /foo/, que sale con -H 'Accept: text/plain' y -H 'Accept-Language: es'. De un parcial a otro cambia el host, si hay que salir por un proxy SOCKS5 y si el enunciado trae un request capturado con un Authorization: Basic que hay que reusar.

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| curl por proxy SOCKS5: text/plain en español de ejercicio2.sebikul.com:8080/foo/ | http://ejercicio2.sebikul.com:8080/foo/ en text/plain y español | El host solo se alcanza por el proxy socks5h://proxy.sebikul.com:1080. Usuario y clave (usuario:password) salen de decodificar el Authorization: Basic del request que da el enunciado. Hay puntaje parcial por reproducir ese request (GET /abc/ en HTTP/1.0) por el proxy. |
| curl text/plain en español reusando un request capturado (Authorization Basic) | http://ejercicio2.proto.leak.com.ar/foo/ en text/plain y español | Sin proxy. Se reusa tal cual el Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ= (username:password) del request capturado que trae el enunciado. También tomado en Recuperatorio 2020-1C · Ejercicio V (host ejercicio4.proto.leak.com.ar, sin request capturado ni Authorization: el comando está en Variantes). |

### curl por proxy SOCKS5: text/plain en español de ejercicio2.sebikul.com:8080/foo/

**Tomado en:** Parcial 2024-2C · Ejercicio 2.

> **Idea:** La idea es traer con curl la versión text/plain en español de http://ejercicio2.sebikul.com:8080/foo/, saliendo por el proxy SOCKS5 con -x y pidiéndola con los headers Accept y Accept-Language. El truco es usar socks5h para que el proxy resuelva el nombre, y decodificar el Authorization Basic del request dado para mandar usuario:password.

**Buscar:** curl, proxy SOCKS, SOCKS5, SOCKSv5, SOCKv5, socks5h, proxy.sebikul.com, 1080, ejercicio2.sebikul.com, 8080, /foo/, ejercicio2.proto.leak.com.ar, representación textual, representacion textual, media type, text/plain, lenguaje español, lenguaje espanol, es, Accept, Accept-Language, negociación de contenido, negociacion de contenido, Authorization, Basic, dXN1YXJpbzpwYXNzd29yZA==, base64 -d, usuario:password, la red no rutea paquetes, request y una respuesta, PCAP, reproducir el request, Accept-Encoding, Pragma, keep-alive, HTTP/1.0, -x, --socks5-hostname, Ejercicio II, proxy HTTP, -x http://, 407, Proxy Authentication Required, Proxy-Authorization, -U, --proxy-user, http_proxy, HTTP_PROXY, ALL_PROXY, Empty reply from server, apt install, Command 'curl' not found, command not found, Connected to

**Enunciado:** Obtenga la representación textual (media type: text/plain) en el lenguaje español (es) del recurso http://ejercicio2.sebikul.com:8080/foo/ usando curl. Dato: la red no rutea paquetes hacia el host ejercicio2.sebikul.com, pero este es accesible mediante un proxy socks5h://proxy.sebikul.com:1080. Dato: A su poder ha llegado un request y una respuesta sobre el mismo servidor con información que le podría ser útil. Resolución parcial: 1.0 puntos si no llega a la solución pero logra reproducir el request HTTP que se observa en la trama del archivo PCAP utilizando el proxy socks. Si no sabe como hacer un request HTTP utilizando un proxy SOCKv5 puede obtener ayuda sobre como hacerlo, pero con una penalización de 0.5 puntos. El request y la respuesta que da el enunciado:

\> GET /abc/ HTTP/1.0

\> Host: ejercicio2.proto.leak.com.ar

\> Accept-Encoding: gzip, deflate, br

\> Accept-Language: en,it;q=0.9,es-AR;q=0.8,es;q=0.7

\> Authorization: Basic dXN1YXJpbzpwYXNzd29yZA==

\> Connection: keep-alive

\> Pragma: no-cache

\< HTTP/1.1 200 OK

\< Server: nginx/1.1.19

\< Date: Wed, 19 May 2021 02:18:51 GMT

\< Content-Length: 1084

\< Last-Modified: Sun, 22 Apr 2018 23:52:26 GMT

\<

\< esta no es la solucion (estaba en el enunciado como ejemplo)

**Solución:**

> **0.** Instalá curl si falta (en Debian puede no venir; si ya está, no cambia nada).

sudo apt update && sudo apt install -y curl

> **1.** Sacá el usuario y la clave del header Authorization del dato (Basic es base64 de usuario:clave).

echo 'dXN1YXJpbzpwYXNzd29yZA==' \| base64 -d; echo

usuario:password

> **2.** El pedido final: por el proxy SOCKS5 (socks5h hace que el nombre lo resuelva el proxy), con esas credenciales, pidiendo texto plano en español (-u usuario:password manda el mismo header que -H 'Authorization: Basic dXN1YXJpbzpwYXNzd29yZA=='; cualquiera de los dos sirve).

curl -v -x socks5h://proxy.sebikul.com:1080 \\

-u usuario:password \\

-H 'Accept: text/plain' \\

-H 'Accept-Language: es' \\

http://ejercicio2.sebikul.com:8080/foo/

> **3.** Para el puntaje parcial (1.0): reproducir tal cual el request del dato por el proxy. -0 es HTTP/1.0 y los -H 'Nombre:' vacíos sacan los headers que curl agrega solo.

curl -v -0 -x socks5h://proxy.sebikul.com:1080 \\

-H 'Host: ejercicio2.proto.leak.com.ar' \\

-H 'User-Agent:' -H 'Accept:' \\

-H 'Accept-Encoding: gzip, deflate, br' \\

-H 'Accept-Language: en,it;q=0.9,es-AR;q=0.8,es;q=0.7' \\

-H 'Authorization: Basic dXN1YXJpbzpwYXNzd29yZA==' \\

-H 'Connection: keep-alive' \\

-H 'Pragma: no-cache' \\

http://ejercicio2.sebikul.com:8080/abc/

**Demostrar:** la salida de -v del paso 2. Tienen que verse el uso del proxy, los headers enviados y en la respuesta 200, Content-Type: text/plain y, si el servidor lo informa, Content-Language: es.

\* SOCKS5 connect to ejercicio2.sebikul.com:8080 (remotely resolved)

\* SOCKS5 request granted.

\> GET /foo/ HTTP/1.1

\> Host: ejercicio2.sebikul.com:8080

\> Authorization: Basic dXN1YXJpbzpwYXNzd29yZA==

\> User-Agent: curl/8.5.0

\> Accept: text/plain

\> Accept-Language: es

\>

\< HTTP/1.1 200 OK

\< Content-Type: text/plain

\< Content-Language: es

Para el paso 3, lo que curl manda queda idéntico al dato:

\> GET /abc/ HTTP/1.0

\> Host: ejercicio2.proto.leak.com.ar

\> Accept-Encoding: gzip, deflate, br

\> Accept-Language: en,it;q=0.9,es-AR;q=0.8,es;q=0.7

\> Authorization: Basic dXN1YXJpbzpwYXNzd29yZA==

\> Connection: keep-alive

\> Pragma: no-cache

**Ojo:**

- socks5h:// y no socks5://: con socks5:// curl resuelve el nombre en tu máquina y falla con curl: (97) Could not resolve host: ejercicio2.sebikul.com. Forma equivalente: --socks5-hostname proxy.sebikul.com:1080 en vez de -x socks5h://proxy.sebikul.com:1080.

- Sin proxy: curl: (6) Could not resolve host o se queda colgado. Es lo esperado ("la red no rutea").

- curl: (7) Failed to connect to proxy.sebikul.com port 1080: el proxy no responde o el puerto está mal. El apunte del alumno decía que no le resolvía el proxy: ese host solo existe durante el parcial.

- Si el proxy es HTTP y no SOCKS, es el paso 2 cambiando solo el -x: curl -v -x http://PROXY:PUERTO -u usuario:password -H 'Accept: text/plain' -H 'Accept-Language: es' URL. En -v se ve la URL completa en la primera línea: \> GET http://ejercicio2.sebikul.com:8080/foo/ HTTP/1.1. Si responde 407 Proxy Authentication Required, sumá -U USUARIO:CLAVE o usá -x http://USUARIO:CLAVE@PROXY:PUERTO (-U es para el proxy y manda Proxy-Authorization; -u es para el servidor y manda Authorization; si piden los dos, van los dos).

- Proxy sin -x (si no se permiten otras opciones): para un proxy HTTP, export http_proxy=http://PROXY:PUERTO (con 407, http://USUARIO:CLAVE@PROXY:PUERTO), en minúscula, porque curl ignora HTTP_PROXY; para el SOCKS de este ejercicio, export ALL_PROXY=socks5h://proxy.sebikul.com:1080. Al terminar, unset http_proxy (o unset ALL_PROXY): si no, todos los curl que sigan en esa terminal, incluso a localhost, salen por el proxy.

- Para saber si el puerto es SOCKS o HTTP: curl: (52) Empty reply from server con -x http:// (o con -x PROXY:PUERTO sin esquema, que curl toma como http://) es que el puerto es SOCKS. Si con socks5h:// el -v muestra Connected to y después se queda colgado, el puerto es HTTP. Si se cuelga en Trying IP:PUERTO... sin llegar a Connected to, el proxy no responde, sea SOCKS o HTTP.

- El dato sirve por el header Authorization. Sin -u usuario:password lo esperable es un 401 Unauthorized.

- Si responde 406 Not Acceptable, revisá cómo escribiste Accept y Accept-Language. Si devuelve otro idioma, probá -H 'Accept-Language: es, \*;q=0.1' o es-AR, es;q=0.9.

- El apunte del alumno no mandaba las credenciales (solo -x, Accept y Accept-Language).

- El Host del dato es ejercicio2.proto.leak.com.ar y el del enunciado es ejercicio2.sebikul.com: el parcial se recicló de otro dominio (ejercicio2.proto.leak.com.ar es el host del Parcial 2021-1C · Ejercicio 3, que está en este mismo tipo: Resueltos — curl, 'Negociación de contenido: text/plain en español'). Si con el host de la URL devuelve 404 u otro sitio, agregá -H 'Host: ejercicio2.proto.leak.com.ar' al paso 2.

- En la foto del enunciado el cuarto caracter del base64 se confunde entre 1 y l. Con 1 decodifica usuario:password; con l decodifica useario:password, que no tiene sentido.

- Si la respuesta sale como basura binaria en el paso 3, es porque pediste gzip: agregá --compressed o -o salida.bin.

- Si entregan un archivo PCAP en vez del texto: tshark -r ARCHIVO.pcap -q -z follow,http,ascii,0 muestra el request y la respuesta, y tshark -r ARCHIVO.pcap -Y http.authorization -T fields -e http.authbasic imprime directamente usuario:password. En Wireshark: clic derecho sobre el paquete, Follow, HTTP Stream.

- Sin curl, con netcat por el mismo proxy: nc -C -X 5 -x proxy.sebikul.com:1080 ejercicio2.sebikul.com 8080 y escribir el pedido a mano, igual que en 'Seguir el recurso /secreto/143': GET /foo/ HTTP/1.1, Host: ejercicio2.sebikul.com:8080, Authorization: Basic dXN1YXJpbzpwYXNzd29yZA==, Accept: text/plain, Accept-Language: es, Connection: close y una línea vacía (Enter dos veces al final). -X 5 hace que el nombre lo resuelva el proxy (como socks5h) y -C hace que cada Enter mande \r\n.

- Se probó contra un proxy SOCKS5, un proxy HTTP con usuario y un servidor propios que imitan a los del parcial. El comportamiento exacto del servidor real no se pudo comprobar.

*Probado en Ubuntu 24.04 (lab). Sin probar: el apt install del paso 0.*

### curl text/plain en español reusando un request capturado (Authorization Basic)

**Tomado en:** Parcial 2021-1C · Ejercicio 3.

**También tomado en:** Recuperatorio 2020-1C · Ejercicio V (el host es ejercicio4.proto.leak.com.ar y no trae request capturado, así que va sin Authorization; el comando está en Variantes).

> **Idea:** La idea es pedir con curl http://ejercicio2.proto.leak.com.ar/foo/ en texto plano y en español, mandando los headers Accept: text/plain y Accept-Language: es. El truco es reusar tal cual el Authorization Basic del request capturado que trae el enunciado, y negociar con Accept, no con Content-Type.

**Buscar:** ejercicio2.proto.leak.com.ar, representacion textual, text/plain, lenguaje español, es, Accept, Accept-Language, Authorization Basic, dXNlcm5hbWU6cGFzc3dvcmQ=, curl -H, content negotiation, negociacion de contenido, Utilizando curl obtenga, representación textual, media type, lenguaje espanol, /foo/, A su poder ha llegado un request y una respuesta, sobre el mismo servidor, información que le podría ser útil, informacion que le podria ser util, GET /abc/ HTTP/1.0, nginx/1.1.19, ejercicio4.proto.leak.com.ar, ejercicio4.protos.leak.com.ar, media: type/plain, http://ejercicio4.proto.leak.com.ar/foo/

**Enunciado:** "Utilizando curl obtenga la representación textual (media type: text/plain) en el lenguaje español (es) del recurso http://ejercicio2.proto.leak.com.ar/foo/" y "A su poder ha llegado un request y una respuesta sobre el mismo servidor con información que le podría ser útil." El request y la respuesta que traía el enunciado:

\> GET /abc/ HTTP/1.0

\> Host: ejercicio2.proto.leak.com.ar

\> Accept-Encoding: gzip, deflate, br

\> Accept-Language: en,it;q=0.9,es-AR;q=0.8,es;q=0.7

\> Cache-Control: no-cache

\> Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=

\> Connection: keep-alive

\> Pragma: no-cache

\> sec-ch-ua-mobile: ?0

\> Sec-Fetch-Dest: empty

\> Sec-Fetch-Mode: cors

\> Sec-Fetch-Site: same-origin

\> User-Agent: Mozilla/5.0 (X11; Fedora; Linux x86_64)

\< HTTP/1.1 200 OK

\< Server: nginx/1.1.19

\< Date: Wed, 19 May 2021 02:18:51 GMT

\< Content-Length: 1084

\< Last-Modified: Sun, 22 Apr 2018 23:52:26 GMT

**Enunciado en Recuperatorio 2020-1C · Ejercicio V:** "Utilizando curl obtenga la representación textual (media type: text/plain) en el lenguaje español (es) del recurso http://ejercicio4.proto.leak.com.ar/foo/. (2 puntos)" No trae request ni respuesta capturados.

**Solución:** mandás los headers que fuerzan español + texto plano, y reusás la Authorization capturada. Si te tocó el Recuperatorio 2020-1C · Ejercicio V (host ejercicio4.proto.leak.com.ar, sin request capturado), no copies este comando: copiá el de Variantes, que va sin Authorization. Para el Parcial 2021-1C:

curl -L \\

-H 'Accept: text/plain' \\

-H 'Accept-Language: es' \\

-H 'Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=' \\

http://ejercicio2.proto.leak.com.ar/foo/

**Demostrar:** la salida, que tiene que ser texto plano en español. Para ver que el server respondió Content-Type: text/plain, agregá -i (o -D -):

curl -i -H 'Accept: text/plain' -H 'Accept-Language: es' -H 'Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=' http://ejercicio2.proto.leak.com.ar/foo/

**Ojo:**

- Es **Accept** (lo que acepto recibir), NO Content-Type. Content-Type describe el cuerpo que YO mando, y un GET no manda cuerpo. El apunte de MasCompilado usa -H 'Content-Type: text/plain': está MAL, no negocia nada.

- Accept-Language: es exacto. Si pedís es-AR el server puede no tener esa variante y caer a otra.

- La Authorization: Basic ... sale del request que te dieron; decodifica a username:password (echo 'dXNlcm5hbWU6cGFzc3dvcmQ=' \| base64 -d). Sin ella el server puede contestar 401.

- -L sigue redirecciones por si /foo/ redirige.

- En el Recuperatorio 2020-1C · Ejercicio V, MasCompilado escribe el host como ejercicio4.protos.leak.com.ar (con "s"): el Merge dice ejercicio4.proto.leak.com.ar (sin "s"). Usá el del enunciado que te toque; si uno no resuelve, probá el otro.

**Variantes:**

- Recuperatorio 2020-1C · Ejercicio V: no hay request capturado y el host es ejercicio4.proto.leak.com.ar. Es el más corto: el mismo pedido sin la línea de Authorization, que ahí no hace falta. Se demuestra igual: la salida en texto plano y en español, y con -i el Content-Type: text/plain. También probado en el lab: curl manda exactamente Accept: text/plain y Accept-Language: es (verificado con nc).

curl -L -H 'Accept: text/plain' -H 'Accept-Language: es' http://ejercicio4.proto.leak.com.ar/foo/

*Probado en Ubuntu 24.04 (lab): el request crudo capturado con nc muestra exactamente Accept: text/plain, Accept-Language: es y la Authorization; el Basic decodifica a username:password.*

## Seguir un recurso: Host en base64, redirecciones, autenticación y cookies

En todos hay que pedir con curl un recurso a un servidor por su IP, mandando el Host en base64 tal cual lo da el enunciado, y sumar lo que cada respuesta reclama: la ruta del Location (301), el Authorization (401), la cookie (403) o el Accept. De un parcial a otro cambia el recurso, el tipo pedido (text/plain o JSON), de dónde salen los valores (del enunciado o de un stream capturado) y cuántos pasos tiene la cadena.

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| Cadena de curl: Host en base64, 301, 401, 403 y cookie | Partiendo de /api/health con Host: Zm9v, llegar a la representación text/plain (termina en /api/ok/) | Cadena completa: 301, 401, 403 y 200. El Authorization: Basic dXN1YXJpbzpwYXNzd29yZA== y la cookie llave=dmFsb3I vienen en el enunciado. Consigna reconstruida. |
| curl al recurso /secreto/543 a partir de un stream TCP capturado con Wireshark | El JSON (application/json) de /secreto/543 con Host: aG9zdG5hbWUK | El Host, el Authorization: Basic VVNFUjpwYXNz y la cookie JSESSIONID=UEFSQ0lB se sacan de un stream TCP capturado con Wireshark. Dos redirecciones (/raw/def/ y /raw/lalala.js). Trae la tabla de qué agregar según el código de respuesta y la versión en un solo comando con --resolve y -L. |
| Seguir el recurso /secreto/143 | El JSON de /secreto/143 con Host: aG9zdG5hbWUK | Un solo pedido con Host y Accept. Trae el mismo pedido escrito a mano con nc. Consigna reconstruida. También tomado en Parcial 2014-2C · Ejercicio 1 (el pedido ya lleva la cookie JSESSIONID=7227361B90828530 con -b y va contra http://10.6.0.B/: el comando está en Variantes). |

### Cadena de curl: Host en base64, 301, 401, 403 y cookie

**Tomado en:** Parcial 2016-2C · Ejercicio 2.

> **Idea:** La idea es seguir con curl la cadena desde /api/health con Host: Zm9v hasta la representación text/plain, sumando lo que pide cada respuesta: la ruta del Location (301), el Authorization (401), la cookie (403) y el Accept. El truco es mantener la IP y cambiar solo la ruta, porque el nombre del Location no resuelve.

**Buscar:** curl, cadena, seguir el recurso, seguir recurso, /api/health, /api/ok/, Host en base64, Zm9v, 301 Moved Permanently, move permanently, Location, 401 Unauthorized, WWW-Authenticate, 403 Forbidden, Authorization, Basic, dXN1YXJpbzpwYXNzd29yZA==, dXN1YXJpbzpwYXNzd29y, Authorazation, Cookie, llave, llave=dmFsb3I, Accept, text/plain, 10.1.34.189, -Hhost, -H, -i, -I, -v, -b, -L

**Enunciado:** No hay consigna literal en las fuentes; está reconstruido de la resolución de un alumno. A partir del recurso /api/health del servidor del pizarrón (en el apunte, 10.1.34.189) y con el Host Zm9v, seguir lo que pide cada respuesta (redirección, autenticación, cookie, tipo de contenido) hasta obtener la representación text/plain. El valor de Authorization y la cookie llave=dmFsb3I venían en el enunciado.

**Solución:** cada respuesta dice qué falta. Pedí con -i y leé la primera línea, Location y WWW-Authenticate. Cada comando es el anterior más una cosa. IP_SERVIDOR es la IP del pizarrón.

> **1.** Recurso inicial con el Host tal cual lo dan. Responde 301: mirá Location.

curl -i -H 'Host: Zm9v' http://IP_SERVIDOR/api/health

HTTP/1.1 301 Moved Permanently

Location: http://zm9v/api/ok/

> **2.** Misma IP, con la ruta del Location. Responde 401: pide autenticación Basic.

curl -i -H 'Host: Zm9v' http://IP_SERVIDOR/api/ok/

HTTP/1.1 401 Unauthorized

WWW-Authenticate: Basic realm="api"

> **3.** Agregá el Authorization del enunciado. Responde 403: ya te autenticaste, falta la cookie.

curl -i -H 'Host: Zm9v' -H 'Authorization: Basic dXN1YXJpbzpwYXNzd29yZA==' http://IP_SERVIDOR/api/ok/

HTTP/1.1 403 Forbidden

> **4.** Agregá la cookie y el Accept. Responde 200 con el cuerpo en texto plano.

curl -i -H 'Host: Zm9v' -H 'Authorization: Basic dXN1YXJpbzpwYXNzd29yZA==' -H 'Cookie: llave=dmFsb3I' -H 'Accept: text/plain' http://IP_SERVIDOR/api/ok/

HTTP/1.1 200 OK

Content-Type: text/plain

Qué dice cada valor en base64 (sirve para entender; se mandan sin decodificar):

echo Zm9v \| base64 -d; echo

echo dXN1YXJpbzpwYXNzd29yZA== \| base64 -d; echo

echo dmFsb3I= \| base64 -d; echo

foo

usuario:password

valor

**Demostrar:** los cuatro comandos con sus respuestas: 301, 401, 403 y 200 con Content-Type: text/plain y el cuerpo.

**Ojo:**

- El apunte traía el token cortado y el header mal escrito (Authorazation: Basic dXN1YXJpbzpwYXNzd29y). Ese token decodifica a usuario:passwor y con cualquiera de los dos errores el servidor sigue contestando 401. El valor completo es dXN1YXJpbzpwYXNzd29yZA==; en tu examen copiá el del enunciado carácter por carácter.

- El apunte pedía con -I, que manda HEAD: no trae el cuerpo (donde pueden estar las pistas) y algunos servidores contestan otro código. Usá -i.

- El Host va tal cual lo da el enunciado (Zm9v), sin decodificar.

- Si responde 301, cambiá solo la ruta por la de Location y mantené http://IP_SERVIDOR: el nombre que trae Location (zm9v) no resuelve, curl: (6) Could not resolve host. Por lo mismo no sirve -L junto con -H 'Host: ...'.

- -b 'llave=dmFsb3I' es lo mismo que -H 'Cookie: llave=dmFsb3I'. La cookie se manda como está, sin agregarle el = final.

- -u usuario:password arma el mismo Authorization: Basic dXN1YXJpbzpwYXNzd29yZA==. Si el enunciado ya da el base64, pegalo con -H.

*Probado en Ubuntu 24.04 (lab), contra un nginx que imita la cadena (301, 401, 403, 200). Sin probar: el servidor del parcial; el token completo y el cuerpo final son una reconstrucción.*

### curl al recurso /secreto/543 a partir de un stream TCP capturado con Wireshark

**Tomado en:** Parcial 2016-1C (primer parcial) · Ejercicio 2.

> **Idea:** La idea es obtener con curl el JSON de /secreto/543 sumando lo que pide cada respuesta: el Host en base64, el Authorization y la cookie del stream TCP capturado, y el Accept application/json. El truco es que hay dos redirecciones: se siguen a mano cambiando solo la ruta del Location y manteniendo la IP, o en un solo comando con --resolve y -L.

**Buscar:** /secreto/543, secreto/543, stream TCP, Wireshark, interceptar, carga útil, carga util, rumores de pasillo, representación JSON, representacion JSON, RFC4627, rfc4627, application/json, GET /ZXhhbWVu, ZXhhbWVu, Host en base64, aG9zdG5hbWUK, Authorization, Basic VVNFUjpwYXNz, JSESSIONID, UEFSQ0lB, Set-Cookie, 200 Found, application/octet-stream, Hola mundo!, A.B.C.D, A.B.C.E:80, /raw/def/, /raw/lalala.js, 301 Moved Permanently, Location, 401 Unauthorized, 403 Forbidden, Accept, --resolve, -L, seguir el recurso

**Enunciado:** (leído de dos fotos de la hoja; lo que está entre corchetes queda tapado y es reconstrucción, igual que el primer dígito del puerto) "Utilizando la herramienta Wireshark usted pudo interceptar un stream TCP identificado como A.B.C.D:1322/A.B.C.E:80 (ver valores de A, B, C, D, E en el pizarrón) y cuya carga útil es la que se muestra a continuación (notación a tener en cuenta: \> enviado ; \< recibido) \[...\] Usted sabe por ciertos rumores de pasillo que en ese m\[ismo servidor existe un recurso\] identificado por /secreto/543 del cual usted desea \[obtener la representación JSON\] (RFC4627: application/json). Obtenga la representació\[n...\]". La carga útil:

\> GET /ZXhhbWVu HTTP/1.1

\> Host: aG9zdG5hbWUK

\> Authorization: Basic VVNFUjpwYXNz

\> Cache-Control: max-age=180

\> Connection: keep-alive

\> User-Agent: Mozilla/5.0, Safari/537.31

\> DNT: 1

\>

\< HTTP/1.1 200 Found

\< Server: nginx

\< Content-Type: application/octet-stream

\< Date: Tue, 25 Apr 2016 22:30:25 GMT

\< Connection: keep-alive

\< Set-Cookie: JSESSIONID=UEFSQ0lB; Path=/; HttpOnly

\< Vary: Accept-Encoding

\<

\< Hola mundo!

**Solución:** no sale con un solo pedido: el servidor te va llevando. Del stream anotá el Host, el Authorization y la cookie; IP_SERVIDOR es A.B.C.E (el lado :80).

> **1.** Primer pedido, con -i para ver código, headers y cuerpo:

curl -i -H 'Host: aG9zdG5hbWUK' http://IP_SERVIDOR/secreto/543

> **2.** Leé la primera línea de la respuesta y agregá UNA cosa por vez, sin sacar lo que ya pusiste:

| **Respuesta** | **Qué mirar** | **Qué agregar o cambiar** |
|----|----|----|
| 301 / 302 | Header Location | Cambiá solo la ruta de la URL por la de Location, manteniendo http://IP_SERVIDOR |
| 401 Unauthorized | Header WWW-Authenticate | -H 'Authorization: Basic ...' del stream, o -u USUARIO:CLAVE |
| 403 Forbidden | Ya te autenticaste y no alcanza | La cookie del stream: -b 'NOMBRE=VALOR' |
| 200 pero no es lo pedido | Content-Type de la respuesta | -H 'Accept: application/json' |
| 406 Not Acceptable | El Accept no le sirve | Otro Accept, o -H 'Accept: application/json, \*/\*;q=0.1' |
| 404 Not Found | Vhost o ruta | Revisá el Host, la barra final y la ruta |
| 405 Method Not Allowed | Header Allow | No uses -I (HEAD); pedí con GET (-i) |

> **3.** El recorrido que tuvo este ejercicio en el Parcial 2016-1C (primer parcial). Las rutas salen de la resolución de un alumno; en tu examen pueden ser otras. Cada comando es el anterior más lo que pidió la respuesta:

\# 301 con Location .../raw/def/

curl -i -H 'Host: aG9zdG5hbWUK' http://IP_SERVIDOR/secreto/543

\# 200 pero no es JSON

curl -i -H 'Host: aG9zdG5hbWUK' http://IP_SERVIDOR/raw/def/

\# con Accept: otro 301, ahora a /raw/lalala.js

curl -i -H 'Host: aG9zdG5hbWUK' -H 'Accept: application/json' http://IP_SERVIDOR/raw/def/

\# 401

curl -i -H 'Host: aG9zdG5hbWUK' -H 'Accept: application/json' http://IP_SERVIDOR/raw/lalala.js

\# con Authorization: 200 (o 403 si ademas quiere la cookie)

curl -i -H 'Host: aG9zdG5hbWUK' -H 'Accept: application/json' -H 'Authorization: Basic VVNFUjpwYXNz' http://IP_SERVIDOR/raw/lalala.js

\# con la cookie: 200 y el JSON

curl -i -H 'Host: aG9zdG5hbWUK' -H 'Accept: application/json' -H 'Authorization: Basic VVNFUjpwYXNz' -H 'Cookie: JSESSIONID=UEFSQ0lB' http://IP_SERVIDOR/raw/lalala.js

> **4.** Todo en un solo comando, dejando que curl siga las redirecciones. Necesita --resolve en vez de -H 'Host:', porque Location trae el nombre del vhost:

curl -v -L --resolve aG9zdG5hbWUK:80:IP_SERVIDOR -H 'Accept: application/json' -u USER:pass -b 'JSESSIONID=UEFSQ0lB' http://aG9zdG5hbWUK/secreto/543

> **5.** Qué dicen los valores del stream (se mandan sin decodificar):

echo aG9zdG5hbWUK \| base64 -d

echo VVNFUjpwYXNz \| base64 -d; echo

echo ZXhhbWVu \| base64 -d; echo

hostname

USER:pass

examen

> **6.** Si te dan la captura en vez del texto, el stream se saca con tshark (o en Wireshark: clic derecho sobre un paquete, Follow, TCP Stream):

tshark -r captura.pcap -q -z follow,tcp,ascii,0

tshark -r captura.pcap -Y http.request -T fields -e http.host -e http.request.uri -e http.authbasic

**Demostrar:** el último comando del paso 3 (o el del paso 4) terminando en 200 OK con el JSON. Con el del paso 4 se ve todo el camino:

curl -v -L --resolve aG9zdG5hbWUK:80:IP_SERVIDOR -H 'Accept: application/json' -u USER:pass -b 'JSESSIONID=UEFSQ0lB' http://aG9zdG5hbWUK/secreto/543

-v imprime mucho. En la salida, mirá las líneas que empiezan con \> GET, \< HTTP/1.1 y \< Location: tienen que aparecer en este orden (acá se muestran solo esas), y al final viene el JSON.

\> GET /secreto/543 HTTP/1.1

\< HTTP/1.1 301 Moved Permanently

\< Location: http://ag9zdg5hbwuk/raw/def/

\> GET /raw/def/ HTTP/1.1

\< HTTP/1.1 301 Moved Permanently

\< Location: http://ag9zdg5hbwuk/raw/lalala.js

\> GET /raw/lalala.js HTTP/1.1

\< HTTP/1.1 200 OK

**Ojo:**

- Un solo curl a /secreto/543 con todos los headers juntos se queda en el primer 301 Moved Permanently: hay que seguir la cadena.

- Si responde 301, cambiá solo la ruta por la de Location y mantené http://IP_SERVIDOR. Pegando la URL del Location, o con -L junto a -H 'Host:', da curl: (6) Could not resolve host: ag9zdg5hbwuk.

- El Host va con el valor literal del stream (aG9zdG5hbWUK), sin decodificar. Si desde el primer pedido contesta 404 o el sitio por defecto, probá el decodificado.

- En la resolución que circula el Host y el token están cambiados (aGzd9Ddsaf, AJSHasmna54Dk): no sirven, usá los de tu enunciado.

- Las credenciales son USER:pass, con la clave en minúscula. VVNFUjpQQVNT (USER:PASS) es del Parcial 2016-1C (primer parcial) · Ejercicio 1, que está en Resueltos — nginx, 'Proxy reverso: un dominio sirve el contenido de otro'.

- Se pide /secreto/543, no el /ZXhhbWVu del stream: ese GET solo te da los headers para reusar.

- -b 'JSESSIONID=UEFSQ0lB' y -H 'Cookie: JSESSIONID=UEFSQ0lB' son lo mismo. Solo NOMBRE=VALOR, sin Path ni HttpOnly.

- curl: (47) Maximum (50) redirects followed: la redirección depende de un header que no estás mandando (por ejemplo Accept). Seguila a mano con -i.

*Probado en Ubuntu 24.04 (lab), contra un nginx que imita la cadena con los valores de la foto. Sin probar: el servidor del parcial y los clics de Wireshark gráfico (el lab no tiene entorno gráfico; se probó la sintaxis de tshark).*

### Seguir el recurso /secreto/143

**Tomado en:** Parcial 2015 (primer parcial) · Ejercicio 2.

**También tomado en:** Parcial 2014-2C · Ejercicio 1 (el pedido ya lleva la cookie JSESSIONID=7227361B90828530 con -b y el servidor es http://10.6.0.B/; el comando está en Variantes).

> **Idea:** La idea es pedir con curl el JSON de /secreto/143 al servidor del pizarrón en un solo pedido, con el Host en base64 y Accept: application/json. El truco es mandar el Host aG9zdG5hbWUK sin decodificar y, si no da 200 de entrada, agregar una cosa por vez según lo que pida la respuesta.

**Buscar:** /secreto/143, secreto/143, seguir un recurso, seguir recurso, curl, nc, Host en base64, aG9zdG5hbWUK, Accept: application/json, application/json, GET, ZXhhbWVu, examen, 200, 301, 401, 403, -Hhost:aG9zdG5hbWUK, Cookie, JSESSIONID, JSESSIONID=7227361B90828530, 10.6.0.B, -b, -H, -v, nc -C, ncat, CRLF

**Enunciado:** No hay consigna en las fuentes. El apunte solo trae el título "Ejercicio 2 (seguir un recurso)" y un pedido a medio armar con /secreto/143, Host: aG9zdG5hbWUK y Accept: application/json. Reconstruido: obtener la representación JSON del recurso /secreto/143 del servidor del pizarrón, usando el Host del enunciado.

**Enunciado en Parcial 2014-2C · Ejercicio 1:** No hay consigna en las fuentes; el apunte solo trae el comando. Reconstruido: obtener la representación application/json del recurso /secreto/143 de http://10.6.0.B/ (B del pizarrón), usando el Host en base64, la cookie JSESSIONID y Accept: application/json que da el enunciado.

**Solución:** pedí el recurso con el Host literal y el Accept, mirando la respuesta con -i. IP_SERVIDOR es la IP del servidor que dan en el pizarrón.

curl -i -H 'Host: aG9zdG5hbWUK' -H 'Accept: application/json' http://IP_SERVIDOR/secreto/143

El mismo pedido escrito a mano, como lo intentaba el apunte. Abrí la conexión con nc (con ncat es igual: ncat -C IP_SERVIDOR 80):

nc -C IP_SERVIDOR 80

Tipeá o pegá estas líneas. El pedido cierra con una línea vacía: después de Connection: close va Enter dos veces. Si pegaste y el servidor no contesta nada, apretá Enter otra vez (falta la línea vacía).

GET /secreto/143 HTTP/1.1

Host: aG9zdG5hbWUK

Accept: application/json

Connection: close

El servidor contesta en la misma terminal:

HTTP/1.1 200 OK

Content-Type: application/json

(más headers)

{ ...el JSON... }

Si nc no vuelve solo al prompt después de la respuesta, salí con Ctrl+C.

Qué dicen los valores en base64 (se mandan sin decodificar):

echo aG9zdG5hbWUK \| base64 -d

echo ZXhhbWVu \| base64 -d; echo

hostname

examen

**Demostrar:** la respuesta HTTP/1.1 200 OK con Content-Type: application/json y el cuerpo.

**Ojo:**

- Si no da 200 de entrada, seguí la respuesta agregando una cosa por vez: con 301, misma IP y la ruta de Location; con 401, -H 'Authorization: Basic ...' del enunciado; con 403, la cookie. En el Parcial 2014-2C · Ejercicio 1 el mismo recurso pedía -b 'JSESSIONID=7227361B90828530' (el comando completo está abajo, en Variantes).

- El Host va en base64 tal cual (aG9zdG5hbWUK), sin decodificar.

- Con nc, -C hace que cada Enter mande \r\n, que es el fin de línea de HTTP. El pedido cierra con una línea vacía (Enter dos veces al final). Connection: close hace que el servidor corte al terminar. Pegá el pedido de una: si tardás mucho en tipearlo, el servidor puede cortar por timeout.

- El apunte escribió nc ZXhhbWVu/secreto/143 HTTP/1.1 y después GET / HTTP/1.1: está mal. A nc se le pasa la IP y el puerto, y la ruta va en la línea del GET.

- Si no responde JSON, revisá que el Accept: application/json esté bien escrito (sin espacio raro) y que el Host sea el exacto.

**Variantes:**

- Parcial 2014-2C · Ejercicio 1: el enunciado ya daba la cookie y el servidor era http://10.6.0.B/ (reemplazá B por el número del pizarrón). Es el mismo pedido con la cookie en -b 'JSESSIONID=...' (igual que -H 'Cookie: JSESSIONID=...'); el valor es el que te dan. Los valores de Host y cookie del comando de abajo son los del apunte de ese parcial. Se demuestra con el curl -v: tus \> (Host, Cookie, Accept) y el \< HTTP/1.1 200 con el JSON.

curl -v \\

-H 'Host: aG9zdG5hbWUK' \\

-b 'JSESSIONID=7227361B90828530' \\

-H 'Accept: application/json' \\

http://10.6.0.B/secreto/143

*Probado en Ubuntu 24.04 (lab), contra un nginx que imita al servidor (ahí /secreto/143 pide además la cookie de 2014-2C: sin ella 403, con ella 200 y el JSON, con curl y con nc). Sin probar: el servidor del parcial.*

# Resueltos — DNS (BIND y dig)

## Arreglar una zona rota

Te dan un archivo de zona de BIND que no carga: hay que encontrarle los errores de sintaxis y de lógica con named-checkzone, corregirlos y demostrar con dig que la zona volvió a resolver. De un parcial a otro pueden cambiar la zona, el archivo y los errores que trae. En los parciales viejos hay un solo caso.

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| BIND: arreglar la zona rota it.itba.edu.ar y el SOA | Zona it.itba.edu.ar en /etc/bind/db.it.itba.edu.ar (se baja de http://protos.foo/zona): que vuelva a resolver pampero.it.itba.edu.ar | Errores de la hoja: SOA cerrado con corchete, www IN A pampero, ns1.it.itba.edu.ar sin punto final, MX a mail que no existe y admin@ en el SOA. Además pide tocar el SOA (Serial, Refresh, Retry, TTL negativo) para que los cambios se propaguen rápido. |

### BIND: arreglar la zona rota it.itba.edu.ar y el SOA

**Tomado en:** Parcial 2025-2Q · Ejercicio 3.

> **Idea:** La idea es dejar funcionando la zona it.itba.edu.ar de BIND para que vuelva a resolver pampero.it.itba.edu.ar, arreglando con named-checkzone un error por vez hasta que diga OK. La trampa es lo que no avisa: el contacto admin@ del SOA se escribe con punto y, para que los cambios se propaguen rápido, hay que subir el Serial y bajar Refresh, Retry y TTL negativo.

**Buscar:** BIND9, named, DNS, zona, archivo de zona, it.itba.edu.ar, pampero.it.itba.edu.ar, /etc/bind/db.it.itba.edu.ar, http://protos.foo/zona, autoritativo, errores de sintaxis, configuración lógica, configuracion logica, registro SOA, serial, refresh, retry, expire, negative cache TTL, se propaguen, propagar, rápida, rapida, named-checkzone, named-checkconf, rndc reload, admin@it.itba.edu.ar, ns1.it.itba.edu.ar, MX 10 mail, www IN A pampero, correo IN CNAME pampero, 192.168.70.1, 192.168.70.10, 2025060500, 604800, SERVFAIL, extra input text, bad dotted quad, has no address records, not loaded due to errors, Ejercicio III, not a valid number, out of range, bad IPv6 address, CNAME and other data, unknown RR type, no current owner name, has no NS records, unbalanced parentheses, unexpected end of input, unexpected end of line, ignoring out-of-zone data, no TTL specified, has gone backwards, named-checkzone -D

**Enunciado:** Usted es el administrador de un servidor DNS que utiliza BIND9 y es autoritativo para la zona it.itba.edu.ar. Recientemente, tras una serie de cambios, los clientes han reportado que la resolución de nombres para todos los hosts dentro de esa zona (como pampero.it.itba.edu.ar) ha dejado de funcionar. Sus navegadores arrojan problemas al resolver nombres, y administradores de DNS de los ISP lo han contactado por problemas en la zona. Al revisar los registros del sistema, observa que el servicio BIND9 se queja de múltiples errores al intentar cargar el archivo de zona, ubicado en /etc/bind/db.it.itba.edu.ar. (Disponible en http://protos.foo/zona). Inspeccione el archivo de zona, identifique y corrija todos los errores de sintaxis y de configuración lógica que impiden su correcto funcionamiento. Una vez corregidos los errores, haga los cambios necesarios en el registro SOA para asegurar que los cambios se propaguen de manera correcta y rápida. Archivo tal como está en la hoja (el cierre del paréntesis del SOA se ve como un corchete o una llave):

\$TTL 604800

@ IN SOA ns1.it.itba.edu.ar. admin@it.itba.edu.ar. (

2025060500 ; Serial

604800 ; Refresh

86400 ; Retry

2419200 ; Expire

604800 ; Negative Cache TTL

\]

; Name Servers

@ IN NS ns1.it.itba.edu.ar.

; Mail

@ IN MX 10 mail

; Hosts

ns1.it.itba.edu.ar IN A 192.168.70.1

pampero IN A 192.168.70.10

; Aliases

www IN A pampero

correo IN CNAME pampero

**Solución:**

> **1.** Instalá BIND si falta y bajá el archivo roto (no lo tipees). Guardá una copia. El cat muestra lo que bajó: si sale vacío o HTML, revisá la URL.

sudo apt install -y bind9 bind9-utils bind9-dnsutils curl

sudo curl -sL http://protos.foo/zona -o /etc/bind/db.it.itba.edu.ar

cat /etc/bind/db.it.itba.edu.ar

sudo cp /etc/bind/db.it.itba.edu.ar /etc/bind/db.it.itba.edu.ar.roto

\# sin curl: sudo wget -qO /etc/bind/db.it.itba.edu.ar http://protos.foo/zona

> **2.** Declará la zona en /etc/bind/named.conf.local (el grep muestra si ya está, para no duplicarla).

grep -n 'zone "it.itba.edu.ar"' /etc/bind/named.conf\*

Si no muestra nada, hay que hacer sudo nano /etc/bind/named.conf.local y agregar al final lo siguiente:

zone "it.itba.edu.ar" {

type master;

file "/etc/bind/db.it.itba.edu.ar";

};

> **3.** Mirá qué se queja. named-checkzone muestra un error por vez: arreglás, volvés a correr, hasta que diga OK. Los otros tres comandos son el "antes" para mostrar: BIND recargado con el archivo roto, su log (el grep deja solo las líneas de esta zona: mirá las últimas) y el dig, que en la línea status: dice SERVFAIL.

named-checkzone it.itba.edu.ar /etc/bind/db.it.itba.edu.ar

sudo systemctl restart named

sudo journalctl -u named --no-pager \| grep it.itba

dig @127.0.0.1 pampero.it.itba.edu.ar

Lo que importa de las salidas (del dig, solo la línea status:):

dns_rdata_fromtext: /etc/bind/db.it.itba.edu.ar:7: near '\]': extra input text

zone it.itba.edu.ar/IN: loading from master file /etc/bind/db.it.itba.edu.ar failed: extra input text

zone it.itba.edu.ar/IN: not loaded due to errors.

;; -\>\>HEADER\<\<- opcode: QUERY, status: SERVFAIL, id: 35749

Las seis primeras filas son los errores de la hoja. Las demás son otros errores que puede traer el archivo real.

| **Mensaje** | **Línea culpable** | **Arreglo** |
|----|----|----|
| near '\]': extra input text (o near '}') | cierre del SOA | poner ) |
| near 'pampero': bad dotted quad | www IN A pampero | www IN CNAME pampero (un A lleva una IP) |
| NS 'ns1.it.itba.edu.ar' has no address records (A or AAAA) | ns1.it.itba.edu.ar IN A ... sin punto final | ns1.it.itba.edu.ar. con punto, o solo ns1 |
| MX 'mail.it.itba.edu.ar' has no address records (A or AAAA) (aviso, carga igual) | @ IN MX 10 mail | agregar mail IN A 192.168.70.10 |
| no avisa | admin@it.itba.edu.ar. en el SOA | admin.it.itba.edu.ar. (el @ del mail se escribe como punto) |
| no avisa | Serial viejo, Refresh de 7 días, TTL negativo de 7 días | subir el Serial y bajar los tiempos |
| near 'mail': not a valid number | @ IN MX mail (MX sin prioridad) | @ IN MX 10 mail |
| near '20261011011': out of range | Serial de 11 dígitos | 10 dígitos: AAAAMMDDNN |
| near '192.168.70.10': bad IPv6 address | pampero IN AAAA 192.168.70.10 | IN A (AAAA es para IPv6) |
| www.it.itba.edu.ar: CNAME and other data | un CNAME y otro registro con el mismo nombre | dejar uno solo |
| unknown RR type 'Hosts' (la palabra que sigue al \#) | comentario escrito con \#, como \# Hosts | los comentarios van con ; |
| unknown RR type '@' (o el nombre de esa línea) | la línea empieza con espacios o tab | el nombre va pegado al margen izquierdo |
| no current owner name | la línea del SOA empieza con espacios | @ IN SOA ... pegado al margen izquierdo |
| has no NS records | falta la línea NS | agregar @ IN NS ns1.it.itba.edu.ar. |
| near '@': extra input text (o el nombre de la línea que sigue al SOA) | al SOA le falta el ) | poner ) después del Negative Cache TTL |
| unbalanced parentheses | sobra un ) | el SOA lleva un ( y un ) |
| near eol: unexpected end of input | al SOA le falta uno de sus 5 números o el ( (sin el ( salen además unexpected end of line y unbalanced parentheses en las líneas de abajo: arreglá solo el primer mensaje); si la línea del mensaje no es del SOA, a ese registro le falta el dato (pampero IN A sin IP) | que el SOA tenga (, Serial, Refresh, Retry, Expire, Negative Cache TTL y ); o completar el dato |
| ignoring out-of-zone data (NOMBRE) (aviso, carga igual pero sin ese registro) | un nombre con punto final de otro dominio | escribirlo relativo (pampero) o terminado en .it.itba.edu.ar. |
| no TTL specified; using SOA MINTTL instead (aviso, carga igual) | falta la línea \$TTL | agregar \$TTL 3600 como primera línea |
| no avisa (se ve con named-checkzone -D it.itba.edu.ar /etc/bind/db.it.itba.edu.ar) | CNAME (o PTR) a un nombre completo sin punto final: queda pampero.it.itba.edu.ar.it.itba.edu.ar. | punto final, o solo pampero |
| no avisa named-checkzone; después de sudo rndc reload it.itba.edu.ar (con systemctl restart no sale) el log de named dice zone serial (2025060500/2026101101) has gone backwards (nuevo/anterior) | Serial menor al que estaba cargado: named lo carga igual, pero los secundarios no lo toman | un Serial mayor al anterior |

> **4.** Archivo corregido completo, /etc/bind/db.it.itba.edu.ar. En el Serial va la fecha de hoy en formato AAAAMMDDNN (en el bloque, 2026101101 es un ejemplo: cambialo por la de hoy); mirá en el grep del final que sea mayor al que venía (2025060500). Si el archivo que bajaste trae registros que no están en la hoja, no pegues este bloque: editá el bajado con sudo nano /etc/bind/db.it.itba.edu.ar aplicando los arreglos de la tabla.

Hay que hacer sudo nano /etc/bind/db.it.itba.edu.ar, borrar lo que tenga y pegar lo siguiente:

\$TTL 3600

@ IN SOA ns1.it.itba.edu.ar. admin.it.itba.edu.ar. (

2026101101 ; Serial: AAAAMMDDNN de hoy, MAYOR al que venia

900 ; Refresh: cada cuanto el secundario pregunta por cambios

300 ; Retry: reintento si fallo el refresh

2419200 ; Expire

300 ; Negative Cache TTL

)

; Name Servers

@ IN NS ns1.it.itba.edu.ar.

; Mail

@ IN MX 10 mail

; Hosts

ns1.it.itba.edu.ar. IN A 192.168.70.1

pampero IN A 192.168.70.10

mail IN A 192.168.70.10

; Aliases

www IN CNAME pampero

correo IN CNAME pampero

grep -n Serial /etc/bind/db.it.itba.edu.ar

> **5.** Validá y recargá.

named-checkzone it.itba.edu.ar /etc/bind/db.it.itba.edu.ar

sudo named-checkconf

sudo systemctl restart named

\# viejo: sudo service named restart (en versiones viejas el servicio se llama bind9)

zone it.itba.edu.ar/IN: loaded serial 2026101101

OK

**Demostrar:**

dig @127.0.0.1 pampero.it.itba.edu.ar

dig @127.0.0.1 it.itba.edu.ar SOA +noall +answer

dig @127.0.0.1 it.itba.edu.ar NS +noall +answer

dig @127.0.0.1 it.itba.edu.ar MX +noall +answer

dig @127.0.0.1 www.it.itba.edu.ar +noall +answer

dig @127.0.0.1 correo.it.itba.edu.ar +noall +answer

dig @127.0.0.1 mail.it.itba.edu.ar +noall +answer

dig @127.0.0.1 ns1.it.itba.edu.ar +noall +answer

En el primero tiene que decir status: NOERROR y flags: qr aa rd ra (el aa es respuesta autoritativa). El resto:

it.itba.edu.ar. 3600 IN SOA ns1.it.itba.edu.ar. admin.it.itba.edu.ar. 2026101101 900 300 2419200 300

it.itba.edu.ar. 3600 IN NS ns1.it.itba.edu.ar.

it.itba.edu.ar. 3600 IN MX 10 mail.it.itba.edu.ar.

www.it.itba.edu.ar. 3600 IN CNAME pampero.it.itba.edu.ar.

pampero.it.itba.edu.ar. 3600 IN A 192.168.70.10

correo.it.itba.edu.ar. 3600 IN CNAME pampero.it.itba.edu.ar.

pampero.it.itba.edu.ar. 3600 IN A 192.168.70.10

mail.it.itba.edu.ar. 3600 IN A 192.168.70.10

ns1.it.itba.edu.ar. 3600 IN A 192.168.70.1

Mostrá también el antes (salidas del paso 3): con el archivo roto, dig @127.0.0.1 pampero.it.itba.edu.ar da status: SERVFAIL.

**Ojo:**

- El archivo real puede traer errores distintos a los de la foto. El método no cambia: named-checkzone, arreglar, repetir. Después revisá a ojo lo que no avisa (mail del SOA, MX, Serial y tiempos).

- El número de línea del mensaje puede estar corrido en uno: el corchete de la línea 8 lo informa como línea 7.

- Punto final: un nombre sin punto se completa con la zona. ns1.it.itba.edu.ar sin punto queda ns1.it.itba.edu.ar.it.itba.edu.ar. y el NS se queda sin dirección.

- admin@it.itba.edu.ar. no da error porque named-checkzone lo toma como un nombre válido, pero es un error lógico: el contacto se escribe admin.it.itba.edu.ar.

- El MX apunta a mail, que no existía. Como correo es alias de pampero, se asume que el servidor de correo es pampero (192.168.70.10). Alternativa igual de válida: cambiar la línea a @ IN MX 10 pampero. Lo que no vale es mail IN CNAME pampero: un MX no puede apuntar a un CNAME (named-checkzone avisa MX 'mail.it.itba.edu.ar' is a CNAME (illegal)).

- Propagación: el Serial tiene que subir o los secundarios no se enteran. Si recargás sin subirlo, el log dice zone serial (2026101101) unchanged. zone may fail to transfer to secondaries. Usá la fecha de hoy con formato AAAAMMDDNN (date +%Y%m%d01) y verificá que sea mayor al que vino.

- Tiempos: Refresh 604800 hacía que un secundario tardara hasta 7 días en enterarse; quedó en 900 (15 minutos) con Retry 300 (5 minutos). El Negative Cache TTL 604800 dejaba cacheado 7 días un "no existe"; quedó en 300. El \$TTL bajó de 604800 a 3600 para que los registros cacheados venzan antes.

- Si editás de nuevo con el servicio andando: subí el Serial y corré sudo rndc reload it.itba.edu.ar. zone reload up-to-date significa que el archivo no cambió.

- rndc: connect failed: 127.0.0.1#953: connection refused: named no está corriendo. sudo systemctl restart named y sudo journalctl -u named -n 30 --no-pager.

- zone 'it.itba.edu.ar': already exists previous definition: la zona quedó declarada dos veces en named.conf.local. Borrá una.

- dig sin @127.0.0.1 le pregunta al DNS del sistema, no a tu servidor.

*Probado en Ubuntu 24.04 (lab).*

## Crear una zona en BIND

Los dos piden instalar BIND y servir como autoridad la zona demiloo.com.ar, con contacto demiloo@leak.com.ar, registro A con validez de 1 hora, www como CNAME con 2 semanas y los MX copiados de proto.leak.com.ar con 1 semana. De un parcial a otro cambia si es una zona o dos, a qué IP apunta el registro A y si además piden SPF. La versión de una sola zona se tomó dos veces con el mismo enunciado (2022-2C y 2015-2C): está resuelta una sola vez.

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| BIND: servir la zona demiloo.com.ar (A, CNAME www, MX copiados, TTL por registro) | Zona demiloo.com.ar: A a la IP de foo.leak.com.ar, www CNAME a la raíz de la zona, MX de proto.leak.com.ar | Una sola zona. La IP del A y los MX se averiguan con dig y se copian al archivo. En la hoja figura como "Ejercicio 2". También tomado en Parcial 2015-2C · Ejercicio 2 (mismo enunciado, no cambia nada): el Ojo suma los errores de los apuntes de esa época (zona cerrada con } sin punto y coma, ns apuntando a localhost) y las equivalencias de TTL (1h, 1w, 2w). |
| BIND: zonas demiloo.com.br y demiloo.com.ar con A, CNAME www, MX de proto.leak.com.ar y SPF | Dos zonas, demiloo.com.br y demiloo.com.ar, con A, www CNAME, MX de proto.leak.com.ar y SPF | Un solo archivo /etc/bind/db.demiloo sirve a las dos zonas. Agrega el TXT de SPF con mail.damiloo.com.ar, mail.damiloo.com.br y los MX. El enunciado no dice a qué IP va el A: se usa la de tu máquina. |

### BIND: servir la zona demiloo.com.ar (A, CNAME www, MX copiados, TTL por registro)

**Tomado en:** Parcial 2022-2C · Ejercicio 4.

**También tomado en:** Parcial 2015-2C · Ejercicio 2 (mismo enunciado, no cambia nada)

> **Idea:** La idea es que BIND sea autoridad de la zona demiloo.com.ar, con un A a la IP de foo.leak.com.ar (1 hora), www como CNAME a la raíz (2 semanas) y los MX de proto.leak.com.ar con sus mismas prioridades (1 semana). El truco es sacar la IP y los MX con dig y poner el TTL en cada registro, antes del IN.

**Buscar:** BIND, named, servidor de nombres, zona, demiloo.com.ar, demiloo@leak.com.ar, foo.leak.com.ar, proto.leak.com.ar, autoridad, SOA, NS, registro A, validez de 1 hora, CNAME, www, raíz de la zona, raiz de la zona, validez de 2 semanas, MX, recibir correos electrónicos, recibir correos electronicos, mismos servidores de correos, manteniendo el orden, validez de 1 semana, TTL, 3600, 1209600, 604800, named.conf.local, named-checkzone, named-checkconf, dig, demostrar con dig, smtp20.proto.leak.com.ar, smtp30.proto.leak.com.ar, Ejercicio 2, CNAMEs, 2w, 1w, db.demiloo.com.ar, demostrar la correcta configuración utilizando dig, demostrar la correcta configuracion utilizando dig, MX 20, MX 21, IP_VM, IP_FOO, IP_DE_TU_VM, IP_DE_FOO, bad dotted quad, zona inversa, zona reversa, reversa, resolución inversa, resolucion inversa, PTR, in-addr.arpa, dig -x, secundario, slave, esclavo, primario, maestro, allow-transfer, also-notify, transferencia de zona, AXFR, Transfer failed

**Enunciado:** (en la hoja figura como "Ejercicio 2") Instale un servidor de nombres BIND y realize los cambios necesarios para servir la zona demiloo.com.ar considerando que: 1. su servidor de DNS será la autoridad para estas zonas. 2. la dirección de contacto con la persona responsable por la zona es demiloo@leak.com.ar. 3. demiloo.com.ar debe tener un registro A apuntando a la dirección IP de foo.leak.com.ar. El registro tendrá una validez de 1 hora. 4. el subdominio www debe ser registros CNAMEs apuntando a la raíz de la zona. Los registros tendrán una validez de 2 semanas. 5. la zona deben poder recibir correos electrónicos. El correo electrónico será atendido por los mismos servidores de correos que atienden los del dominio proto.leak.com.ar (manteniendo el orden). Estos registros tendrán una validez de 1 semana. Deberá demostrar la correcta configuración utilizando dig.

**Solución:**

> **1.** Instalá BIND si falta.

sudo apt install -y bind9 bind9-utils bind9-dnsutils

> **2.** Averiguá los datos que pide el enunciado y anotalos:

- Punto 3, la IP de foo.leak.com.ar: dig +short foo.leak.com.ar A (en el ejemplo, 76.76.21.241). Si antes de la IP salen nombres, son CNAME: la IP es la línea con números.

- Punto 5, los servidores de correo de proto.leak.com.ar, con su prioridad: dig +short proto.leak.com.ar MX (en el ejemplo, 20 smtp20.proto.leak.com.ar. y 21 smtp30.proto.leak.com.ar.).

- Punto 1, la IP de tu VM, que va en el NS: ip -br a (la de tu placa, no la 127.0.0.1, y sin lo que viene después de la barra: 192.168.1.50/24 se anota 192.168.1.50).

> **3.** Armá el archivo de zona /etc/bind/db.demiloo.com.ar con esos datos. Los TTL van por registro: 3600 es 1 hora, 1209600 son 2 semanas, 604800 es 1 semana. En el Serial va la fecha de hoy en formato AAAAMMDDNN (2026101101 es un ejemplo).

Hay que hacer sudo nano /etc/bind/db.demiloo.com.ar, borrar lo que tenga y pegar lo siguiente, cambiando IP_DE_TU_VM, IP_DE_FOO y las líneas MX por lo que anotaste (una línea MX por cada servidor, con la misma prioridad y el mismo nombre, con punto final):

\$TTL 86400

@ IN SOA ns.demiloo.com.ar. demiloo.leak.com.ar. (

2026101101 ; Serial

3600 ; Refresh

900 ; Retry

604800 ; Expire

3600 ) ; Negative cache TTL

@ IN NS ns.demiloo.com.ar.

ns IN A IP_DE_TU_VM

@ 3600 IN A IP_DE_FOO

www 1209600 IN CNAME demiloo.com.ar.

@ 604800 IN MX 20 smtp20.proto.leak.com.ar.

@ 604800 IN MX 21 smtp30.proto.leak.com.ar.

Con los datos de ejemplo, las dos líneas A quedan así:

ns IN A 192.168.1.50

@ 3600 IN A 76.76.21.241

Si te olvidás de cambiar un marcador, named-checkzone (paso 5) dice near 'IP_DE_TU_VM': bad dotted quad (o 'IP_DE_FOO') y no carga. Si copiaste la IP con la barra, dice near '192.168.1.50/24': bad dotted quad: borrá el /24.

> **4.** Declará la zona en /etc/bind/named.conf.local (el grep muestra si ya está, para no duplicarla).

grep -n 'zone "demiloo.com.ar"' /etc/bind/named.conf\*

Si no muestra nada, hay que hacer sudo nano /etc/bind/named.conf.local y agregar al final lo siguiente:

zone "demiloo.com.ar" {

type master;

file "/etc/bind/db.demiloo.com.ar";

};

> **5.** Validá y reiniciá.

named-checkzone demiloo.com.ar /etc/bind/db.demiloo.com.ar

sudo named-checkconf

sudo systemctl restart named

\# viejo: sudo service named restart (en versiones viejas el servicio se llama bind9)

zone demiloo.com.ar/IN: loaded serial 2026101101

OK

**Demostrar:**

dig @127.0.0.1 demiloo.com.ar SOA +noall +answer

dig @127.0.0.1 demiloo.com.ar NS +noall +answer

dig @127.0.0.1 demiloo.com.ar A +noall +answer

dig @127.0.0.1 www.demiloo.com.ar A +noall +answer

dig @127.0.0.1 demiloo.com.ar MX +noall +answer

dig @127.0.0.1 demiloo.com.ar A

La segunda columna es el TTL en segundos: 3600 en el A, 1209600 en el CNAME, 604800 en los MX. Del último dig mirá dos líneas: la de status: tiene que decir NOERROR y la que empieza con ;; flags: tiene que tener aa (autoridad). Más abajo hay otra con flags: (la de ; EDNS:): esa no cuenta.

demiloo.com.ar. 86400 IN SOA ns.demiloo.com.ar. demiloo.leak.com.ar. 2026101101 3600 900 604800 3600

demiloo.com.ar. 86400 IN NS ns.demiloo.com.ar.

demiloo.com.ar. 3600 IN A 76.76.21.241

www.demiloo.com.ar. 1209600 IN CNAME demiloo.com.ar.

demiloo.com.ar. 3600 IN A 76.76.21.241

demiloo.com.ar. 604800 IN MX 20 smtp20.proto.leak.com.ar.

demiloo.com.ar. 604800 IN MX 21 smtp30.proto.leak.com.ar.

;; -\>\>HEADER\<\<- opcode: QUERY, status: NOERROR, id: 17038

;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

Mostrá también los dos dig del paso 2: prueban de dónde salieron la IP y los MX.

**Ojo:**

- El 11/10/2026 foo.leak.com.ar y proto.leak.com.ar daban NXDOMAIN, así que los dig del paso 2 no devuelven nada con esos nombres. Si lo vuelven a tomar va a ser con otros dominios: cambiá los dos nombres en el paso 2. Los valores 76.76.21.241, smtp20 y smtp30 son los de la resolución del alumno.

- near eol: unexpected end of input en named-checkzone: quedó un registro A o MX sin dato (por ejemplo, borraste el marcador y no escribiste la IP). Completá el dato en el archivo del paso 3.

- Contacto demiloo@leak.com.ar se escribe demiloo.leak.com.ar. en el SOA: punto en lugar de @ y punto final. Sin el punto final (así estaba en el apunte del Parcial 2015-2C) el contacto queda demiloo.leak.com.ar.demiloo.com.ar..

- "Manteniendo el orden" es copiar las mismas prioridades y los mismos nombres que devuelve dig proto.leak.com.ar MX: no las inventes. En los apuntes eran 20 para smtp20 y 21 para smtp30 (no 30). Los nombres van con punto final. dig puede listar los MX en cualquier orden entre una consulta y otra: lo que vale es el número de prioridad.

- El CNAME a "la raíz de la zona" es demiloo.com.ar. con punto final (o @). No puede haber otro registro con el nombre www: da CNAME and other data.

- La raíz de la zona no puede ser CNAME: por eso el punto 3 pide un registro A.

- ns tiene que tener un A dentro de la zona. Si falta: NS 'ns.demiloo.com.ar' has no address records (A or AAAA) y la zona no carga. El alumno le puso 1.2.3.4 y los apuntes del Parcial 2015-2C localhost: va la IP real de la VM.

- TTL equivalentes: 3600 = 1h, 604800 = 1w = 7d, 1209600 = 2w = 14d. Van en la columna antes de IN.

- Aviso MX '...' (out of zone) has no addresses records (A or AAAA): named-checkzone no pudo resolver el servidor de correo externo. La zona carga igual.

- expected quoted string near (y una comilla curva) en named-checkconf: pegaste comillas tipográficas en la línea file (el apunte del alumno las tenía). Tienen que ser comillas rectas. En el nombre de la zona no avisa y la zona queda con otro nombre: reescribí las comillas a mano.

- missing ';' before '}' (o before 'zone', o before end of file) en named-checkconf: falta un punto y coma en named.conf.local. La zona se cierra con };; el apunte del Parcial 2015-2C la cerraba con } sola.

- zone 'demiloo.com.ar': already exists previous definition: la zona quedó declarada dos veces.

- loading from master file ... failed: file not found: la ruta del file no coincide con el archivo creado.

- Si cambiás el archivo con el servicio andando, subí el Serial y corré sudo rndc reload demiloo.com.ar.

- dig sin @127.0.0.1 le pregunta al DNS del sistema, no a tu servidor.

- Variante con dos zonas (demiloo.com.br y demiloo.com.ar): repetí los pasos 3 y 4 con el otro nombre y otro archivo. Si además piden SPF, agregá en cada zona @ IN TXT "v=spf1 a:mail.damiloo.com.ar a:mail.damiloo.com.br mx:demiloo.com.ar mx:demiloo.com.br -all" (los hosts están escritos damiloo en ese enunciado). Está resuelta entera, con SPF, en el Parcial 2013-2C · Ejercicio 2 (en este mismo tipo: Resueltos — DNS, 'Crear una zona en BIND').

- Variante con zona inversa (PTR, que dig -x devuelva el nombre de una IP): en named.conf.local va zone "1.168.192.in-addr.arpa" { type master; file "/etc/bind/db.192.168.1"; }; (los tres primeros números de la red, al revés). El archivo /etc/bind/db.192.168.1 lleva el \$TTL, el SOA y el NS del paso 3, y una línea por IP con el último número como nombre: 50 IN PTR ns.demiloo.com.ar. (para 192.168.1.50; el destino con punto final). Se valida con named-checkzone 1.168.192.in-addr.arpa /etc/bind/db.192.168.1 y sudo named-checkconf, se aplica con sudo systemctl restart named y se demuestra con dig @127.0.0.1 -x 192.168.1.50 +noall +answer. Si la respuesta termina en .1.168.192.in-addr.arpa., al PTR le faltó el punto final (named-checkzone no avisa).

- Variante con secundario (otro servidor que copia la zona; IP_PRI e IP_SEC son las IP del primario y del secundario): en el primario, dentro del zone del paso 4, allow-transfer { IP_SEC; }; also-notify { IP_SEC; };. En named.conf.local del secundario, zone "demiloo.com.ar" { type slave; masters { IP_PRI; }; file "db.demiloo.com.ar"; }; (sin ruta: lo guarda en /var/cache/bind). En los dos, sudo named-checkconf y sudo systemctl restart named. Se demuestra con dig @IP_SEC demiloo.com.ar SOA +short, que tiene que dar el mismo Serial que el primario; si subís el Serial y recargás el primario, el secundario lo toma solo. dig @IP_PRI demiloo.com.ar AXFR (la transferencia de zona) responde solo a las IP de allow-transfer: desde otra da Transfer failed.

- Se probó con una zona local que imita las respuestas de leak.com.ar, porque los nombres reales ya no existen.

*Probado en Ubuntu 24.04 (lab). La zona inversa y el secundario se probaron con dos named 9.18 de Ubuntu 24.04 en la misma máquina, en otros puertos. Sin probar: el apt install (el lab no permite instalar; BIND ya venía instalado) y el secundario entre dos VM en el puerto 53.*

### BIND: zonas demiloo.com.br y demiloo.com.ar con A, CNAME www, MX de proto.leak.com.ar y SPF

**Tomado en:** Parcial 2013-2C · Ejercicio 2.

> **Idea:** La idea es que BIND sea autoridad de demiloo.com.ar y demiloo.com.br, con A a la IP de tu máquina (1 hora), www como CNAME (2 semanas), los MX de proto.leak.com.ar en el mismo orden (1 semana) y SPF como TXT v=spf1 en la raíz. El truco es usar nombres relativos para que un solo archivo sirva a las dos zonas.

**Contexto del parcial:** en el Parcial 2013-2C · Ejercicio 1 (Resueltos — nginx, 'Proxy reverso: un dominio sirve el contenido de otro') se instala en tu computadora un nginx que atiende demiloo.com.ar y demiloo.com.br. Por eso acá los registros A de las dos zonas apuntan a la IP de tu máquina.

**Buscar:** BIND, bind9, named, servidor de nombres, zona, zonas, demiloo.com.br, demiloo.com.ar, autoridad, autoritativo, SOA, persona responsable, demiloo@leak.com.ar, registro A, validez de 1 hora, 3600, CNAME, www, 2 semanas, 2w, 1209600, correos electrónicos, correos electronicos, MX, proto.leak.com.ar, manteniendo el orden, 1 semana, 1w, 604800, SPF, reputación, reputacion, TXT, v=spf1, -all, mail.damiloo.com.ar, mail.damiloo.com.br, damiloo, named.conf.local, db.demiloo, named-checkzone, named-checkconf, rndc reload, dig

**Enunciado:** "Instale un servidor de nombres BIND y realice los cambios necesarios para servir las zonas demiloo.com.br y demiloo.com.ar considerando que 1. su servidor de DNS será la autoridad para estas zonas 2. la dirección de contacto con la persona responsable por la zona es demiloo@leak.com.ar 3. demiloo.com.br y demiloo.com.ar deben resolver registros A. Los registros tendrán una validez de 1 hora. 4. los subdominios www deben ser registros CNAMEs. Los registros tendrán una validez de 2 semanas. 5. las zonas deben poder recibir correos electrónicos. El correo electrónico será atendido por los mismos servidores de correos que atienden los del dominio proto.leak.com.ar (manteniendo el orden). Estos registros tendrán una validez de 1 semana. 6. se desea proteger la reputación de los dominios utilizando SPF. Los unicos hosts que se espera que envíen correos electronicos de estos dominios son: mail.damiloo.com.ar, mail.damiloo.com.br, y los servidores que reciben los mails para demiloo.com.ar y para demiloo.com.br"

**Solución:**

> **1.** Instalá BIND y anotá los datos que pide el enunciado.

sudo apt update

sudo apt install -y bind9 bind9-dnsutils

- Puntos 1 y 3, tu IP, que va en el NS y en los registros A (es la máquina donde corre el nginx del Parcial 2013-2C · Ejercicio 1, que está en Resueltos — nginx, 'Proxy reverso: un dominio sirve el contenido de otro'): ip -br a (la de tu placa, no la 127.0.0.1, y sin lo que viene después de la barra: 172.17.0.9/16 se anota 172.17.0.9).

- Punto 5, los MX de proto.leak.com.ar con sus prioridades: dig +short proto.leak.com.ar MX (en el ejemplo, 20 smtp20.proto.leak.com.ar. y 21 smtp30.proto.leak.com.ar.).

> **2.** Creá el archivo de zona. Sirve para las dos zonas porque usa nombres relativos (@ es el nombre de la zona). Ruta: /etc/bind/db.demiloo.

Hay que hacer sudo nano /etc/bind/db.demiloo, borrar lo que tenga y pegar lo siguiente, cambiando IP_DE_TU_VM (está dos veces) por la IP que anotaste:

\$TTL 3600

@ IN SOA ns demiloo.leak.com.ar. (

2013100201 ; serial: subilo cada vez que edites

3600 ; refresh

900 ; retry

604800 ; expire

3600 ) ; TTL negativo

; punto 1: este servidor es la autoridad

@ IN NS ns

ns IN A IP_DE_TU_VM

; punto 3: registro A, 1 hora

@ 3600 IN A IP_DE_TU_VM

; punto 4: www es CNAME, 2 semanas

www 2w IN CNAME @

; punto 5: mismos MX que proto.leak.com.ar, mismo orden, 1 semana

@ 1w IN MX 20 smtp20.proto.leak.com.ar.

@ 1w IN MX 21 smtp30.proto.leak.com.ar.

; punto 6: SPF (va como TXT, entre comillas)

@ IN TXT "v=spf1 a:mail.damiloo.com.ar a:mail.damiloo.com.br mx:demiloo.com.ar mx:demiloo.com.br -all"

> **3.** Con la IP del ejemplo, las dos líneas que llevan tu IP quedan así. Si queda algún IP_DE_TU_VM sin cambiar, named-checkzone (paso 6) dice near 'IP_DE_TU_VM': bad dotted quad y no carga. Si copiaste la IP con la barra, dice near '172.17.0.9/16': bad dotted quad: borrá el /16.

ns IN A 172.17.0.9

@ 3600 IN A 172.17.0.9

> **4.** Las líneas MX traen los datos de ejemplo (20 smtp20, 21 smtp30). Si dig +short proto.leak.com.ar MX devolvió otros servidores u otras prioridades, corregí las líneas MX con sudo nano /etc/bind/db.demiloo (una por servidor, número y nombre tal cual, con punto final).
>
> **5.** Declará las dos zonas. Ruta: /etc/bind/named.conf.local (se agrega al final). Cada zona se agrega solo si no está: si el grep muestra la línea donde figura, ya estaba y no hay que agregarla.

grep -n 'zone "demiloo.com.ar"' /etc/bind/named.conf\*

Si no muestra nada, hay que hacer sudo nano /etc/bind/named.conf.local y agregar al final lo siguiente:

zone "demiloo.com.ar" {

type master;

file "/etc/bind/db.demiloo";

};

grep -n 'zone "demiloo.com.br"' /etc/bind/named.conf\*

Si no muestra nada, hay que hacer sudo nano /etc/bind/named.conf.local y agregar al final lo siguiente:

zone "demiloo.com.br" {

type master;

file "/etc/bind/db.demiloo";

};

> **6.** Validá y reiniciá.

sudo named-checkconf

sudo named-checkzone demiloo.com.ar /etc/bind/db.demiloo

sudo named-checkzone demiloo.com.br /etc/bind/db.demiloo

sudo systemctl restart named

\# mismo servicio con el nombre viejo: sudo systemctl restart bind9 / sudo service bind9 restart

\# (si dice "Unit bind9.service not found", el nombre que vale es named)

\# si solo cambiaste la zona (y subiste el serial): sudo rndc reload

**Demostrar:**

dig @127.0.0.1 demiloo.com.ar SOA +noall +answer

dig @127.0.0.1 demiloo.com.ar NS +noall +answer

dig @127.0.0.1 demiloo.com.ar A +noall +answer

dig @127.0.0.1 www.demiloo.com.ar A +noall +answer

dig @127.0.0.1 demiloo.com.ar MX +noall +answer

dig @127.0.0.1 demiloo.com.ar TXT +noall +answer

dig @127.0.0.1 demiloo.com.br SOA +noall +answer

dig @127.0.0.1 demiloo.com.br NS +noall +answer

dig @127.0.0.1 demiloo.com.br A +noall +answer

dig @127.0.0.1 www.demiloo.com.br A +noall +answer

dig @127.0.0.1 demiloo.com.br MX +noall +answer

dig @127.0.0.1 demiloo.com.br TXT +noall +answer

dig @127.0.0.1 demiloo.com.br A

Del último dig mirá la línea que empieza con ;; flags:, que tiene que tener aa (la de ; EDNS: version: 0, flags:; es otra cosa). Lo demás:

demiloo.com.ar. 3600 IN SOA ns.demiloo.com.ar. demiloo.leak.com.ar. 2013100201 3600 900 604800 3600

demiloo.com.ar. 3600 IN NS ns.demiloo.com.ar.

demiloo.com.ar. 3600 IN A 172.17.0.9

www.demiloo.com.ar. 1209600 IN CNAME demiloo.com.ar.

demiloo.com.ar. 3600 IN A 172.17.0.9

demiloo.com.ar. 604800 IN MX 20 smtp20.proto.leak.com.ar.

demiloo.com.ar. 604800 IN MX 21 smtp30.proto.leak.com.ar.

demiloo.com.ar. 3600 IN TXT "v=spf1 a:mail.damiloo.com.ar a:mail.damiloo.com.br mx:demiloo.com.ar mx:demiloo.com.br -all"

(lo mismo para demiloo.com.br)

;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

Lo que mira el corrector: flag aa (autoridad), TTL 3600 en A, 1209600 en CNAME, 604800 en MX, el contacto en el SOA y el TXT con SPF. dig puede listar los dos MX en cualquier orden: lo que vale es el número de prioridad.

**Ojo:**

- Los MX del ejemplo (20 smtp20, 21 smtp30) salen de apuntes de la época. Hoy proto.leak.com.ar no resuelve: en el examen copiá lo que devuelva dig, con las mismas prioridades ("manteniendo el orden").

- El enunciado no dice a qué IP apuntan los registros A: se usa la de tu máquina, que es donde corre el nginx de Demiloo (el del Parcial 2013-2C · Ejercicio 1).

- En la hoja los hosts del SPF figuran como mail.damiloo (con a), distinto de demiloo. La receta copia el enunciado. Si en el examen te confirman que es un error de tipeo, cambiá damiloo por demiloo y agregá mail IN A IP_DEL_MAIL a la zona.

- SPF es un registro TXT que empieza con v=spf1. a:HOST autoriza las IP de ese host, mx:DOMINIO autoriza a los servidores MX de ese dominio ("los servidores que reciben los mails") y -all rechaza al resto. El tipo de registro SPF (99) está obsoleto: no lo uses.

- El apunte de alumno ponía el TXT colgado de smtp20.proto.leak.com.ar.: está mal, va en la raíz de cada zona (@).

- El contacto demiloo@leak.com.ar se escribe demiloo.leak.com.ar.: punto en lugar de arroba y punto final.

- Nombres fuera de la zona siempre con punto final. Sin el punto, smtp20.proto.leak.com.ar se convierte en smtp20.proto.leak.com.ar.demiloo.com.ar.

- www es CNAME y no puede tener otro registro con el mismo nombre. Un CNAME en la raíz (@) da CNAME and other data y la zona no carga.

- has no addresses records (A or AAAA) con (out of zone) en named-checkzone es solo un aviso: los MX viven en otra zona. Si termina en OK, cargó.

- En named.conf.local cada bloque zone termina con };. El apunte lo cerraba con } solo y named no arranca.

- /etc/bind/named.conf.local:20: zone 'demiloo.com.ar': already exists previous definition: /etc/bind/named.conf.local:10 (lo dice named-checkconf, y systemctl restart named falla con Job for named.service failed): la zona quedó declarada dos veces, por pegar el bloque sin el grep de adelante o por una práctica anterior. El primer número (20) es la línea del bloque repetido y el último (10) la del original: sudo nano /etc/bind/named.conf.local, borrá el bloque repetido entero (desde zone hasta };) y repetí el paso 6.

- Si en el paso 5 el grep mostró una línea, esa zona ya estaba declarada y no se agregó nada. Mirá con cat /etc/bind/named.conf.local que su file sea /etc/bind/db.demiloo; si apunta a otro archivo, corregilo con nano.

- Si editás la zona y no ves el cambio: faltó sudo rndc reload (o sudo systemctl restart named). Subí el serial igual en cada edición: es lo que miran los servidores secundarios.

- Para que tu propia máquina resuelva con este servidor: dig @127.0.0.1 ... siempre funciona; no hace falta tocar /etc/resolv.conf para demostrar.

*Probado en Ubuntu 24.04 (lab). Sin probar: el apt install (el lab ya trae BIND) y los MX reales de proto.leak.com.ar (hoy da NXDOMAIN; se cargaron los valores históricos).*

## Consultas con dig

Se responde consultando con dig, sin configurar ningún servidor. De un parcial a otro cambia qué registro se pregunta y sobre qué dominio. Acá hay un solo caso; si te piden los MX o el SPF de un dominio, mirá también el Recuperatorio 2020-1C · Ejercicio I (Resueltos — Mail, 'MX y SPF de un dominio').

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| dig: servidores SMTP que reciben mail de gmail.com y nameservers que intervienen al resolver www.google.com | a\) Los servidores SMTP que reciben el mail de info@gmail.com (MX de gmail.com). b) Todos los nameservers que intervienen al resolver www.google.com desde un servidor sin forwarders y con la caché vacía | Se hace desde el host G, entrando por ssh, porque tu máquina no tiene Internet. El punto b) sale con dig +trace: servidores raíz, TLD com y autoritativos de google.com. |

### dig: servidores SMTP que reciben mail de gmail.com y nameservers que intervienen al resolver www.google.com

**Tomado en:** Parcial 2011 · Ejercicio 3.

> **Idea:** La idea es responder con dig desde el host G, entrando por ssh porque tu máquina no tiene Internet: los MX de gmail.com son los SMTP que reciben el mail, y dig +trace muestra los nameservers que intervienen al resolver www.google.com en forma iterativa (raíz, TLD com y autoritativos de google.com). Si se corta, se le indica otro resolver con @8.8.8.8.

**Contexto del parcial:** tu máquina está en la red de los alumnos (192.168.10.0/25) y el host G (192.168.20.1 de tu lado, 10.6.0.127 del lado del laboratorio 10.6.0.0/24) no encamina paquetes ni hace NAT. Tenés una cuenta SSH en G con usuario user y contraseña pdcpass.

**Buscar:** dig, MX, servidores SMTP, reciben los mails, info@gmail.com, gmail.com, no utiliza forwarders, cache vacio, caché vacío, nameservers que entran en juego, www.google.com, dig +trace, +norecurse, root servers, root-servers.net, gtld-servers.net, ns1.google.com, resolución iterativa, resolucion iterativa, NS, dig . NS

**Enunciado:** "Desde G o Host en 10.6.0.0/24: a) Obtener los nombres de los servidores SMTP que reciben los mails destinados a info@gmail.com. b) Suponga que posee un servidor de nombres que no utiliza forwarders y tiene su cache vacio, obtener todos los nameservers que entran en juego al querer resolver www.google.com."

**Depende de:** Parcial 2011 · Ejercicio 1 (Resueltos — Red, 'Llegar a una red detrás de un gateway y descubrir sus servicios'). Ahí se configura la interfaz y la ruta a 192.168.20.1; sin eso el ssh a G del paso 1 no sale.

**Solución:**

> **1.** Tu host no tiene Internet (G no encamina): entrá a G y trabajá desde ahí.

ssh user@192.168.20.1

> **2.** a) Los que reciben el mail de un dominio son sus registros MX.

dig +short gmail.com MX

\# equivalente viejo: host -t MX gmail.com / nslookup -type=MX gmail.com

dig los lista en cualquier orden: acá están ordenados por el número (la prioridad).

5 gmail-smtp-in.l.google.com.

10 alt1.gmail-smtp-in.l.google.com.

20 alt2.gmail-smtp-in.l.google.com.

30 alt3.gmail-smtp-in.l.google.com.

40 alt4.gmail-smtp-in.l.google.com.

> **3.** b) Un servidor sin forwarders y sin caché resuelve en forma iterativa: pregunta a un servidor raíz, que lo deriva a los del TLD com, que lo derivan a los autoritativos de google.com. dig +trace hace ese recorrido y muestra los nameservers de cada nivel.

dig +trace +nodnssec www.google.com

. 87203 IN NS a.root-servers.net.

(... hasta m.root-servers.net.)

;; Received 239 bytes from IP_DE_TU_RESOLVER#53(IP_DE_TU_RESOLVER) in 47 ms

com. 172800 IN NS a.gtld-servers.net.

(... hasta m.gtld-servers.net.)

;; Received 839 bytes from 192.58.128.30#53(j.root-servers.net) in 31 ms

google.com. 172800 IN NS ns1.google.com.

(... hasta ns4.google.com.)

;; Received 291 bytes from 192.54.112.30#53(h.gtld-servers.net) in 170 ms

www.google.com. 300 IN A 142.251.151.119

Respuesta: los 13 servidores raíz (a hasta m .root-servers.net), los 13 del TLD com (a hasta m .gtld-servers.net) y los 4 autoritativos de google.com (ns1 a ns4 .google.com). En cada paso se usa uno solo de cada grupo.

> **4.** El mismo recorrido a mano, un salto por comando (lo que hace un servidor recursivo):

dig @a.root-servers.net www.google.com +norecurse

dig @a.gtld-servers.net www.google.com +norecurse

dig @ns1.google.com www.google.com +norecurse

\# si esos nombres no resuelven, las mismas tres consultas por IP:

\# dig @198.41.0.4 www.google.com +norecurse

\# dig @192.5.6.30 www.google.com +norecurse

\# dig @216.239.32.10 www.google.com +norecurse

Las dos primeras responden con una lista de NS (la derivación al nivel siguiente) y la tercera con los registros A y el flag aa.

**Demostrar:** la salida de dig +short gmail.com MX y la de dig +trace, marcando los tres grupos de NS.

**Ojo:**

- dig +trace muestra una sola línea ;; Received 17 bytes from ... (pocos bytes, sin la lista de NS) y no sigue: el resolver local no entrega la lista de servidores raíz. Indicá otro: dig @8.8.8.8 +trace +nodnssec www.google.com.

- La versión del apunte (dig . NS; dig com NS; dig google.com NS) lista los mismos tres grupos preguntándole al resolver local. Sirve, pero no muestra quién deriva a quién. Si dig . NS vuelve vacío, agregale @8.8.8.8.

- En a) responden los MX de gmail.com, no los de google.com. El número es la preferencia: el más chico se prueba primero.

- Sin +nodnssec la salida de +trace trae además registros RRSIG, DS y NSEC3 que tapan lo importante.

*Probado en Ubuntu 24.04 (lab). Sin probar: dig +trace contra el resolver de una VM real (con el resolver interno de Docker se queda en "Received 17 bytes"; con @8.8.8.8 hace el recorrido completo).*

# Resueltos — Mail (SMTP, POP3, MX y SPF)

## Enviar un mail a mano por SMTP y leerlo por POP3

En los tres parciales que lo tomaron hay que armar un mensaje completo (Date, From, To, Subject, Message-ID, headers MIME y un header X-Parcial) y entregarlo hablando SMTP a mano con nc -C al puerto 25. De un parcial a otro cambian el destinatario y el servidor, el valor de X-Parcial, de dónde sale el asunto, cómo se codifica ¡Hola mundo! (base64 o quoted-printable), si hay imagen adjunta y si después hay que leerlo por POP3. Hay dos resoluciones: el Parcial 2015 (primer parcial) · Ejercicio 1 es el mismo mensaje que el Parcial 2016-2C · Ejercicio 1 con otros valores y sin POP3, y está como variante dentro de ese.

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| Mail por SMTP a mano: header X-Parcial, asunto y cuerpo con ¡, base64, y lectura por POP3 | Mail a {legajo}@mail.sebikul.com con X-Parcial: 2024/2, asunto ¡Buenos días!, texto ¡Hola mundo! y remitente con nombre, apellido y cuenta del ITBA. Verificarlo por POP3 en mail.sebikul.com (usuario y clave: la misma dirección) | Prohibido quoted-printable: asunto como encoded-word en base64 y cuerpo en base64. Sin adjunto; en sus Variantes está el mismo mail con imagen adjunta y todo en base64, sin quoted-printable. Asumir SMTPUTF8 vale solo 1.0 punto. Trae cómo escribir ¡ sin teclado |
| Enviar un mail (X-Parcial: 2016/2, Subject sacado de un registro TXT, imagen adjunta) y leerlo por POP3 | Mail a LEGAJO@proto.leak.com.ar con X-Parcial: 2016/2, texto ¡Hola mundo! e imagen adjunta header_bg.gif. Leerlo por POP3 con el usuario pdc-LEGAJO y mostrar la imagen | El Subject sale de un registro TXT (google-public-dns-a.google.com). El MX de número más bajo (smtp20) apunta a 127.0.0.1: va smtp30. Mensaje multipart: texto en quoted-printable y adjunto en base64, que después se recupera de la sesión POP3 (si prohíben quoted-printable, se cambia solo la parte de texto, que pasa a base64 como en la variante con adjunto de 2024: lo explica el Ojo de este ejercicio). También tomado en Parcial 2015 (primer parcial) · Ejercicio 1: casilla del ITBA, X-Parcial: 2015/1, asunto fijo Ejercicio 1, servidor el MX de menor número de itba.edu.ar y sin lectura por POP3 |

### Mail por SMTP a mano: header X-Parcial, asunto y cuerpo con ¡, base64, y lectura por POP3

**Tomado en:** Parcial 2024-2C · Ejercicio 4.

> **Idea:** La idea es mandar a mano por SMTP, con nc -C al puerto 25, un mail a {legajo}@mail.sebikul.com con el header X-Parcial: 2024/2, asunto ¡Buenos días! y texto ¡Hola mundo!, y verificar que llegó por POP3. El truco es no usar quoted-printable ni depender de SMTPUTF8: el asunto va como encoded-word =?UTF-8?B?...?= y el cuerpo en base64.

**Buscar:** correo electrónico, correo electronico, mail, SMTP, POP3, netcat, nc -C, mail.sebikul.com, legajo, {legajo}@mail.sebikul.com, 40123@mail.sebikul.com, X-Parcial, 2024/2, Subject, título, titulo, ¡Buenos días!, Buenos dias, ¡Hola mundo!, Hola mundo, remitente, From, nombre apellido, cuenta de correo del ITBA, estándares, estandares, buenas prácticas, buenas practicas, quoted printable, quoted-printable, MUAs, base64, Content-Transfer-Encoding, MIME-Version, charset UTF-8, encoded-word, =?UTF-8?B?, SMTPUTF8, EHLO, MAIL FROM, RCPT TO, DATA, USER, PASS, STAT, LIST, RETR, wqFCdWVub3MgZMOtYXMh, wqFIb2xhIG11bmRvIQ0K, dead keys, teclas muertas, man pages, man iso_8859-1, carácter ¡, caracter ¡, signo de apertura de exclamación, signo de apertura de exclamacion, INVERTED EXCLAMATION MARK, no lo puede escribir, teclado, printf '\xc2\xa1', wqE=, Ejercicio IV, netcat-openbsd, apt install, command not found, Plaintext authentication disallowed, non-secure, cifrado, STARTTLS, STLS, openssl s_client, -starttls pop3, puerto 995, pop3s, --ssl-reqd, AUTH PLAIN, autenticación SMTP, autenticacion SMTP, Authentication required, 530 5.7.0, 235 2.7.0, puerto 587, IMAP, puerto 143, a1 LOGIN, SELECT INBOX, FETCH, BODY\[\], LOGOUT, adjunto, attachment, imagen adjunta, adjunto sin quoted-printable, multipart/mixed, boundary, MIXTO-7207, Content-Disposition, base64 -w 76, header_bg.gif, iter_attachments, KeyError, KeyError: 'multipart/mixed', PRIVACYREQUIRED, puerto 993, imaps, Authentication successful, Relay access denied, Must issue a STARTTLS command first, 554 5.7.1, No such file or directory, recibido.eml, certificado autofirmado, RENEGOTIATING, 535 5.7.8, authentication failed, nano mail.txt, Ctrl+R, insertar archivo, adjunto.b64, ADJUNTO_EN_BASE64, tee sesion.txt, base64 -d -i, curl smtp, --mail-from, --mail-rcpt, date -R, ip -br a, imagen.b64

**Enunciado:** Envíe un correo electrónico cumpliendo todos los estándares y buenas prácticas que cumpla con los siguientes requisitos: a. Contenga el header X-Parcial con el valor 2024/2. b. Contenga un título (subject): ¡Buenos días! c. Contenga como remitente su nombre, apellido y cuenta de correo del ITBA. d. Contenga el texto: ¡Hola mundo! e. El correo debe entregarse a la dirección {legajo}@mail.sebikul.com donde {legajo} es número de legajo. Por ejemplo si su legajo 40123 entonces deberá entregar el correo a la dirección 40123@mail.sebikul.com. f. Debe poder ser leido por MUAs que no soporten la codificacion quoted printable. Debe verificar su recepción utilizando el protocolo POP3. Para ello dispone de un servidor POP3 en el host mail.sebikul.com. El nombre de usuario es {legajo}@mail.sebikul.com y la password será el mismo valor que el usuario. Resolucion parcial: 1.0 puntos si asume que el servidor soporta SMTPUTF8. Nota al pie: Es importante que se incluya el carácter "¡". Si no lo puede escribir el caracter en su teclado (se puede recurrir a las dead keys) puede recurrir a las man pages del sistema, o a copiarlo de (la nota está cortada ahí en la fuente).

**Solución:**

> **1.** Anotá los dos datos que hay que averiguar:

- La fecha, para el header Date: corré date -R y copiá la salida (en el ejemplo, Sun, 11 Oct 2026 20:04:44 +0000).

- La IP de tu VM, para el EHLO del paso 3: corré ip -br a y anotá la de tu placa, no la 127.0.0.1, sin la barra ni lo que viene después (en el ejemplo, la placa muestra 172.17.0.4/16 y anotás 172.17.0.4).

Armá el mensaje en mail.txt. Hay que hacer nano mail.txt, borrar lo que tenga y pegar lo siguiente, cambiando FECHA por la salida de date -R, NOMBRE APELLIDO y USUARIO por tu nombre y tu cuenta del ITBA, y LEGAJO por tu legajo. Si tu nombre lleva tilde o ñ, no lo pegues así: va codificado en base64, como muestra el Ojo (From: =?UTF-8?B?Sm9zw6kgUMOpcmV6?= \<USUARIO@itba.edu.ar\> para José Pérez). NUMERO es cualquier número que no hayas usado en otro mail (por ejemplo, la hora sin los dos puntos): hace único al Message-ID. Todo el archivo queda en ASCII: el asunto va como encoded-word en base64 y el cuerpo en base64.

Date: FECHA

From: NOMBRE APELLIDO \<USUARIO@itba.edu.ar\>

To: \<LEGAJO@mail.sebikul.com\>

Subject: =?UTF-8?B?wqFCdWVub3MgZMOtYXMh?=

Message-ID: \<NUMERO.LEGAJO@itba.edu.ar\>

X-Parcial: 2024/2

MIME-Version: 1.0

Content-Type: text/plain; charset=UTF-8

Content-Transfer-Encoding: base64

wqFIb2xhIG11bmRvIQ0K

Guardá con Ctrl+O y Enter, y salí con Ctrl+X. Mirá con cat mail.txt que no quede ningún marcador (FECHA, NOMBRE APELLIDO, USUARIO, NUMERO, LEGAJO), y con grep -nP '\[^\x00-\x7F\]' mail.txt que todo sea ASCII: no tiene que mostrar nada (si muestra una línea, ahí quedó una tilde, ñ o ¡ sin codificar). Con los datos del ejemplo, los headers quedan como en el RETR de Demostrar.

> **2.** De dónde salen los dos base64 (para mostrarlo o rehacerlos si cambia el texto).

printf '¡Buenos días!' \| base64

printf '¡Hola mundo!\r\n' \| base64

echo wqFCdWVub3MgZMOtYXMh \| base64 -d; echo

echo wqFIb2xhIG11bmRvIQ0K \| base64 -d

wqFCdWVub3MgZMOtYXMh

wqFIb2xhIG11bmRvIQ0K

¡Buenos días!

¡Hola mundo!

Si nc o curl dan command not found (en Debian curl no viene instalado), instalalos antes del paso 3. file lo usa la variante con adjunto:

sudo apt update && sudo apt install -y netcat-openbsd curl file

> **3.** Envialo por SMTP a mano. Abrí la conexión (-C hace que cada Enter mande CRLF, como pide SMTP):

nc -C mail.sebikul.com 25

Esperá el saludo 220 y tipeá estas líneas de a una, esperando la respuesta de cada una. En el EHLO va, entre corchetes, la IP que anotaste en el paso 1; USUARIO y LEGAJO son los mismos que pusiste en el mensaje:

EHLO \[IP_DE_TU_VM\]

MAIL FROM:\<USUARIO@itba.edu.ar\>

RCPT TO:\<LEGAJO@mail.sebikul.com\>

DATA

> **4.** Cuando contesta 354, pegá el mensaje: mostralo con cat mail.txt en otra pestaña de la terminal, seleccioná desde Date: hasta la última línea, copialo con Ctrl+Shift+C y pegalo en la sesión con Ctrl+Shift+V. Si después de pegar el cursor quedó al final de wqFIb2xhIG11bmRvIQ0K, apretá Enter: el punto tiene que ir solo en su línea. Después tipeá una línea que solo tenga un punto, que cierra el mensaje, y QUIT. Si después del 221 no vuelve el prompt, salí con Ctrl+C:

.

QUIT

Así se ve la sesión entera. Vos tipeás EHLO, MAIL FROM, RCPT TO, DATA, el punto y QUIT, y pegás el mensaje; el resto lo contesta el servidor:

220 mail.sebikul.com ESMTP Postfix (Ubuntu)

EHLO \[172.17.0.4\]

250-mail.sebikul.com

250-PIPELINING

250-8BITMIME

250 CHUNKING

MAIL FROM:\<USUARIO@itba.edu.ar\>

250 2.1.0 Ok

RCPT TO:\<40123@mail.sebikul.com\>

250 2.1.5 Ok

DATA

354 End data with \<CR\>\<LF\>.\<CR\>\<LF\>

(el contenido de mail.txt)

.

250 2.0.0 Ok: queued as CCBE427689

QUIT

221 2.0.0 Bye

> **5.** Verificá la recepción por POP3. Abrí la conexión y tipeá de a una línea, esperando cada respuesta. Usuario y clave son la misma dirección. En RETR va el número del último mensaje que mostró LIST. Si después del +OK Logging out. no vuelve el prompt, salí con Ctrl+C.

nc -C mail.sebikul.com 110

USER LEGAJO@mail.sebikul.com

PASS LEGAJO@mail.sebikul.com

STAT

LIST

RETR 1

QUIT

**Demostrar:** la sesión SMTP con el 250 2.0.0 Ok: queued y la sesión POP3 con el mensaje completo. Así se ve la sesión POP3: vos tipeás USER, PASS, STAT, LIST, RETR 1 y QUIT; el resto lo contesta el servidor:

+OK Dovecot (Ubuntu) ready.

USER 40123@mail.sebikul.com

+OK

PASS 40123@mail.sebikul.com

+OK Logged in.

STAT

+OK 1 628

LIST

+OK 1 messages:

1 628

.

RETR 1

+OK 628 octets

Return-Path: \<USUARIO@itba.edu.ar\>

X-Original-To: 40123@mail.sebikul.com

Delivered-To: 40123@mail.sebikul.com

Received: from \[172.17.0.4\] (localhost \[127.0.0.1\])

by mail.sebikul.com (Postfix) with ESMTP id E11E427C9F

for \<40123@mail.sebikul.com\>; Sun, 11 Oct 2026 20:04:46 +0000 (UTC)

Date: Sun, 11 Oct 2026 20:04:44 +0000

From: NOMBRE APELLIDO \<USUARIO@itba.edu.ar\>

To: \<40123@mail.sebikul.com\>

Subject: =?UTF-8?B?wqFCdWVub3MgZMOtYXMh?=

Message-ID: \<200444.40123@itba.edu.ar\>

X-Parcial: 2024/2

MIME-Version: 1.0

Content-Type: text/plain; charset=UTF-8

Content-Transfer-Encoding: base64

wqFIb2xhIG11bmRvIQ0K

.

QUIT

+OK Logging out.

Para mostrar que se lee bien, decodificá el asunto y el cuerpo que se ven en el RETR (los headers From, To y X-Parcial ya se leen tal cual):

echo wqFCdWVub3MgZMOtYXMh \| base64 -d; echo

echo wqFIb2xhIG11bmRvIQ0K \| base64 -d

¡Buenos días!

¡Hola mundo!

**Ojo:**

- Punto f: nada de quoted-printable. El cuerpo va con Content-Transfer-Encoding: base64 y el asunto con encoded-word tipo B (=?UTF-8?B?...?=). El tipo Q (=?UTF-8?Q?=C2=A1Buenos_d=C3=ADas!?=) es la variante quoted-printable de los headers: no la uses.

- Escribir Subject: ¡Buenos días! con los caracteres crudos solo es válido si el servidor soporta SMTPUTF8, y eso vale 1.0 punto en vez de 2.5. Lo mismo para el cuerpo en 8 bits.

- El apunte del alumno tenía varios errores: asunto crudo sin codificar, Hola Mundo con mayúscula (el enunciado dice ¡Hola mundo!), una parte HTML cuyo base64 venía de texto en quoted-printable (dir=3D"ltr"), HELO en lugar de EHLO, y los headers Date y Message-ID copiados de otro mail.

- Si tu nombre lleva tilde o ñ, también va codificado: printf 'José Pérez' \| base64 da Sm9zw6kgUMOpcmV6 y la línea queda From: =?UTF-8?B?Sm9zw6kgUMOpcmV6?= \<USUARIO@itba.edu.ar\>.

- Headers obligatorios o esperados: Date, From, To, Subject, Message-ID, MIME-Version, Content-Type con charset, Content-Transfer-Encoding. Entre headers y cuerpo va una línea en blanco.

- MAIL FROM:\<...\> y RCPT TO:\<...\> van sin espacio después de los dos puntos y con los signos menor y mayor.

- 503 5.5.1 Error: need MAIL command: mandaste RCPT antes de MAIL FROM (o pegaste todo junto sin esperar).

- 550 5.1.1 \<...\>: Recipient address rejected: User unknown: legajo mal escrito en RCPT TO.

- -ERR \[AUTH\] Authentication failed. en POP3: usuario y clave son la dirección completa, con @mail.sebikul.com.

- Tipeá los comandos de a uno y esperá cada respuesta: si pegás todo el diálogo de golpe, algunos servidores cortan por mandar comandos antes de tiempo. Lo único que se pega de una vez es el mensaje, después del 354.

- Sin -C, nc manda solo LF y el servidor puede no reconocer el . final. Con telnet no hace falta (telnet mail.sebikul.com 25 ya manda CRLF).

- Si el servidor exige cifrado: SMTP con openssl s_client -starttls smtp -crlf -quiet -connect mail.sebikul.com:25 (o puerto 587) en lugar de nc -C mail.sebikul.com 25; POP3 con openssl s_client -crlf -quiet -connect mail.sebikul.com:995.

- -ERR \[AUTH\] Plaintext authentication disallowed on non-secure (SSL/TLS) connections. en POP3: el servidor no acepta la clave sin cifrar. Mismo diálogo (USER, PASS, STAT, LIST, RETR, QUIT) con openssl s_client -starttls pop3 -crlf -quiet -connect mail.sebikul.com:110 (o openssl s_client -crlf -quiet -connect mail.sebikul.com:995). En el paso 5 abrí la sesión con ese comando en vez de nc -C mail.sebikul.com 110 y tipeá lo mismo (en la variante con adjunto, con el mismo \| tee sesion.txt al final). No saques el -quiet: sin él, openssl toma las líneas RETR y QUIT como comandos propios (se ve RENEGOTIATING y el mensaje no baja).

- Si el servidor pide usuario y clave para enviar: el EHLO lista AUTH y el MAIL FROM o el RCPT TO te rechazan (530 5.7.0 Authentication required en algunos servidores; en Postfix, un 554 5.7.1 \<...\>: Relay access denied o ... rejected: Access denied). En el paso 3, después del EHLO, tipeá AUTH PLAIN TOKEN; tiene que contestar 235 (en Postfix, 235 2.7.0 Authentication successful). El token sale de printf '\0%s\0%s' 'USUARIO' 'CLAVE' \| base64 -w0; echo (con usuario y clave 40123@mail.sebikul.com da ADQwMTIzQG1haWwuc2ViaWt1bC5jb20ANDAxMjNAbWFpbC5zZWJpa3VsLmNvbQ==). Usá esa forma: con printf '\0USUARIO\0CLAVE', un usuario que empieza con dígitos (el legajo) sale mal, porque \0 seguido de números se lee como un número octal. Si contesta 535 (en Postfix, 535 5.7.8 Error: authentication failed), el token está mal: usuario o clave equivocados, o armado con \0 pegado a los dígitos. Rehacelo con la forma de %s. Si el EHLO no lista AUTH, o el MAIL FROM contesta 530 5.7.0 Must issue a STARTTLS command first, primero va el openssl s_client -starttls smtp de arriba.

- Si piden verificar por IMAP en vez de POP3: nc -C mail.sebikul.com 143 y tipeá, esperando cada respuesta, a1 LOGIN USUARIO CLAVE, a2 SELECT INBOX (el \* N EXISTS dice cuántos mensajes hay), a3 FETCH N BODY\[\] y a4 LOGOUT. Cada comando lleva adelante una etiqueta cualquiera (a1, a2...). Si contesta a1 NO \[PRIVACYREQUIRED\] Plaintext authentication disallowed ..., usá openssl s_client -starttls imap -crlf -quiet -connect mail.sebikul.com:143 (o el 993 sin -starttls imap).

- Enviar con curl en vez de nc (hace el diálogo SMTP solo, leyendo mail.txt, y con -v lo muestra): curl -v --crlf smtp://mail.sebikul.com/NOMBRE_DE_TU_VM --mail-from USUARIO@itba.edu.ar --mail-rcpt LEGAJO@mail.sebikul.com -T mail.txt. NOMBRE_DE_TU_VM es lo que da hostname y es lo que curl manda en el EHLO: si la URL no lo trae, manda EHLO mail.txt. En la salida, las líneas que empiezan con \< son las del servidor y tiene que aparecer \< 250 2.0.0 Ok: queued as .... Si el servidor exige STARTTLS, agregá --ssl-reqd -k.

- Si pegás ¡ en la terminal y el base64 no te da igual al de arriba, la terminal no está en UTF-8: usá los valores ya calculados.

- Para escribir ¡ sin teclado (la nota al pie habla de dead keys y man pages): copialo de este documento, o imprimilo con printf '\xc2\xa1' (en UTF-8 son los bytes C2 A1; en octal, printf '\302\241'). En base64 el signo solo es wqE=. Los dos base64 del mail sin tipear ningún caracter especial:

printf '\xc2\xa1Buenos d\xc3\xadas!' \| base64

printf '\xc2\xa1Hola mundo!\r\n' \| base64

wqFCdWVub3MgZMOtYXMh

wqFIb2xhIG11bmRvIQ0K

- Man page que trae el caracter para copiarlo con el mouse: man iso_8859-1, fila 241 161 A1 ¡ INVERTED EXCLAMATION MARK (buscala con /INVERTED). Ese A1 es el código Latin-1 de un byte; en UTF-8 son los dos bytes C2 A1.

- Con el teclado: en distribución Latinoamérica es Shift + la tecla ¿; en Español (España), la tecla a la derecha de '; en Inglés internacional con dead keys (teclas muertas), AltGr+1.

- Se probó contra un Postfix y un Dovecot locales que imitan a mail.sebikul.com (el real no tenía registro A público el 11/10/2026).

**Variantes:**

- Con imagen adjunta (como en 2015 y 2016) y sin quoted-printable: el mensaje pasa a multipart/mixed con dos partes, el texto en base64 igual que en el paso 1 y la imagen también en base64. No lleva parte text/html. No copies el mensaje de 2016: manda el texto en quoted-printable y pierde el punto f. Este mensaje reemplaza al del paso 1. Primero pasá la imagen que te dan a base64, cortada en líneas de 76 caracteres:

base64 -w 76 header_bg.gif \> adjunto.b64

- Hay que hacer nano mail.txt, borrar lo que tenga y pegar lo siguiente, cambiando FECHA, NOMBRE APELLIDO, USUARIO, NUMERO y LEGAJO como en el paso 1. Después llevá el cursor a la línea ADJUNTO_EN_BASE64, borrala con Ctrl+K y, sin mover el cursor, insertá ahí la imagen: Ctrl+R, escribí adjunto.b64 y Enter. Guardá con Ctrl+O y Enter, y salí con Ctrl+X. Si la imagen es otra, cambiá su nombre en las dos líneas donde aparece y el tipo, que lo dice file --mime-type IMAGEN (image/gif, image/png, image/jpeg).

Date: FECHA

From: NOMBRE APELLIDO \<USUARIO@itba.edu.ar\>

To: \<LEGAJO@mail.sebikul.com\>

Subject: =?UTF-8?B?wqFCdWVub3MgZMOtYXMh?=

Message-ID: \<NUMERO.LEGAJO@itba.edu.ar\>

X-Parcial: 2024/2

MIME-Version: 1.0

Content-Type: multipart/mixed; boundary="MIXTO-7207"

--MIXTO-7207

Content-Type: text/plain; charset=UTF-8

Content-Transfer-Encoding: base64

wqFIb2xhIG11bmRvIQ0K

--MIXTO-7207

Content-Type: image/gif; name="header_bg.gif"

Content-Disposition: attachment; filename="header_bg.gif"

Content-Transfer-Encoding: base64

ADJUNTO_EN_BASE64

--MIXTO-7207--

- Revisá con cat mail.txt que no quede ningún marcador, que ninguna parte diga quoted-printable y que al final queden el base64 de la imagen, una línea en blanco y --MIXTO-7207--. grep -nP '\[^\x00-\x7F\]' mail.txt no tiene que mostrar nada.

- Envío: como en los pasos 3 y 4, pegando todo mail.txt después del 354. Como el adjunto lo hace largo de copiar, es más fácil mandarlo con curl, que hace el mismo diálogo SMTP leyendo el archivo. NOMBRE_DE_TU_VM es lo que da hostname (curl lo manda en el EHLO). Con -v se ve el diálogo, y tiene que terminar en \< 250 2.0.0 Ok: queued as ...:

curl -v --crlf smtp://mail.sebikul.com/NOMBRE_DE_TU_VM --mail-from USUARIO@itba.edu.ar --mail-rcpt LEGAJO@mail.sebikul.com -T mail.txt

- Lectura: como en el paso 5, pero abrí la sesión así, para que lo que contesta el servidor quede guardado en sesion.txt además de verse:

nc -C mail.sebikul.com 110 \| tee sesion.txt

- Para mostrar la imagen, sacala de una copia de la sesión, así sesion.txt queda entera para mostrar el RETR: cp sesion.txt imagen.b64. Hay que hacer nano imagen.b64 y dejar solo el base64 de la imagen. Arriba, borrá con Ctrl+K todas las líneas hasta la línea en blanco que sigue al Content-Transfer-Encoding: base64 de la imagen, inclusive. Podés dejarlo apretado, pero soltalo cuando falten pocas líneas y terminá de a una. Si te pasaste, Alt+U deshace todo ese borrado y lo repetís. Abajo, borrá las que siguen a la última línea del base64 (la línea en blanco, --MIXTO-7207--, el . y +OK Logging out.). Si queda alguna línea en blanco, no molesta. Guardá con Ctrl+O y Enter, y salí con Ctrl+X. Si algo salió mal, volvé a copiar con cp sesion.txt imagen.b64 y repetí. Después decodificalo y comparalo con la original (el -i hace que base64 saltee los fines de línea CRLF que trae la sesión):

base64 -d -i imagen.b64 \> recibida.img

file recibida.img

sha256sum header_bg.gif recibida.img

- En el RETR (cat sesion.txt), las dos partes tienen que decir Content-Transfer-Encoding: base64 (ninguna quoted-printable), y el hash tiene que dar igual en las dos líneas:

recibida.img: GIF image data, version 89a, 320 x 64

MISMO_HASH header_bg.gif

MISMO_HASH recibida.img

*Probado en Ubuntu 24.04 (lab). Contra un Dovecot 2.3.21 (el de Ubuntu 24.04) que no acepta la clave sin cifrar se probaron el error de POP3 e IMAP en claro, POP3 e IMAP con STARTTLS y por 995 y 993 y el token de AUTH PLAIN (con el AUTH PLAIN de POP3, que usa el mismo formato). La forma actual (mensaje armado con nano, con Ctrl+K y Ctrl+R para el adjunto; sesiones SMTP y POP3 tipeadas con nc -C; envío con curl; imagen sacada de una copia de sesion.txt con nano y base64 -d -i) se probó en Ubuntu 24.04 (WSL, nano 7.2) contra un SMTP y un POP3 mínimos que contestan como Postfix y Dovecot. Sin probar: man iso_8859-1 (el lab no trae man pages; la fila se verificó en manpages.debian.org), las combinaciones de teclado para ¡ (el lab no tiene entorno gráfico), el apt install de nc, curl y file (ya estaban), el --ssl-reqd del curl de SMTP y AUTH PLAIN contra un servidor SMTP, con sus mensajes de rechazo.*

### Enviar un mail (X-Parcial: 2016/2, Subject sacado de un registro TXT, imagen adjunta) y leerlo por POP3

**Tomado en:** Parcial 2016-2C · Ejercicio 1.

**También tomado en:** Parcial 2015 (primer parcial) · Ejercicio 1 (el header es X-Parcial: 2015/1, el asunto es fijo, Ejercicio 1, el destinatario es una casilla del ITBA y no pide leerlo por POP3; el mensaje y el envío de esa variante están en Variantes).

> **Idea:** La idea es mandar por SMTP a LEGAJO@proto.leak.com.ar un mail multipart con X-Parcial: 2016/2, el texto ¡Hola mundo!, el Subject sacado del registro TXT de google-public-dns-a.google.com y una imagen adjunta en base64, y después recuperar esa imagen de la sesión POP3. La trampa es que el MX de número más bajo (smtp20) apunta a 127.0.0.1: va smtp30.

**Buscar:** mail, correo, SMTP, POP3, puerto 25, puerto 110, X-Parcial, 2016/2, Subject, Subject desde registro TXT, dig TXT, google-public-dns-a.google.com, xkcd, http://xkcd.com/1361/, adjunto, attachment, imagen adjunta, multipart/mixed, multipart/alternative, boundary, base64, base64 -w 76, header_bg.gif, dig MX, MX localhost, 127.0.0.1, proto.leak.com.ar, smtp20.proto.leak.com.ar, smtp30.proto.leak.com.ar, LEGAJO@proto.leak.com.ar, pdc-LEGAJO, pdc-53248, pdc53559, USER, PASS, LIST, RETR, nc -C, nc 25, nc 110, telnet 25, EHLO, HELO, MAIL FROM, RCPT TO, DATA, Date, Message-ID, MIME-Version, Content-Type, Content-Disposition, Content-Transfer-Encoding, quoted-printable, =C2=A1Hola mundo!, ¡Hola mundo!, Hola mundo, xdg-open, mostrar la imagen, enviar correo, leer correo, legajo, 2015/1, Ejercicio 1 (email), asunto Ejercicio 1, asunto fijo, dig MX itba.edu.ar, casilla del ITBA, @itba.edu.ar, enviar un mail con adjunto, sin POP3, nano mensaje.eml, Ctrl+R, insertar archivo, adjunto.b64, ADJUNTO_EN_BASE64, tee sesion.txt, base64 -d -i, curl smtp, recibida.img, imagen.b64, cp sesion.txt

**Enunciado:** No hay consigna literal en las fuentes; está reconstruido del apunte de un alumno ("Había que enviar un mail con ciertas condiciones"). Enviar a LEGAJO@proto.leak.com.ar un mail con el header X-Parcial: 2016/2, remitente con nombre y apellido, Subject igual al contenido de un registro TXT (era el de google-public-dns-a.google.com, que devolvía "http://xkcd.com/1361/"), el texto ¡Hola mundo! y una imagen adjunta (header_bg.gif). Después leerlo por POP3 con el usuario pdc-LEGAJO y la clave que te dan, y mostrar la imagen.

**Enunciado en Parcial 2015 (primer parcial) · Ejercicio 1:** No hay consigna en las fuentes; el apunte solo trae la resolución ("mandate un mail con todos los datos que te piden y después te quedás con la estructura"). Reconstruido: enviar a una casilla del ITBA un mail con el header X-Parcial: 2015/1, remitente con nombre y apellido, el texto ¡Hola mundo! y una imagen adjunta (header_bg.gif). No tiene la parte de POP3; en el apunte el asunto era Ejercicio 1.

**Solución:**

> **1.** Servidor SMTP: pedí los MX y resolvé cada nombre a su IP. El de número más bajo (smtp20) resolvía a 127.0.0.1: es una trampa, se usa el otro. Anotá el nombre del que sirve (en el ejemplo, smtp30.proto.leak.com.ar; el apunte vio smtp20 en 127.0.0.1 y smtp30 en 192.168.122.235).

dig +short proto.leak.com.ar MX

dig +short smtp20.proto.leak.com.ar A

dig +short smtp30.proto.leak.com.ar A

\# equivalente viejo: host -t MX proto.leak.com.ar ; nslookup -type=MX proto.leak.com.ar

20 smtp20.proto.leak.com.ar.

21 smtp30.proto.leak.com.ar.

127.0.0.1

192.168.122.235

> **2.** Texto del Subject: el contenido del registro TXT. Anotalo sin las comillas que muestra dig (en el ejemplo, http://xkcd.com/1361/).

dig +short TXT google-public-dns-a.google.com

\# el apunte le preguntaba a un DNS puntual: dig google-public-dns-a.google.com @8.8.8.8 -t TXT

"http://xkcd.com/1361/"

> **3.** Armá el mensaje. Anotá también la fecha (date -R) y la IP de tu VM para el EHLO del paso 4 (ip -br a, la de tu placa, no la 127.0.0.1, sin la barra ni lo que viene después). Después pasá la imagen que te dan a base64, cortada en líneas de 76 caracteres:

base64 -w 76 header_bg.gif \> adjunto.b64

Hay que hacer nano mensaje.eml, borrar lo que tenga y pegar lo siguiente, cambiando FECHA por la salida de date -R, NOMBRE APELLIDO y USUARIO por tus datos, LEGAJO por tu legajo, TEXTO_DEL_TXT por lo que anotaste en el paso 2 y NUMERO por cualquier número que no hayas usado en otro mail (por ejemplo, la hora sin los dos puntos). Después llevá el cursor a la línea ADJUNTO_EN_BASE64, borrala con Ctrl+K y, sin mover el cursor, insertá ahí la imagen: Ctrl+R, escribí adjunto.b64 y Enter. Guardá con Ctrl+O y Enter, y salí con Ctrl+X. Si la imagen es otra, cambiá su nombre en las dos líneas donde aparece y el tipo, que lo dice file --mime-type IMAGEN.

Date: FECHA

From: NOMBRE APELLIDO \<USUARIO@itba.edu.ar\>

To: \<LEGAJO@proto.leak.com.ar\>

Subject: TEXTO_DEL_TXT

Message-ID: \<NUMERO.LEGAJO@itba.edu.ar\>

MIME-Version: 1.0

X-Parcial: 2016/2

Content-Type: multipart/mixed; boundary="MIXTO-7207"

--MIXTO-7207

Content-Type: multipart/alternative; boundary="ALT-7207"

--ALT-7207

Content-Type: text/plain; charset=UTF-8

Content-Transfer-Encoding: quoted-printable

=C2=A1Hola mundo!

--ALT-7207

Content-Type: text/html; charset=UTF-8

Content-Transfer-Encoding: quoted-printable

\<div dir=3D"ltr"\>=C2=A1Hola mundo!\</div\>

--ALT-7207--

--MIXTO-7207

Content-Type: image/gif; name="header_bg.gif"

Content-Disposition: attachment; filename="header_bg.gif"

Content-Transfer-Encoding: base64

ADJUNTO_EN_BASE64

--MIXTO-7207--

Mirá con cat mensaje.eml que al final queden el base64 de la imagen, una línea en blanco y --MIXTO-7207-- (si todavía está la línea ADJUNTO_EN_BASE64, faltó el Ctrl+R). Después mirá los headers con head -9 mensaje.eml. Así tienen que quedar (que Subject: tenga el texto del TXT y no quede ningún marcador):

Date: Sun, 11 Oct 2026 21:53:12 +0000

From: NOMBRE APELLIDO \<USUARIO@itba.edu.ar\>

To: \<53248@proto.leak.com.ar\>

Subject: http://xkcd.com/1361/

Message-ID: \<215312.53248@itba.edu.ar\>

MIME-Version: 1.0

X-Parcial: 2016/2

Content-Type: multipart/mixed; boundary="MIXTO-7207"

> **4.** Envialo por SMTP a mano, al servidor del paso 1:

nc -C smtp30.proto.leak.com.ar 25

\# o telnet smtp30.proto.leak.com.ar 25, que ya manda CRLF; con ncat: ncat -C smtp30.proto.leak.com.ar 25

Esperá el 220 y tipeá estas líneas de a una, esperando la respuesta de cada una. En el EHLO va, entre corchetes, la IP que anotaste en el paso 3; USUARIO y LEGAJO son los mismos que pusiste en el mensaje:

EHLO \[IP_DE_TU_VM\]

MAIL FROM:\<USUARIO@itba.edu.ar\>

RCPT TO:\<LEGAJO@proto.leak.com.ar\>

DATA

Cuando contesta 354, pegá todo el mensaje: mostralo con cat mensaje.eml en otra pestaña, seleccioná desde Date: hasta --MIXTO-7207--, copialo con Ctrl+Shift+C y pegalo en la sesión con Ctrl+Shift+V. Si el cursor quedó al final de --MIXTO-7207--, apretá Enter. Después tipeá una línea que solo tenga un punto y QUIT (si después del 221 no vuelve el prompt, Ctrl+C):

.

QUIT

Así se ve la sesión (vos tipeás EHLO, MAIL FROM, RCPT TO, DATA, el punto y QUIT, y pegás el mensaje):

220 juan-virtual-machine ESMTP Postfix (Ubuntu)

EHLO \[IP_DE_TU_VM\]

250-juan-virtual-machine

250-PIPELINING

...

250 CHUNKING

MAIL FROM:\<USUARIO@itba.edu.ar\>

250 2.1.0 Ok

RCPT TO:\<53248@proto.leak.com.ar\>

250 2.1.5 Ok

DATA

354 End data with \<CR\>\<LF\>.\<CR\>\<LF\>

(el contenido de mensaje.eml)

.

250 2.0.0 Ok: queued as DBBF7274F6

QUIT

221 2.0.0 Bye

Si copiar el mensaje entero se hace largo por el adjunto, curl hace el mismo diálogo leyendo el archivo. NOMBRE_DE_TU_VM es lo que da hostname (curl lo manda en el EHLO; si la URL no lo trae, manda el nombre del archivo). Con -v se ve el diálogo, y tiene que terminar en \< 250 2.0.0 Ok: queued as ...:

curl -v --crlf smtp://smtp30.proto.leak.com.ar/NOMBRE_DE_TU_VM --mail-from USUARIO@itba.edu.ar --mail-rcpt LEGAJO@proto.leak.com.ar -T mensaje.eml

> **5.** Leelo por POP3, guardando lo que contesta el servidor en sesion.txt (el \| tee sesion.txt lo guarda y además lo muestra). Si el POP3 está en otro host, cambiá el nombre.

nc -C smtp30.proto.leak.com.ar 110 \| tee sesion.txt

Tipeá estas líneas de a una, esperando cada respuesta. CLAVE_POP3 es la que te dan. Si el LIST muestra más de un mensaje, en RETR poné el número del tuyo. Si después del +OK Logging out. no vuelve el prompt, salí con Ctrl+C: lo que llegó ya quedó en sesion.txt.

USER pdc-LEGAJO

PASS CLAVE_POP3

LIST

RETR 1

QUIT

> **6.** Sacá la imagen de la sesión guardada y mostrala. Primero copiá la sesión, para que sesion.txt quede entera para Demostrar: cp sesion.txt imagen.b64. Hay que hacer nano imagen.b64 y dejar solo el base64 de la imagen. Arriba, borrá con Ctrl+K todas las líneas hasta la línea en blanco que sigue al Content-Transfer-Encoding: base64 de la imagen, inclusive. Podés dejarlo apretado, pero soltalo cuando falten pocas líneas y terminá de a una. Si te pasaste, Alt+U deshace todo ese borrado y lo repetís. Abajo, borrá las que siguen a la última línea del base64 (la línea en blanco, --MIXTO-7207--, el . y +OK Logging out.). Si queda alguna línea en blanco, no molesta. Guardá con Ctrl+O y Enter, y salí con Ctrl+X. Si algo salió mal, volvé a copiar con cp sesion.txt imagen.b64 y repetí. Después decodificalo, comparalo con la original y abrilo (el -i hace que base64 saltee los fines de línea CRLF que trae la sesión):

base64 -d -i imagen.b64 \> recibida.img

file recibida.img

sha256sum header_bg.gif recibida.img

xdg-open recibida.img

**Demostrar:** el 250 2.0.0 Ok: queued as ... del paso 4; en el RETR, los headers Subject: con el texto del TXT y X-Parcial: 2016/2; y la imagen recuperada, con el mismo hash que la original. En el bloque van solo las respuestas del servidor, que es lo que quedó en sesion.txt (mostralo con cat sesion.txt); en pantalla aparecen intercaladas con USER, PASS, LIST, RETR 1 y QUIT:

+OK Dovecot (Ubuntu) ready.

+OK

+OK Logged in.

+OK 1 messages:

1 9344

.

+OK 9344 octets

Return-Path: \<USUARIO@itba.edu.ar\>

X-Original-To: 53248@proto.leak.com.ar

Delivered-To: 53248@proto.leak.com.ar

Date: Sun, 11 Oct 2026 21:53:12 +0000

From: NOMBRE APELLIDO \<USUARIO@itba.edu.ar\>

To: \<53248@proto.leak.com.ar\>

Subject: http://xkcd.com/1361/

Message-ID: \<215312.53248@itba.edu.ar\>

MIME-Version: 1.0

X-Parcial: 2016/2

Content-Type: multipart/mixed; boundary="MIXTO-7207"

...

+OK Logging out.

recibida.img: GIF image data, version 89a, 320 x 64

b5e950b27908b81f5a03fb5acb980b192fd1c24201a79a1bdf42b5e9045be026 header_bg.gif

b5e950b27908b81f5a03fb5acb980b192fd1c24201a79a1bdf42b5e9045be026 recibida.img

**Ojo:**

- El MX de número más bajo (smtp20) apunta a 127.0.0.1, o sea a tu propia máquina: el nc vuelve al prompt sin mostrar ni el saludo 220 (con nc -v -C se ve nc: connect to smtp20.proto.leak.com.ar (127.0.0.1) port 25 (tcp) failed: Connection refused). Va el otro (smtp30), aunque tenga peor prioridad. Tampoco uses "la IP que te dan" sin resolver los MX.

- El Subject va sin comillas: las comillas son de la salida de dig. El apunte lo mandó como Subject: "http://xkcd.com/1361/" y está mal.

- Si el dig del paso 2 no devuelve nada, probá con el DNS que diga el enunciado: dig @IP_DNS +short TXT NOMBRE.

- MAIL FROM:\<...\> y RCPT TO:\<...\> van sin espacio después de los dos puntos y con los signos menor y mayor. El apunte de 2015 escribe MAIL FROM: \<...\> con un espacio: va pegado.

- No mandes todo el diálogo de golpe (cat original_msg.txt \| nc ip 25, como el apunte): un servidor estricto contesta 554 5.5.0 Error: SMTP protocol synchronization y corta. Por eso los comandos se tipean de a uno, esperando cada respuesta, y solo el mensaje se pega entero después del 354.

- El apunte no mandaba Date ni Message-ID y usaba HELO: van los dos headers y EHLO. El apunte de 2015 tampoco tiene Date ni Message-ID.

- El adjunto va en base64 cortado en líneas de 76 (base64 -w 76). En una sola línea supera las 998 columnas que admite SMTP.

- ¡Hola mundo! en quoted-printable es =C2=A1Hola mundo!. El apunte de 2016 tiene un espacio después del signo (=C2=A1 Hola mundo!): copiá el texto exacto de tu enunciado.

- El . solo en una línea cierra el DATA. Si falta, el servidor se queda esperando y corta con 421 4.4.2 ... Error: timeout exceeded.

- Usuario POP3: pdc-LEGAJO con guion (en el apunte, USER pdc-53248; en otra nota figura pdc53559 sin guion). Con -ERR \[AUTH\] Authentication failed. probá la otra forma.

- base64: invalid input, o recibida.img no da GIF o da otro hash: faltó el -i (sin él, base64 no acepta los CRLF de la sesión), o en imagen.b64 quedó algo que no es el base64 de la imagen (un header, el --MIXTO-7207--, el . o el +OK Logging out.): volvé a copiar con cp sesion.txt imagen.b64 y recortalo de nuevo. Si el RETR no llegó a mostrarse entero, repetí el paso 5.

- Las dos fronteras tienen que coincidir letra por letra con las de los Content-Type, y cada cierre lleva dos guiones al final (--ALT-7207--, --MIXTO-7207--).

- Si el enunciado prohíbe quoted-printable (como el de 2024): en el mensaje del paso 3, cambiá todo lo que va desde Content-Type: multipart/alternative; boundary="ALT-7207" hasta --ALT-7207-- inclusive por Content-Type: text/plain; charset=UTF-8, Content-Transfer-Encoding: base64, una línea en blanco y wqFIb2xhIG11bmRvIQ0K. Es la parte de texto de la variante con adjunto del Parcial 2024-2C · Ejercicio 4, más arriba en este mismo tipo. El resto del mensaje, el envío del paso 4 y la extracción del paso 6 quedan igual.

**Variantes:**

- Parcial 2015 (primer parcial) · Ejercicio 1: el mismo mensaje multipart con otros valores. Va a una casilla del ITBA, el header es X-Parcial: 2015/1, el asunto es fijo (Ejercicio 1, no sale de un TXT) y no se lee por POP3. Tiene su mensaje y su envío propios acá abajo.

- Servidor en esa variante: el MX de menor número del dominio del destinatario. Esto reemplaza al paso 1: anotá el nombre del MX con el número más chico (la salida no viene ordenada). El paso 2 no va: el asunto es fijo.

dig +short itba.edu.ar MX

- Si el dig no devuelve MX, usá el servidor que diga el enunciado. Lo mismo si ese MX resuelve a 127.0.0.1, como el smtp20 de 2016 (se ve con dig +short NOMBRE_DEL_MX A).

- Mensaje de esa variante, en lugar del del paso 3: se arma igual (fecha, IP, adjunto.b64 y nano mensaje.eml con Ctrl+K y Ctrl+R), pero pegando este. DESTINO es el usuario de la casilla del ITBA que da el enunciado, lo que va antes de @itba.edu.ar (si la casilla es juan@itba.edu.ar, DESTINO es juan). Va igual en el RCPT TO del envío.

Date: FECHA

From: NOMBRE APELLIDO \<USUARIO@itba.edu.ar\>

To: \<DESTINO@itba.edu.ar\>

Subject: Ejercicio 1

Message-ID: \<NUMERO.USUARIO@itba.edu.ar\>

MIME-Version: 1.0

X-Parcial: 2015/1

Content-Type: multipart/mixed; boundary="MIXTO-7207"

--MIXTO-7207

Content-Type: multipart/alternative; boundary="ALT-7207"

--ALT-7207

Content-Type: text/plain; charset=UTF-8

Content-Transfer-Encoding: quoted-printable

=C2=A1Hola mundo!

--ALT-7207

Content-Type: text/html; charset=UTF-8

Content-Transfer-Encoding: quoted-printable

\<div dir=3D"ltr"\>=C2=A1Hola mundo!\</div\>

--ALT-7207--

--MIXTO-7207

Content-Type: image/gif; name="header_bg.gif"

Content-Disposition: attachment; filename="header_bg.gif"

Content-Transfer-Encoding: base64

ADJUNTO_EN_BASE64

--MIXTO-7207--

- Envío de esa variante, en lugar del paso 4: igual, pero conectando al MX que anotaste y con la casilla del enunciado en RCPT TO. Con curl, cambiá lo mismo en el comando del paso 4.

nc -C NOMBRE_DEL_MX 25

EHLO \[IP_DE_TU_VM\]

MAIL FROM:\<USUARIO@itba.edu.ar\>

RCPT TO:\<DESTINO@itba.edu.ar\>

DATA

(después del 354, el contenido de mensaje.eml)

.

QUIT

- Los pasos 5 y 6 no van. Si además piden verificar por POP3: nc -C SERVIDOR_POP3 110 y tipeás USER USUARIO, PASS CLAVE, LIST, RETR 1 y QUIT, esperando cada respuesta. Acá el usuario es el que te den y el host es el POP3 que indiquen, no el pdc-LEGAJO ni el smtp30 de 2016.

- Se demuestra con la sesión SMTP terminando en 250 2.0.0 Ok: queued as ... y con los headers que imprime head -9 mensaje.eml, que tienen que quedar así:

Date: Sun, 11 Oct 2026 21:55:15 +0000

From: NOMBRE APELLIDO \<USUARIO@itba.edu.ar\>

To: \<DESTINO@itba.edu.ar\>

Subject: Ejercicio 1

Message-ID: \<215515.USUARIO@itba.edu.ar\>

MIME-Version: 1.0

X-Parcial: 2015/1

Content-Type: multipart/mixed; boundary="MIXTO-7207"

- El mensaje de esta variante se probó en el lab contra un postfix que recibe correo para itba.edu.ar: llegó entero y el adjunto recuperado tiene el mismo hash. Sin probar: los servidores reales del ITBA.

*Probado en Ubuntu 24.04 (lab), contra un postfix y un dovecot que imitan a proto.leak.com.ar (MX 20 a 127.0.0.1, MX 21 al servidor, casilla LEGAJO con usuario POP3 pdc-LEGAJO) y un registro TXT local con el valor del apunte. La forma actual (mensaje armado con nano, con Ctrl+K y Ctrl+R para el adjunto; sesiones SMTP y POP3 tipeadas con nc -C; envío con curl; imagen sacada de una copia de sesion.txt con nano y base64 -d -i) se probó en Ubuntu 24.04 (WSL, nano 7.2) contra un SMTP y un POP3 mínimos que contestan como Postfix y Dovecot. Sin probar: xdg-open (el lab no tiene entorno gráfico), los nombres reales, que ya no existen, y el envío a un Postfix real del mensaje sin quoted-printable del Ojo.*

## MX y SPF de un dominio

Preguntas sobre el correo de un dominio que se contestan con dig: qué servidores reciben su mail (los MX, resueltos a IP) y qué IPs pueden enviar en su nombre (el SPF, que está en un TXT). Son dos ejercicios: en uno la respuesta son las IPs y en el otro la IP del MX de mayor prioridad se usa como passphrase. Si lo vuelven a tomar cambian el dominio y los términos de su SPF. El mismo pedido de MX, sin resolver a IP, está en el Parcial 2011 · Ejercicio 3 (Resueltos — DNS, 'Consultas con dig'). La IP del MX de mayor prioridad (el de itba.edu.ar) se pide en el Parcial 2012-1C · Ejercicio 2, más abajo en este mismo tipo; la captura que desencripta se analiza en el Parcial 2012-1C · Ejercicio 3 (Resueltos — Capturas (Wireshark), 'Responder preguntas sobre una captura').

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| IPs de los SMTP entrantes (MX) y SPF de proto.leak.com.ar | Sobre proto.leak.com.ar: A, las IPs de los servidores SMTP que reciben el correo entrante; B, las IPs que la política SPF autoriza a enviar correo saliente | Piden IPs, no nombres: cada MX se resuelve a su registro A (el apunte vio smtp20 en 127.0.0.1 y smtp30 en 192.168.122.235). El SPF real no está en las fuentes: va la tabla para traducir cada término y cómo seguir a mano los include y redirect |
| Desencriptar con gpg -d capture.pcap.gpg; la passphrase es la IP del servidor de correo con mayor prioridad del ITBA | Desencriptar capture.pcap.gpg con gpg -d; la passphrase es la IP del servidor de correo con mayor prioridad del ITBA | El MX de itba.edu.ar con el número más chico, resuelto a su IP, es la passphrase. No se abre ninguna captura |

### IPs de los SMTP entrantes (MX) y SPF de proto.leak.com.ar

**Tomado en:** Recuperatorio 2020-1C · Ejercicio I.

> **Idea:** La idea es contestar con dig dos preguntas sobre proto.leak.com.ar: las IPs de los MX que reciben su correo y las IPs que su política SPF autoriza a enviar. El truco es que piden IPs, no nombres: cada MX se resuelve a su registro A, y del TXT con v=spf1 hay que seguir los include y redirect y resolver los mecanismos a y mx.

**Buscar:** proto.leak.com.ar, servidores SMTP, direcciones IPs de los servidores SMTP que reciben el correo entrante, correo entrante, MX, dig MX, SPF, Según la política SPF, Segun la politica SPF, política SPF, politica SPF, direcciones IP que tienen permitido enviar correos electrónicos salientes, correos electronicos salientes, dig TXT, v=spf1, include, redirect, ip4, ip6, mecanismo a, mecanismo mx, all, -all, ~all, ?all, +all, softfail, Responda las siguientes preguntas sobre el dominio, Cuales son las direcciones IPs, Cuáles son las direcciones IPs, Enumere todas las direcciones IP de los servidores SMTP, Cuales son las direcciones IP que tienen permitido enviar correos electronicos salientes, Concejo, Consejo, comentario de la entrega de campus, primera corrección rápida, primera correccion rapida

**Enunciado:** "Responda las siguientes preguntas sobre el dominio proto.leak.com.ar: A. ¿Cuales son las direcciones IPs de los servidores SMTP que reciben el correo entrante? (1 punto) B. Según la política SPF. ¿Cuales son las direcciones IP que tienen permitido enviar correos electrónicos salientes? (1 punto) Concejo. Escriba como comentario de la entrega de campus las soluciones para una primera corrección rápida." ("Concejo" está así en el original.)

**Solución:**

A. Los MX dan los NOMBRES; hay que resolver cada uno a su IP (la pregunta pide IPs):

dig proto.leak.com.ar MX +short

\# el apunte vio: 20 smtp20.proto.leak.com.ar. y 21 smtp30.proto.leak.com.ar.

dig smtp20.proto.leak.com.ar A +short \# el apunte vio 127.0.0.1

dig smtp30.proto.leak.com.ar A +short \# el apunte vio 192.168.122.235

B. La política SPF es el registro TXT del dominio que empieza con v=spf1. Hay que traducir cada término a direcciones IP.

> **1.** Traé el SPF. Un dominio puede tener varios TXT: en la salida, mirá el que empieza con v=spf1.

dig proto.leak.com.ar TXT +short

> **2.** Leé cada término de izquierda a derecha.

| **Término** | **Qué IPs autoriza** |
|----|----|
| ip4:1.2.3.4 o ip4:1.2.3.0/24 | esa IP o esa red |
| ip6:2001:db8::/32 | esa IP o red IPv6 |
| a | las IPs (A y AAAA) del propio dominio |
| a:host.dominio | las IPs de ese host |
| mx | las IPs de los MX del propio dominio (las del inciso A) |
| mx:otro.dominio | las IPs de los MX de ese dominio |
| include:otro.dominio | todo lo que autorice el SPF de ese dominio (hay que consultarlo) |
| redirect=otro.dominio | reemplaza la política entera por la de ese dominio |
| all | cualquier otra IP; va último y lo que importa es su calificador |

| **Calificador** | **Resultado**                                 |
|-----------------|-----------------------------------------------|
| \+ (o nada)     | pass: autorizado                              |
| \-              | fail: rechazar                                |
| ~               | softfail: aceptar pero marcar como sospechoso |
| ?               | neutral: sin opinión                          |

> **3.** Por cada include: o redirect= repetí la consulta sobre ese dominio y mirá su v=spf1, hasta que queden solo IPs. Si el dominio solo existe en un DNS puntual, agregá @IP_DNS a cada dig.

dig DOMINIO_DEL_INCLUDE TXT +short

> **4.** Para a y mx resolvé los nombres (para a:host o mx:dominio, lo mismo con ese nombre). Si piden también IPv6, repetí los de tipo A con AAAA.

dig proto.leak.com.ar A +short

dig proto.leak.com.ar MX +short

dig smtp20.proto.leak.com.ar A +short

dig smtp30.proto.leak.com.ar A +short

> **5.** Juntá en una lista, sin repetir, todas las IPs y redes que salieron en los pasos 2 a 4: esa es la respuesta de B.

**Demostrar:** la ANSWER SECTION de cada dig (sin +short) y, para B, el TXT con la cadena de include. Escribí las IPs como comentario en campus. Ejemplo con un SPF inventado (el real de proto.leak.com.ar no está en las fuentes): el TXT de proto.leak.com.ar y el de su include dan

"v=spf1 ip4:10.1.2.3 ip4:192.168.50.0/24 a mx include:spf.proveedor.test ~all"

"v=spf1 ip4:203.0.113.0/24 -all"

y la respuesta de B queda:

10.1.2.3

127.0.0.1

192.168.122.10

192.168.122.235

192.168.50.0/24

203.0.113.0/24

Ahí 10.1.2.3, 192.168.50.0/24 y 203.0.113.0/24 salen de los ip4:, 192.168.122.10 del a, y 127.0.0.1 y 192.168.122.235 del mx.

**Ojo:**

- A pide **IPs**, no nombres → siempre resolvé cada MX a su registro A. Responder "smtp20/smtp30" no alcanza.

- El número antes del nombre en el MX es la prioridad (menor = preferido); manténela si te la piden.

- B: si el SPF tiene include: o a/mx, las IPs permitidas incluyen las de esos mecanismos, no solo los ip4: literales. El apunte de MasCompilado solo decía "todas las ips q estan en el spf record".

- El all del final no agrega IPs: dice qué hacer con el resto (-all rechazar, ~all aceptar marcado, ?all neutral). Si termina en +all, autoriza a cualquier IP.

- El TXT sale partido en pedazos entre comillas ("...include:mh" ".b" "lackboard.com ..."): son trozos de 255 caracteres de un mismo registro. Se pegan sin espacio.

- No hay ningún TXT con v=spf1: el dominio no publica política; no hay lista de IPs autorizadas.

- dig proto.leak.com.ar SPF no devuelve nada: el tipo SPF está obsoleto. La política se publica en TXT.

- El 11/10/2026 proto.leak.com.ar daba NXDOMAIN en el DNS público: si lo vuelven a tomar va a ser con otro dominio, cambiá el nombre en los comandos.

*Probado en Ubuntu 24.04 (lab), contra una zona proto.leak.com.ar local en BIND con los MX del apunte y un SPF de ejemplo.*

### Desencriptar con gpg -d capture.pcap.gpg; la passphrase es la IP del servidor de correo con mayor prioridad del ITBA

**Tomado en:** Parcial 2012-1C · Ejercicio 2.

> **Idea:** La idea es descifrar capture.pcap.gpg con gpg -d usando como passphrase la IP del servidor de correo de mayor prioridad del ITBA. El truco es que mayor prioridad es el MX de itba.edu.ar con el número más chico, que hay que resolver a su IP con dig; esa IP, escrita con sus puntos, es la passphrase.

**Buscar:** gpg, gpg -d, capture.pcap.gpg, capture.pcap, desencriptar, descifrar, passphrase, contraseña, contrasena, servidor de correo con mayor prioridad, mayor prioridad, MX, itba.edu.ar, dig MX, pinentry, loopback, Bad session key, Inappropriate ioctl for device

**Enunciado:** "Desencriptar el archivo descargado utilizando el siguiente comando: gpg -d capture.pcap.gpg \> capture.pcap. Para obtener la passphrase a ingresar responder lo siguiente: ¿Cual es la dirección IP del servidor de correo con mayor prioridad del ITBA?"

**Depende de:** Parcial 2012-1C · Ejercicio 1 (Resueltos — nginx, 'Proxy reverso: un dominio sirve el contenido de otro'). El "archivo descargado" es capture.pcap.gpg, que se baja en ese ejercicio.

**Solución:**

> **1.** Pedí los MX del ITBA. Mayor prioridad es el número más chico: buscalo en la salida, que no viene ordenada, y anotá ese nombre (hoy, 1 aspmx.l.google.com.).

dig +short MX itba.edu.ar

\# equivalente viejo: host -t MX itba.edu.ar / nslookup -type=MX itba.edu.ar

> **2.** Resolvé la IP de ese nombre. Esa IP, escrita con sus puntos, es la passphrase.

dig +short A NOMBRE_DEL_MX

\# hoy: dig +short A aspmx.l.google.com

> **3.** Desencriptá. El comando del enunciado abre un cuadro que pide la passphrase:

gpg -d capture.pcap.gpg \> capture.pcap

> **4.** Si no aparece el cuadro o estás por consola pura, pasala en el mismo comando (reemplazá IP_DEL_MX):

gpg --batch --pinentry-mode loopback --passphrase 'IP_DEL_MX' -d capture.pcap.gpg \> capture.pcap

**Demostrar:**

file capture.pcap

tshark -r capture.pcap -c 5

capture.pcap: pcap capture file, microsecond ts (little-endian) - version 2.4 (Ethernet, capture length 262144)

**Ojo:**

- gpg: decryption failed: Bad session key: passphrase equivocada. Además queda un capture.pcap vacío (0 bytes): volvé a correr el comando.

- Si el MX devuelve varias IP, probá cada una.

- Si hay dos MX con la misma prioridad mínima, probá la IP de cada uno.

- Si no pide la passphrase y falla con un mensaje que incluye Inappropriate ioctl for device o No pinentry: usá la forma del paso 4 (mensajes citados de memoria).

- En el paso 4 van las tres opciones juntas: sin --batch o sin --pinentry-mode loopback, gpg ignora --passphrase.

- El dato depende del día: hoy el MX de itba.edu.ar es de Google (1 aspmx.l.google.com.) y en 2012 era otro. El procedimiento es el mismo; el archivo y la passphrase originales no están en las fuentes.

*Sin probar: el lab no tiene gpg y el archivo y la passphrase originales no están en las fuentes. Fuera del lab, con GnuPG 2.4.9, se probó el comando del paso 4 sobre un archivo cifrado con passphrase (la correcta descifra; la incorrecta da "Bad session key" y deja el archivo en 0 bytes). El cuadro interactivo del paso 3 no se pudo ver. Los dig sí se probaron en el lab.*

# Resueltos — SSH (llaves y túneles)

## Entrar con una llave privada: passphrase o nombre de archivo por deducir

En todos hay que conseguir una llave privada (.pem), cerrarle los permisos con chmod y entrar por ssh como el usuario pdc; lo que cambia de un parcial a otro es el dato a deducir (la passphrase de la llave o el nombre del archivo), de dónde sale ese dato, el puerto del SSH y si antes hay que agregar una ruta para llegar al host.

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| Llave con passphrase = MAC de bd1, SSH en el puerto 2222 | Shell de pdc en parcial.leak.com.ar, SSH en el puerto 2222; llave en http://parcial.leak.com.ar/llave.pem | Passphrase = MAC de bd1 (17 caracteres). bd1 es otro host: su MAC sale de la tabla ARP de pampero (ip neigh), después de resolver su IP y pingearlo. |
| Llave con passphrase = MAC de la interfaz en3 de pampero, puerto 2222 | Shell de pdc en parcial.leak.com.ar, puerto 2222; llave en http://parcial.leak.com.ar/llave.pem | Passphrase = MAC de la interfaz en3 de pampero. Es una interfaz propia: sale de ip link show en3, no de ARP. |
| Llave con passphrase = IP asignada por DHCP en dhcp-parcial.pcap.gz, puerto 9090 | Shell de pdc en parcial.leak.com.ar, puerto 9090; llave en http://foo.leak.com.ar/llave-pdc.pem | Passphrase = IP (11 caracteres) que el servidor DHCP le asigna al cliente en dhcp-parcial.pcap.gz: campo "Your (client) IP address" del ACK, con Wireshark o tshark. Da 10.20.20.20. |
| Llave con passphrase = MAC del gateway de pampero (llave-pdc-recu.pem), puerto 9090 | Shell de pdc en parcial.leak.com.ar, puerto 9090; llave en http://foo.leak.com.ar/llave-pdc-recu.pem | Passphrase = MAC del gateway de pampero: ip route da la IP del gateway y ip neigh su MAC. El apunte anota el puerto 9091. |
| Ruta a ubuntu a través de foo, llave /{pkname}.pem con la MAC de foo, ssh como pdc | Shell de pdc@ubuntu (192.168.122.C), que está detrás de foo (IP A del pizarrón); llave en /{pkname}.pem del servidor HTTP de ubuntu | Se deduce el nombre del archivo, no una passphrase: {pkname} es la MAC de foo sin los dos puntos. Antes hay que agregar la ruta a 192.168.122.0/24 vía foo sin perder internet. |
| Ruta a ubuntu, MAC, llave por HTTP, ssh y túnel reverso al recurso web | Shell de pdc@ubuntu (192.168.122.156), detrás de foo (10.6.0.170); llave por HTTP en ubuntu; al final, acceder a un recurso web | El nombre del .pem es una MAC sin los dos puntos (en el apunte, la de foo: 0090f5ca1d73). Suma un túnel reverso -R 8080:itba.edu.ar:80 para que ubuntu llegue a un sitio web. Sin consigna: reconstruido de un apunte. |
| Agregar la ruta a 192.168.122.0/24, bajar la llave ospf.pem por HTTP y entrar por ssh como pdc | Shell de pdc en bar (192.168.122.189), detrás de 10.6.0.243; llave en http://192.168.122.189/ospf.pem | No hay nada que deducir: el nombre ospf.pem viene dado. Es ruta a 192.168.122.0/24, curl, chmod y ssh. Sin consigna: reconstruido de un apunte. |

### Llave con passphrase = MAC de bd1, SSH en el puerto 2222

**Tomado en:** Parcial 2021-1C · Ejercicio 2.

> **Idea:** La idea es entrar por ssh como pdc a parcial.leak.com.ar, puerto 2222, con llave.pem, cuya passphrase es la MAC de bd1. El truco es que bd1 es otro host: en pampero resolvés su IP, lo pingeás y leés su MAC en ip neigh; después salís de pampero y te conectás desde tu máquina, con la llave en chmod 600.

**Buscar:** llave.pem, passphrase, 17 caracteres, direccion MAC, bd1, pampero.itba.edu.ar, puerto 2222, pdc, parcial.leak.com.ar, ssh -i, chmod 600, ip neigh, arp -n, ping, Existe un servidor SSH en el puerto 2222, Obtenga control de la línea de comandos, obtenga control de la linea de comandos, usuario pdc de dicho host, llave privada, http://parcial.leak.com.ar/llave.pem, protegida por una passphrase, dirección MAC, dirección MAC del host bd1, direccion MAC del host bd1, accesible desde pampero.itba.edu.ar, xx:xx:xx:xx:xx:xx, 0123456789abcdef, TODOS los pasos para encontrar la passphrase, control de la línea de comando, control de la linea de comando, ej date

**Enunciado:** "Existe un servidor SSH en el puerto 2222 del host parcial.leak.com.ar. Obtenga control de la línea de comandos del usuario pdc de dicho host. Una llave privada que permite ingresar al usuario pdc se encuentra disponible en http://parcial.leak.com.ar/llave.pem. La llave se encuentra protegida por una passphrase. La passphrase es la dirección MAC del host bd1 accesible desde pampero.itba.edu.ar. (17 caracteres «xx:xx:xx:xx:xx:xx», donde x puede ser los caracteres 0123456789abcdef). En el video de la respuesta que envíe: 1. Muestre cómo TODOS los pasos para encontrar la passphrase. 2. Muestre cómo descargar de llave privada y como conectarse. 3. Una vez en el shell demuestre que tiene el control de la línea de comando ejecutando algún comando (ej date)."

**Solución:**

> **1.** Bajá la llave y ponele permisos (SSH exige que nadie más la lea):

wget http://parcial.leak.com.ar/llave.pem

chmod 600 llave.pem

> **2.** Entrá a pampero (con tu usuario del ITBA):

ssh USUARIO@pampero.itba.edu.ar

> **3.** Dentro de pampero, resolvé la IP de bd1 y después sacá su MAC de la tabla de vecinos ARP. El ping puebla la tabla si bd1 no estaba:

getent hosts bd1 \# o: dig +search +short bd1 -\> te da la IP de bd1

ping -c1 bd1 \# genera trafico para que aparezca en ARP

ip neigh show \# forma vieja: arp -n -\> busca la linea de la IP de bd1

La columna lladdr (vieja: HWaddress) es la MAC: esos 17 caracteres son la passphrase.

> **4.** Salí de pampero con exit (o usá otra terminal de tu máquina, donde está llave.pem). Conectate con la llave al puerto 2222 y pegá la MAC cuando pida Enter passphrase:

ssh -i llave.pem -p 2222 pdc@parcial.leak.com.ar

date \# ya adentro, mostrá que tenés el shell

**Demostrar:** la línea de ip neigh con la MAC de bd1, la conexión SSH entrando, y un comando (date, hostname, id).

**Ojo:**

- La MAC es de **bd1**, NO del gateway ni de pampero. Primero resolvé la IP de bd1 (getent hosts bd1 / dig +search +short bd1; dig +short bd1 a secas no usa el dominio de búsqueda y puede salir vacío) y recién ahí buscá esa IP en ip neigh. El apunte de alumno grepeó 10.16.1.254, que es el gateway de pampero (el del Parcial 2020-1C · Ejercicio II, en Resueltos — Red, 'MAC y puertos abiertos de un host') — puede no ser bd1.

- Si ip neigh no muestra bd1, es porque todavía no hubo tráfico: ping -c1 bd1 primero.

- Sin chmod 600 SSH aborta con WARNING: UNPROTECTED PRIVATE KEY FILE! y Permissions 0644 for 'llave.pem' are too open.

- Warning: Identity file llave.pem not accessible: No such file or directory. y pide pdc@parcial.leak.com.ar's password: en vez de Enter passphrase → corriste el ssh del paso 4 dentro de pampero, donde llave.pem no está. Cortá con Ctrl+C, exit y repetilo desde tu máquina.

- La passphrase va en minúsculas y con los dos puntos tal cual (00:87:31:e1:0f:d2).

*Sin probar: bd1, pampero y parcial.leak.com.ar son hosts externos fuera del lab. Con los tres simulados en el lab sí está probado el flujo completo en una sola terminal (wget, pampero, MAC de bd1 con ip neigh, exit, ssh con passphrase) y el error de correr el paso 4 dentro de pampero.*

### Llave con passphrase = MAC de la interfaz en3 de pampero, puerto 2222

**Tomado en:** Parcial o Recuperatorio 2021-1C · Ejercicio III.

> **Idea:** La idea es entrar por ssh como pdc a parcial.leak.com.ar, puerto 2222, con llave.pem, cuya passphrase es la MAC de la interfaz en3 de pampero. El truco es que en3 es una interfaz propia de pampero: la MAC sale de la línea link/ether de ip link show en3, no de la tabla ARP.

**Buscar:** en3, interface en3, direccion MAC, pampero.itba.edu.ar, puerto 2222, llave.pem, 17 caracteres, ip link, ifconfig, Obtenga control de la línea de comandos, obtenga control de la linea de comandos, usuario pdc, pdc, parcial.leak.com.ar, existe un servidor SSH en el puerto 2222, llave privada, http://parcial.leak.com.ar/llave.pem, passphrase, protegida con una passphrase, dirección MAC, XX:XX:XX:XX:XX:XX, 0123456789abcdef, de la interface en3 del host pampero.itba.edu.ar, Muestre cómo encontrar la passphrase, Muestre como encontrar la passphrase, ej date

**Enunciado:** "Obtenga control de la línea de comandos del usuario pdc del host parcial.leak.com.ar (existe un servidor SSH en el puerto 2222). Una llave privada que permite ingresar al usuario pdc se encuentra disponible en http://parcial.leak.com.ar/llave.pem, pero está protegida con una passphrase. La passphrase tiene 17 caracteres y es la dirección MAC XX:XX:XX:XX:XX:XX (donde X puede ser 0123456789abcdef) de la interface en3 del host pampero.itba.edu.ar. En el video de la respuesta que envíe: 1. Muestre cómo encontrar la passphrase. 2. Muestre cómo descargar de llave privada y como conectarse 3. Una vez en el shell, muestre la ejecución de algún comando (ej date)." (La interfaz se lee en3, no ens3 como en 2020-1C. "descargar de llave privada" está así en el original.)

**Solución:** acá la MAC es de una interfaz **propia de pampero** (en3), así que sale de ip link, NO de la tabla ARP.

> **1.** wget http://parcial.leak.com.ar/llave.pem && chmod 600 llave.pem
>
> **2.** ssh USUARIO@pampero.itba.edu.ar
>
> **3.** En pampero, la MAC de en3 (campo link/ether):

ip link show en3 \# mirá la linea "link/ether xx:xx:xx:xx:xx:xx"

\# forma vieja:

ifconfig en3 \# mirá "ether xx:xx:xx:xx:xx:xx" (o "HWaddr")

> **4.** Salí de pampero con exit (o usá otra terminal de tu máquina, donde está llave.pem): ssh -i llave.pem -p 2222 pdc@parcial.leak.com.ar y pegá esa MAC como passphrase; después date.

**Demostrar:** la línea link/ether de en3, la conexión y un comando.

**Ojo:**

- en3 es **de pampero**, su MAC está en ip link/ifconfig, no en ip neigh/arp. Esto difiere del Parcial 2021-1C · Ejercicio 2 (MAC de **bd1**, que sí sale de ARP) y del Recuperatorio 2020-1C · Ejercicio III (MAC del **gateway**, de ARP); los dos están en este mismo tipo (Resueltos — SSH, 'Entrar con una llave privada: passphrase o nombre de archivo por deducir'). El Parcial 2021-1C y el Parcial o Recuperatorio 2021-1C son dos exámenes distintos.

- El "ens3 como en 2020-1C" de la nota del enunciado es el del Parcial 2020-1C · Ejercicio I (Resueltos — Red, 'MAC y puertos abiertos de un host'): allá la interfaz de pampero se llama ens3, acá en3.

- En la VM del examen tu interfaz NO se llama en3; pero acá en3 es el nombre literal de la interfaz de pampero, usalo tal cual en ip link show en3.

*Sin probar: pampero y parcial.leak.com.ar son externos. El comando ip link show IFACE para leer la MAC propia sí está probado en el lab.*

### Llave con passphrase = IP asignada por DHCP en dhcp-parcial.pcap.gz, puerto 9090

**Tomado en:** Parcial 2020-1C · Ejercicio V.

> **Idea:** La idea es entrar por ssh como pdc a parcial.leak.com.ar, puerto 9090, con llave-pdc.pem, cuya passphrase es la IP que el servidor DHCP le asigna al cliente en dhcp-parcial.pcap.gz. El truco es leer en Wireshark el campo Your (client) IP address del DHCP ACK: da 10.20.20.20, no la IP del servidor DHCP ni la del DNS.

**Buscar:** llave-pdc.pem, passphrase, 11 caracteres, direccion IP, DHCP, dhcp-parcial.pcap.gz, puerto 9090, pdc, parcial.leak.com.ar, Your (client) IP address, yiaddr, Obtenga control de la línea de comandos, obtenga control de la linea de comandos, usuario pdc, existe un servidor SSH en el puerto 9090, llave privada, foo.leak.com.ar, http://foo.leak.com.ar/llave-pdc.pem, se encontrar en, está con una passphrase, esta con una passphrase, dirección IP, XX.XX.XX.XX, asignada por el servidor DHCP al cliente, secuencia DHCP, http://foo.leak.com.ar/dhcp-parcial.pcap.gz, muestre en Wireshark como encontrar la passphrase, llegue a mostrar el campo, ej date

**Enunciado:** "Obtenga control de la línea de comandos del usuario pdc del host parcial.leak.com.ar (existe un servidor SSH en el puerto 9090). Una llave privada que permite ingresar al usuario pdc se encontrar en http://foo.leak.com.ar/llave-pdc.pem, pero está con una passphrase. La passphrase tiene 11 caracteres y es la dirección IP (XX.XX.XX.XX) asignada por el servidor DHCP al cliente en la secuencia DHCP disponible en el archivo http://foo.leak.com.ar/dhcp-parcial.pcap.gz. En el video respuesta que envíe: 1. Con el archivo pcap ya descargado muestre en Wireshark como encontrar la passphrase (llegue a mostrar el campo). 2. Muestre descarga de llave privada y como conectarse 3. Una vez en el shell ejecute algún comando (ej date)." ("se encontrar en" está así en el original.)

**Solución:**

> **1.** Bajá y descomprimí el pcap:

wget http://foo.leak.com.ar/dhcp-parcial.pcap.gz

gunzip -k dhcp-parcial.pcap.gz

> **2.** Sacá la IP que el servidor le asigna al cliente (campo "Your (client) IP address" del ACK):

tshark -r dhcp-parcial.pcap -Y 'dhcp.option.dhcp == 5' -T fields -e dhcp.ip.your

\# imprime: 10.20.20.20 (11 caracteres)

GUI Wireshark (lo que pide mostrar el video): abrir el paquete "DHCP ACK" \> "Dynamic Host Configuration Protocol (ACK)" \> "Your (client) IP address: 10.20.20.20"; clic derecho \> Copy \> Value.

> **3.** Bajá la llave, permisos, y conectate al 9090 con esa IP como passphrase:

wget http://foo.leak.com.ar/llave-pdc.pem

chmod 600 llave-pdc.pem

ssh -i llave-pdc.pem -p 9090 pdc@parcial.leak.com.ar

date

**Demostrar:** el campo "Your (client) IP address" en Wireshark, la conexión SSH y un comando. (Para chequear la passphrase sin conectarte al host real: ssh-keygen -y -P '10.20.20.20' -f llave-pdc.pem — si acierta, imprime la clave pública y sale con 0; nunca imprime la privada.)

**Ojo:**

- 11 caracteres = **10.20.20.20** (la IP del cliente). NO 10.20.20.4 (servidor DHCP) ni 10.20.20.1 (DNS): esas tienen 10 caracteres.

- openssl pkey -in llave-pdc.pem ... NO sirve para comprobarla ("Could not read key"): la llave está en formato OpenSSH, usá ssh-keygen -y.

- Esta captura tiene solo 4 paquetes (Discover, Offer, Request, ACK); son los mismos que en botica.pcap, la captura del Parcial 2021-1C · Ejercicio 5, que está en Resueltos — Capturas (Wireshark), 'Responder preguntas sobre una captura'.

*Probado en Ubuntu 24.04 (lab): tshark da 10.20.20.20; ssh-keygen -y -P '10.20.20.20' -f llave-pdc.pem sale con 0 (passphrase CORRECTA) y con otra IP da "incorrect passphrase supplied to decrypt private key".*

### Llave con passphrase = MAC del gateway de pampero (llave-pdc-recu.pem), puerto 9090

**Tomado en:** Recuperatorio 2020-1C · Ejercicio III.

> **Idea:** La idea es entrar por ssh como pdc a parcial.leak.com.ar, puerto 9090, con llave-pdc-recu.pem, cuya passphrase es la MAC del gateway de pampero. El truco es sacar en pampero la IP del gateway con ip route, pingearlo y leer su MAC en ip neigh; si el 9090 falla, probá el 9091 que anota el apunte.

**Buscar:** llave-pdc-recu.pem, passphrase, 17 caracteres, direccion MAC, gateway, pampero.itba.edu.ar, puerto 9090, route -n, ip route, ip neigh, arp, puerto 9091, Obtenga control de la línea de comandos, obtenga control de la linea de comandos, usuario pdc, pdc, parcial.leak.com.ar, existe un servidor SSH en el puerto 9090, llave privada, foo.leak.com.ar, http://foo.leak.com.ar/llave-pdc-recu.pem, se encontrar en, está con una passphrase, esta con una passphrase, protegida con una passphrase, dirección MAC, XX:XX:XX:XX:XX:XX, 0123456789abcdef, del gateway del host pampero.itba.edu.ar, Muestre encontrar la passphrase, ej date

**Enunciado:** "Obtenga control de la línea de comandos del usuario pdc del host parcial.leak.com.ar (existe un servidor SSH en el puerto 9090). Una llave privada que permite ingresar al usuario pdc se encontrar en http://foo.leak.com.ar/llave-pdc-recu.pem, pero está con una passphrase. La passphrase tiene 17 caracteres y es la dirección MAC (XX:XX:XX:XX:XX:XX) del gateway del host pampero.itba.edu.ar. En el video respuesta que envíe: 1. Muestre encontrar la passphrase. 2. Muestre descarga de llave privada y como conectarse 3. Una vez en el shell ejecute algún comando (ej date). (2 puntos)" ("se encontrar en" y "Muestre encontrar la passphrase" están así en el original.)

**Solución:** la MAC es la del **gateway** de pampero → sacás la IP del gateway y después su MAC de la tabla ARP.

> **1.** ssh USUARIO@pampero.itba.edu.ar
>
> **2.** IP del gateway y su MAC. Primero la IP del gateway:

ip route

En la salida, mirá la línea default via X: X es la IP del gateway (el apunte vio 10.16.1.254). Forma vieja: route -n, columna Gateway de la fila con Destination 0.0.0.0.

Después pingealo (así aparece en la tabla ARP si no estaba) y leé su MAC, cambiando IP_GW por esa IP:

ping -c1 IP_GW

ip neigh show IP_GW \# forma vieja: arp -n IP_GW

En la línea que sale, lo que sigue a lladdr (forma vieja: columna HWaddress) es la MAC, que es la passphrase.

> **3.** wget http://foo.leak.com.ar/llave-pdc-recu.pem && chmod 600 llave-pdc-recu.pem
>
> **4.** ssh -i llave-pdc-recu.pem -p 9090 pdc@parcial.leak.com.ar y pegá la MAC; después date.

**Demostrar:** la línea default del ruteo, la línea de ip neigh con la MAC del gateway, la conexión y un comando.

**Ojo:**

- MAC del **gateway** (de la tabla ARP tras pingearlo), NO de una interfaz propia. Distinto del Parcial o Recuperatorio 2021-1C · Ejercicio III (interfaz en3, de ip link), que está en este mismo tipo (Resueltos — SSH, 'Entrar con una llave privada: passphrase o nombre de archivo por deducir').

- El apunte MasCompilado anota el puerto como **9091** (en su bloque "2C 2020", que es este mismo recuperatorio); el enunciado dice **9090**. Si uno falla, probá el otro (-p 9090 / -p 9091).

- 17 caracteres = la MAC con los dos puntos.

*Sin probar: hosts externos. El flujo ip route → ping → ip neigh para la MAC del gateway está probado en el lab.*

### Ruta a ubuntu a través de foo, llave /{pkname}.pem con la MAC de foo, ssh como pdc

**Tomado en:** Parcial 2016-1C (segundo parcial) · Ejercicio 1.

> **Idea:** La idea es entrar al shell de pdc@ubuntu (192.168.122.C), detrás de foo, con una ruta a 192.168.122.0/24 vía foo sin perder internet. El truco es que la llave se llama como la MAC de foo, en minúscula y sin los dos puntos: la leés con ip neigh tras pingear a foo y bajás el .pem con curl desde ubuntu.

**Buscar:** foo, ubuntu, pdc@ubuntu, 192.168.122.C, 192.168.122.B, curl http://129.168.122.C/{pkname}.pem, 129.168.122, default gateway es foo, IP forwarding activado, no realiza ningún tipo de NAT, no realiza ningun tipo de NAT, único virtual host, unico virtual host, red del laboratorio, laboratrio, no deben afectar la conectividad con internet, resto de la LAN, ruta, route add, route add -host, ip route, arp, arp -a, ip neigh, MAC, dirección MAC de la interface, direccion MAC de la interface, pkname, {pkname}.pem, 0123456789abcdef, .pem, curl pem, llave privada, ssh -i, ssh -l pdc, chmod 400, chmod 600, Permissions too open, UNPROTECTED PRIVATE KEY

**Enunciado:** "El host con dirección IP A (el valor de A se encuentra escrito en el pizarrón) conectado a la red del laboratorio al que llamaremos desde ahora foo y que cuenta con: IP forwarding activado; no realiza ningún tipo de NAT. El host ubuntu tiene la dirección IP 192.168.122.C (el valor de C se encuentra escrito en el pizarrón). Cuenta con: un servidor HTTP en el puerto 80 el cual aloja un único virtual host; una configuración de red que dice que el default gateway es foo. El objetivo de este ejercicio es poder usar el shell del usuario pdc@ubuntu. Tenga en cuenta que: los cambios que realice para tener conectividad con el servicio ssh de ubuntu no deben afectar la conectividad con internet y el resto de la LAN. La llave privada utilizada para acceder al usuario pdc del host ubuntu se encuentra en el recurso /{pkname}.pem del servidor HTTP del host ubuntu, donde {pkname} es la dirección MAC de la interface de red hacia la red del laboratrio del host foo (utilizando únicamente los caracteres 0123456789abcdef). Tip: puede obtener la llave ejecutando curl http://129.168.122.C/{pkname}.pem". Diagrama: alumno, switch, foo (A en la red del laboratorio, 192.168.122.B del otro lado, con B: 1), switch, ubuntu (192.168.122.C).

**Solución:**

> **1.** Agregá una ruta hacia la red de ubuntu (192.168.122.0/24) usando a foo como gateway. Ruta de host alcanza; de red es más prolijo.

\# IP_FOO = A del pizarron (foo en la red del lab)

\# forma moderna

sudo ip route add 192.168.122.0/24 via IP_FOO

\# equivalente viejo

\# sudo route add -net 192.168.122.0 netmask 255.255.255.0 gw IP_FOO

> **2.** Pingeá a ubuntu (pasa por foo) y después leé la MAC de foo de la tabla ARP. Ese es el nombre del .pem.

ping -c1 192.168.122.C \# prueba que la ruta anda (pasa por foo)

ping -c1 IP_FOO \# fuerza la entrada ARP de foo

ip neigh show IP_FOO \# la MAC esta en "lladdr"; equivalente viejo: arp -a (busca la linea de IP_FOO)

\# sacale los ':' a mano -\> ese es el pkname (ej 4a:4b:9b:01:ed:d0 -\> 4a4b9b01edd0)

> **3.** Bajá la llave con curl (el nombre es la MAC sin :) y guardala.

curl -s http://192.168.122.C/MACDEFOO.pem \> foo.pem

> **4.** Restringí los permisos (ssh rechaza llaves legibles por otros) y conectate como pdc.

chmod 400 foo.pem \# o 600; lo importante es quitar permisos a grupo/otros

ssh -i foo.pem -l pdc 192.168.122.C

\# (la primera vez te pregunta por el fingerprint: respondé yes)

**Demostrar:** ip neigh/arp -a mostrando la MAC de foo; el curl bajando el .pem cuyo nombre es esa MAC; y el prompt de pdc@ubuntu con un whoami/date adentro.

**Ojo:**

- El {pkname} es la MAC de **foo** (el gateway), no la de ubuntu ni la tuya. La leés de tu tabla ARP después de pingear a foo.

- MAC **sin** los dos puntos y en minúscula: 4a:4b:9b:01:ed:d0 → 4a4b9b01edd0.pem.

- Si no bajás permisos, ssh corta con WARNING: UNPROTECTED PRIVATE KEY FILE! / Permissions 0664 for 'foo.pem' are too open / bad permissions (el número puede ser 0644). chmod 400 foo.pem lo arregla.

- -l pdc (o pdc@192.168.122.C) es obligatorio: sin el usuario correcto no entrás.

- El Tip del enunciado dice curl http://129.168.122.C/... con un typo (129 en vez de 192). La IP correcta es 192.168.122.C.

- Ruta de host vs de red: ip route add 192.168.122.C via IP_FOO (solo ubuntu) también sirve y no toca el resto de la LAN.

*Probado en Ubuntu 24.04 (lab): topología alumno–foo(router)–ubuntu con netns; ruta vía foo, ip neigh revela la MAC de foo, curl del \<MAC\>.pem desde ubuntu, y login por llave con chmod 400 (y el rechazo con permisos abiertos). La MAC real y la IP C salen del pizarrón.*

### Ruta a ubuntu, MAC, llave por HTTP, ssh y túnel reverso al recurso web

**Tomado en:** Parcial 2014-1C · Ejercicio 1.

> **Idea:** La idea es llegar a pdc@ubuntu (192.168.122.156) con una ruta vía foo, bajar por HTTP la llave cuyo nombre es la MAC de foo sin los dos puntos y, ya por ssh, darle a ubuntu acceso a un recurso web. El truco es el túnel reverso -R 8080:itba.edu.ar:80, porque foo no hace NAT y ubuntu no tiene salida.

**Buscar:** foo, ubuntu, pdc@ubuntu, 192.168.122.156, 192.168.122.1, 10.6.0.170, 10.6.0.188, 00:90:f5:ca:1d:73, 0090f5ca1d73, ruta, route add, route add -net, ip route, arp, arp -n, ip neigh, MAC, macaddress, llave privada, clave ssh privada, webserver, pem, curl, ssh -i, ssh -l pdc, chmod 600, chmod 400, tunel reverso, túnel reverso, ssh -R, -R 8080:itba.edu.ar:80, recurso web, Acceder al recurso web, netcat, remote port forwarding failed

**Enunciado:** No hay consigna en las fuentes; está reconstruido de la resolución de un alumno. Topología: "MI HOST (10.6.0.188) ---\> (10.6.0.170) FOO (192.168.122.1) ---\> (192.168.122.156) pdc@ubuntu". Llegar a ubuntu a través de foo, bajar la llave privada de pdc que está "hosteada en un webserver" (el nombre del archivo es una MAC, en el apunte la de foo: 00:90:f5:ca:1d:73, 0090f5ca1d73), conectarse por ssh como pdc y "acceder al recurso web" con un túnel reverso.

**Solución:**

> **1.** Ruta hacia la red de ubuntu vía foo:

\# IP_FOO = foo en la red del lab (en el apunte, 10.6.0.170)

sudo ip route add 192.168.122.0/24 via IP_FOO

\# equivalente viejo

\# sudo route add -net 192.168.122.0 netmask 255.255.255.0 gw IP_FOO dev IFACE

> **2.** Conseguí la MAC de foo. Pingeá a ubuntu: el paquete sale por foo, y así foo aparece en tu tabla ARP:

ping -c1 192.168.122.156

ip neigh \# forma moderna; la MAC esta en lladdr

\# arp -n \# equivalente viejo

En la salida, mirá la fila de IP_FOO (en el apunte, 10.6.0.170): lo que sigue a lladdr es la MAC de foo. Sacale los : a mano (00:90:f5:ca:1d:73 → 0090f5ca1d73) y bajá la llave poniendo eso en lugar de ARCHIVO_CON_LA_MAC:

curl -s http://192.168.122.156/ARCHIVO_CON_LA_MAC.pem \> clave.pem

> **3.** Permisos y ssh:

chmod 600 clave.pem \# o 400

ssh -i clave.pem -l pdc 192.168.122.156

> **4.** Túnel reverso a un recurso web. ubuntu no tiene salida (foo no hace NAT): con -R se abre un puerto en ubuntu que sale por tu PC. En tu PC:

ssh -i clave.pem -R 8080:itba.edu.ar:80 pdc@192.168.122.156

Y en la sesión que queda abierta, ya en ubuntu (en la salida de ss -ltn, mirá las líneas con :8080):

ss -ltn

curl -H 'Host: itba.edu.ar' http://localhost:8080/

La forma general es -R PUERTO_EN_UBUNTU:HOST_DESTINO:PUERTO_DESTINO: HOST_DESTINO se resuelve y se alcanza desde tu PC, y se prueba del otro lado contra localhost:PUERTO_EN_UBUNTU.

> **5.** Variante para darle salida a cualquier sitio, no a uno solo: SOCKS reverso.

\# en tu PC: abre un proxy SOCKS EN ubuntu que sale por tu PC

ssh -i clave.pem -R 1080 pdc@192.168.122.156

\# ya en ubuntu:

\# curl --socks5-hostname localhost:1080 http://api.ipify.org

**Demostrar:** ip neigh con la MAC; el curl bajando el .pem; el login pdc@ubuntu; y, ya en ubuntu, el puerto del túnel escuchando y la respuesta del sitio:

10.6.0.170 dev IFACE lladdr 00:90:f5:ca:1d:73 REACHABLE

LISTEN 0 128 127.0.0.1:8080 0.0.0.0:\*

LISTEN 0 128 \[::1\]:8080 \[::\]:\*

HTTP/1.1 301 Moved Permanently

(el 301 se ve agregando -i al curl; es la respuesta de itba.edu.ar, que redirige a https)

**Ojo:**

- El apunte escribe pdc@192.168.1.156 en el comando del túnel: es 192.168.122.156. Con la IP mal da Connection timed out.

- El nombre del .pem es una MAC sin los :. Como ubuntu está detrás de foo, en tu tabla ARP nunca aparece la MAC de ubuntu: aparece la de foo, en la fila de IP_FOO.

- chmod 600 o 400 obligatorio; si no, ssh rechaza con UNPROTECTED PRIVATE KEY FILE / Permissions 0664 for 'clave.pem' are too open (el número puede ser 0644).

- Túnel reverso es -R (abre el puerto en el host remoto). Túnel local es -L (abre el puerto en tu PC). Para que ubuntu use algo que solo alcanza tu PC, es -R.

- Sin -H 'Host: itba.edu.ar' el pedido llega con Host: localhost:8080 y el servidor contesta su sitio por defecto.

- Warning: remote port forwarding failed for listen port 8080: ese puerto ya está en uso en ubuntu (otro alumno u otro túnel tuyo). Usá otro, por ejemplo tu legajo.

- ssh -R 1080 host sin destino arma un SOCKS reverso (OpenSSH 7.6 o posterior en tu PC).

*Probado en Ubuntu 24.04 (lab), con namespaces PC, foo y ubuntu (ubuntu sin salida) y itba.edu.ar simulado por un servidor del lab: ruta, MAC de foo en ip neigh, llave por HTTP, login como pdc, el túnel -R 8080 y el SOCKS reverso -R 1080.*

### Agregar la ruta a 192.168.122.0/24, bajar la llave ospf.pem por HTTP y entrar por ssh como pdc

**Tomado en:** Parcial sin fecha · Ejercicio 1.

> **Idea:** La idea es entrar por ssh como pdc a bar (192.168.122.189) agregando una ruta a 192.168.122.0/24 vía 10.6.0.243 y bajando por HTTP la llave ospf.pem. No hay nada que deducir: la trampa es no tocar la ruta por defecto, para no perder internet, y hacer el chmod 600 antes del ssh.

**Contexto del parcial:** las fuentes no traen la consigna de este parcial, solo la resolución de un alumno (el apunte que nombra el Ojo); el enunciado está reconstruido de ahí.

**Buscar:** bar, foo, 192.168.122.189, 192.168.122.0/24, 10.6.0.243, ospf.pem, pdc, llave privada, curl, ssh -i, chmod 600, chmod og-rw, route add -net, ip route add, UNPROTECTED PRIVATE KEY FILE, Permissions are too open, bad permissions

**Enunciado:** reconstruido: llegar al host bar (192.168.122.189), que está detrás de un host del laboratorio (10.6.0.243); bajar la llave privada publicada en http://192.168.122.189/ospf.pem y usarla para entrar por ssh como el usuario pdc.

**Solución:**

> **1.** Ruta hacia la red de bar a través del host intermedio. No toques la ruta por defecto: así no perdés Internet.

sudo ip route add 192.168.122.0/24 via 10.6.0.243

\# forma vieja: sudo route add -net 192.168.122.0 netmask 255.255.255.0 gw 10.6.0.243

\# en Mac: sudo route add 192.168.122.0/24 10.6.0.243

> **2.** Bajá la llave, cerrale los permisos y entrá.

curl -s -o ospf.pem http://192.168.122.189/ospf.pem

chmod 600 ospf.pem

ssh -i ospf.pem pdc@192.168.122.189

**Demostrar:**

ip route get 192.168.122.189

ssh -i ospf.pem pdc@192.168.122.189 'whoami; hostname -I'

192.168.122.189 via 10.6.0.243 dev IFACE src 10.6.0.100

pdc

192.168.122.189

**Ojo:**

- El apunte hace el chmod después del ssh: tiene que ir antes. Sin él, ssh muestra WARNING: UNPROTECTED PRIVATE KEY FILE!, Permissions 0664 for 'ospf.pem' are too open. (el número puede ser 0644), Load key "ospf.pem": bad permissions y termina en Permission denied (publickey,password).

- chmod og-rw ospf.pem (el del apunte) también sirve; chmod 600 es lo mismo, más corto.

- El usuario es pdc: sin pdc@ (o -l pdc) ssh prueba con tu usuario local y falla.

- Si el ping a 192.168.122.189 no vuelve después de agregar la ruta, revisá que el gateway sea alcanzable: ping -c 1 10.6.0.243.

*Probado en Ubuntu 24.04 (lab). Sin probar: la forma de Mac.*

## Seguir un archivo remoto con tail -f por un túnel

En todos hay que lograr que un tail -f sobre un archivo de tu host muestre en vivo lo que se escribe en un archivo de otro host, canalizando con tail -f, netcat y un túnel SSH; cambian los archivos, el host remoto, el puerto y si el túnel puede ser local (-L) o tiene que ser reverso (-R).

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| Seguir /tmp/pdc de pampero con tail -f archivo.log por un túnel SSH | Que tail -f archivo.log en tu host siga /tmp/pdc de pampero.itba.edu.ar | Puerto = tu legajo. Túnel local -L (la variante -R vale igual); trae las dos con los errores de cada una. Sin túnel vale la mitad. También tomado en Parcial 2020-1C · Ejercicio III (sigue /tmp/bar en lugar de /tmp/pdc). |
| Ver con tail -f /var/log/remoteapp.log el log /var/log/app.log de hal.popegolabs.com en tiempo real | Que tail -f /var/log/remoteapp.log en tu host muestre /var/log/app.log de hal.popegolabs.com | A hal se llega en dos saltos, por G (192.168.20.1), con ssh -J. El túnel tiene que ser reverso (-R): hal no puede iniciar conexiones hacia tu host. El archivo local va en /var/log (hay que darle permisos) y lo lee otro usuario por ssh. Puertos 1234 y 12000, no el legajo. Cuenta user / pdcpass. |

Si la consigna pide "el socket pasivo en el cliente" (así lo anota el apunte MasCompilado en su bloque "RECU 1C 2021"), es la variante -L, la solución principal del Parcial 2022-2C · Ejercicio 2; con -R el socket pasivo queda en pampero.

### Seguir /tmp/pdc de pampero con tail -f archivo.log por un túnel SSH

**Tomado en:** Parcial 2022-2C · Ejercicio 2.

**También tomado en:** Parcial 2020-1C · Ejercicio III (sigue /tmp/bar en lugar de /tmp/pdc).

> **Idea:** La idea es que tail -f archivo.log en tu host siga en vivo /tmp/pdc de pampero, pasando los datos con netcat por un túnel local -L en el puerto de tu legajo. El truco es el orden: primero el ssh que deja en pampero el tail volcado a un nc escuchando, y recién después el nc local que escribe archivo.log.

**Buscar:** túnel, tunel, túneles, tuneles, SSH, ssh -L, ssh -R, port forwarding, tail -f archivo.log, archivo.log, /tmp/pdc, pampero.itba.edu.ar, seguir los cambios, en vivo, netcat, nc -l, pipes, redirecciones, número de legajo, numero de legajo, puertos para túneles, coliciones, colisiones, mitad del puntaje, canalizar la información, canalizar la informacion, tail -f, /tmp/bar, nc, socket pasivo, legajo, pipe, redireccion, Realice los cambios necesarios para que desde una terminal de su host, al ejecutar el comando tail -f archivo.log, pueda seguir los cambios (en vivo), del archivo /tmp/bar del host pampero.itba.edu.ar, Cualquier solución que no involucre túneles, cualquier solucion que no involucre tuneles, mitad del puntaje posible, Si necesita asignar puertos para túneles, Si necesita asignar puertos para tuneles, pipes de UNIX, redirecciones de archivo, pueden canalizar la información, pueden canalizar la informacion, ssh -J, ProxyJump, salto por pampero, host interno, dos saltos, ProxyCommand, -W %h:%p, host intermedio, solo se llega desde pampero, accesible desde pampero, llave.pem, 2222, LLAVE_PAMPERO, Address already in use, pkill

**Enunciado:** Realice los cambios necesarios para que desde una terminal de su host al ejecutar el comando tail -f archivo.log pueda seguir los cambios (en vivo) del archivo /tmp/pdc del host pampero.itba.edu.ar. Nota: Cualquier solución que no involucre túneles obtendrá la mitad del puntaje posible. Si necesita asignar puertos para túneles use su número de legajo para evitar coliciones. Pista: tail -f, netcat y los pipes de UNIX (\|) y las redirecciones de archivo ( \> ) pueden canalizar la información. ("coliciones" está así en el original. En el Parcial 2020-1C · Ejercicio III el texto es el mismo palabra por palabra, con /tmp/bar en lugar de /tmp/pdc.)

**Solución:**

Túnel SSH local (-L): el puerto LEGAJO de tu máquina (socket pasivo en el cliente) queda reenviado al puerto LEGAJO del loopback de pampero, donde un netcat entrega lo que va sacando el tail. Reemplazá LEGAJO por tu número de legajo (es el puerto) y USUARIO por tu usuario de pampero. Si te toca el Parcial 2020-1C · Ejercicio III el archivo es /tmp/bar: el paso 1 trae ese comando ya escrito; en los demás cambiá /tmp/pdc por /tmp/bar. Son tres terminales en tu máquina.

> **1.** Terminal 1: abre el túnel local y deja a pampero sirviendo el archivo en su puerto LEGAJO, solo en su loopback. Pide la clave y queda colgada sin mostrar nada: está bien.

ssh -L LEGAJO:127.0.0.1:LEGAJO USUARIO@pampero.itba.edu.ar 'tail -n +1 -f /tmp/pdc \| nc -l 127.0.0.1 LEGAJO'

En el Parcial 2020-1C · Ejercicio III (el mismo comando, con /tmp/bar):

\# -L LEGAJO:127.0.0.1:LEGAJO = el puerto LEGAJO de tu maquina reenvia a 127.0.0.1:LEGAJO de pampero

ssh -L LEGAJO:127.0.0.1:LEGAJO USUARIO@pampero.itba.edu.ar 'tail -n +1 -f /tmp/bar \| nc -l 127.0.0.1 LEGAJO'

> **2.** Terminal 2: en tu máquina, lee lo que sale del túnel y lo vuelca en el archivo. También queda colgada.

nc 127.0.0.1 LEGAJO \> archivo.log

> **3.** Terminal 3: el comando del enunciado.

tail -f archivo.log

**Demostrar:** la terminal 3 muestra las últimas líneas de /tmp/pdc y cada línea nueva apenas aparece (archivo.log tiene el archivo entero). Para forzar un cambio, desde otra sesión en pampero (si tenés permiso de escritura):

ssh USUARIO@pampero.itba.edu.ar 'echo "prueba LEGAJO" \>\> /tmp/pdc'

Y que el dato viaja por el túnel: en tu máquina el puerto LEGAJO lo escucha ssh. En la salida, mirá las líneas con :LEGAJO (tu legajo; en el ejemplo, 40123): al final dicen ssh.

ss -ltnp

\# viejo: netstat -ltnp

LISTEN 0 128 127.0.0.1:40123 0.0.0.0:\* users:(("ssh",pid=4123,fd=5))

LISTEN 0 128 \[::1\]:40123 \[::\]:\* users:(("ssh",pid=4123,fd=4))

**Ojo:**

- Orden: primero la terminal 1, después la 2. Si la 2 arranca antes de que el nc -l de pampero esté escuchando, sale channel 2: open failed: connect failed: Connection refused en la terminal 1 (el número de channel puede variar: también sale como channel 3: open failed: connect failed: Connection refused) y archivo.log queda vacío. Cortá las dos con Ctrl+C y empezá de nuevo.

- bind \[127.0.0.1\]:LEGAJO: Address already in use y Could not request local forwarding. (con -L): el puerto ya está usado en tu máquina (quedó un ssh o un nc viejo). pkill -f 'ssh -L' o elegí otro puerto.

- Antes de reintentar, matá lo que quedó de la vuelta anterior: en tu máquina pkill -x nc y en pampero ssh USUARIO@pampero.itba.edu.ar 'pkill -u USUARIO -x nc; pkill -u USUARIO -x tail'. El netcat de OpenBSD deja abrir un segundo nc -l tuyo en el mismo puerto sin avisar, y la conexión puede caer en el viejo.

- nc: Address already in use en pampero (con -L) o en tu máquina (con -R): ese puerto lo está usando otro usuario u otro programa. Elegí otro puerto (en todos los comandos).

- Usá 127.0.0.1 y no localhost en los tres lugares: con localhost el nc de pampero puede escuchar solo en IPv6 (::1) y el túnel ir por IPv4.

- tail -n +1 -f manda el archivo desde la primera línea y después sigue. Con tail -f solo, arrancás con las últimas 10 líneas.

- nc -l es con ele minúscula (listen), no con un uno. Si el nc de pampero es el netcat tradicional, la escucha se escribe nc -l -p LEGAJO -s 127.0.0.1.

- El puerto tiene que estar entre 1024 y 65535. Si tu legajo es mayor a 65535 usá los últimos cuatro dígitos con un 1 adelante.

- Cuando cortás la terminal 2, el nc -l de pampero termina: hay que relanzar la terminal 1.

- Variante con túnel remoto (-R, el socket pasivo queda en pampero; así lo resolvía el apunte del Parcial 2020-1C), igual de válida. Acá el orden es al revés: primero la escucha en tu máquina (terminal 1) y después el ssh (terminal 2); la terminal 3 es el mismo tail -f archivo.log.

nc -l 127.0.0.1 LEGAJO \> archivo.log

ssh -R LEGAJO:127.0.0.1:LEGAJO USUARIO@pampero.itba.edu.ar 'tail -n +1 -f /tmp/pdc \| nc 127.0.0.1 LEGAJO'

- Con -R el puerto LEGAJO lo escucha sshd en el loopback de pampero: ssh USUARIO@pampero.itba.edu.ar ss -ltn (mirá la línea con :LEGAJO). Si el nc -l de tu máquina no estaba escuchando, la terminal del ssh muestra connect_to 127.0.0.1 port LEGAJO: failed. y archivo.log queda vacío. Cortá con Ctrl+C y empezá de nuevo.

- Warning: remote port forwarding failed for listen port LEGAJO (con -R): ese puerto ya está usado en pampero. Elegí otro puerto en todos los comandos.

- Variante si el archivo está en un host al que solo se llega desde pampero: la terminal 1 salta por pampero con -J. Las terminales 2 y 3 quedan iguales. HOST_INTERNO es el host final y USUARIO_FINAL, tu usuario ahí.

ssh -J USUARIO@pampero.itba.edu.ar -L LEGAJO:127.0.0.1:LEGAJO USUARIO_FINAL@HOST_INTERNO 'tail -n +1 -f /tmp/pdc \| nc -l 127.0.0.1 LEGAJO'

\# si el host final pide llave y otro puerto (ej. 2222):

ssh -J USUARIO@pampero.itba.edu.ar -i llave.pem -p 2222 -L LEGAJO:127.0.0.1:LEGAJO USUARIO_FINAL@HOST_INTERNO 'tail -n +1 -f /tmp/pdc \| nc -l 127.0.0.1 LEGAJO'

\# si pampero tambien pide su propia llave (LLAVE_PAMPERO): ProxyCommand en lugar de -J

ssh -o ProxyCommand='ssh -i LLAVE_PAMPERO -W %h:%p USUARIO@pampero.itba.edu.ar' -i llave.pem -p 2222 -L LEGAJO:127.0.0.1:LEGAJO USUARIO_FINAL@HOST_INTERNO 'tail -n +1 -f /tmp/pdc \| nc -l 127.0.0.1 LEGAJO'

- En esa variante, el 127.0.0.1 del -L es el loopback del host final, no el de pampero. Puede pedir dos veces: primero tu clave de pampero y después la del host final (o la passphrase de llave.pem, si tiene). -i y -p son solo del host final: el salto a pampero (-J, ProxyJump) no los usa y entra con tu clave de siempre. Si pampero también te pide una llave propia, -J no tiene dónde ponerla: usá la tercera línea, con -o ProxyCommand en lugar de -J (sacale -i llave.pem -p 2222 si el host final no los pide). Para limpiar antes de reintentar, los pkill que iban en pampero van en el host final (el pkill -x nc de tu máquina queda igual): ssh -J USUARIO@pampero.itba.edu.ar USUARIO_FINAL@HOST_INTERNO 'pkill -u USUARIO_FINAL -x nc; pkill -u USUARIO_FINAL -x tail' (con -i llave.pem -p 2222 si hacen falta, y con el mismo -o ProxyCommand=... en lugar de -J si lo usaste). Si sale bind \[127.0.0.1\]:LEGAJO: Address already in use, el pkill -f 'ssh -L' de más arriba no agarra estos comandos (acá -L no va pegado a ssh): matá el ssh viejo con pkill -f 'LEGAJO:127.0.0.1:LEGAJO'.

- Sin túnel funciona pero vale la mitad: ssh USUARIO@pampero.itba.edu.ar 'tail -n +1 -f /tmp/pdc' \> archivo.log.

- El apunte del alumno usaba tres pasos equivalentes (ssh -L 12345:localhost:12345, tail -f /tmp/pdc \| nc -l localhost 12345 en pampero y nc localhost 12345 \> archivo.log), con un puerto que no era el legajo.

- El apunte de MasCompilado, para el Parcial 2020-1C · Ejercicio III, usaba el puerto 9090 (ssh -R9090:localhost:9090 pampero.itba.edu.ar, adentro tail -f /tmp/bar \| nc localhost 9090 y afuera nc -l 9090 \> archivo.log): el nc -l lo ponía último (tiene que ir primero), el puerto no era el legajo y arranca con las últimas 10 líneas.

- En el Parcial 2020-1C el host de este ejercicio es pampero.itba.edu.ar, sin ".it.". En el Parcial 2020-1C · Ejercicio I y el Parcial 2020-1C · Ejercicio II (Resueltos — Red, 'MAC y puertos abiertos de un host') figura como pampero.it.itba.edu.ar.

- Se probó contra un sshd local que imita a pampero, con el netcat de OpenBSD en las dos puntas. Las dos variantes (-L y -R) y los mensajes de error citados se probaron también de punta a punta entre tres máquinas simuladas (laptop, router y pampero).

*Probado en Ubuntu 24.04 (lab). Sin probar en el lab: la variante con salto por pampero (-J o ProxyCommand, con -L); se probó solo con dos sshd locales.*

### Ver con tail -f /var/log/remoteapp.log el log /var/log/app.log de hal.popegolabs.com en tiempo real

**Tomado en:** Parcial 2011 · Ejercicio 6.

> **Idea:** La idea es que quien entre por ssh a tu host vea en vivo, con tail -f /var/log/remoteapp.log, el /var/log/app.log de hal.popegolabs.com, saltando por G con ssh -J. El truco es que hal no puede conectarse a tu host: el túnel es reverso (-R 12000:localhost:1234) hacia tu nc, y el archivo de /var/log hay que crearlo y darle permisos.

**Contexto del parcial:** tu host está en la red de alumnos (192.168.10.0/25; en los ejemplos es 192.168.10.3) y entre esa red y la del laboratorio (10.6.0.0/24) está el host G (192.168.20.1 del lado de los alumnos), que no encamina paquetes ni hace NAT; hal.popegolabs.com está en Internet y se llega saltando por G. Para llegar a G por ssh hacen falta la IP y la ruta del Parcial 2011 · Ejercicio 1 (Resueltos — Red, 'Llegar a una red detrás de un gateway y descubrir sus servicios').

**Buscar:** tail -f, tail -F, /var/log/remoteapp.log, remoteapp.log, /var/log/app.log, app.log, en tiempo real, log de una aplicacion, log de una aplicación, hal.popegolabs.com, netcat, nc, nc -l, nc -lk, ssh -R, ssh -J, túnel reverso, tunel reverso, dos saltos, usuario que tenga acceso via ssh a su host, openssh-server, systemctl start ssh, Unit ssh.service not found, Connection refused, Unable to locate package, has no installation candidate, apt update, whoami, chown, invalid user

**Enunciado:** "Realizar los cambios necesarios para que un usuario que tenga acceso via ssh a su host pueda haciendo un tail -f /var/log/remoteapp.log ver en tiempo real el log de una aplicacion que loguea en /var/log/app.log en hal.popegolabs.com. Ayuda: Puede usar el comando netcat (nc) en la solucion." Datos del contexto: cuenta user / pdcpass en G (192.168.20.1) y en hal; hal no puede iniciar conexiones hacia tu host.

**Solución:**

> **1.** Prepará el archivo local para poder escribirlo sin ser root y para que otro usuario lo pueda leer. El servidor ssh de tu host tiene que estar instalado y corriendo. Antes averiguá tu usuario: corré whoami y anotalo (por ejemplo, alumno). Va en lugar de TU_USUARIO.

sudo touch /var/log/remoteapp.log

sudo chown TU_USUARIO /var/log/remoteapp.log

sudo chmod 644 /var/log/remoteapp.log

sudo apt install -y openssh-server \# si ya esta, no cambia nada

sudo systemctl start ssh

> **2.** Camino corto, un solo comando: corré el tail en hal saltando por G y volcá su salida en el archivo local (pide pdcpass dos veces). Dejalo corriendo.

ssh -J user@192.168.20.1 user@hal.popegolabs.com 'tail -F /var/log/app.log' \>\> /var/log/remoteapp.log

> **3.** Camino con netcat, el de la ayuda. Terminal 1, en tu host: nc escucha y guarda lo que llega.

nc -lk 1234 \>\> /var/log/remoteapp.log

> **4.** Terminal 2, en tu host: túnel reverso hasta hal (su puerto 12000 termina en tu puerto 1234) y, en hal, el tail hacia ese puerto.

ssh -J user@192.168.20.1 -R 12000:localhost:1234 user@hal.popegolabs.com 'tail -F /var/log/app.log \| nc localhost 12000'

\# forma vieja, en dos saltos encadenados:

\# en tu host: ssh -R 11000:localhost:1234 user@192.168.20.1

\# en G: ssh -R 12000:localhost:11000 user@hal.popegolabs.com

\# en hal: tail -f /var/log/app.log \| nc localhost 12000

**Demostrar:** desde otra máquina, con un usuario de tu host:

ssh USUARIO@192.168.10.3 tail -f /var/log/remoteapp.log

19:32:38 linea 421 de app en hal

19:32:39 linea 422 de app en hal

19:32:40 linea 423 de app en hal

Las líneas nuevas tienen que ir apareciendo solas, al ritmo en que la aplicación escribe.

**Ojo:**

- La conexión siempre la inicia tu host: hal está en Internet y no tiene cómo llegar a 192.168.10.x. Por eso el túnel es reverso (-R) y no local.

- bash: /var/log/remoteapp.log: Permission denied: en /var/log solo escribe root, y sudo nc -l 1234 \>\> archivo no lo arregla porque la redirección la hace tu shell. Hacé el paso 1 y fijate que el chown no haya dado error.

- chown: invalid user: ‘TU_USUARIO’: no cambiaste TU_USUARIO por tu usuario (el que muestra whoami). Repetí ese chown con tu usuario.

- nc -l 1234 sin -k termina cuando se corta la conexión. Con netcat tradicional la sintaxis es nc -l -p 1234.

- tail -F sigue al archivo aunque lo roten; tail -f deja de mostrar si lo reemplazan.

- Si en el paso 4 ves Warning: remote port forwarding failed for listen port 12000: ese puerto está ocupado en hal, cambiá el número en los dos lugares.

- Si cerrás la terminal se corta todo. Para dejarlo andando, corrélo dentro de screen o tmux.

- Failed to start ssh.service: Unit ssh.service not found. en el paso 1: openssh-server no quedó instalado (Ubuntu Desktop no lo trae) porque falló el apt install. Si dio E: Unable to locate package openssh-server o E: Package 'openssh-server' has no installation candidate, corré sudo apt update, repetí sudo apt install -y openssh-server y de nuevo sudo systemctl start ssh. Sin servidor, el ssh de Demostrar da Connection refused.

*Probado en Ubuntu 24.04 (lab). Sin probar: el ingreso con la contraseña pdcpass (en el lab se entró con llaves) y el apt install de openssh-server con su mensaje Unit ssh.service not found (el lab ya trae el servidor SSH y no permite instalar).*

## Salir a internet o llegar a un servicio interno con túneles

En todos hay un host que no llega a donde hace falta (no tiene salida a internet, o el servicio escucha solo en el loopback de otro host) y se resuelve con túneles SSH sobre una conexión que sí está permitida; cambia quién necesita llegar a qué, el tipo de túnel (SOCKS reverso -R PUERTO, dinámico -D, local -L saltando por otro host) y con qué llave o cuenta se entra.

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| Salida a internet sin NAT (túnel SOCKS dinámico) | Que parcial.leak.com.ar (IP privada, gateway sin NAT) acceda con curl a http://api.ipify.org, https://campus.itba.edu.ar/ y cualquier otro sitio | Depende del Parcial o Recuperatorio 2021-1C · Ejercicio III: se entra con llave.pem por el puerto 2222. SOCKS reverso -R 1080 desde tu laptop y curl -x socks5h://localhost:1080. Sin túnel vale la mitad. También tomado en Recuperatorio 2020-1C · Ejercicio IV (depende del Recuperatorio 2020-1C · Ejercicio III: se entra con llave-pdc-recu.pem por el puerto 9090; el enunciado dice "ejercicio VIII" por error). |
| Acceder al Apache que escucha en el loopback de hal.popegolabs.com (virtualhost www.popego.com) con túneles encadenados y proxy | Ver en un browser el virtualhost www.popego.com, que un Apache sirve en el puerto 80 del loopback de hal.popegolabs.com | Es el único que llega a un servicio interno; los otros dan salida a internet. Túnel local -L 80:localhost:80 saltando por G con -J, /etc/hosts para el nombre y un segundo túnel hasta proxy.itba.edu.ar:8080 por pampero. Cuenta user / pdcpass, sin llave. |
| Que bar navegue por Internet a través de tu máquina: proxy SOCKS con ssh -D y túnel reverso -R | Que bar (192.168.122.189) pueda pedir una página de Internet (http://google.com) saliendo por tu máquina | Depende del Parcial sin fecha · Ejercicio 1: se entra con ospf.pem. SOCKS reverso -R 1234 y curl --socks5-hostname localhost:1234; trae la forma vieja en dos pasos (-D 1234 más -R 1234:localhost:1234). Sin consigna: reconstruido de un apunte. |

Hay otro túnel reverso en el Parcial 2014-1C · Ejercicio 1 (Resueltos — SSH, 'Entrar con una llave privada: passphrase o nombre de archivo por deducir'): después de entrar con la llave abre -R 8080:itba.edu.ar:80 para que ubuntu llegue a un sitio web.

### Salida a internet sin NAT (túnel SOCKS dinámico)

**Tomado en:** Parcial o Recuperatorio 2021-1C · Ejercicio IV.

**También tomado en:** Recuperatorio 2020-1C · Ejercicio IV (se entra con llave-pdc-recu.pem por el puerto 9090 en lugar de llave.pem por el 2222, y depende del Recuperatorio 2020-1C · Ejercicio III).

> **Idea:** La idea es que parcial.leak.com.ar, con IP privada y gateway sin NAT, acceda con curl a cualquier sitio de internet. El truco es entrar desde tu laptop con ssh -i llave.pem -p 2222 -R 1080 (SOCKS reverso: escucha en parcial y sale por tu laptop) y usar curl -x socks5h://localhost:1080, con la h para resolver el DNS por el proxy.

**Buscar:** única interfaz, IP privada, gateway no realiza NAT, acceder a internet, curl, api.ipify.org, campus.itba.edu.ar, tunel dinamico, SOCKS, socks5, socks5h, ssh -D, ssh -R, proxy, única interfaz de red, unica interfaz de red, dirección IP privada, direccion IP privada, su gateway no realiza NAT, se pueda acceder a cualquier sitio de internet utilizando curl, puede pedir la solución, puede pedir la solucion, unica interfaz, túnel dinámico, parcial.leak.com.ar, al cual accedió anteriormente en el Ejercicio III, al cual accedio anteriormente en el Ejercicio III, desde la línea de comandos, desde la linea de comandos, http://api.ipify.org, https://campus.itba.edu.ar/, cualquier otro arbitrario, requiere de la realización del Ejercicio III, requiere de la realizacion del Ejercicio III, Si no puede resolver aquel ejercicio, perdiendo esos puntos, Cualquier solución que no involucre túneles, cualquier solucion que no involucre tuneles, mitad del puntaje posible, al cual accedió anteriormente en el ejercicio VIII, al cual accedio anteriormente en el ejercicio VIII, ejercicio VIII, requiere de la realización del ejercicio III, requiere de la realizacion del ejercicio III, Si no sabe resolver ese ejercicio, llave-pdc-recu.pem, puerto 9090

**Enunciado:** "El host parcial.leak.com.ar (al cual accedió anteriormente en el Ejercicio III) tiene una única interfaz de red con una dirección IP privada, y su gateway no realiza NAT. Realice los cambios necesarios para que desde la línea de comandos se pueda acceder a cualquier sitio de internet utilizando curl. Por ejemplo se tiene que poder acceder a http://api.ipify.org y a https://campus.itba.edu.ar/ y a cualquier otro arbitrario. Atención Este ejercicio requiere de la realización del Ejercicio III. Si no puede resolver aquel ejercicio puede pedir la solución para realizarlo (perdiendo esos puntos). Cualquier solución que no involucre túneles obtendrá la mitad del puntaje posible."

**Enunciado en Recuperatorio 2020-1C · Ejercicio IV:** "El host parcial.leak.com.ar (al cual accedió anteriormente en el ejercicio VIII) tiene una única interfaz de red con una dirección IP privada, y su gateway no realiza NAT. Realice los cambios necesarios para que desde la línea de comandos se pueda acceder a cualquier sitio de internet utilizando curl. Por ejemplo se tiene que poder acceder a http://api.ipify.org y a https://campus.itba.edu.ar/ y a cualquier otro arbitrario. Atención requiere de la realización del ejercicio III. Si no sabe resolver ese ejercicio puede pedir la solución para realizarlo (perdiendo esos puntos). (2 puntos)" (Dice "ejercicio VIII" en el original, aunque el recuperatorio tiene V ejercicios y más abajo remite al ejercicio III.)

**Depende de:** el ejercicio de la llave de tu parcial.

- Parcial o Recuperatorio 2021-1C · Ejercicio III (Resueltos — SSH, 'Entrar con una llave privada: passphrase o nombre de archivo por deducir'): de ahí salen llave.pem, su passphrase (la MAC de la interfaz en3 de pampero) y el acceso a pdc@parcial.leak.com.ar por el puerto 2222.

- Recuperatorio 2020-1C · Ejercicio III (Resueltos — SSH, 'Entrar con una llave privada: passphrase o nombre de archivo por deducir'): de ahí salen llave-pdc-recu.pem, su passphrase (la MAC del gateway de pampero) y el acceso a pdc@parcial.leak.com.ar por el puerto 9090. El "ejercicio VIII" que dice su enunciado es una errata: es ese Ejercicio III.

**Solución:** parcial no puede salir solo (IP privada, sin NAT), pero tu laptop sí tiene internet y ya tiene una conexión SSH con parcial. Abrís un **SOCKS dinámico remoto** (-R): el proxy escucha EN parcial y el tráfico egresa por tu laptop.

Desde tu laptop (la que tiene internet):

\# -R 1080 = SOCKS dinamico en el lado del servidor (parcial); el trafico sale por tu laptop

ssh -i llave.pem -p 2222 -R 1080 pdc@parcial.leak.com.ar

En el Recuperatorio 2020-1C · Ejercicio IV es el mismo comando con la otra llave y el otro puerto:

ssh -i llave-pdc-recu.pem -p 9090 -R 1080 pdc@parcial.leak.com.ar

Ya adentro de parcial, curl a través del proxy local:

curl -x socks5h://localhost:1080 http://api.ipify.org

curl -x socks5h://localhost:1080 https://campus.itba.edu.ar/

Alternativa con el dinámico clásico (-D): desde parcial, ssh -f -N -D 1080 USUARIO@pampero.itba.edu.ar (-f -N lo deja en segundo plano sin abrir shell) y luego curl -x socks5h://localhost:1080 ... en parcial. Solo sirve si desde parcial hay ruta a pampero: probalo antes con nc -vz -w 3 pampero.itba.edu.ar 22 (tiene que decir succeeded!); si no conecta (Network is unreachable, timed out, Temporary failure in name resolution), usá la forma -R 1080 desde tu máquina.

**Demostrar:** curl -x socks5h://localhost:1080 http://api.ipify.org devuelve una IP pública (prueba que salió a internet por el túnel).

**Ojo:**

- Sin túnel = mitad de puntaje (lo avisa el enunciado del Parcial o Recuperatorio 2021-1C; el del Recuperatorio 2020-1C no trae esa nota). El ssh -D60567 localhost del apunte es dudoso: localhost es el propio parcial, que no tiene internet; el egreso tiene que ser por un host que SÍ salga (tu laptop con -R, o pampero con -D).

- Usá socks5h:// (la **h**) para que la resolución DNS también pase por el proxy; con socks5:// parcial intenta resolver localmente y puede fallar.

- -R 1080 (SOCKS dinámico remoto) necesita OpenSSH ≥ 7.6; la VM del ITBA lo tiene.

- Verificá el túnel con ss -tlnp (vieja: netstat -ntlp) en el lado donde abriste el SOCKS: tiene que aparecer una línea con :1080.

*Probado en Ubuntu 24.04 (lab): con parcial simulado en un namespace sin ruta por defecto ni DNS, ssh -R 1080 desde la laptop y curl -x socks5h://localhost:1080 traen http://api.ipify.org y https://campus.itba.edu.ar/ egresando por la laptop. La alternativa -D se probó con un enlace directo de parcial a un pampero simulado.*

### Acceder al Apache que escucha en el loopback de hal.popegolabs.com (virtualhost www.popego.com) con túneles encadenados y proxy

**Tomado en:** Parcial 2011 · Ejercicio 5.

> **Idea:** La idea es ver en un browser el virtualhost www.popego.com, que Apache sirve solo en el loopback de hal.popegolabs.com, con un túnel -L 80:localhost:80 saltando por G con -J. El truco es apuntar www.popego.com a 127.0.0.1 en /etc/hosts para que viaje el Host correcto, y sumar un segundo túnel al proxy del ITBA por pampero para lo externo.

**Contexto del parcial:** tu host está en la red de alumnos (192.168.10.0/25) y entre esa red y la del laboratorio (10.6.0.0/24) está el host G (192.168.20.1 del lado de los alumnos), que no encamina paquetes ni hace NAT; hal.popegolabs.com está en Internet. Para llegar a G por ssh hacen falta la IP y la ruta del Parcial 2011 · Ejercicio 1 (Resueltos — Red, 'Llegar a una red detrás de un gateway y descubrir sus servicios').

**Buscar:** hal.popegolabs.com, hal, Apache, puerto 80 de la interfaz loopback, loopback, virtualhost, www.popego.com, popego, www.popego123.com, se visualice correctamente desde un browser, túneles encadenados, tuneles encadenados, túnel, tunel, ssh -L, ssh -J, ProxyJump, proxy.itba.edu.ar, 8080, pampero.itba.edu.ar, proxy HTTP, Firefox, sin proxy para, no proxy for, /etc/hosts, bind: Permission denied

**Enunciado:** "En el host hal.popegolabs.com existe un servidor Apache que sirve en el puerto 80 de la interfaz loopback el virtualhost www.popego.com. Realizar los cambios necesarios para poder acceder a dicho site y que se visualice correctamente desde un browser." Datos del contexto: cuenta user / pdcpass en G (192.168.20.1) y en hal; proxy HTTP en proxy.itba.edu.ar:8080, accesible desde pampero.itba.edu.ar; tu host no tiene Internet porque G no encamina.

**Solución:**

> **1.** El Apache solo escucha en el 127.0.0.1 de hal: hay que llegar por SSH hasta hal y pedirle la página desde adentro. Tu host no llega a hal, pero G sí: se salta por G. Túnel desde tu puerto 80 hasta el puerto 80 del loopback de hal (pide la contraseña pdcpass dos veces, una por G y otra por hal). Dejalo abierto.

sudo ssh -N -J user@192.168.20.1 -L 80:localhost:80 user@hal.popegolabs.com

\# forma vieja, en dos pasos (tuneles encadenados):

\# en tu host: sudo ssh -L 80:localhost:8082 user@192.168.20.1

\# en G: ssh -L 8082:localhost:80 user@hal.popegolabs.com

> **2.** El virtualhost se elige por nombre: hacé que www.popego.com apunte a tu extremo del túnel.

Hay que hacer sudo nano /etc/hosts y agregar al final lo siguiente:

127.0.0.1 www.popego.com

> **3.** "Que se visualice correctamente": la página trae estilos e imágenes de otros sitios de Internet y tu host no tiene salida. Segundo túnel, hasta el proxy del ITBA, saltando por G y por pampero (USUARIO_ITBA es tu cuenta en pampero). Dejalo abierto en otra terminal.

ssh -N -J user@192.168.20.1 -L 8080:proxy.itba.edu.ar:8080 USUARIO_ITBA@pampero.itba.edu.ar

\# forma vieja, en dos pasos:

\# en tu host: ssh -L 8080:localhost:8081 user@192.168.20.1

\# en G: ssh -L 8081:proxy.itba.edu.ar:8080 USUARIO_ITBA@pampero.itba.edu.ar

> **4.** En Firefox: Ajustes, Configuración de red, Configuración manual del proxy. Proxy HTTP localhost, puerto 8080. En el campo de excepciones ("Sin proxy para", en inglés "No proxy for") escribí www.popego.com. Abrí http://www.popego.com/.

**Demostrar:**

\| head muestra solo las primeras 10 líneas del HTML, para que la página no tape la pantalla.

\# el site, por el tunel a hal

curl -s http://www.popego.com/ \| head

\# Internet, por el tunel al proxy

curl -s -x http://localhost:8080 http://www.example.org/ \| head

\# lo mismo que hace el browser: proxy para todo menos para popego

curl -s -x http://localhost:8080 --noproxy www.popego.com http://www.popego.com/ \| head

ss -ltn \# mirá las líneas con :80 y :8080

LISTEN 0 128 127.0.0.1:80 0.0.0.0:\*

LISTEN 0 128 127.0.0.1:8080 0.0.0.0:\*

**Ojo:**

- En -L 80:localhost:80, el localhost lo resuelve el último salto: es el loopback de hal, no el tuyo.

- Si entrás por http://localhost/ o por IP, Apache responde con su virtualhost por defecto y no con popego (probado). Tiene que viajar Host: www.popego.com, por eso el /etc/hosts.

- El apunte pone www.popego123.com en /etc/hosts y en Firefox, pero el virtualhost del enunciado es www.popego.com. Usá el nombre exacto del virtualhost.

- bind \[127.0.0.1\]:80: Permission denied y Could not request local forwarding.: abrir el puerto local 80 necesita root (por eso el sudo). Sin sudo usá -L 8082:localhost:80 y entrá a http://www.popego.com:8082/.

- Sin la excepción "Sin proxy para", el browser le pide www.popego.com al proxy del ITBA, que va al sitio de Internet y no a tu túnel.

- -J hace que hal.popegolabs.com lo resuelva G, que tiene DNS. Tu host no puede resolverlo y no hace falta.

- -N deja el túnel sin abrir una shell. Para mandarlo al fondo agregá -f.

- La primera vez ssh pregunta Are you sure you want to continue connecting (yes/no/\[fingerprint\])? por cada salto: respondé yes.

*Probado en Ubuntu 24.04 (lab). Sin probar: los clics de Firefox (el lab no tiene entorno gráfico; se probó lo mismo con curl) y los hosts reales (hal se simuló con un nginx en su loopback en lugar de Apache, y el proxy con uno que solo acepta a pampero).*

### Que bar navegue por Internet a través de tu máquina: proxy SOCKS con ssh -D y túnel reverso -R

**Tomado en:** Parcial sin fecha · Ejercicio 2.

> **Idea:** La idea es que bar (192.168.122.189), cuyos paquetes bloquea el host intermedio, pueda pedir una página de Internet saliendo por tu máquina. El truco es entrar con ospf.pem y ssh -R 1234, que abre en bar un proxy SOCKS cuyo tráfico sale por tu máquina, y pedir con curl --socks5-hostname localhost:1234 para que el nombre lo resuelva tu máquina.

**Contexto del parcial:** las fuentes no traen la consigna de este parcial, solo la resolución de un alumno (el apunte que nombra la Solución); el enunciado está reconstruido de ahí.

**Buscar:** SOCKS, socks5, proxy SOCKS, ssh -D, ssh -R, -D 1234, -R1234:localhost:1234, túnel reverso, tunel reverso, reenvío dinámico, reenvio dinamico, curl --socks5, --socks5-hostname, socks5h, paquetes bloqueados por A, bar sin internet, 192.168.122.189, ospf.pem, pdc, google.com, openssh-server, systemctl start ssh, Unit ssh.service not found, Unable to locate package, has no installation candidate, apt update, Connection refused, Failed to start ssh.service

**Enunciado:** reconstruido: desde bar (192.168.122.189) no se llega a Internet porque sus paquetes los bloquea el host intermedio A; lograr que desde bar se pueda pedir una página de Internet usando tu máquina como salida.

**Depende de:** Parcial sin fecha · Ejercicio 1 (Resueltos — SSH, 'Entrar con una llave privada: passphrase o nombre de archivo por deducir'). De ahí salen la ruta a 192.168.122.0/24 por el host intermedio 10.6.0.243, la llave ospf.pem y el usuario pdc de bar.

**Solución:**

> **1.** Un solo comando desde tu máquina: -R con solo un puerto abre en bar un proxy SOCKS (127.0.0.1:1234) cuyo tráfico sale por tu máquina. Dejá la sesión abierta.

ssh -i ospf.pem -R 1234 pdc@192.168.122.189

> **2.** En esa sesión (ya estás en bar), usá el proxy:

curl --socks5-hostname localhost:1234 http://google.com

> **3.** Forma del apunte, en dos pasos, para clientes ssh viejos (anteriores a OpenSSH 7.6). Necesita un servidor ssh instalado y corriendo en tu propia máquina.

sudo apt install -y openssh-server \# si ya esta, no cambia nada

sudo systemctl start ssh

\# terminal 1: proxy SOCKS local en tu puerto 1234

ssh -N -D 1234 localhost

\# terminal 2: el puerto 1234 de bar termina en tu puerto 1234

ssh -i ospf.pem -R 1234:localhost:1234 pdc@192.168.122.189

\# en bar:

curl --socks5-hostname localhost:1234 http://google.com

**Demostrar:** en bar, sin proxy falla y con proxy responde (\| head -5 muestra solo las primeras 5 líneas de la página; en la salida de ss -ltn, mirá la línea con :1234).

curl -m 5 http://google.com

curl -s -m 5 --socks5-hostname localhost:1234 http://google.com \| head -5

ss -ltn

curl: (7) Failed to connect to google.com port 80 after 3 ms: Couldn't connect to server

\<HTML\>\<HEAD\>\<meta http-equiv="content-type" content="text/html;charset=utf-8"\>

\<TITLE\>301 Moved\</TITLE\>\</HEAD\>\<BODY\>

LISTEN 0 128 127.0.0.1:1234 0.0.0.0:\*

Sin proxy, curl falla y muestra el código entre paréntesis: curl: (6) Could not resolve host: google.com si bar ni siquiera resuelve el nombre, curl: (7) ... Couldn't connect to server si el host intermedio rechaza, y curl: (28) Connection timed out after 5000 milliseconds (más o menos) si descarta en silencio y vence el tiempo.

**Ojo:**

- --socks5-hostname (igual que -x socks5h://localhost:1234) hace que el nombre lo resuelva tu máquina. Con --socks5 a secas lo resuelve bar, que no tiene DNS hacia afuera, y da Could not resolve host.

- -D abre el proxy en la máquina donde corrés ssh; -R PUERTO lo abre en la máquina remota. Acá hace falta en bar, por eso -R.

- No hace falta ser root en bar ni en el host intermedio: todo sale por la conexión ssh que ya tenés permitida.

- Warning: remote port forwarding failed for listen port 1234: el puerto está ocupado en bar (otro alumno o un túnel viejo). Cambiá el número.

- Para un browser en bar: proxy SOCKS v5, host 127.0.0.1, puerto 1234, y tildar el DNS por SOCKS.

- Failed to start ssh.service: Unit ssh.service not found. en el paso 3: openssh-server no quedó instalado (Ubuntu Desktop no lo trae) porque falló el apt install. Si dio E: Unable to locate package openssh-server o E: Package 'openssh-server' has no installation candidate, corré sudo apt update, repetí sudo apt install -y openssh-server y de nuevo sudo systemctl start ssh. Sin servidor, la terminal 1 (ssh -N -D 1234 localhost) da ssh: connect to host localhost port 22: Connection refused.

*Probado en Ubuntu 24.04 (lab). Sin probar: el apt install de openssh-server y su mensaje Unit ssh.service not found (el lab ya trae el servidor SSH y no permite instalar).*

# Resueltos — Red (rutas, descubrimiento y ARP)

## Llegar a una red detrás de un gateway y descubrir sus servicios

En todos hay un host o una red que no está en tu segmento: se llega agregando una ruta estática por un host intermedio (el gateway), sin tocar la ruta por defecto. De un parcial a otro cambia cómo te enterás de quién es el gateway y qué hay que mostrar una vez que llegás (servicios con nmap, HTTP con curl, routers con traceroute, ssh o ping).

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| Gateway 192.168.50.250: descubrir el servidor de 10.0.0.0/24, sus servicios, HTTP y routers | Gateway 192.168.50.250, red 10.0.0.0/24: descubrir la IP del servidor y sus servicios, mostrar el acceso por HTTP y los routers | No dan la IP del servidor: barrido con nmap -sn y hay que distinguirlo del gateway, que también aparece en esa red |
| Ruta por el gateway 192.168.1.3, servicios de 10.0.0.1, HTTP y routers | Gateway 192.168.1.3, red 10.0.0.0/24: servicios del host 10.0.0.1, acceso por HTTP y routers | La IP del servidor ya viene dada; el HTTP se muestra solo "de existir" |
| Acceder a un servidor HTTP detrás de otra máquina sin perder internet | Servidor HTTP en 192.168.1.1, detrás de una máquina cuya IP te dan en el momento | Hay que seguir teniendo internet: ruta específica y la default no se toca. No pide nmap ni traceroute |
| Paquete de Wireshark: hablar con un servicio en el puerto 22 detrás de un firewall | Servicio del puerto 22 del host 192.168.123.1 | El gateway se deduce de la MAC de un screenshot de Wireshark; el firewall del host solo deja pasar el 22, así que no se prueba con ping |
| Configurar la interfaz 192.168.10.x/25, que ping 10.6.0.127 responda G y que ping hostG funcione | Solo la interfaz del switch levantada, con IP 192.168.10.x/25; que respondan ping 10.6.0.127 y ping hostG | El gateway G (192.168.20.1) está en tu switch pero fuera de tu subred: primero ruta directa a esa IP y después la red 10.6.0.0/24 por ella. Suma configurar la interfaz y un nombre en /etc/hosts |

### Gateway 192.168.50.250: descubrir el servidor de 10.0.0.0/24, sus servicios, HTTP y routers

**Tomado en:** Parcial 2025-2Q · Ejercicio 2.

> **Idea:** La idea es llegar a la red 10.0.0.0/24 con una ruta estática por 192.168.50.250, encontrar el servidor con nmap -sn, listar sus servicios con nmap y mostrar el curl por HTTP y el traceroute. La trampa es que el barrido también devuelve la otra pata del gateway: el servidor es el que aparece a dos saltos y con ttl 63.

**Buscar:** gateway, 192.168.50.250, 10.0.0.0/24, ruta estática, ruta estatica, ip route add, route add, descubra la dirección del servidor, descubra la direccion del servidor, servidor distinto al gateway, servicios que tiene expuesto, nmap, nmap -sn, ping sweep, escaneo de puertos, -sV, acceder vía HTTP, acceder via HTTP, curl, routers que intervienen, traceroute, tracepath, saltos, hops, modo Bridge, Puente, red abierta, Ejercicio II, apt install, Command 'nmap' not found, sudo: nmap: command not found, Command 'traceroute' not found, Command 'curl' not found, command not found, Couldn't open a raw socket, dnet: Failed to open device, snap, inetutils-traceroute, sin nmap, sin traceroute, /dev/tcp, ping -t, Time to live exceeded, https, 443, curl -k, SSL certificate problem, Temporary failure resolving, Failed to fetch, NAT, wget, invalid option -- 'n', Connecting to, plan B, nc -zv, succeeded, Connection refused, open, inverse host lookup failed, netcat-traditional

**Enunciado:** El host en 192.168.50.250 es el gateway de la red 10.0.0.0/24 (en la hoja la red está corregida a mano). Sabe que existe un servidor distinto al gateway en esta red, pero no está seguro de qué dirección IP tiene asignada. Descubra la dirección del servidor, y los servicios que tiene expuesto el host. Debe mostrar: el resultado de acceder vía HTTP a dicho servidor; los routers que intervienen en esta comunicación. Nota: Recomendamos utilizar una VM para resolver este ejercicio, pero no es necesario de poder hacerlo en su Host. De usar una VM, el adaptador de red debe estar en modo Bridge (Puente) conectado a la red abierta.

**Solución:**

> **1.** Antes de grabar, instalá lo que falte (primera línea). Después verificá que la VM está en la red del gateway y que llegás a él. Tu IP tiene que ser 192.168.50.X.

sudo apt update && sudo apt install -y nmap traceroute curl \# si ya están, no cambia nada

ip -br a

ping -c 2 192.168.50.250

\# viejo: ifconfig

> **2.** Agregá la ruta a 10.0.0.0/24 por ese gateway y comprobala.

sudo ip route add 10.0.0.0/24 via 192.168.50.250

ip route get 10.0.0.1

\# viejo: sudo route add -net 10.0.0.0 netmask 255.255.255.0 gw 192.168.50.250 ; route -n

10.0.0.1 via 192.168.50.250 dev IFACE src 192.168.50.X uid 1000

> **3.** Descubrí qué hosts hay en la red (tarda unos 20 segundos).

sudo nmap -sn -n 10.0.0.0/24

Nmap scan report for 10.0.0.1

Host is up (0.000069s latency).

Nmap scan report for 10.0.0.77

Host is up (0.000084s latency).

Nmap done: 256 IP addresses (2 hosts up) scanned in 17.02 seconds

Si no aparece ninguno (ping filtrado), buscá por puertos abiertos:

sudo nmap -n -Pn -p 22,80,443,8080 --open 10.0.0.0/24

> **4.** Separá el gateway del servidor. Esperá 10 segundos después del barrido y, para cada IP que encontraste, hacé un ping y un traceroute (reemplazá IP_ENCONTRADA y repetí con la otra). Entre una IP y la otra esperá unos 5 segundos: dos traceroute pegados hacen que el router no conteste el segundo (\* \* \*).

ping -c 1 IP_ENCONTRADA

traceroute -n IP_ENCONTRADA

La IP que responde en un solo salto y con ttl=64 es la otra pata del gateway. La que muestra primero a 192.168.50.250 y después a sí misma, con un ttl menor (63), es el servidor: esa es IP_SERVIDOR.

64 bytes from 10.0.0.1: icmp_seq=1 ttl=64 time=0.014 ms

traceroute to 10.0.0.1 (10.0.0.1), 30 hops max, 60 byte packets

1 10.0.0.1 0.033 ms 0.007 ms 0.005 ms

64 bytes from 10.0.0.77: icmp_seq=1 ttl=63 time=0.017 ms

traceroute to 10.0.0.77 (10.0.0.77), 30 hops max, 60 byte packets

1 192.168.50.250 0.015 ms 0.006 ms 0.006 ms

2 10.0.0.77 0.010 ms 0.008 ms 0.008 ms

> **5.** Servicios expuestos del servidor (todos los puertos TCP, con versión).

sudo nmap -n -Pn -sS -sV -p- IP_SERVIDOR

PORT STATE SERVICE VERSION

22/tcp open ssh OpenSSH 9.6p1 Ubuntu 3ubuntu13.19 (Ubuntu Linux; protocol 2.0)

80/tcp open http SimpleHTTPServer 0.6 (Python 3.12.3)

> **6.** Acceso por HTTP.

curl -i http://IP_SERVIDOR/

> **7.** Routers que intervienen.

traceroute -n IP_SERVIDOR

**Demostrar:** tres salidas: el nmap -sV del paso 5 (servicios), el curl -i del paso 6 (línea HTTP/1.x 200 OK, headers y cuerpo) y el traceroute -n del paso 7. Los routers son todos los saltos menos el último, que es el servidor. El primero tiene que ser 192.168.50.250.

**Ojo:**

- sudo: nmap: command not found (sin sudo: Command 'nmap' not found, Command 'traceroute' not found o Command 'curl' not found; en Debian, bash: traceroute: command not found): falta el paso 1. Instalá con apt aunque Ubuntu sugiera primero sudo snap install nmap. El snap no tiene permiso para raw sockets y sudo nmap -sn o -sS pueden fallar (por ejemplo con dnet: Failed to open device IFACE o Couldn't open a raw socket). Si ya lo instalaste así: sudo snap remove nmap y sudo apt install -y nmap. traceroute va del paquete traceroute. Si antes instalaste inetutils-traceroute (la segunda sugerencia de Ubuntu), traceroute -n da traceroute: invalid option -- 'n' aunque el paso 1 instale traceroute: queda el que ya estaba. Sacalo con sudo apt remove -y inetutils-traceroute.

- Error: Nexthop has invalid gateway. al agregar la ruta: tu VM no tiene IP en 192.168.50.0/24. Está en NAT (IP 10.0.2.15) o sin IP. Pasá el adaptador a Bridge (Puente) sobre la red abierta, desconectá y reconectá la red, y mirá ip -br a.

- RTNETLINK answers: File exists: la ruta ya estaba. Para pisarla: sudo ip route replace 10.0.0.0/24 via 192.168.50.250.

- El ping a 10.0.0.X no responde (100% packet loss) o dice ping: connect: Network is unreachable: falta la ruta del paso 2. ip route get 10.0.0.1 tiene que decir via 192.168.50.250; si dice otro gateway, el paquete se va por la ruta default.

- You requested a scan type which requires root privileges. y QUITTING!: a nmap -sS le falta sudo.

- El nmap -sn devuelve dos IPs: una es el gateway visto desde adentro. El enunciado pide el servidor "distinto al gateway": no entregues la del gateway.

- \* \* \* en el primer salto justo después de un nmap o de otro traceroute: el router limita los ICMP que genera (en Linux, uno por segundo con ráfaga de 6). Esperá 10 segundos y repetí el traceroute. En la prueba pasó siempre que se corrió pegado al nmap -sn o a un traceroute hacia la IP del gateway.

- El traceroute sigue de largo con saltos \* \* \* después de llegar al servidor (por ejemplo 6 \* \* \* y 7 \* 10.0.0.77): mismo límite, esta vez del servidor, por correr dos traceroute seguidos. Esperá 10 segundos y repetilo.

- Un salto con \* \* \* que no se va es un router que no contesta ICMP. Cuenta igual como router. Probá las variantes: sudo traceroute -n -I IP_SERVIDOR (ICMP), sudo traceroute -n -T -p 80 IP_SERVIDOR (TCP al puerto 80), tracepath -n IP_SERVIDOR, sudo nmap -n -Pn -p 80 --traceroute IP_SERVIDOR.

- You do not have enough privileges to use this traceroute method.: a traceroute -T (y en sistemas viejos a -I) le falta sudo.

- Si el primer salto es 10.0.2.2, la VM está en NAT y el traceroute no sirve para el puntaje.

- -p- escanea los 65535 puertos. Si tarda mucho, sacalo y quedan los 1000 más comunes. Servicios UDP: sudo nmap -n -Pn -sU --top-ports 20 IP_SERVIDOR.

- Si el HTTP no está en el 80, usá el puerto que mostró nmap: curl -i http://IP_SERVIDOR:PUERTO/. Si solo hay 443 (https): curl -k -i https://IP_SERVIDOR/. El -k es porque el certificado no es confiable: sin él da curl: (60) SSL certificate problem.

- La ruta se pierde al reiniciar. Para borrarla a mano: sudo ip route del 10.0.0.0/24.

- Las IP 10.0.0.1 y 10.0.0.77 de las salidas son de una maqueta de prueba, no del parcial.

- Temporary failure resolving 'archive.ubuntu.com' (u otro servidor, como deb.debian.org) o Failed to fetch en el apt del paso 1, o si se queda más de 10 segundos en 0% \[Connecting to ...\] (cortalo con Ctrl+C: tarda minutos en fallar): en Bridge esa red no te da internet. Pasá el adaptador a NAT, desconectá y reconectá la red y corré la primera línea del paso 1. Después volvé a Bridge, desconectá y reconectá la red otra vez y mirá ip -br a. Si ya la habías agregado, rehacé la ruta del paso 2. Si en NAT tampoco anda, usá el plan B sin nmap.

- Sin nmap ni traceroute (apt no anda), con la ruta del paso 2 ya puesta: plan B con ping y nc, en las tres partes que siguen. No muestra versiones como -sV ni puertos raros: vale puntaje parcial.

- Plan B, el servidor: sin nmap la única forma es un barrido que le hace un ping a cada IP de 10.0.0.1 a 10.0.0.254 y muestra las que responden, con su ttl. La de ttl 64 es el gateway y la de ttl 63, el servidor: esa es IP_SERVIDOR.

for i in {1..254}; do ping -n -c1 -W1 10.0.0.\$i & done \| grep 'bytes from'

- Plan B, los servicios: nc -zv -w 1 IP_SERVIDOR 21 22 23 25 53 80 110 143 443 3306 8000 8080 prueba los puertos TCP más comunes, uno por línea. En Ubuntu los abiertos dicen Connection to IP_SERVIDOR 22 port \[tcp/ssh\] succeeded! y los cerrados Connection refused (o timed out si el firewall los descarta). En Debian el nc es otro: los abiertos dicen (UNKNOWN) \[IP_SERVIDOR\] 22 (ssh) open, los cerrados no aparecen y antes puede salir inverse host lookup failed, que no es un error. Si no tenés nc, probá de a un puerto con timeout 1 bash -c '\</dev/tcp/IP_SERVIDOR/80' && echo abierto, cambiando el 80.

- Plan B, los routers: ping -n -c 1 -t 1 IP_SERVIDOR, después lo mismo con -t 2, -t 3 y así. Cada From IP icmp_seq=1 Time to live exceeded es un router, en orden, y cuando sale bytes from llegaste al servidor. Si un -t no contesta nada (100% packet loss sin From), esperá 10 segundos y repetilo (pasa si lo corrés justo después del barrido); si sigue igual, ese router no contesta y cuenta igual. Si tenés tracepath, tracepath -n IP_SERVIDOR hace lo mismo de una vez.

- Sin curl (Debian no lo trae): wget -S -O - http://IP_SERVIDOR/ muestra los headers y el cuerpo.

*Probado en Ubuntu 24.04 (lab). El plan B sin nmap (barrido con ping, nc -zv con varios puertos, /dev/tcp y ping -t) y el curl -k, en Ubuntu 24.04 (WSL) contra IPs propias y de una red de casa. El nc de Debian, con el binario de netcat-traditional sacado de su .deb sin instalar. El invalid option -- 'n' de inetutils-traceroute, con su .deb sin instalar y update-alternatives en una carpeta propia. El cuelgue en Connecting to, simulado con unshare -n y una red que descarta todo. Sin probar: el apt install (solo simulado con apt-get -s y sin red con unshare -n), el pase a NAT para instalar y el error del snap de nmap.*

### Ruta por el gateway 192.168.1.3, servicios de 10.0.0.1, HTTP y routers

**Tomado en:** Parcial 2024-2C · Ejercicio 3.

> **Idea:** La idea es agregar una ruta estática a 10.0.0.0/24 por 192.168.1.3, listar los servicios de 10.0.0.1 con sudo nmap -sS -sV -p-, entrar por HTTP con curl si aparece y mostrar los routers con traceroute. La VM tiene que estar en Bridge, y los routers son todos los saltos menos el último, empezando por 192.168.1.3.

**Buscar:** gateway, 192.168.1.3, 10.0.0.0/24, 10.0.0.1, ruta estática, ruta estatica, ip route add, route add, servicios que tiene expuesto, nmap, -sS, -sV, -Pn, escaneo de puertos, acceder vía HTTP, acceder via HTTP, curl, routers que intervienen, traceroute, tracepath, saltos, hops, modo Bridge, Puente, red abierta Lab, Ejercicio III, apt install, Command 'nmap' not found, sudo: nmap: command not found, Command 'traceroute' not found, Command 'curl' not found, command not found, Couldn't open a raw socket, dnet: Failed to open device, snap, inetutils-traceroute, sin nmap, sin traceroute, /dev/tcp, https, 443, curl -k, SSL certificate problem, Temporary failure resolving, Failed to fetch, NAT, wget, invalid option -- 'n', Connecting to, plan B, nc -zv, ping -t

**Enunciado:** El host en 192.168.1.3 es el gateway de la red 10.0.0.0/24. Descubra los servicios que tiene expuesto el host 10.0.0.1 y, de existir, muestre el resultado de acceder vía HTTP a dicho servidor. Muestre tambien todos los routers que intervienen en esta comunicacion. Nota: Recomendamos utilizar una VM para resolver este ejercicio, pero no es necesario de poder hacerlo en su Host. De usar una VM, el adaptador de red debe estar en modo Bridge (Puente) conectado a la red abierta Lab.

**Solución:**

> **1.** Antes de grabar, instalá lo que falte (primera línea). Después verificá que la VM está en la red del gateway y que llegás a él. Tu IP tiene que ser 192.168.1.X.

sudo apt update && sudo apt install -y nmap traceroute curl \# si ya están, no cambia nada

ip -br a

ping -c 2 192.168.1.3

\# viejo: ifconfig

> **2.** Agregá la ruta a 10.0.0.0/24 por ese gateway y comprobala.

sudo ip route add 10.0.0.0/24 via 192.168.1.3

ip route get 10.0.0.1

\# viejo: sudo route add -net 10.0.0.0 netmask 255.255.255.0 gw 192.168.1.3 ; route -n

10.0.0.1 via 192.168.1.3 dev IFACE src 192.168.1.X uid 1000

> **3.** Servicios expuestos de 10.0.0.1 (todos los puertos TCP, con versión).

sudo nmap -n -Pn -sS -sV -p- 10.0.0.1

PORT STATE SERVICE VERSION

22/tcp open ssh OpenSSH 9.6p1 Ubuntu 3ubuntu13.19 (Ubuntu Linux; protocol 2.0)

80/tcp open http SimpleHTTPServer 0.6 (Python 3.12.3)

> **4.** Si aparece HTTP, accedé.

curl -i http://10.0.0.1/

> **5.** Routers que intervienen.

traceroute -n 10.0.0.1

traceroute to 10.0.0.1 (10.0.0.1), 30 hops max, 60 byte packets

1 192.168.1.3 0.036 ms 0.008 ms 0.009 ms

2 172.16.0.2 0.023 ms 0.009 ms 0.009 ms

3 10.0.0.1 0.021 ms 0.011 ms 0.014 ms

**Demostrar:** las salidas de los pasos 3, 4 y 5. Los routers son todos los saltos del traceroute menos el último (10.0.0.1 es el destino). El primero tiene que ser 192.168.1.3.

**Ojo:**

- sudo: nmap: command not found (sin sudo: Command 'nmap' not found, Command 'traceroute' not found o Command 'curl' not found; en Debian, bash: traceroute: command not found): falta el paso 1. Instalá con apt aunque Ubuntu sugiera primero sudo snap install nmap. El snap no tiene permiso para raw sockets y sudo nmap -sS puede fallar (por ejemplo con dnet: Failed to open device IFACE o Couldn't open a raw socket). Si ya lo instalaste así: sudo snap remove nmap y sudo apt install -y nmap. traceroute va del paquete traceroute. Si antes instalaste inetutils-traceroute (la segunda sugerencia de Ubuntu), traceroute -n da traceroute: invalid option -- 'n' aunque el paso 1 instale traceroute: queda el que ya estaba. Sacalo con sudo apt remove -y inetutils-traceroute.

- Error: Nexthop has invalid gateway. al agregar la ruta: tu VM no tiene IP en 192.168.1.0/24. Está en NAT (IP 10.0.2.15) o sin IP. Pasá el adaptador a Bridge (Puente) sobre la red abierta Lab, desconectá y reconectá la red, y mirá ip -br a.

- RTNETLINK answers: File exists: la ruta ya estaba (con route add el mensaje es SIOCADDRT: File exists). Para pisarla: sudo ip route replace 10.0.0.0/24 via 192.168.1.3.

- Si en tu casa la red es 192.168.1.0/24 y ya tenés otra ruta a 10.0.0.0/24 (VPN, Docker), mirá ip route antes.

- El apunte del alumno corría nmap -sS -Pn 10.0.0.1 sin sudo: falla con You requested a scan type which requires root privileges. y QUITTING!. Además sin -sV no muestra versiones y sin -p- solo mira 1000 puertos.

- -Pn saltea el ping previo: sirve si el host no contesta ICMP.

- "de existir": si nmap no muestra ningún puerto http, decilo y mostrá el nmap. Si el HTTP está en otro puerto: curl -i http://10.0.0.1:PUERTO/. Si solo hay 443 (https): curl -k -i https://10.0.0.1/. El -k es porque el certificado no es confiable: sin él da curl: (60) SSL certificate problem.

- \* \* \* en los primeros saltos justo después de un barrido con nmap, o saltos de más con \* \* \* después de llegar a 10.0.0.1 (por ejemplo 7 \* \* \* y 8 \* 10.0.0.1) por correr dos traceroute pegados: los routers y el servidor limitan los ICMP que generan (en Linux, uno por segundo con ráfaga de 6). Esperá 10 segundos y repetí el traceroute.

- Un salto con \* \* \* que no se va es un router que no contesta ICMP. Cuenta igual. Variantes: sudo traceroute -n -I 10.0.0.1 (ICMP), sudo traceroute -n -T -p 80 10.0.0.1 (TCP al 80), tracepath -n 10.0.0.1, sudo nmap -n -Pn -p 80 --traceroute 10.0.0.1.

- You do not have enough privileges to use this traceroute method.: a traceroute -T (y en sistemas viejos a -I) le falta sudo.

- Si el primer salto es 10.0.2.2, la VM está en NAT y el traceroute no sirve para el puntaje.

- Servicios UDP: sudo nmap -n -Pn -sU --top-ports 20 10.0.0.1.

- Temporary failure resolving 'archive.ubuntu.com' (u otro servidor, como deb.debian.org) o Failed to fetch en el apt del paso 1, o si se queda más de 10 segundos en 0% \[Connecting to ...\] (cortalo con Ctrl+C: tarda minutos en fallar): en Bridge esa red no te da internet. Pasá el adaptador a NAT, desconectá y reconectá la red y corré la primera línea del paso 1. Después volvé a Bridge, desconectá y reconectá la red otra vez y mirá ip -br a. Si ya la habías agregado, rehacé la ruta del paso 2. Si en NAT tampoco anda, usá el plan B sin nmap.

- Sin nmap ni traceroute (apt no anda): usá el plan B con ping y nc del Ojo del ejercicio anterior (Parcial 2025-2Q · Ejercicio 2, 'Gateway 192.168.50.250: descubrir el servidor de 10.0.0.0/24...', en este mismo tipo, más arriba), con 10.0.0.1 en lugar de IP_SERVIDOR. El barrido no hace falta: la IP ya viene dada.

- Para borrar la ruta: sudo ip route del 10.0.0.0/24.

- El salto 172.16.0.2 de la salida es de la maqueta de prueba (dos routers en el medio), no del parcial.

*Probado en Ubuntu 24.04 (lab). El curl -k, en Ubuntu 24.04 (WSL). El invalid option -- 'n' de inetutils-traceroute, con su .deb sin instalar y update-alternatives en una carpeta propia. El cuelgue en Connecting to, simulado con unshare -n y una red que descarta todo. Sin probar: el apt install (solo simulado con apt-get -s y sin red con unshare -n), el pase a NAT para instalar y el error del snap de nmap.*

### Acceder a un servidor HTTP detrás de otra máquina sin perder internet

**Tomado en:** Parcial 2015 (segundo parcial) · Ejercicio 1 (otra tanda).

> **Idea:** La idea es llegar al servidor HTTP de 192.168.1.1 agregando con ip route add una ruta específica a 192.168.1.0/24 (o solo al host) vía la máquina intermedia que te dan. El truco es no tocar la ruta por defecto: así el curl llega al servidor de atrás y seguís teniendo internet.

**Contexto del parcial:** del segundo parcial de 2015 hay dos tandas de tres ejercicios, numeradas igual. Las marcadas "(otra tanda)" son las de máquina, relatadas por un alumno; la tanda en papel (subnetting, DHCP y ruteo, CSMA/CD) está en Resueltos — En papel.

**Buscar:** servidor http detras de una maquina, detrás de esa, 192.168.1.1, había un servidor http, habia un servidor http, ruta, route add, ip route add, acceder a ese servicio sin dejar de tener internet, sin perder internet, gateway, diagrama de una red, ip de una máquina que tenían ellos, detras de esa, ip de una maquina que tenian ellos

**Enunciado:** No hay hoja en las fuentes; es el relato de un alumno: "Te daban un diagrama de una red. Te decían la ip de una máquina que tenían ellos y te contaban que detrás de esa en la dirección 192.168.1.1 había un servidor http. Configurar tu computadora para poder acceder a ese servicio sin dejar de tener internet en la compu".

**Solución:** agregás una ruta solo hacia la red del servidor, usando como gateway la máquina intermedia que te dan (IP_INTERMEDIA, la que está "adelante"). Así no tocás la default y seguís con internet.

sudo ip route add 192.168.1.0/24 via IP_INTERMEDIA

\# o solo el host: sudo ip route add 192.168.1.1 via IP_INTERMEDIA

\# viejo: sudo route add -net 192.168.1.0 netmask 255.255.255.0 gw IP_INTERMEDIA

**Demostrar:**

curl -v http://192.168.1.1/ \# llega al servidor de atras

curl -I http://itba.edu.ar/ \# internet sigue andando: contesta con una linea HTTP/1.1 (301 o 200)

ip route \# ves tu default intacta + la ruta nueva a 192.168.1.0/24

**Ojo:**

- Agregás una ruta **específica** a la red del servidor; NO cambies la default (si la cambiás perdés internet).

- La máquina intermedia tiene que rutear hacia esa red (forwarding). Si no hay conectividad, comprobá con ping a la intermedia y después al servidor.

*Probado en Ubuntu 24.04 (lab): ruta específica vía un gateway intermedio con netns; se llega al host de atrás y la default/Internet queda intacta.*

### Paquete de Wireshark: hablar con un servicio en el puerto 22 detrás de un firewall

**Tomado en:** Parcial 2015 (segundo parcial) · Ejercicio 3 (otra tanda).

> **Idea:** La idea es conectarte por ssh al puerto 22 de 192.168.123.1 agregando una ruta a 192.168.123.0/24 por el gateway, sin tocar la default. El truco es que la MAC del paquete es la del gateway y su IP sale de buscar esa MAC en ip neigh; como el firewall solo deja pasar el 22, se prueba con nc -vz, no con ping.

**Contexto del parcial:** del segundo parcial de 2015 hay dos tandas de tres ejercicios, numeradas igual. Las marcadas "(otra tanda)" son las de máquina, relatadas por un alumno; la tanda en papel (subnetting, DHCP y ruteo, CSMA/CD) está en Resueltos — En papel.

**Buscar:** wireshark, screenshot, paquete, direcciones ip, Mac address, MAC, puerto 22, servicio en el puerto 22, ssh, firewall, 192.168.123.1, 192.168.123.0/24, ruta, route add, ip route add, hablar con ese servicio, host de la red del itba que tenía acceso, ip neigh, arp -a, host de la red del itba que tenia acceso

**Enunciado:** No hay hoja en las fuentes; es el relato de un alumno: "Te muestran un screenshot de un paquete de wireshark. Lo importante del paquete es la direcciones ip y las Mac address (eso deberías poder encontrarlo en un screenshot de wireshark). Y te decían que el Host de origen tenía un servicio en el puerto 22, y que había algún host de la red del itba que tenía acceso a ese host. Te contaban que, sabías que el Host tenía un firewall que para algunos servicios podía no aceptar, pero te aseguraban que para el puerto 22 si podías. Configurar para poder hablar con ese servicio. La ip del Host era 192.168.123.1".

**Solución:** el paquete se capturó en la red del ITBA, así que su MAC de origen no es la del host 192.168.123.1 sino la del equipo que lo rutea: ese es tu gateway. Buscás qué IP de la red tiene esa MAC, agregás una ruta a 192.168.123.0/24 por ahí y entrás por ssh.

> **1.** Para la ruta necesitás la IP del gateway (IP_GW): corré ip neigh (la tabla ARP) y, en la salida, buscá la línea que tiene la MAC del screenshot (MAC_DEL_PAQUETE). La IP del principio de esa línea es IP_GW. La tabla ARP solo tiene los vecinos con los que ya hablaste: si no aparece, pingeá primero a los candidatos y volvé a mirar. Si el screenshot ya muestra la IP de ese host del ITBA, usala directo como IP_GW.

ip neigh

\# viejo: arp -a

IP_GW dev IFACE lladdr MAC_DEL_PAQUETE REACHABLE

> **2.** Ruta a la red del host por ese gateway, sin tocar la ruta por defecto.

sudo ip route add 192.168.123.0/24 via IP_GW

ip route get 192.168.123.1

\# equivalente viejo

\# sudo route add -net 192.168.123.0 netmask 255.255.255.0 gw IP_GW

> **3.** Hablá con el servicio del puerto 22.

nc -vz -w 3 192.168.123.1 22

ssh USUARIO@192.168.123.1

**Demostrar:** la ruta nueva (ip route get 192.168.123.1 del paso 2), el puerto 22 abierto y la sesión ssh (o al menos el pedido de clave, que prueba que llegaste al servicio):

192.168.123.1 via IP_GW dev IFACE src TU_IP

Connection to 192.168.123.1 22 port \[tcp/ssh\] succeeded!

**Ojo:**

- Sin la ruta, el pedido sale por tu gateway por defecto y no llega: ssh: connect to host 192.168.123.1 port 22: Connection timed out.

- No pruebes la ruta con ping ni con otro puerto: el firewall del host solo acepta el 22. En la prueba el ping dio 100% packet loss y el puerto 80 timed out, con el 22 abierto. Probá con nc -vz 192.168.123.1 22.

- Del screenshot importan las IP (origen y destino) y las MAC. La MAC que ves al lado de la 192.168.123.1 es la del gateway, porque el host está en otra red.

- Agregás una ruta específica a esa red; la ruta por defecto no se toca, así no perdés Internet.

*Probado en Ubuntu 24.04 (lab), con tres namespaces: alumno, gateway con reenvío y host 192.168.123.1 con sshd y un firewall que solo acepta el 22. Sin probar: la lectura del screenshot (las fuentes no lo traen).*

### Configurar la interfaz 192.168.10.x/25, que ping 10.6.0.127 responda G y que ping hostG funcione

**Tomado en:** Parcial 2011 · Ejercicio 1.

> **Idea:** La idea es dejar levantada solo la interfaz del switch con 192.168.10.x/25, rutear 10.6.0.0/24 por G (192.168.20.1) y agregar hostG en /etc/hosts. La trampa es que G está fuera de tu subred: primero va una ruta directa a 192.168.20.1 por la interfaz y recién después la de la red del laboratorio.

**Contexto del parcial:** la red de los alumnos es 192.168.10.0/25 y la del laboratorio 10.6.0.0/24. Entre las dos está el host G, con 192.168.20.1 del lado de los alumnos y 10.6.0.127 del lado del laboratorio; G no encamina paquetes ni hace NAT.

**Buscar:** solamente quede habilitada la interfaz, interfaz que se conecta al switch, 192.168.10.3, 192.168.10.0/25, 255.255.255.128, netmask, 10.6.0.127, 192.168.20.1, host G, hostG, ping hostG, no encamina paquetes, ip addr add, ip link set down, ip route add, onlink, ifconfig, eth0:1, route add -host, route add -net, gw, /etc/hosts, Nexthop has invalid gateway, Network is unreachable, SIOCADDRT

**Enunciado:** "a) Una vez iniciada la maquina virtual, configurar las interfaces para que solamente quede habilitada la interfaz que se conecta al switch. Establecerle a dicha interfaz la siguiente direccion IP: 192.168.10. b) Realizar los cambios necesarios para que al ejecutar ping 10.6.0.127 se obtenga respuesta del host G. c) Realizar los cambios necesarios para que al ejecutar ping hostG se obtenga respuesta del host G." El último número de la IP se daba en el momento; el apunte usa 192.168.10.3.

**Solución:**

> **1.** a) Mirá las interfaces, bajá las que no van al switch y poné la IP con máscara /25 en la que sí. IFACE es la conectada al switch (muestra LOWER_UP en ip -br link); OTRA_IFACE es cada una de las demás, sin contar lo.

ip -br link

sudo ip link set OTRA_IFACE down

sudo ip addr flush dev IFACE

sudo ip addr add 192.168.10.3/25 dev IFACE

sudo ip link set IFACE up

\# forma vieja:

\# ifconfig -a

\# sudo ifconfig OTRA_IFACE down

\# sudo ifconfig IFACE 192.168.10.3 netmask 255.255.255.128 up

> **2.** b) G está en tu mismo switch pero con una IP de otra red (192.168.20.1). Primero decile al kernel que esa IP se alcanza directo por la interfaz y después mandá la red del laboratorio por ahí.

sudo ip route add 192.168.20.1 dev IFACE

sudo ip route add 10.6.0.0/24 via 192.168.20.1

\# forma vieja:

\# sudo route add -host 192.168.20.1 dev IFACE

\# sudo route add -net 10.6.0.0 netmask 255.255.255.0 gw 192.168.20.1

> **3.** c) Dale nombre a G.

Hay que hacer sudo nano /etc/hosts y agregar al final lo siguiente:

192.168.20.1 hostG

**Demostrar:**

ip -br a

ip route

ping -c 2 10.6.0.127

ping -c 2 hostG

IFACE UP 192.168.10.3/25

OTRA_IFACE DOWN

10.6.0.0/24 via 192.168.20.1 dev IFACE

192.168.10.0/25 dev IFACE proto kernel scope link src 192.168.10.3

192.168.20.1 dev IFACE scope link

2 packets transmitted, 2 received, 0% packet loss

PING hostG (192.168.20.1) 56(84) bytes of data.

64 bytes from hostG (192.168.20.1): icmp_seq=1 ttl=64 time=0.034 ms

**Ojo:**

- Error: Nexthop has invalid gateway. (o SIOCADDRT: Network is unreachable con route): agregaste la ruta a 10.6.0.0/24 antes que la ruta a 192.168.20.1. El gateway tiene que ser alcanzable primero. En un solo comando: sudo ip route add 10.6.0.0/24 via 192.168.20.1 dev IFACE onlink.

- ping: connect: Network is unreachable: todavía no hay ruta hacia 10.6.0.0/24.

- La máscara es /25 = 255.255.255.128. Con /24 funciona parecido pero no es lo pedido.

- G "no encamina": 10.6.0.127 responde porque es una dirección del propio G. Un ping a otro host del laboratorio (10.6.0.100) no vuelve, y eso no es un error tuyo.

- El apunte escribe 192.168.120.1 hostG: está mal, la IP de G es 192.168.20.1 (también sirve 10.6.0.127 una vez hecha la ruta).

- Si G no responde ni al ping a 192.168.20.1: a G le falta esa IP o la ruta de vuelta hacia los alumnos. Es el "0) En G" del apunte y lo hace quien administra G (IFACE_DE_G es su interfaz hacia el switch): sudo ip addr add 192.168.20.1/24 dev IFACE_DE_G y sudo ip route add 192.168.10.0/24 dev IFACE_DE_G. Forma vieja del apunte: sudo ifconfig eth0:1 192.168.20.1 y sudo route add -net 192.168.10.0 netmask 255.255.255.0 dev eth0.

- sudo echo "..." \>\> /etc/hosts da Permission denied porque la redirección la hace tu shell sin privilegios. Por eso se edita con sudo nano /etc/hosts.

- Nada de esto sobrevive a un reinicio; para el parcial alcanza. Si NetworkManager te cambia la IP: sudo nmcli device set IFACE managed no y repetí (sin probar).

*Probado en Ubuntu 24.04 (lab). Sin probar: el comando nmcli (el lab no tiene NetworkManager).*

## MAC y puertos abiertos de un host

Los dos se hacen desde pampero.it.itba.edu.ar, entrando por ssh, y piden un dato de otro equipo de su red que se escribe como comentario en la entrega de campus. Cambia el equipo y el dato: la MAC de un host con nombre o los puertos TCP abiertos del gateway.

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| MAC de esmerald.it.itba.edu.ar desde ens3 de pampero | MAC de esmerald.it.itba.edu.ar vista desde la interfaz ens3 de pampero.it.itba.edu.ar | Sale de la tabla ARP de pampero: dig, ping y ip neigh |
| Todos los puertos TCP abiertos del gateway de pampero | Todos los puertos TCP abiertos del gateway de pampero.it.itba.edu.ar | La IP del gateway sale de la ruta default de pampero; nmap con -p- para no perder los puertos altos |

### MAC de esmerald.it.itba.edu.ar desde ens3 de pampero

**Tomado en:** Parcial 2020-1C · Ejercicio I.

> **Idea:** La idea es entrar por ssh a pampero, resolver la IP de esmerald con dig, hacerle un ping para que quede en la tabla ARP y leer su MAC con ip neigh, que después se escribe como comentario en campus. Ojo: la MAC pedida es la de esmerald, no la de pampero.

**Buscar:** esmerald.it.itba.edu.ar, ens3, pampero.it.itba.edu.ar, direccion MAC, arp, ip neigh, dig, ping, primera corrección rápida, primera correccion rapida, comentario de la entrega de campus, esmerald, Encuentre la dirección MAC del host, Encuentre la direccion MAC del host, dirección MAC, dirección de MAC, direccion de MAC, desde la interfaz ens3 del host, la dirección solución, la direccion solucion, arp -n

**Enunciado:** "Encuentre la dirección MAC del host esmerald.it.itba.edu.ar desde la interfaz ens3 del host pampero.it.itba.edu.ar. Escriba como comentario de la entrega de campus la dirección solución para una primera corrección rápida."

**Solución:**

> **1.** ssh USUARIO@pampero.it.itba.edu.ar
>
> **2.** Resolvé la IP de esmerald, hacele un ping para que quede en la tabla ARP y mirá la tabla.

dig +short esmerald.it.itba.edu.ar \# te da la IP (el apunte vio 10.16.1.102)

ping -c1 esmerald.it.itba.edu.ar \# puebla la tabla ARP

ip neigh \# forma vieja: arp -n

En la salida de ip neigh, buscá la línea que empieza con la IP de esmerald (y dice dev ens3): lo que sigue a lladdr es la MAC pedida. Con arp -n es la columna HWaddress.

IP_DE_ESMERALD dev ens3 lladdr MAC_DE_ESMERALD REACHABLE

**Demostrar:** la línea de ip neigh/arp -n con la IP de esmerald y su MAC. Escribila como comentario en campus para la corrección rápida.

**Ojo:**

- La MAC se aprende por ARP solo si hubo tráfico a esa IP en la misma LAN: si ip neigh está vacío para esmerald, ping -c1 esmerald... primero.

- ens3 es la interfaz de pampero por la que sale a esa red; la MAC que buscás es la de **esmerald**, no la de pampero.

*Sin probar: hosts externos. El flujo dig → ping → ip neigh está probado en el lab.*

### Todos los puertos TCP abiertos del gateway de pampero

**Tomado en:** Parcial 2020-1C · Ejercicio II.

> **Idea:** La idea es entrar por ssh a pampero, sacar la IP del gateway de la línea default de ip route y escanearla con nmap. El truco es usar -p-: nmap a secas solo mira los 1000 puertos más comunes y se pierde los altos, y la consigna pide todos.

**Buscar:** puertos abiertos TCP, gateway, pampero.it.itba.edu.ar, nmap, route -n, ip route, default, primera corrección rápida, primera correccion rapida, comentario de la entrega de campus, Enumere todos los puertos abiertos TCP del gateway del host, gateway del host pampero.it.itba.edu.ar, los puertos solución, los puertos solucion, nmap -p-, default via

**Enunciado:** "Enumere todos los puertos abiertos TCP del gateway del host pampero.it.itba.edu.ar. Escriba como comentario de la entrega de campus los puertos solución para una primera corrección rápida."

**Solución:**

> **1.** ssh USUARIO@pampero.it.itba.edu.ar
>
> **2.** Para el escaneo necesitás la IP del gateway (IP_GW): corré ip route y anotá la IP que sigue a default via (el apunte vio 10.16.1.254). Después escaneá todos los puertos TCP de esa IP con nmap (los pide todos el enunciado).

ip route \# forma vieja: route -n (la linea con Destination 0.0.0.0, columna Gateway)

nmap -n -p- -T4 IP_GW

El apunte vio abiertos 22/ssh, 23/telnet, 80/http, 443/https.

**Demostrar:** la línea default de la tabla de ruteo y la salida de nmap con los puertos open.

**Ojo:**

- nmap IP_GW a secas escanea solo los 1000 puertos más comunes y se pierde los altos (en la prueba no mostró un servicio en el 50022). La consigna dice "todos": siempre -p- (los 65535). -n saltea el DNS inverso (en la prueba el mismo escaneo tardó 15 segundos sin -n y 2 con -n) y -T4 acelera.

- El gateway es la IP de la ruta por defecto (default / 0.0.0.0), no la red donde estás conectado.

*Probado en Ubuntu 24.04 (lab), contra un pampero y un gateway simulados: la línea default via de ip route da la IP y nmap -n -p- -T4 lista los puertos TCP open, incluido uno alto (50022) que nmap IP a secas no muestra.*

# Resueltos — DHCP

## Configurar un servidor DHCP

Hay que levantar un servidor DHCP para una red y mostrar lo que ofrece. Es el único parcial con este tipo: si cambian los valores (red, rango, DNS, gateway, reserva por MAC), el punto "Si piden otra cosa" de **Ojo** dice qué línea tocar.

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| DHCP: IP fija por MAC, DNS de Google y rango 10-200 | Servidor DHCP para 192.168.100.0/24: IP fija 192.168.100.8 para la MAC 08:00:27:AE:C8:6A, DNS 8.8.8.8 y 8.8.4.4, rango del 10 al 200 | Se verifica con nmap --script broadcast-dhcp-discover y no se puede hacer sobre la WIFI del laboratorio: va en una interfaz virtual (par veth con un namespace) |

### DHCP: IP fija por MAC, DNS de Google y rango 10-200

**Tomado en:** Parcial 2025-2Q · Ejercicio 1.

> **Idea:** La idea es levantar un isc-dhcp-server para 192.168.100.0/24 que reparta del .10 al .200 con los DNS de Google y le dé siempre la 192.168.100.8 a la impresora por su MAC, con un bloque host y fixed-address fuera del range. El truco es no usar la WIFI del laboratorio: todo va sobre un par veth con un namespace.

**Buscar:** DHCP, servidor DHCP, isc-dhcp-server, dhcpd, dhcpd.conf, pequeña oficina, pequena oficina, 192.168.100.0/24, impresora, 08:00:27:AE:C8:6A, 192.168.100.8, IP estática, IP estatica, IP fija, reserva, fixed-address, hardware ethernet, host, range, rango 10-200, DNS de Google, 8.8.8.8, 8.8.4.4, domain-name-servers, nmap --script broadcast-dhcp-discover, broadcast-dhcp-discover.mac, AA:BB:CC:DD:EE:FF, DHCP Request, DHCPDISCOVER, DHCPOFFER, red interna, interfaz virtual, veth, veth-srv, veth-cli, namespace, netns, ip netns exec cliente, dummy, arp on, INTERFACESv4, dnsmasq, Ejercicio I, Job for isc-dhcp-server.service failed, invoke-rc.d, dhclient, isc-dhcp-client, exec of "dhclient" failed, gateway, option routers, dominio, option domain-name, default-lease-time, max-lease-time, duración del lease, otro rango, varios rangos, dos rangos, pool, solo clientes conocidos, clientes desconocidos, deny unknown-clients, bloquear una MAC, deny booting, ntp-servers, host-name, dhcp-range, dhcp-host, dhcp-option, dhcp-ignore, no free leases, unknown client, booting disallowed, segunda red, dos redes, otra subred, veth-srv2, veth-cli2, duracion del lease, router por defecto, puerta de enlace, authoritative, autoritativo, servidor NTP, nombre del cliente, declared twice, already exists, otra red, cambiar la red, bad subnet number/mask combination

**Enunciado:** Usted administra la red de una pequeña oficina (192.168.100.0/24) que utiliza un servidor DHCP para la asignación de direcciones IP. Configure el servidor para que cumpla con los siguientes requisitos: 1. Una impresora de red crítica, con la dirección MAC 08:00:27:AE:C8:6A, debe tener siempre la dirección IP estática 192.168.100.8 para que los scripts de impresión funcionen correctamente. 2. Se desea que todos los clientes tengan un servidor DNS siempre disponible, y que el proveedor sea confiable. Deben utilizar los servidores DNS de Google (8.8.8.8 y 8.8.4.4). 3. Se desea usar el rango de direcciones de 10-200 para asignar. El resto queda reservado para uso futuro. Verifique la configuración con el siguiente comando, que le permite simular DHCP Request: sudo nmap --script broadcast-dhcp-discover --script-args "broadcast-dhcp-discover.mac=AA:BB:CC:DD:EE:FF". Aviso: No resuelva el ejercicio sobre una interfaz conectada a la red WIFI del Laboratorio. Hágalo en una red interna o interfaz virtual.

**Solución:**

> **1.** Instalá lo que falte (si ya está, no cambia nada).

sudo apt install -y isc-dhcp-server nmap

> **2.** Creá la interfaz virtual: un par veth. veth-srv (192.168.100.1) es la placa del servidor en la red de la oficina; veth-cli queda dentro del namespace cliente y hace de otro equipo (la .250 es solo para que nmap pueda salir por ahí). Así no tocás la red del laboratorio.

sudo ip netns add cliente

sudo ip link add veth-srv type veth peer name veth-cli

sudo ip link set veth-cli netns cliente

sudo ip addr add 192.168.100.1/24 dev veth-srv

sudo ip link set veth-srv up

sudo ip netns exec cliente ip link set lo up

sudo ip netns exec cliente ip link set veth-cli up

sudo ip netns exec cliente ip addr add 192.168.100.250/24 dev veth-cli

ip -br a

\# viejo (IP del servidor): sudo ifconfig veth-srv 192.168.100.1 netmask 255.255.255.0 up ; ifconfig

> **3.** Archivo /etc/dhcp/dhcpd.conf (pisa el de ejemplo; queda copia en dhcpd.conf.orig).

sudo cp /etc/dhcp/dhcpd.conf /etc/dhcp/dhcpd.conf.orig

Hay que hacer sudo nano /etc/dhcp/dhcpd.conf, borrar lo que tenga y pegar lo siguiente:

authoritative;

ddns-update-style none;

default-lease-time 600;

max-lease-time 7200;

subnet 192.168.100.0 netmask 255.255.255.0 {

range 192.168.100.10 192.168.100.200; \# punto 3: se asigna del 10 al 200

option subnet-mask 255.255.255.0;

option broadcast-address 192.168.100.255;

option routers 192.168.100.1; \# gateway: no lo pide el enunciado, es la IP de este servidor

option domain-name-servers 8.8.8.8, 8.8.4.4; \# punto 2: DNS de Google

}

host impresora {

hardware ethernet 08:00:27:AE:C8:6A; \# punto 1: MAC de la impresora

fixed-address 192.168.100.8; \# siempre esta IP (queda fuera del range)

}

> **4.** Decile al servicio en qué interfaz escuchar (archivo /etc/default/isc-dhcp-server).

Hay que hacer sudo nano /etc/default/isc-dhcp-server, buscar la línea INTERFACESv4="" (está al final) y dejarla así:

INTERFACESv4="veth-srv"

> **5.** Validá la sintaxis y arrancá el servicio. dhcpd -t muestra solo la cabecera (Internet Systems Consortium DHCP Server ..., Config file:, Database file:, PID file:) y ningún error. is-active tiene que decir active, y en la salida de ss -lunp mirá que haya una línea con :67 y dhcpd (el servidor escuchando en el puerto 67). Si pegaste todo junto y no aparece esa línea, esperá un segundo y corré de nuevo systemctl is-active isc-dhcp-server y sudo ss -lunp: recién arrancado, el servicio puede no haber abierto el puerto todavía, o decir active justo antes de caerse (el motivo sale en sudo journalctl -u isc-dhcp-server -n 20 --no-pager).

sudo dhcpd -t -cf /etc/dhcp/dhcpd.conf

sudo systemctl restart isc-dhcp-server

systemctl is-active isc-dhcp-server

sudo ss -lunp

\# viejo: sudo service isc-dhcp-server restart ; sudo netstat -lunp

**Demostrar:**

Un cliente cualquiera, con el comando literal del enunciado (tarda 10 segundos). Tiene que ofrecer una IP entre .10 y .200 y los dos DNS de Google.

sudo nmap --script broadcast-dhcp-discover \\

--script-args "broadcast-dhcp-discover.mac=AA:BB:CC:DD:EE:FF"

Pre-scan script results:

\| broadcast-dhcp-discover:

\| Response 1 of 1:

\| Interface: veth-srv

\| IP Offered: 192.168.100.10

\| DHCP Message Type: DHCPOFFER

\| Server Identifier: 192.168.100.1

\| IP Address Lease Time: 10m00s

\| Subnet Mask: 255.255.255.0

\| Router: 192.168.100.1

\| Domain Name Server: 8.8.8.8, 8.8.4.4

\|\_ Broadcast Address: 192.168.100.255

La impresora: mismo comando con su MAC. Tiene que ofrecer 192.168.100.8.

sudo nmap --script broadcast-dhcp-discover \\

--script-args "broadcast-dhcp-discover.mac=08:00:27:AE:C8:6A"

\| Interface: veth-srv

\| IP Offered: 192.168.100.8

\| Domain Name Server: 8.8.8.8, 8.8.4.4

El log del servidor (el grep DHCP deja solo las líneas de DHCP de la salida):

sudo journalctl -u isc-dhcp-server -n 20 --no-pager \| grep DHCP

dhcpd\[138\]: DHCPDISCOVER from aa:bb:cc:dd:ee:ff via veth-srv

dhcpd\[138\]: DHCPOFFER on 192.168.100.10 to aa:bb:cc:dd:ee:ff via veth-srv

dhcpd\[138\]: DHCPDISCOVER from 08:00:27:ae:c8:6a via veth-srv

dhcpd\[138\]: DHCPOFFER on 192.168.100.8 to 08:00:27:ae:c8:6a via veth-srv

**Ojo:**

- Job for isc-dhcp-server.service failed because the control process exited with error code. y invoke-rc.d: initscript isc-dhcp-server, action "start" failed. durante el apt install del paso 1: es esperable, todavía no hay subnet ni INTERFACESv4. Es lo mismo que el No subnet declaration de más abajo. Sale en Debian; en Ubuntu 24.04 no avisa nada, pero el servicio queda failed. Seguí con el paso 2; el paso 5 lo arranca bien.

- nmap termina en 0.1 s y no imprime Pre-scan script results: falta sudo (avisa NSE: \[broadcast-dhcp-discover\] not running for lack of privileges.), o lo corriste dentro del namespace cliente y veth-cli no tiene IPv4 (repetí la última línea ip addr add del paso 2).

- I cannot figure out what source address to use for device veth-cli, does it even exist? y QUITTING!: le pasaste -e veth-cli desde la VM, y esa punta vive dentro del namespace. Usá -e veth-srv, o el prefijo sudo ip netns exec cliente.

- Si aparecen varias Response, la tuya es la que dice Server Identifier: 192.168.100.1. Las otras son del DHCP real del laboratorio (el script pregunta por todas las placas). Para preguntar solo por la tuya agregá -e veth-srv, o corré el mismo comando desde la otra punta con sudo ip netns exec cliente nmap --script broadcast-dhcp-discover --script-args "broadcast-dhcp-discover.mac=AA:BB:CC:DD:EE:FF" (ahí sale Interface: veth-cli).

- Una interfaz type dummy en lugar del veth solo sirve con sudo ip link set IFACE arp on (sin eso nmap la lista como tipo other en sudo nmap --iflist y el script no manda nada por ella); con dnsmasq no sirve ni con arp on. El par veth del paso 2 anda con los dos servidores.

- Cannot open network namespace "cliente": No such file or directory: falta el paso 2.

- Cannot create namespace file "/run/netns/cliente": File exists, RTNETLINK answers: File exists, Cannot find device "veth-cli" o Error: ipv4: Address already assigned. al correr el paso 2: ya lo habías corrido antes. No rompe nada: seguí con el paso 3.

- No subnet declaration for IFACE (...) seguido de Not configured to listen on any interfaces!: la interfaz de INTERFACESv4 no tiene una IP de 192.168.100.0/24. Revisá el paso 2 y el paso 4.

- /etc/dhcp/dhcpd.conf line N: semicolon expected.: falta un ; en la línea anterior a la N.

- La IP fija (.8) va fuera del range (.10 a .200). Si la ponés dentro, el servidor puede darle esa IP a otro equipo.

- El nmap solo hace DISCOVER y recibe OFFER. No hay REQUEST ni ACK, así que /var/lib/dhcp/dhcpd.leases queda sin leases: es normal.

- Cliente real en vez de nmap (hace DISCOVER, OFFER, REQUEST y ACK; no toca la red de la VM porque corre en el namespace cliente del paso 2 y no aplica la configuración). La primera línea instala dhclient, que en Ubuntu 24.04 no viene: si falta, la última corta con exec of "dhclient" failed: No such file or directory. Las tres siguientes le ponen a veth-cli la MAC de la impresora. No cambies las rutas de -lf y -pf a /tmp: en Ubuntu AppArmor solo deja a dhclient escribir en /var/lib/dhcp/dhclient\* y /run/dhclient\*.pid:

sudo apt install -y isc-dhcp-client \# trae dhclient; en Ubuntu 24.04 no viene

sudo ip netns exec cliente ip link set veth-cli down

sudo ip netns exec cliente ip link set veth-cli address 08:00:27:AE:C8:6A

sudo ip netns exec cliente ip link set veth-cli up

sudo ip netns exec cliente dhclient -v -1 -sf /bin/true -lf /var/lib/dhcp/dhclient.cliente.leases -pf /run/dhclient.cliente.pid veth-cli

DHCPDISCOVER on veth-cli to 255.255.255.255 port 67 interval 3

DHCPOFFER of 192.168.100.8 from 192.168.100.1

DHCPREQUEST for 192.168.100.8 on veth-cli to 255.255.255.255 port 67

DHCPACK of 192.168.100.8 from 192.168.100.1

bound to 192.168.100.8 -- renewal in 249 seconds.

- Si piden otra cosa: qué línea tocar en dhcpd.conf y su equivalente en el plan B con dnsmasq (más abajo).

| **Te piden** | **dhcpd.conf** | **dnsmasq** |
|----|----|----|
| Otra red (en vez de 192.168.100.0/24) | en subnet RED netmask MASCARA la dirección de red exacta (termina en .0 en un /24; si no, dhcpd -t dice bad subnet number/mask combination.), y adentro range, option subnet-mask, option broadcast-address y option routers; la fixed-address del host; y en el paso 2 las IP de veth-srv (.1) y veth-cli (.250) | dhcp-range, dhcp-option=option:router y la IP de dhcp-host; y las mismas IP del paso 2 |
| Otro rango | la línea range INICIO FIN; | la línea dhcp-range=INICIO,FIN,MASCARA,LEASE |
| Gateway / router por defecto | option routers IP_GATEWAY; | dhcp-option=option:router,IP_GATEWAY |
| DNS | option domain-name-servers IP1, IP2; | dhcp-option=option:dns-server,IP1,IP2 |
| Dominio | option domain-name "DOMINIO"; | dhcp-option=option:domain-name,DOMINIO |
| Duración del lease | default-lease-time SEG; y max-lease-time SEG; (en segundos) | último campo de dhcp-range (10m, 12h, 1d) |
| Otra reserva | otro bloque host OTRO_NOMBRE { hardware ethernet MAC; fixed-address IP; } con un nombre distinto (si repetís impresora, dhcpd -t dice host impresora: already exists) | otra línea dhcp-host=MAC,IP |
| Varios rangos | varias líneas range | varias líneas dhcp-range= |
| Solo clientes conocidos | deny unknown-clients; | dhcp-ignore=tag:!known |
| Bloquear una MAC | bloque host con deny booting; | dhcp-host=MAC,ignore |
| Servidor autoritativo | authoritative; | dhcp-authoritative |
| Servidor NTP | option ntp-servers IP; | dhcp-option=option:ntp-server,IP |
| Nombre para el cliente | option host-name "NOMBRE"; dentro del host | dhcp-host=MAC,IP,NOMBRE |

- deny unknown-clients; va dentro de un bloque pool { } dentro del subnet, y la línea range del paso 3 se muda adentro del pool: pool { range 192.168.100.10 192.168.100.200; deny unknown-clients; }. Si dejás el range también afuera del pool, dhcpd -t corta con lease 192.168.100.10 is declared twice!. Una MAC sin bloque host no recibe oferta y el log dice no free leases (o unknown client si lo ponés directo en el subnet, sin pool). Con deny booting; el log dice booting disallowed.

- Servir una segunda red (para el ejemplo, 192.168.200.0/24): primero armá otro par veth con estos comandos (sin ip netns add, porque el namespace cliente ya existe):

sudo ip link add veth-srv2 type veth peer name veth-cli2

sudo ip link set veth-cli2 netns cliente

sudo ip addr add 192.168.200.1/24 dev veth-srv2

sudo ip link set veth-srv2 up

sudo ip netns exec cliente ip link set veth-cli2 up

sudo ip netns exec cliente ip addr add 192.168.200.250/24 dev veth-cli2

Después agregá el subnet de la red nueva. Lleva su propio option routers y sus DNS: las option de adentro del primer subnet no pasan al segundo.

Hay que hacer sudo nano /etc/dhcp/dhcpd.conf y agregar al final lo siguiente:

subnet 192.168.200.0 netmask 255.255.255.0 {

range 192.168.200.10 192.168.200.200;

option routers 192.168.200.1;

option domain-name-servers 8.8.8.8, 8.8.4.4;

}

Por último, en el paso 4 poné las dos interfaces (INTERFACESv4="veth-srv veth-srv2") y repetí el paso 5.

- Con una segunda placa en "Red interna" de VirtualBox en lugar del veth: asignale 192.168.100.1/24 y poné su nombre (lo ves con ip -br a) en INTERFACESv4. Esa variante no está probada.

- Plan B si no podés instalar isc-dhcp-server: dnsmasq (el binario suele estar porque lo usa NetworkManager). Como dnsmasq no ve los paquetes que salen de su propia interfaz, el nmap se corre desde la otra punta del veth, en el namespace cliente. Necesita el veth y el namespace del paso 2 y que no haya otro servidor DHCP corriendo en el puerto 67:

Hay que hacer sudo nano /tmp/dnsmasq-parcial.conf, borrar lo que tenga y pegar lo siguiente:

port=0

interface=veth-srv

bind-interfaces

dhcp-authoritative

dhcp-range=192.168.100.10,192.168.100.200,255.255.255.0,10m

dhcp-option=option:router,192.168.100.1

dhcp-option=option:dns-server,8.8.8.8,8.8.4.4

dhcp-host=08:00:27:AE:C8:6A,192.168.100.8

sudo dnsmasq --test -C /tmp/dnsmasq-parcial.conf

sudo dnsmasq -C /tmp/dnsmasq-parcial.conf --log-dhcp

sudo ip netns exec cliente nmap --script broadcast-dhcp-discover --script-args "broadcast-dhcp-discover.mac=08:00:27:AE:C8:6A"

- Para dejar todo como estaba: sudo systemctl stop isc-dhcp-server; si usaste el cliente real, cat /run/dhclient.cliente.pid te da el número de proceso de dhclient y sudo kill NUMERO lo corta; si usaste el plan B, sudo pkill dnsmasq; y al final sudo ip link del veth-srv (borra también veth-cli) y sudo ip netns del cliente.

*Probado en Ubuntu 24.04 (lab). Sin probar: el apt install de isc-dhcp-client y los avisos del apt install de isc-dhcp-server (se sacaron de los scripts del paquete y de su unidad de systemd).*

# Resueltos — Firewall (iptables)

## Filtrar, redirigir y restringir con iptables

En todos hay reglas de iptables: cortar lo que sale (cadena OUTPUT, por usuario o por puerto), cortar lo que entra (INPUT) o redirigir un puerto a otro (tabla nat). De un parcial a otro cambia a quién se le corta (root, tu usuario, todos, todos menos un host), qué puerto, y si además hay una redirección o un servicio que levantar.

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| iptables: que root no acceda a HTTP (80/tcp) y que no se llegue al 9090 de localhost | root sin HTTP (80/tcp) y el resto de los usuarios sí; no llegar al 9090 de localhost pero sí al 9090 de otros hosts | El 9090 propio se corta por la interfaz lo, en IPv4 y en IPv6 (ip6tables); sin redirección |
| iptables: root sin HTTP y redirección 9090 → 9091 | root sin HTTP y los demás usuarios sí; lo que viene de la 10.6.0.x al 9090/tcp se redirige al 9091 | Redirección solo para una red de origen (-s 10.6.0.0/24) |
| iptables: bloquear HTTP a un usuario, permitir a root y redirigir 9090 → 9091 | Tu usuario (pablo en el apunte) sin HTTP y root autorizado; lo que llega al 9090/tcp se redirige al 9091 | Al revés que en los parciales 2016-1C y 2015 de esta tabla: se bloquea al usuario común y root pasa. Reglas atadas a una interfaz (la de la ruta por defecto) |
| Habilitar el servicio echo por TCP y que se use solo desde un host de la red 10.6.0.0/24 | Servicio echo por TCP (puerto 7) que se use solo desde un host de la red 10.6.0.0/24 | Antes del filtro hay que levantar el servicio (systemd, xinetd o ncat) y publicarlo con un túnel ssh por G; la regla va en G o en tu host según el camino |
| iptables: no responder ICMP Echo Request y ssh también por el puerto 2222, salvo para G | No responder ICMP Echo Request; ssh por el 22 para todos y también por el 2222, salvo para G | Excepción por origen: las dos IP de G (192.168.20.1 y 10.6.0.127) salen con RETURN antes del REDIRECT al 22 |
| iptables en tu PC: bloquear lo que entra al puerto 9091 y que no se pueda navegar (puerto 80 saliente) | Que nadie entre a tu puerto 9091/tcp y que desde tu máquina no se navegue por HTTP (80 saliente) | El más simple: dos reglas DROP para todos, sin usuarios ni redirección |

### iptables: que root no acceda a HTTP (80/tcp) y que no se llegue al 9090 de localhost

**Tomado en:** Parcial 2016-1C (segundo parcial) · Ejercicio 2.

> **Idea:** La idea es que root no salga por HTTP (80/tcp) y que desde tu host no se llegue a tu propio 9090, pero sí al de otros hosts, con reglas REJECT en OUTPUT. A root se lo filtra con --uid-owner root, y el 9090 se corta por interfaz con -o lo, también con ip6tables, porque localhost puede entrar por ::1.

**Contexto del parcial:** el host A del consejo del enunciado (nc A 9090) es foo, un host del laboratorio cuya IP A estaba escrita en el pizarrón. Es el mismo foo de Parcial 2016-1C (segundo parcial) · Ejercicio 1, que está en Resueltos — SSH, "Entrar con una llave privada: passphrase o nombre de archivo por deducir".

**Buscar:** iptables, ip6tables, el usuario root NO pueda acceder a páginas web, paginas web, root sin HTTP, HTTP (80/tcp), el resto de los usuarios, curl http://itba.edu.ar/, puerto localhost 9090, pero si a los de otro hosts, nc -l 9090, nc 127.0.0.1 9090, nc localhost 9090, nc A 9090, filtro remoto, uid-owner, --uid-owner root, owner, -m owner, OUTPUT, REJECT, puerto 80, 9090, localhost, lo, -o lo, -i lo, ::1, script iptables, archivo.sh, bloquear localhost 9090, el usuario root NO pueda acceder a paginas web

**Enunciado:** "Realizar todos los cambios necesarios para que en su host: el usuario root NO pueda acceder a páginas web por el protocolo HTTP (80/tcp). el resto de los usuarios puede acceder a páginas web por el protocolo HTTP (80/tcp). Por ejemplo deben poder acceder a http://itba.edu.ar/ (Verificar utilizando curl http://itba.edu.ar/). Desde su host no pueda comunicarse al puerto localhost 9090 de su host, pero si a los de otro hosts. Consejo: Puede utilizar nc -l 9090 y nc 127.0.0.1 9090 para verificar el correcto funcionamiento localmente y puede utilizar nc A 9090 para verificar el correcto funcionamiento del filtro remoto."

**Solución:** la regla de root va en OUTPUT con -m owner --uid-owner root. El 9090 propio se corta sobre la interfaz lo, en IPv4 y en IPv6.

> **1.** Creá el script. El bloque 1 (las líneas con -F y -P del principio) borra todas las reglas anteriores: si ya pusiste reglas de iptables para otro ejercicio, sacá ese bloque.

Hay que hacer nano ~/fw-2016.sh, borrar lo que tenga y pegar lo siguiente:

\#!/bin/bash

\# Parcial 2016-1C, ejercicio 2. Correr con: sudo bash ~/fw-2016.sh

\# 1. Arrancar de cero: sin reglas viejas y politicas en ACCEPT (IPv4 e IPv6)

iptables -F

iptables -t nat -F

iptables -P INPUT ACCEPT

iptables -P OUTPUT ACCEPT

iptables -P FORWARD ACCEPT

ip6tables -F

ip6tables -t nat -F

ip6tables -P INPUT ACCEPT

ip6tables -P OUTPUT ACCEPT

ip6tables -P FORWARD ACCEPT

\# 2. root NO sale por HTTP (80/tcp). El resto de los usuarios si: no matchean y cae la politica ACCEPT

iptables -A OUTPUT -p tcp --dport 80 -m owner --uid-owner root -j REJECT

ip6tables -A OUTPUT -p tcp --dport 80 -m owner --uid-owner root -j REJECT

\# 3. Desde mi host no se habla con el 9090 de mi propio host (ese trafico sale por lo).

\# Al 9090 de otros hosts se sale por la placa de red, asi que no matchea.

iptables -A OUTPUT -o lo -p tcp --dport 9090 -j REJECT

iptables -A OUTPUT -o lo -p udp --dport 9090 -j REJECT

ip6tables -A OUTPUT -o lo -p tcp --dport 9090 -j REJECT

ip6tables -A OUTPUT -o lo -p udp --dport 9090 -j REJECT

\# 4. Mostrar como quedo

iptables -L OUTPUT -n -v --line-numbers

> **2.** Corrielo.

sudo bash ~/fw-2016.sh

\# forma de los apuntes: chmod +x ~/fw-2016.sh y despues sudo ~/fw-2016.sh

> **3.** Sin script, las mismas reglas pegadas a mano:

sudo iptables -A OUTPUT -p tcp --dport 80 -m owner --uid-owner root -j REJECT

sudo iptables -A OUTPUT -o lo -p tcp --dport 9090 -j REJECT

sudo iptables -A OUTPUT -o lo -p udp --dport 9090 -j REJECT

sudo ip6tables -A OUTPUT -p tcp --dport 80 -m owner --uid-owner root -j REJECT

sudo ip6tables -A OUTPUT -o lo -p tcp --dport 9090 -j REJECT

sudo ip6tables -A OUTPUT -o lo -p udp --dport 9090 -j REJECT

**Demostrar:**

Antes, en otra terminal, dejá nc -l 9090 escuchando (es el consejo del enunciado). Después, en esta:

\# root no puede

sudo curl http://itba.edu.ar/

\# el resto si (301 Moved Permanently es una respuesta HTTP valida)

curl -i http://itba.edu.ar/

\# 9090 local: no conecta, ni por 127.0.0.1 ni por localhost

nc -v -w 3 127.0.0.1 9090

nc -v -w 3 localhost 9090

\# 9090 de otro host (el A del enunciado, con nc -l 9090 corriendo alla): conecta

nc -v -w 3 IP_OTRO_HOST 9090

\# contadores

sudo iptables -L OUTPUT -n -v --line-numbers

curl: (7) Failed to connect to itba.edu.ar port 80 after 0 ms: Couldn't connect to server

HTTP/1.1 301 Moved Permanently

nc: connect to 127.0.0.1 port 9090 (tcp) failed: Connection refused

nc: connect to localhost (::1) port 9090 (tcp) failed: Connection refused

nc: connect to localhost (127.0.0.1) port 9090 (tcp) failed: Connection refused

Connection to IP_OTRO_HOST 9090 port \[tcp/\*\] succeeded!

Chain OUTPUT (policy ACCEPT 17 packets, 1102 bytes)

num pkts bytes target prot opt in out source destination

1 1 60 REJECT 6 -- \* \* 0.0.0.0/0 0.0.0.0/0 tcp dpt:80 owner UID match 0 reject-with icmp-port-unreachable

2 2 120 REJECT 6 -- \* lo 0.0.0.0/0 0.0.0.0/0 tcp dpt:9090 reject-with icmp-port-unreachable

3 0 0 REJECT 17 -- \* lo 0.0.0.0/0 0.0.0.0/0 udp dpt:9090 reject-with icmp-port-unreachable

**Ojo:**

- Sin las reglas de ip6tables, si lo que escucha en el 9090 también atiende IPv6 (nc -6 -l 9090, o cualquier servicio en \[::\]:9090), nc localhost 9090 entra por ::1 y conecta igual (Connection to localhost (::1) 9090 port \[tcp/\*\] succeeded!), aunque nc 127.0.0.1 9090 falle. La resolución que circula solo bloquea IPv4.

- El flush inicial (bloque 1 del script) es opcional: borra todas las reglas de iptables que ya tenga la máquina, también las que hayas puesto para otro ejercicio. Si ya tenés reglas puestas, usá el paso 3.

- -m owner --uid-owner solo existe en OUTPUT. En INPUT da RULE_APPEND failed (Invalid argument): rule in chain INPUT.

- El 9090 propio se bloquea por interfaz (-o lo), no por IP: así el 9090 de otros hosts, que sale por la placa de red, sigue andando. En OUTPUT va -o lo; -i lo da Can't use -i with OUTPUT.

- REJECT corta al instante con Connection refused; con DROP el cliente queda colgado hasta el timeout.

- La resolución que circula además baja y sube ip_forward y agrega reglas -m state --state ESTABLISHED,RELATED -j ACCEPT: con política ACCEPT no hacen nada, no las copies.

- Errores solo en las líneas de ip6tables: la VM no tiene IPv6. Las de iptables quedan puestas.

- sudo curl sigue respondiendo: la URL es https (la regla es del puerto 80) o el script no corrió. Mirá sudo iptables -S OUTPUT.

- nc IP_OTRO_HOST 9090 da Connection refused: allá no hay nada escuchando en el 9090. No es tu firewall (el contador de la regla 2 no sube).

*Probado en Ubuntu 24.04 (lab), con itba.edu.ar simulado por un servidor del lab que responde 301.*

### iptables: root sin HTTP y redirección 9090 → 9091

**Tomado en:** Parcial 2015 (segundo parcial) · Ejercicio 2 (otra tanda).

> **Idea:** La idea es que root no salga por HTTP (80/tcp) con --uid-owner root en OUTPUT, que el resto de los usuarios pase por la política ACCEPT y que lo que llega desde 10.6.0.0/24 al 9090/tcp se redirija al 9091 con REDIRECT en nat PREROUTING. El truco es que PREROUTING solo ve lo que llega por la red, así que se prueba desde otro host.

**Contexto del parcial:** del segundo parcial de 2015 hay dos tandas de tres ejercicios, numeradas igual. Las marcadas "(otra tanda)" son las de máquina, relatadas por un alumno; la tanda en papel (subnetting, DHCP y ruteo, CSMA/CD) está en Resueltos — En papel.

**Buscar:** iptables, el root no pueda hablar por el puerto http de tcp, root sin http, los demás usuarios si puedan, los demas usuarios si puedan, puerto 80, uid-owner root, --uid-owner, REJECT, redireccion, redirección, redireccionados al puerto 9091, 9090, 9091, REDIRECT, PREROUTING, nat, 10.6.0.x, 10.6.0.0/24, --src, -s, --to-ports, nc -lk 9091

**Enunciado:** No hay hoja en las fuentes; es el relato de un alumno: "Configurar para que: el root no pueda hablar por el puerto http de tcp. Pero que los demás usuarios si puedan. los request que vengan de la 10.6.0.x y vayan al puerto 9090 de tcp sean redireccionados al puerto 9091".

**Solución:**

Hay que hacer sudo nano /tmp/fw.sh, borrar lo que tenga y pegar lo siguiente:

\#!/bin/bash

iptables -F

iptables -t nat -F

iptables -P INPUT ACCEPT

iptables -P OUTPUT ACCEPT

iptables -P FORWARD ACCEPT

\# root no sale por HTTP (80/tcp); el resto si (politica OUTPUT ACCEPT)

iptables -A OUTPUT -p tcp --dport 80 -m owner --uid-owner root -j REJECT

\# lo que llega desde 10.6.0.0/24 al 9090/tcp se redirige al 9091

iptables -t nat -A PREROUTING -s 10.6.0.0/24 -p tcp --dport 9090 -j REDIRECT --to-ports 9091

sudo bash /tmp/fw.sh

**Demostrar:**

> **1.** En tu máquina: root no puede y el resto sí. Después dejá algo escuchando en el 9091.

sudo curl http://itba.edu.ar/

curl -i http://itba.edu.ar/

nc -lk 9091

> **2.** Desde un host de la 10.6.0.x, conectate al 9090 de tu máquina. Para esto necesitás tu IP en esa red: en tu máquina corré ip -br a y anotá la de tu placa (no la 127.0.0.1), sin la barra ni lo que viene después (10.6.0.20/24 se anota 10.6.0.20); va en lugar de IP_DE_TU_PC. Cuando diga succeeded!, escribí hola y Enter: tiene que aparecer en la terminal del nc -lk 9091. Cortalo con Ctrl+C.

nc -v IP_DE_TU_PC 9090

> **3.** De vuelta en tu máquina (otra terminal): el contador pkts de la regla tiene que estar en 1 o más.

sudo iptables -t nat -L PREROUTING -n -v

curl: (7) Failed to connect to itba.edu.ar port 80 after 1 ms: Couldn't connect to server

HTTP/1.1 301 Moved Permanently

Connection to IP_DE_TU_PC 9090 port \[tcp/\*\] succeeded!

(en la terminal del nc -lk 9091 aparece: hola)

Chain PREROUTING (policy ACCEPT 1 packets, 338 bytes)

pkts bytes target prot opt in out source destination

1 60 REDIRECT 6 -- \* \* 10.6.0.0/24 0.0.0.0/0 tcp dpt:9090 redir ports 9091

**Ojo:**

- En el 9090 no hay nada escuchando: si conecta es porque la regla lo mandó al 9091. Desde un origen que no es 10.6.0.x da Connection refused.

- -m owner --uid-owner solo en OUTPUT. El apunte lo intentó en INPUT y no se puede. La inversa ("bloquear mi usuario, permitir root") usa --uid-owner MIUSUARIO -j REJECT: está resuelta en Parcial 2014-1C · Ejercicio 2, en este mismo tipo (Resueltos — Firewall, "Filtrar, redirigir y restringir con iptables").

- "Que los demás usuarios sí puedan" no necesita regla: alcanza con la política OUTPUT ACCEPT.

- La redirección va en -t nat -A PREROUTING ... -j REDIRECT --to-ports 9091. REDIRECT manda a un puerto del **mismo** host; para otra máquina es DNAT.

- -s 10.6.0.0/24 es la forma de escribir "la 10.6.0.x" (--src es lo mismo que -s).

- PREROUTING solo ve lo que llega desde la red. Una prueba desde tu propia máquina (nc localhost 9090) no pasa por ahí: probá desde otro host.

- Las líneas iptables -F e iptables -t nat -F del principio del script borran todas las reglas: si ya pusiste reglas para otro ejercicio, sacá esas dos líneas (las de -P pueden quedar).

*Probado en Ubuntu 24.04 (lab), con un host 10.6.0.5 en un namespace y itba.edu.ar simulado por un servidor del lab.*

### iptables: bloquear HTTP a un usuario, permitir a root y redirigir 9090 → 9091

**Tomado en:** Parcial 2014-1C · Ejercicio 2.

> **Idea:** La idea es que tu usuario no salga por HTTP (80/tcp), con --uid-owner y REJECT en OUTPUT, que root sí pueda salir y que lo que entra al 9090/tcp se redirija al 9091 con REDIRECT en nat PREROUTING. El truco es cambiar TU_INTERFAZ_DE_RED y TU_USUARIO por los tuyos: con una interfaz equivocada iptables no da error, pero las reglas no matchean nunca.

**Buscar:** iptables, bloqueo mi usuario, bloquear usuario, autorizo a root a salir, pablo, uid-owner, --uid-owner, OUTPUT, REJECT, root ACCEPT, redirigir un puerto a otro, redirigir puerto, 9090, 9091, REDIRECT, PREROUTING, nat, --to-port, eth0, ip route, iptables --list -t nat, IFNAMSIZ

**Enunciado:** No hay consigna en las fuentes; está reconstruido de la resolución de un alumno ("Bloqueo mi usuario", "Autorizo a root a salir", "Redirigir un puerto a otro"): bloquear que tu usuario (en el apunte, pablo) salga por HTTP (80/tcp), autorizar a root a salir, y redirigir lo que llega al puerto 9090/tcp al 9091.

**Solución:**

Antes anotá dos datos: tu interfaz de red, con ip route (en la línea default via ... dev enp0s3, es la palabra que sigue a dev; en el ejemplo, enp0s3), y tu usuario, con whoami.

Hay que hacer sudo nano /tmp/fw.sh, borrar lo que tenga y pegar lo siguiente, cambiando TU_INTERFAZ_DE_RED (aparece tres veces) por tu interfaz y TU_USUARIO por tu usuario:

\#!/bin/bash

iptables -F

iptables -t nat -F

iptables -P INPUT ACCEPT

iptables -P OUTPUT ACCEPT

iptables -P FORWARD ACCEPT

\# bloquear HTTP saliente a tu usuario (pablo en el apunte)

iptables -A OUTPUT -o TU_INTERFAZ_DE_RED -p tcp --dport 80 -m owner --uid-owner TU_USUARIO -j REJECT

\# autorizar explicitamente a root (opcional, la politica OUTPUT ya es ACCEPT)

iptables -A OUTPUT -o TU_INTERFAZ_DE_RED -p tcp --dport 80 -m owner --uid-owner root -j ACCEPT

\# redirigir 9090 -\> 9091 (lo que entra por tu interfaz)

iptables -t nat -A PREROUTING -i TU_INTERFAZ_DE_RED -p tcp --dport 9090 -j REDIRECT --to-port 9091

sudo bash /tmp/fw.sh

**Demostrar:**

\# como tu usuario: rechazado

curl -i http://itba.edu.ar/

\# como root: pasa

sudo curl -i http://itba.edu.ar/

\# ves el REDIRECT a 9091

sudo iptables -t nat -L PREROUTING -n -v

curl: (7) Failed to connect to itba.edu.ar port 80 after 1 ms: Couldn't connect to server

HTTP/1.1 301 Moved Permanently

(El error (7) es que curl no llegó a conectar: la regla lo rechazó. 301 es la respuesta de itba.edu.ar, que redirige a https)

**Ojo:**

- Si dejás TU_INTERFAZ_DE_RED, iptables da interface name ... must be shorter than IFNAMSIZ (15), y si dejás TU_USUARIO (o ponés un usuario que no existe) da owner: Bad value for "--uid-owner" option: esas reglas no se cargan. Si ponés una interfaz que existe pero no es la de la ruta por defecto (VM con dos placas), iptables no da error y las reglas no matchean nunca: tu usuario sigue saliendo.

- -m owner --uid-owner USUARIO solo en OUTPUT. La regla de root ACCEPT es redundante si la política OUTPUT es ACCEPT, pero no molesta y deja clara la intención.

- La redirección va en -t nat -A PREROUTING ... -j REDIRECT --to-port 9091 (acá es --to-port, singular; en Parcial 2015 (segundo parcial) · Ejercicio 2 (otra tanda) y en Parcial 2011 · Ejercicio 4, de este mismo tipo, es --to-ports: ambas formas las acepta REDIRECT).

- REDIRECT manda a un puerto del mismo host; para mandar a OTRA máquina es DNAT (-j DNAT --to-destination IP:PUERTO).

*Probado en Ubuntu 24.04 (lab): reglas de owner (usuario rechazado / root pasa) y REDIRECT 9090→9091 cargadas y visibles en la tabla nat.*

### Habilitar el servicio echo por TCP y que se use solo desde un host de la red 10.6.0.0/24

**Tomado en:** Parcial 2011 · Ejercicio 2.

> **Idea:** La idea es levantar el servicio echo en el puerto 7 de tu host y que solo un host de 10.6.0.0/24 pueda usarlo: como G no encamina, se publica en G con un túnel ssh -R y el filtro por origen va en G. Si G no publica el puerto hacia afuera (GatewayPorts), el túnel lo abre ese host con ssh -L.

**Contexto del parcial:** la red de los alumnos es 192.168.10.0/25 y la del laboratorio 10.6.0.0/24; entre las dos está el host G (192.168.20.1 del lado de los alumnos, 10.6.0.127 del lado del laboratorio), que no encamina paquetes ni hace NAT. Tenés una cuenta SSH en G: usuario user, contraseña pdcpass.

**Buscar:** servicio echo, echo bajo tcp, puerto 7, xinetd, inetd, /etc/xinetd.d/echo, disable = no, only_from, systemd socket, ListenStream, Accept=yes, ncat -e, solo desde un host de la red 10.6.0.0/24, cliente echo, túnel remoto, tunel remoto, ssh -R, ssh -L, GatewayPorts, clientspecified, Administrador de G, iptables, --dport 10000, ! -s 10.6.0.100, DROP, nc, telnet, IFNAMSIZ

**Enunciado:** "Habilitar el servicio echo bajo tcp. Realizar los cambios necesarios para que solo desde un host de la red 10.6.0.0/24 pueda accederse a dicho servicio (el host G y los hosts de la red 192.168.10.0/25 pueden acceder). El cliente echo debe ejecutar en ese host de la red 10.6.0.0/24." Tal cual figura en el apunte; el paréntesis es ambiguo y abajo están cubiertas las dos lecturas.

**Depende de:** Parcial 2011 · Ejercicio 1 (Resueltos — Red, "Llegar a una red detrás de un gateway y descubrir sus servicios"): de ahí salen la IP de tu host (192.168.10.3 en los ejemplos) y la ruta para llegar a G en 192.168.20.1.

**Solución:**

> **1.** Habilitá echo en el puerto 7 de tu host. Opción sin instalar nada (systemd hace de inetd). Rutas: /etc/systemd/system/echo.socket y /etc/systemd/system/echo@.service.

Hay que hacer sudo nano /etc/systemd/system/echo.socket, borrar lo que tenga y pegar lo siguiente:

\[Unit\]

Description=Servicio echo TCP (puerto 7)

\[Socket\]

ListenStream=7

Accept=yes

\[Install\]

WantedBy=sockets.target

Hay que hacer sudo nano /etc/systemd/system/echo@.service, borrar lo que tenga y pegar lo siguiente:

\[Unit\]

Description=Echo por conexion

\[Service\]

ExecStart=/bin/cat

StandardInput=socket

StandardOutput=socket

sudo systemctl daemon-reload

sudo systemctl enable --now echo.socket

systemctl status echo.socket

En la salida del último comando tienen que aparecer estas dos líneas:

Active: active (listening) since ...

Listen: \[::\]:7 (Stream)

> **2.** Opción del apunte ("activar echo en xinetd y restartear"), si la VM tiene xinetd. Ruta: /etc/xinetd.d/echo.

Hay que hacer sudo nano /etc/xinetd.d/echo, borrar lo que tenga y pegar lo siguiente:

service echo

{

disable = no

type = INTERNAL

id = echo-stream

socket_type = stream

protocol = tcp

user = root

wait = no

}

sudo systemctl restart xinetd

\# forma vieja: sudo service xinetd restart

> **3.** Opción de emergencia en una línea (queda en primer plano; cortala con Ctrl+C):

sudo ncat -lk 7 --exec /bin/cat

> **4.** Los hosts de 10.6.0.0/24 no tienen ruta hacia vos porque G no encamina. Publicá el servicio en G con un túnel remoto desde tu host (contraseña pdcpass). Dejá esa sesión abierta.

ssh -R 10.6.0.127:10000:localhost:7 user@192.168.20.1

> **5.** En esa sesión (ya estás en G), mirá dónde quedó escuchando el puerto 10000:

ss -ltn

En la salida, mirá la línea del puerto 10000. Si dice 10.6.0.127:10000, seguí con el paso 6. Si dice 127.0.0.1:10000, el sshd de G no permite publicar el puerto hacia afuera (necesita GatewayPorts clientspecified en /etc/ssh/sshd_config, que solo puede poner el administrador de G): usá el paso 8.

> **6.** Restricción a un solo host del laboratorio (10.6.0.100 en el apunte). La aplica el administrador de G, como root en G. INTERFAZ_LAB_DE_G es la interfaz de G que tiene la 10.6.0.127: corré ip -br a en G y anotá el nombre de la placa que tiene esa IP. Si lo dejás sin cambiar, iptables da interface name ... must be shorter than IFNAMSIZ (15).

sudo iptables -A INPUT -i INTERFAZ_LAB_DE_G -p tcp --dport 10000 ! -s 10.6.0.100 -j DROP

> **7.** Cliente echo en el host 10.6.0.100: escribí una línea y tiene que volver igual. Se corta con Ctrl+C.

nc 10.6.0.127 10000

\# equivalente: telnet 10.6.0.127 10000

> **8.** Camino sin ser root en G: el túnel lo abre el host del laboratorio, con la cuenta de G, hacia tu echo. El puerto 7777 queda en el loopback de ese host, así que solo ese host puede usarlo.

\# en el host del laboratorio (10.6.0.x)

ssh -L 7777:192.168.10.3:7 user@10.6.0.127

\# en otra terminal del mismo host

nc localhost 7777

Y en tu host limitá quién entra al puerto 7: G (por donde sale el túnel) y la red de alumnos, como dice el paréntesis del enunciado.

sudo iptables -A INPUT -p tcp --dport 7 -s 192.168.20.1 -j ACCEPT

sudo iptables -A INPUT -p tcp --dport 7 -s 192.168.10.0/25 -j ACCEPT

sudo iptables -A INPUT -p tcp --dport 7 -j DROP

**Demostrar:**

En el host permitido del laboratorio (10.6.0.100), conectate y escribí hola y Enter: abajo aparece otra vez hola, que es el eco. Cortalo con Ctrl+C.

nc 10.6.0.127 10000

En otro host del laboratorio: no conecta. El -w 3 hace que nc se rinda a los 3 segundos; sin él tarda unos 2 minutos en rendirse (o cortalo con Ctrl+C).

nc -v -w 3 10.6.0.127 10000

En G: la regla y sus contadores. El contador pkts sube con cada intento bloqueado (con -w 3, cada intento suma 3: el SYN y dos reintentos).

sudo iptables -L INPUT -n -v

hola

hola

nc: connect to 10.6.0.127 port 10000 (tcp) timed out: Operation now in progress

pkts bytes target prot opt in out source destination

3 180 DROP 6 -- INTERFAZ_LAB_DE_G \* !10.6.0.100 0.0.0.0/0 tcp dpt:10000

**Ojo:**

- Con -R, la conexión al echo la hace tu propio cliente ssh desde 127.0.0.1: en tu host no podés distinguir quién es el cliente real. Por eso el filtro por IP de origen del camino con -R va en G y no en tu máquina.

- ssh -R 10000:localhost:7 (como en el apunte) y también -R 10.6.0.127:10000:... quedan en 127.0.0.1 de G si G tiene GatewayPorts no, que es el valor por defecto. No da error: hay que mirarlo con ss -ltn.

- Sin -i INTERFAZ_LAB_DE_G, la regla del paso 6 también corta a los procesos del propio G.

- Si el paréntesis se lee al revés (G y los alumnos NO deben acceder): usá el túnel -R y en tu host dejá entrar al puerto 7 solo por loopback, que es por donde entrega el túnel: sudo iptables -A INPUT -p tcp --dport 7 ! -i lo -j DROP.

- Con xinetd también se puede filtrar por origen sin iptables: agregá only_from = 192.168.20.1 192.168.10.0/25 dentro del bloque service echo.

- Probá el eco escribiendo a mano, como en Demostrar: con ncat --exec, un cliente que manda y cierra al instante (echo hola \| nc -N ...) puede no recibir el eco. Con la opción systemd no pasa.

- nc -l 7 solo no es un servicio echo: no devuelve lo recibido.

*Probado en Ubuntu 24.04 (lab). Sin probar: xinetd (no está instalado en el lab; el archivo y only_from se revisaron contra la página de manual xinetd.conf).*

### iptables: no responder ICMP Echo Request y ssh también por el puerto 2222, salvo para G

**Tomado en:** Parcial 2011 · Ejercicio 4.

> **Idea:** La idea es que tu host no responda pings, descartando --icmp-type echo-request en INPUT, y que ssh atienda también por el 2222 redirigiéndolo al 22 en nat PREROUTING, salvo para G. El truco es que G tiene dos IP, así que cada una sale antes con RETURN, y una regla en nat OUTPUT cubre las conexiones desde tu propio host.

**Contexto del parcial:** la red de los alumnos es 192.168.10.0/25 y la del laboratorio 10.6.0.0/24. Entre las dos está el host G, con 192.168.20.1 del lado de los alumnos y 10.6.0.127 del lado del laboratorio: por eso G puede llegar con dos IP de origen distintas.

**Buscar:** no responda ICMP Echo Requests, ping, icmp, echo-request, icmp-type 8, DROP, icmp_echo_ignore_all, puerto estándar, puerto estandar, servidor ssh por el puerto 2222, 2222, excepto si vienen de G, REDIRECT, --to-ports 22, PREROUTING, nat OUTPUT, RETURN, ! -s, sshd_config, Port 2222, Connection refused, openssh-server, Unit ssh.service not found

**Enunciado:** "Realizar los cambios necesarios para que: a) su host no responda ICMP Echo Requests b) además de por el puerto estándar, los usuarios puedan acceder al servidor ssh por el puerto 2222 (excepto si vienen de G) y 2222 (todos)". Tal cual figura en el apunte; por la solución, el final es "y 22 (todos)": por el 22 entran todos y por el 2222 todos menos G.

**Depende de:** Parcial 2011 · Ejercicio 1 (Resueltos — Red, "Llegar a una red detrás de un gateway y descubrir sus servicios"): de ahí sale la IP de tu host, 192.168.10.3 en los ejemplos.

**Solución:**

> **1.** a) Descartá los pings que llegan.

sudo iptables -A INPUT -p icmp --icmp-type echo-request -j DROP

> **2.** b) El servidor ssh tiene que estar corriendo en el puerto estándar.

sudo apt install -y openssh-server \# si ya está, no cambia nada

sudo systemctl start ssh

\# forma vieja: sudo service ssh start

> **3.** b) Lo que llega al 2222 se entrega al 22, salvo que venga de G (sus dos direcciones salen de la cadena antes de la redirección).

sudo iptables -t nat -A PREROUTING -p tcp --dport 2222 -s 192.168.20.1 -j RETURN

sudo iptables -t nat -A PREROUTING -p tcp --dport 2222 -s 10.6.0.127 -j RETURN

sudo iptables -t nat -A PREROUTING -p tcp --dport 2222 -j REDIRECT --to-ports 22

\# conexiones hechas desde tu propio host: no pasan por PREROUTING

sudo iptables -t nat -A OUTPUT -o lo -p tcp --dport 2222 -j REDIRECT --to-ports 22

**Demostrar:**

USUARIO es tu usuario en tu host (lo ves con whoami).

\# a) desde tu host y desde otro (por ejemplo G): 100% packet loss

ping -c 2 127.0.0.1

\# b) desde otro host de la red de alumnos: entra por los dos puertos

ssh -p 2222 USUARIO@192.168.10.3

ssh -p 22 USUARIO@192.168.10.3

\# b) desde G: por 2222 no, por 22 si

ssh -p 2222 USUARIO@192.168.10.3

\# reglas con contadores

sudo iptables -L INPUT -n -v

sudo iptables -t nat -L -n -v

2 packets transmitted, 0 received, 100% packet loss

ssh: connect to host 192.168.10.3 port 2222: Connection refused

Chain INPUT (policy ACCEPT)

4 336 DROP 1 -- \* \* 0.0.0.0/0 0.0.0.0/0 icmptype 8

Chain PREROUTING (policy ACCEPT)

1 60 RETURN 6 -- \* \* 192.168.20.1 0.0.0.0/0 tcp dpt:2222

0 0 RETURN 6 -- \* \* 10.6.0.127 0.0.0.0/0 tcp dpt:2222

1 60 REDIRECT 6 -- \* \* 0.0.0.0/0 0.0.0.0/0 tcp dpt:2222 redir ports 22

Chain OUTPUT (policy ACCEPT)

2 120 REDIRECT 6 -- \* lo 0.0.0.0/0 0.0.0.0/0 tcp dpt:2222 redir ports 22

**Ojo:**

- Con la regla de a) tu host sigue pudiendo hacer ping a otros: las respuestas que recibe son echo-reply (tipo 0), no echo-request (tipo 8). No bloquees todo -p icmp.

- Alternativa a a) sin iptables: sudo sysctl -w net.ipv4.icmp_echo_ignore_all=1.

- El apunte usa una sola regla, sudo iptables -t nat -A PREROUTING -p tcp --dport 2222 ! -s 192.168.20.1 -j REDIRECT --to-ports 22. Sirve si G llega siempre con esa IP; con ! no se pueden negar dos orígenes en la misma regla, por eso arriba van los RETURN. La IP 10.211.55.2 del apunte era de su máquina de prueba, no de G.

- Sin la regla en nat OUTPUT, ssh -p 2222 localhost desde tu propio host da Connection refused.

- G recibe Connection refused en el 2222 porque ahí no escucha nadie; solo existe la redirección, y a G no se le aplica.

- Unit ssh.service not found: falta openssh-server (sudo apt install -y openssh-server).

- Variante con sshd en los dos puertos de verdad, en Ubuntu 24.04: hay que hacer sudo nano /etc/ssh/sshd_config.d/puertos.conf, borrar lo que tenga y pegar dos líneas, Port 22 y Port 2222 (una abajo de la otra); después sudo systemctl daemon-reload y sudo systemctl restart ssh.socket (en versiones anteriores: sudo systemctl restart ssh), y a G se lo corta con sudo iptables -A INPUT -p tcp --dport 2222 -s 192.168.20.1 -j REJECT --reject-with tcp-reset.

- Para deshacer todo: sudo iptables -F y sudo iptables -t nat -F.

*Probado en Ubuntu 24.04 (lab). Sin probar: la instalación real de openssh-server (en Ubuntu 24.04 solo se simuló con apt-get -s install openssh-server).*

### iptables en tu PC: bloquear lo que entra al puerto 9091 y que no se pueda navegar (puerto 80 saliente)

**Tomado en:** Parcial sin fecha · Ejercicio 3.

> **Idea:** La idea es que nadie pueda conectarse al puerto 9091/tcp de tu máquina y que desde ella no se navegue por HTTP, con dos reglas DROP en tu PC: una en INPUT con --dport 9091 y otra en OUTPUT con --dport 80. Si algo sigue pasando, va por IPv6 y hay que repetirlas con ip6tables.

**Contexto del parcial:** las fuentes no traen la consigna; el enunciado está reconstruido de la resolución de un alumno. foo y bar son los hosts de la cátedra de los otros dos ejercicios, Parcial sin fecha · Ejercicio 1 (Resueltos — SSH, "Entrar con una llave privada: passphrase o nombre de archivo por deducir") y Parcial sin fecha · Ejercicio 2 (Resueltos — SSH, "Salir a internet o llegar a un servicio interno con túneles"); acá no se usan.

**Buscar:** iptables, bloquear paquetes, 9091, --destination-port 9091, --dport, INPUT, OUTPUT, DROP, REJECT, puerto 80, no se puede navegar, política default ACCEPT, politica default ACCEPT, ni en bar ni en foo somos root

**Enunciado:** reconstruido: a) que nadie pueda conectarse al puerto TCP 9091 de tu máquina; b) que desde tu máquina no se pueda navegar por HTTP. No sos root ni en foo ni en bar: toda la solución va en tu PC. Se asume política por defecto ACCEPT.

**Solución:**

> **1.** a) Paquetes que entran con destino a tu puerto 9091: cadena INPUT.

sudo iptables -A INPUT -p tcp --dport 9091 -j DROP

> **2.** b) Paquetes que salen de tu máquina hacia el puerto 80 de cualquiera: cadena OUTPUT.

sudo iptables -A OUTPUT -p tcp --dport 80 -j DROP

**Demostrar:**

Para a) necesitás la IP de tu PC: corré ip -br a y anotá la de tu placa (no la 127.0.0.1), sin la barra ni lo que viene después (192.168.1.50/24 se anota 192.168.1.50); va en lugar de IP_DE_TU_PC.

\# a) desde OTRA maquina (en la tuya deja antes algo escuchando en otra terminal: nc -lk 9091)

nc -zv -w 2 IP_DE_TU_PC 9091

\# b) desde tu maquina

curl -m 5 http://www.google.com/

sudo iptables -L -n -v

nc: connect to IP_DE_TU_PC port 9091 (tcp) timed out: Operation now in progress

curl: (28) Connection timed out after 5000 milliseconds

Chain INPUT (policy ACCEPT)

2 120 DROP 6 -- \* \* 0.0.0.0/0 0.0.0.0/0 tcp dpt:9091

Chain OUTPUT (policy ACCEPT)

3 180 DROP 6 -- \* \* 0.0.0.0/0 0.0.0.0/0 tcp dpt:80

**Ojo:**

- --destination-port (apunte) y --dport son lo mismo. Las dos necesitan -p tcp antes.

- Con DROP el cliente se queda esperando hasta el timeout. Con -j REJECT falla al instante: curl: (7) Failed to connect ... Couldn't connect to server.

- La regla de b) no corta HTTPS (443). Si piden "no navegar" en general, agregá sudo iptables -A OUTPUT -p tcp --dport 443 -j DROP.

- La regla de a) con -A INPUT también corta las conexiones al 9091 hechas desde tu propia máquina (entran por lo).

- iptables solo filtra IPv4. Si después de las reglas curl sigue trayendo la página o el 9091 sigue conectando, está saliendo por IPv6: repetí las dos reglas con ip6tables (sudo ip6tables -A INPUT -p tcp --dport 9091 -j DROP y sudo ip6tables -A OUTPUT -p tcp --dport 80 -j DROP).

- Para borrar una regla repetí el comando con -D en lugar de -A. Para borrar todas: sudo iptables -F.

*Probado en Ubuntu 24.04 (lab).*

# Resueltos — Capturas (Wireshark)

## Responder preguntas sobre una captura

En todos te dan un archivo de captura (.pcap, a veces comprimido o cifrado) y hay que sacar de adentro respuestas puntuales con filtros de Wireshark o tshark y, cuando hace falta, siguiendo un stream y decodificando base64. De un parcial a otro cambian la captura, cómo se consigue (adjunta, bajada con wget o, en el Parcial 2012-1C, desencriptada antes con gpg) y el protocolo de cada pregunta: RIP, DHCP, traceroute, Telnet, SMTP, DNS, HTTP y TCP.

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| Análisis de botica.pcap.gz, preguntas A a E | Sobre botica.pcap.gz: redes a las que llega 10.0.0.1, DNS que propone 10.20.20.4, saltos de 192.168.0.27 a 8.8.8.8, password del que se loguea en 192.168.0.1 y cuerpo del mail a jcodagno@itba.edu.ar | Cinco protocolos en una sola captura: RIPv1, DHCP (opción 6), traceroute UDP, Telnet y SMTP con el cuerpo en base64 |
| Mostrar la imagen que viaja dentro de parcial-correo.pcap.gz | Mostrar la imagen del correo que viaja en parcial-correo.pcap.gz, que se baja de http://foo.leak.com.ar/parcial-correo.pcap.gz | Export Objects no sirve: se sigue el stream SMTP (el 23) en raw y se decodifica a mano el adjunto image/png |
| Captura parcial-dns-y-otros: IP de ejercicio.leak.com.ar y cuerpo del mail a jcodagno | Sobre parcial-dns-y-otros.pcap.gz: IP del registro ejercicio.leak.com.ar y cuerpo del mail a jcodagno@itba.edu.ar | DNS (campo dns.a) más SMTP en base64. Sin probar: esa captura no está en las fuentes |
| Preguntas de Wireshark sobre capture.pcap: IP de cliente y servidor, netstat -tn, recursos HTTP, servidor, conexiones persistentes, quién cierra | Sobre capture.pcap: IP del cliente y del servidor, entradas de netstat -tn, recursos HTTP y su tipo, aplicación servidor, conexiones persistentes y quién cierra | Todo HTTP y TCP: SYN, header Server, tcp.stream y primer FIN. Las salidas son de una captura de ejemplo, no la del parcial |

### Análisis de botica.pcap.gz, preguntas A a E

**Tomado en:** Parcial 2021-1C · Ejercicio 5.

> **Idea:** La idea es sacar de botica.pcap.gz, con tshark o Wireshark, cinco respuestas de cinco protocolos: RIPv1, DHCP, traceroute UDP, Telnet y SMTP. Las trampas: el DNS es la opción 6 del Offer (10.20.20.4 es el servidor DHCP), los saltos salen del TTL de la sonda que contesta 8.8.8.8 y el cuerpo del mail viene en base64.

**Buscar:** botica.pcap, botica.pcap.gz, 10.0.0.1 redes IP, RIP, RIPv1, 10.20.20.4 servidor DNS, DHCP, 8.8.8.8 saltos, 192.168.0.27, traceroute, password 192.168.0.1, telnet, jcodagno@itba.edu.ar, cuerpo del mensaje, SMTP, base64, tshark, Follow TCP Stream, archivo adjunto, archivo PCAP producido con Wireshark, pedidos y respuestas DNS, tramas de otros protocolos, En base a la información contenida en el archivo, En base a la informacion contenida en el archivo, 10.0.0.1, a qué redes IP tiene acceso, a que redes IP tiene acceso, dirección IP del servidor DNS que propone 10.20.20.4, direccion IP del servidor DNS que propone 10.20.20.4, cuando le piden configuración de red, cuando le piden configuracion de red, A cuántos saltos se encuentra 8.8.8.8, A cuantos saltos se encuentra 8.8.8.8, password del usuario que se loguea remotamente, correo electrónico destinado a, correo electronico destinado a, qué texto lee en su pantalla, que texto lee en su pantalla, Se aceptan respuestas parciales

**Enunciado:** "El archivo adjunto botica.pcap.gz es un archivo PCAP producido con Wireshark que contiene pedidos y respuestas DNS, y tramas de otros protocolos. En base a la información contenida en el archivo responda las siguientes preguntas: A. El host 10.0.0.1, ¿a qué redes IP tiene acceso? \[ 0.4 puntos \] B. ¿Cuál es la dirección IP del servidor DNS que propone 10.20.20.4 cuando le piden configuración de red? \[ 0.5 puntos \] C. ¿A cuántos saltos se encuentra 8.8.8.8 desde 192.168.0.27? \[ 0.4 puntos \] D. ¿Cuál es la password del usuario que se loguea remotamente 192.168.0.1? \[0.4 puntos\] E. Un usuario que recibe el correo electrónico destinado a jcodagno@itba.edu.ar qué texto lee en su pantalla como cuerpo del mensaje \[ 0.4 puntos \] Consejo. Escriba como comentario de la entrega de campus la dirección solución para una primera corrección rápida. Puede armar un video o varios videos mostrando como llega a las respuestas. Se aceptan respuestas parciales (se puntúa según lo que se responda)."

**Solución:** descomprimí y abrí con tshark (o Wireshark gráfico, File \> Open abre el .gz directo):

gunzip -k botica.pcap.gz \# deja botica.pcap y conserva el .gz

tshark -r botica.pcap -q -z io,phs \# panorama de protocolos

**A. 10.0.0.1 → tiene acceso a 10.0.0.0, a 200.0.1.0/24 (directa) y a 200.0.2.0/24 (aprendida por RIP).** 10.0.0.1 es un router que habla RIPv1.

tshark -r botica.pcap -Y 'rip && ip.src == 10.0.0.1' -T fields -E header=y -E separator='\|' -e frame.number -e rip.command -e rip.ip -e rip.metric

tshark -r botica.pcap -Y 'rip && ip.src == 10.0.0.2' -T fields -E header=y -E separator='\|' -e frame.number -e rip.ip -e rip.metric

Filtro Wireshark: rip (o ip.addr == 10.0.0.1). GUI: abrir un Response y expandir "Routing Information Protocol" \> "IP Address: 200.0.1.0, Metric: 1". 10.0.0.1 anuncia 200.0.1.0 con métrica 1 (directa); el vecino 10.0.0.2 anuncia 200.0.2.0 (que 10.0.0.1 alcanza a 2 saltos). La red 10.0.0.0 es donde está su interfaz (RIPv1 es classful, clase A = /8, no manda máscara).

**B. 10.20.20.1** (opción DHCP 6 "Domain Name Server" del Offer y del ACK que manda el servidor DHCP 10.20.20.4).

tshark -r botica.pcap -Y 'dhcp' -T fields -E header=y -E separator='\|' -e frame.number -e ip.src -e dhcp.option.dhcp -e dhcp.ip.your -e dhcp.option.domain_name_server

Filtro Wireshark: dhcp.option.domain_name_server (frames 322 Offer y 328 ACK). GUI: abrir el Offer \> "Option: (6) Domain Name Server". NO confundir: 10.20.20.4 es el servidor DHCP ("Server Identifier"), no el DNS.

**C. 9 saltos** (8 routers intermedios; 8.8.8.8 responde recién a la sonda con TTL 9). Es un traceroute UDP, una sonda por TTL (1 a 12).

tshark -r botica.pcap -Y 'ip.addr == 192.168.0.27' -T fields -E header=y -E separator='\|' -e frame.number -e ip.src -e ip.dst -e ip.ttl -e udp.dstport -e icmp.type -e icmp.code

El destino contesta ICMP "Port unreachable" (type 3 code 3); los routers del medio contestan "TTL exceeded" (type 11). La sonda de TTL más bajo que contesta el destino es la de TTL 9 → 8.8.8.8 está a 9 saltos.

**D. user** (el usuario es fake; sesión Telnet 192.168.0.2 → 192.168.0.1:23, texto plano).

tshark -r botica.pcap -q -z follow,tcp,ascii,0

\# solo lo que tipeó el cliente (sale una tecla por línea):

tshark -r botica.pcap -Y 'telnet.data && ip.dst == 192.168.0.1' -T fields -e telnet.data

Filtro Wireshark: ip.addr == 192.168.0.1 && telnet; GUI: clic derecho \> Follow \> TCP Stream. Se lee fake (Enter) user (Enter) y después ls, ls -a, etc. En la salida del segundo comando leé las teclas de arriba abajo: f, a, k, e, Enter, u, s, e, r, Enter. El Enter no sale como una letra: es la línea que muestra \r, porque telnet lo manda como retorno de carro.

**E. áéíóú** (las cinco vocales con tilde + salto de línea). Viaja por SMTP, multipart/alternative, cuerpo en base64.

tshark -r botica.pcap -q -z follow,tcp,ascii,26

\# el cuerpo text/plain en base64 que aparece abajo de "Content-Type: text/plain":

echo 'w6HDqcOtw7PDug0K' \| base64 -d

Filtro Wireshark: smtp \|\| imf; para ir directo frame contains "jcodagno@itba.edu.ar". GUI: Follow \> TCP Stream, copiás la línea base64 bajo text/plain y la decodificás; o File \> Export Objects \> IMF.

**Demostrar:** cada respuesta con su comando tshark (o el campo en Wireshark) y el valor: A 10.0.0.0, 200.0.1.0 y 200.0.2.0; B 10.20.20.1; C 9; D user; E áéíóú.

**Ojo (trampas de esta captura):**

- A: la máscara real de 10.0.0.x no se puede saber (RIPv1 no transporta máscaras). 10.0.0.1 no reenvía 200.0.2.0 por split horizon. Vale 0.4; conviene responder las tres redes aclarando cuál es directa y cuál aprendida.

- C: contar los que respondieron (192.168.0.1, 72.14.194.198 y 8.8.8.8 = 3) está MAL (la mayoría de los routers no contesta). Restar TTL de la respuesta (128-116) está MAL (es la vuelta). El "Expert Info: Possible traceroute hop \#3" de Wireshark acá miente (asume 3 sondas por salto, pero hay una sola). Hay OTRO traceroute en la captura (192.168.0.11 → 172.217.28.195): no es el que preguntan.

- D: en el Follow Stream el usuario se ve duplicado (ffaakkee) porque el servidor hace eco de cada tecla; la password NO tiene eco (se ve una sola vez, user). tshark -z credentials NO la encuentra, hay que seguir el stream.

- E: es la misma respuesta que el inciso B del Recuperatorio 2020-1C · Ejercicio II (Resueltos — Capturas (Wireshark), 'Responder preguntas sobre una captura'): el cuerpo del mail a jcodagno. Si en vez de follow usás Export Objects y no sale, seguí el stream a mano.

*Probado en Ubuntu 24.04 (lab): las cinco respuestas verificadas con tshark 4.2 sobre botica.pcap (A=10.0.0.0/200.0.1.0/200.0.2.0, B=10.20.20.1, C=9, D=user, E=áéíóú).*

### Mostrar la imagen que viaja dentro de parcial-correo.pcap.gz

**Tomado en:** Parcial 2020-1C · Ejercicio VI.

> **Idea:** La idea es bajar parcial-correo.pcap.gz, encontrar el correo SMTP (stream 23), reconstruir la imagen PNG que viaja adjunta en base64 y mostrarla. El truco es que Export Objects no sirve por el pipelining del cliente: hay que seguir el stream en raw, aislar la parte del adjunto y decodificarla con base64 -d.

**Buscar:** parcial-correo.pcap.gz, correo electronico, imagen, SMTP, base64, image.png, Follow TCP Stream, Export Objects, multipart, foo.leak.com.ar, http://foo.leak.com.ar/parcial-correo.pcap.gz, correo electrónico, contiene entre sus paquetes un correo electrónico, contiene entre sus paquetes un correo electronico, siendo transportado por alguno de los protocolos vistos en clase, protocolos vistos en clase, El correo contiene una imagen, Muestra visualmente la imagen, Muestre visualmente la imagen, muestre en Wireshark como encontrar el correo, cómo lo procesa para obtener la imagen, como lo procesa para obtener la imagen, Finalmente muestre la imagen, Puede adjuntar la imagen

**Enunciado:** "El archivo disponible en http://foo.leak.com.ar/parcial-correo.pcap.gz contiene entre sus paquetes un correo electrónico siendo transportado por alguno de los protocolos vistos en clase. El correo contiene una imagen. Muestra visualmente la imagen. En el video respuesta que envíe: 1. Con el archivo pcap ya descargado muestre en Wireshark como encontrar el correo 2. Luego muestre cómo lo procesa para obtener la imagen 3. Finalmente muestre la imagen. Nota: Puede adjuntar la imagen en la respuesta que envíe por campus."

**Solución:** el correo viaja por **SMTP** (TCP 25), tcp.stream 23, con la imagen como adjunto MIME image/png en base64. Export Objects no arma el mensaje (el cliente mandó todo antes del saludo del servidor), así que se sigue el stream y se decodifica a mano.

> **1.** Bajá la captura, descomprimila y abrila en Wireshark:

wget http://foo.leak.com.ar/parcial-correo.pcap.gz

gunzip -k parcial-correo.pcap.gz

wireshark parcial-correo.pcap &

> **2.** Encontrá el correo con el filtro smtp: todos los paquetes son de la misma conexión, 192.168.122.1 → 192.168.122.235:25 (tcp.stream 23). Desde la terminal, tshark -r parcial-correo.pcap -Y smtp -T fields -e tcp.stream da 23 en todas las líneas.
>
> **3.** Guardá lo que mandó el cliente: clic derecho sobre un paquete SMTP \> Follow \> TCP Stream. Abajo, en el desplegable de la conversación, elegí el sentido del cliente ("192.168.122.1:33572 → 192.168.122.235:25"), "Show data as: Raw" (en pantalla se ve en hexadecimal, pero Save as guarda el texto original) y Save as... con el nombre correo.txt.
>
> **4.** Dejá en el archivo solo el base64 de la imagen. Abrí nano correo.txt y, con el cursor al principio del archivo:

- Alt+A (empieza a marcar), Ctrl+W, escribí iVBOR y Enter (así empieza el base64 de todo PNG; está abajo de Content-Type: image/png y de una línea en blanco), y Ctrl+K: borra todo lo de arriba.

- Ctrl+W, escribí -- y Enter (es la línea --00000000000046263c05a58f74b8-- que cierra la imagen), y Alt+T: borra desde ahí hasta el final.

- Ctrl+O, Enter y Ctrl+X para guardar y salir. Queda un bloque de líneas base64 que empieza con iVBORw0KGgo y termina con ErkJggg==.

> **5.** Decodificá el base64 y fijate que salga un PNG:

base64 -d correo.txt \> image.png

file image.png

**Demostrar:**

file image.png \# PNG image data, 1200 x 628, 8-bit/color RGB

xdg-open image.png \# (o: eog image.png / abrir con Firefox) para verla en la VM

Adjuntá la imagen en la respuesta de campus.

**Ojo:**

- File \> Export Objects \> IMF NO sirve en esta captura (el filtro imf da 0 frames): el cliente mandó EHLO/MAIL/RCPT/DATA y el mensaje todo junto (pipelining) antes de las respuestas, así que Wireshark marca todo como "DATA fragment". Hay que seguir el stream raw y decodificar.

- Si base64 -d dice invalid input, quedó algo que no es base64: volvé a nano correo.txt y mirá la primera y la última línea.

- Si file image.png dice que no es PNG, mirá qué dice y rehacé la extracción con el tipo correcto (el adjunto podría ser jpg: su base64 empieza con /9j/ en vez de iVBOR).

- Sin entorno gráfico (solo tshark) no hay forma simple; este comando hace los pasos 3 a 5 de una (sigue el stream en raw, pasa de hex a texto, aísla la parte image/png y la decodifica): tshark -r parcial-correo.pcap -q -z follow,tcp,raw,23 \| grep -E '^\[0-9a-f\]+\$' \| perl -pe 'chomp; \$\_=pack("H\*",\$\_)' \| tr -d '\r' \| sed -n '/^Content-Type: image\\/,/^--/p' \| sed '1,/^\$/d' \| grep -v '^--' \| base64 -d \> image.png.

*Probado en Ubuntu 24.04 (lab): el comando del Ojo reconstruye image.png = PNG 1200x628, 437326 bytes (sha256 8c0072cc...). El recorte con nano de los pasos 4 y 5 se probó en nano 7.2 sobre lo que manda el cliente en esa conexión y da el mismo image.png (mismo sha256). Sin probar: los clics de Wireshark y el xdg-open (el lab no tiene entorno gráfico).*

### Captura parcial-dns-y-otros: IP de ejercicio.leak.com.ar y cuerpo del mail a jcodagno

**Tomado en:** Recuperatorio 2020-1C · Ejercicio II.

> **Idea:** La idea es bajar parcial-dns-y-otros.pcap.gz y responder dos cosas: la IP del registro A de ejercicio.leak.com.ar, que sale del campo dns.a de la respuesta, y el cuerpo del mail a jcodagno@itba.edu.ar. El truco es mirar la sección Answers y no la query, y decodificar el cuerpo text/plain que viene en base64.

**Buscar:** parcial-dns-y-otros.pcap.gz, ejercicio.leak.com.ar, jcodagno@itba.edu.ar, DNS, registro A, cuerpo del mensaje, SMTP, RCPT TO, tcp.stream, base64, tshark, foo.leak.com.ar, http://foo.leak.com.ar/parcial-dns-y-otros.pcap.gz, archivo PCAP producido con Wireshark, pedidos y respuestas DNS, tramas de otros procolos, tramas de otros protocolos, En base a la información contenida en el archivo, En base a la informacion contenida en el archivo, cuál es la dirección IP del registro ejercicio.leak.com.ar, cual es la direccion IP del registro ejercicio.leak.com.ar, correo electrónico destinado a, correo electronico destinado a, qué texto lee en su pantalla, que texto lee en su pantalla, Concejo, Consejo

**Enunciado:** "En http://foo.leak.com.ar/parcial-dns-y-otros.pcap.gz se encuentra un archivo PCAP producido con Wireshark que contiene pedidos y respuestas DNS, y tramas de otros procolos. En base a la información contenida en el archivo responda las siguientes preguntas: A. ¿cuál es la dirección IP del registro ejercicio.leak.com.ar? (1 punto) B. Un usuario que recibe el correo electrónico destinado a jcodagno@itba.edu.ar qué texto lee en su pantalla como cuerpo del mensaje (1 punto) Concejo. Escriba como comentario de la entrega de campus las soluciones para una primera corrección rápida. (2 puntos)" ("procolos" y "Concejo" están así en el original.)

**Solución:**

wget http://foo.leak.com.ar/parcial-dns-y-otros.pcap.gz

gunzip -k parcial-dns-y-otros.pcap.gz

A. La IP del registro A:

tshark -r parcial-dns-y-otros.pcap -Y 'dns.qry.name == "ejercicio.leak.com.ar" && dns.flags.response == 1' -T fields -e dns.a

\# en las capturas equivalentes el registro A da 4.3.2.1 (TTL 300)

B. El cuerpo del mail (SMTP, parte text/plain en base64). Primero buscá en qué conexión viaja: corré esto, buscá la línea cuya Info tiene RCPT TO con jcodagno@itba.edu.ar y anotá el número de la segunda columna (tcp.stream). Si sale en dos conexiones, cualquiera sirve; si no sale en ninguna, mirá el Ojo.

tshark -r parcial-dns-y-otros.pcap -Y 'smtp \|\| imf' -T fields -E separator='\|' -e frame.number -e tcp.stream -e \_ws.col.Info

\# en botica.pcap, las líneas que sirven (stream 26 o 27):

\# 1873\|26\|C: RCPT TO: \<jcodagno@itba.edu.ar\>

\# 1932\|27\|C: MAIL FROM:\<juan@leak.com.ar\> SIZE=837 \| RCPT TO:\<jcodagno@itba.edu.ar\> \| DATA

Seguí esa conexión, cambiando NUM_DE_STREAM por el número que anotaste, y copiá la línea base64 de la parte Content-Type: text/plain: es la que está después de Content-Transfer-Encoding: base64 y una línea en blanco (en botica.pcap es w6HDqcOtw7PDug0K; la que está más abajo, en la parte text/html, es otra):

tshark -r parcial-dns-y-otros.pcap -q -z follow,tcp,ascii,NUM_DE_STREAM

Decodificala, pegándola en lugar de BASE64_DEL_CUERPO:

echo 'BASE64_DEL_CUERPO' \| base64 -d

\# en la captura hermana (botica.pcap) este cuerpo decodifica a: áéíóú

GUI: dns.qry.name == "ejercicio.leak.com.ar" para A; smtp + Follow TCP Stream para B.

**Demostrar:** la ANSWER del DNS (4.3.2.1) y el texto decodificado (áéíóú).

**Ojo:**

- A pide la IP, no el nombre: mirá la sección Answers (dns.a), no la query.

- B: el cuerpo viene en base64; copiás la línea bajo Content-Type: text/plain y la decodificás con base64 -d.

- B: si el RCPT TO a jcodagno sale en dos conexiones, usá cualquiera de las dos: en la captura hermana (botica.pcap) el mismo mail pasa por dos conexiones SMTP, del cliente al servidor de correo local y de ahí a Google, y las dos llevan el mismo cuerpo. Una conexión cuyo RCPT TO es otro destinatario no es la que buscás.

- B: si ninguna línea muestra RCPT TO con jcodagno (pasa si el cliente mandó todo junto antes de que conteste el servidor y Wireshark lo marca como DATA fragment, como el mail a juan@localhost de parcial-correo.pcap; en Info no se ve el destinatario), buscá la conexión por la dirección: tshark -r parcial-dns-y-otros.pcap -Y 'frame contains "jcodagno@itba.edu.ar"' -T fields -e frame.number -e tcp.stream y anotá el número de la segunda columna (en botica.pcap da 26 y 27).

- Las capturas que nombran los comentarios y la nota final son de otros ejercicios: botica.pcap es la del Parcial 2021-1C · Ejercicio 5 (su inciso E es esta misma pregunta B, resuelta paso a paso sobre esa captura) y parcial-correo.pcap es la del Parcial 2020-1C · Ejercicio VI. Los dos están en Resueltos — Capturas (Wireshark), 'Responder preguntas sobre una captura'.

*Sin probar: no tengo el archivo parcial-dns-y-otros.pcap. Las MISMAS respuestas aparecen y SÍ fueron verificadas en botica.pcap / parcial-correo.pcap (ejercicio.leak.com.ar → 4.3.2.1; cuerpo a jcodagno → áéíóú). Los comandos de B se probaron con tshark 4.2 sobre botica.pcap: el RCPT TO a jcodagno sale en los streams 26 y 27 (también con frame contains), y seguir cualquiera de los dos da w6HDqcOtw7PDug0K.*

### Preguntas de Wireshark sobre capture.pcap: IP de cliente y servidor, netstat -tn, recursos HTTP, servidor, conexiones persistentes, quién cierra

**Tomado en:** Parcial 2012-1C · Ejercicio 3.

> **Idea:** La idea es responder, con Wireshark o tshark sobre capture.pcap ya desencriptado, quién es cliente y servidor, qué mostraría netstat -tn, qué recursos HTTP viajan, qué aplicación hace de servidor, si hay conexiones persistentes y quién cierra. El truco es leer cada cosa de su marca: el SYN sin ACK, el header Server, varios pedidos en un mismo tcp.stream y el primer FIN.

**Buscar:** wireshark, tshark, capture.pcap, IP del cliente y del servidor, netstat -tn, qué entradas vería, que entradas veria, ESTABLISHED, TIME_WAIT, recursos HTTP, de qué tipo, de que tipo, Content-Type, nombre de la aplicación que hace de servidor HTTP, aplicacion, header Server, conexiones persistentes, keep-alive, Connection: close, HTTP/1.0, HTTP/1.1, quién está cerrando, quien esta cerrando, FIN, RST, tcp.stream, tcp.flags.fin, tcp.flags.syn, http.request, http.response, Follow TCP Stream, Export Objects, Conversations

**Enunciado:** "Habiendo desencriptado el archivo, abrirlo en el wireshark y responder lo siguiente: a. Identifique el IP del cliente y del servidor b. Si ejecutara netstat -tn tanto en el cliente como en el servidor, qué entradas vería para dicha comunicación mientras se produce? c. ¿Cuantos recursos HTTP y de qué tipo se transfieren? d. ¿Cual es el nombre de la aplicación que hace de servidor HTTP? e. ¿Se utilizan en la comunicación conexiones persistentes? f. ¿Quién está cerrando la/s conexión/es TCP?"

**Depende de:** Parcial 2012-1C · Ejercicio 2 (Resueltos — Mail, 'MX y SPF de un dominio'). De ahí sale capture.pcap ya desencriptado.

**Solución:**

> **1.** Abrí la captura: wireshark capture.pcap & (o menú File, Open). Los menús van en inglés, que es como suele estar instalado. Cada pregunta tiene abajo el comando tshark equivalente, que da la respuesta en texto para copiar.
>
> **2.** a) El cliente es el que manda el SYN (sin ACK) y el GET; el servidor responde desde el puerto 80. En Wireshark: filtro tcp.flags.syn==1 && tcp.flags.ack==0 (Source es el cliente, Destination el servidor), o menú Statistics, Conversations, solapa TCP.

tshark -r capture.pcap -Y 'tcp.flags.syn==1 && tcp.flags.ack==0' -T fields -e tcp.stream -e ip.src -e tcp.srcport -e ip.dst -e tcp.dstport

tshark -r capture.pcap -q -z conv,tcp

0 10.1.34.10 33012 10.1.34.180 80

1 10.1.34.10 33028 10.1.34.180 80

Columnas: número de conexión, IP cliente, puerto cliente, IP servidor, puerto servidor. Hay una línea por conexión TCP.

> **3.** b) Una entrada por cada conexión TCP abierta, en estado ESTABLISHED, con la dirección local primero. Armalas con los datos del punto a):

En el cliente:

Proto Recv-Q Send-Q Local Address Foreign Address State

tcp 0 0 10.1.34.10:33012 10.1.34.180:80 ESTABLISHED

En el servidor:

Proto Recv-Q Send-Q Local Address Foreign Address State

tcp 0 0 10.1.34.180:80 10.1.34.10:33012 ESTABLISHED

En el ejemplo hay otra conexión (puerto 33028): mientras dura, da una línea igual con ese puerto. netstat -tn no muestra el socket en escucha del servidor (para eso es netstat -tan, que agrega 0.0.0.0:80 ... LISTEN). Cuando la conexión termina, el lado que cerró primero queda unos 60 segundos en TIME_WAIT. Equivalente moderno: ss -tn.

> **4.** c) Contá los pedidos y mirá el Content-Type de cada respuesta. En Wireshark: filtro http.request para contarlos y menú File, Export Objects, HTTP para ver la lista con el tipo (Content Type) y el tamaño de cada uno.

tshark -r capture.pcap -Y http.request -T fields -e frame.number -e tcp.stream -e http.request.method -e http.host -e http.request.uri -e http.request.version

tshark -r capture.pcap -Y http.response -T fields -e frame.number -e tcp.stream -e http.response.code -e http.content_type -e http.content_length

\# guardar los recursos en una carpeta para abrirlos

mkdir objetos

tshark -r capture.pcap -q --export-objects http,objetos

ls -l objetos

4 0 GET www.parcialpdc.com / HTTP/1.1

8 0 GET www.parcialpdc.com /style.css HTTP/1.1

10 0 GET www.parcialpdc.com /logo.png HTTP/1.1

19 1 GET www.parcialpdc.com /style.css HTTP/1.0

6 0 200 text/html 121

9 0 200 text/css 22

11 0 200 image/png 3000

21 1 200 text/css 22

Columnas: número de paquete, número de conexión, y después método, Host, recurso y versión (pedidos) o código, tipo y tamaño (respuestas). Respuesta con ese ejemplo: 4 transferencias de 3 recursos distintos, un HTML, un CSS (pedido dos veces) y una imagen PNG.

> **5.** d) Es el header Server de las respuestas. En Wireshark: filtro http.response, elegí un paquete y abrí Hypertext Transfer Protocol.

tshark -r capture.pcap -Y http.response -T fields -e http.server

nginx/1.24.0 (Ubuntu)

nginx/1.24.0 (Ubuntu)

nginx/1.24.0 (Ubuntu)

nginx/1.24.0 (Ubuntu)

Sale una línea por respuesta; en el ejemplo, las 4 dicen lo mismo: el servidor es nginx 1.24.0.

> **6.** e) Hay conexiones persistentes si por una misma conexión TCP (mismo tcp.stream) pasa más de un pedido, o si la respuesta dice Connection: keep-alive y la conexión sigue abierta. No las hay si cada pedido va en una conexión distinta y la respuesta dice Connection: close. En Wireshark: clic derecho sobre un paquete, Follow, TCP Stream, y mirá si aparecen varios GET en el mismo flujo.

tshark -r capture.pcap -Y http.request -T fields -e tcp.stream

tshark -r capture.pcap -Y http -T fields -e frame.number -e tcp.stream -e http.request.version -e http.response.version -e http.connection

0

0

0

1

El primer comando da el número de conexión de cada pedido, igual que la segunda columna de los pedidos en c): contá cuántas veces aparece cada número. En el ejemplo la conexión 0 aparece 3 veces, o sea que llevó 3 pedidos (persistente), y la 1 una sola (HTTP/1.0, respuesta con Connection: close). El segundo comando muestra la versión y el header Connection de cada pedido y respuesta.

> **7.** f) Cierra el que manda el primer FIN de cada conexión (o un RST). En Wireshark: filtro tcp.flags.fin==1 \|\| tcp.flags.reset==1 y mirá el Source del primero de cada flujo.

tshark -r capture.pcap -Y 'tcp.flags.fin==1 \|\| tcp.flags.reset==1' -T fields -e frame.number -e tcp.stream -e ip.src -e tcp.srcport -e ip.dst -e tcp.dstport -e tcp.flags.str

13 0 10.1.34.10 33012 10.1.34.180 80 ·······A···F

14 0 10.1.34.180 80 10.1.34.10 33012 ·······A···F

23 1 10.1.34.180 80 10.1.34.10 33028 ·······A···F

24 1 10.1.34.10 33028 10.1.34.180 80 ·······A···F

En el ejemplo: la conexión 0 la cierra el cliente (primer FIN desde 10.1.34.10) y la 1 la cierra el servidor (primer FIN desde el puerto 80).

**Demostrar:** entregá cada respuesta con el número de paquete que la respalda (primera columna de las salidas) y, en b), las líneas de netstat escritas con las IP y puertos reales de la captura.

**Ojo:**

- En b) el puerto del cliente es el efímero que se ve en la captura, no el 80. Si hubo varias conexiones a la vez, va una línea por cada una.

- En c) no cuentes paquetes: un recurso grande ocupa varios segmentos TCP. Contá pedidos (http.request) o respuestas.

- Regla general para f): con HTTP/1.0 o Connection: close cierra el servidor al terminar la respuesta; con keep-alive suele cerrar el cliente cuando no necesita más, o el servidor por timeout.

- Si tshark no muestra nada de HTTP porque el servidor usa otro puerto: agregá -d tcp.port==PUERTO,http.

*Probado en Ubuntu 24.04 (lab). Sin probar: los clics de Wireshark (el lab no tiene entorno gráfico; van de memoria) y la captura del parcial (no está en las fuentes; las salidas de ejemplo son de una captura propia con una conexión persistente y otra HTTP/1.0).*

# Resueltos — En papel: subnetting, ruteo y teoría

## Subnetting y tablas de ruteo

Los tres se resuelven en la hoja: te dan un bloque de direcciones o un diagrama con IPs y hay que armar subredes, tablas de ruteo o la configuración de un router. De un parcial a otro cambia el bloque, la cantidad de hosts por subred, si hay direcciones obligadas y si además piden interfaces, rutas o DHCP.

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| Subnetting 200.16.0.0 a 200.16.7.255 (110/118/126 PCs), interfaces de R1, R2 y R3 y rutas estáticas | Subredes A, B y C (110, 118 y 126 PCs) dentro de 200.16.0.0 a 200.16.7.255, la IP de cada interfaz de R1, R2 y R3 y la tabla de ruteo estática de cada router | Tres routers en triángulo con enlaces /30. Hay que dejar libre la subred más grande posible (200.16.4.0/22). C va en /24 porque en un /25 no queda IP para el router |
| Subnetting 188.44.55.0/24 (60/12/90 hosts con direcciones obligadas) | Tres subredes de 188.44.55.0/24 con 60, 12 y 90 hosts: diagrama, máscara, dirección base, broadcast, máximo de hosts y direcciones sin usar | Cada subred tiene una dirección obligada (188.44.55.40, 188.44.55.70 y 188.44.55.200). Un solo router con 3 interfaces, sin tablas de ruteo. Quedan 48 direcciones libres |
| DHCP (ninguna o discover/offer/request/ack), tabla de ruteo del Host A y config del router | Secuencia DHCP entre el Host A y el Host C para la IP 192.168.1.13 (multiple choice), tabla de ruteo completa del Host A (eth1 192.168.1.13, eth0 192.168.2.1) y configuración del router (192.168.1.1 y 192.168.2.10) para que el Host A salga a internet | No hay subnetting: las IPs vienen dadas y el enunciado no da máscaras (se asume /24). La respuesta del multiple choice es "ninguna". El router lleva ip_forward y MASQUERADE |

### Subnetting 200.16.0.0 a 200.16.7.255 (110/118/126 PCs), interfaces de R1, R2 y R3 y rutas estáticas

**Tomado en:** Parcial 2016 (segundo parcial) · Ejercicio 2.

> **Idea:** La idea es partir 200.16.0.0/21 en las subredes A, B y C más un /30 por enlace entre routers, asignar las IP de las interfaces y armar las rutas estáticas con el router vecino como gateway, dejando entero libre el bloque 200.16.4.0/22. El truco es que C va en /24: en un /25 las 126 PCs no dejan IP para R3.

**Buscar:** subnetting, 200.16.0.0, 200.16.7.255, 200.16.0.0/21, 110, 118, 126, PCs, departamento, subred A, subred B, subred C, R1, R2, R3, interfaz WAN, clientes hogareños, clientes hogarenos, minimizar la cantidad de direcciones, subred lo más grande posible, subred lo mas grande posible, completar la siguiente tabla, Dirección de red, Direccion de red, Máscara, Mascara, Rango hosts, Broadcast, 255.255.255.128, 255.255.255.0, /25, /24, /30, dirección IP asociada a cada interfaz, direccion IP asociada a cada interfaz, por qué se decidió, por que se decidio, rutas estáticas, rutas estaticas, tabla de ruteo, Dirección IP destino, Direccion IP destino, Gateway, Interfaz, VLSM

**Enunciado:** "El esquema responde a una organización que recibe el rango de direcciones IP comprendidos entre 200.16.0.0 y 200.16.7.255 inclusive. Cada subred corresponde a un departamento en el cual se van a instalar PCs donde cada una de ellas deberá tener una única dirección IP dentro del rango 200.16.0.0 y 200.16.7.255. El objetivo es crear subredes en forma eficiente, tratando además de minimizar la cantidad de direcciones a utilizar para poder conformar otra subred lo más grande posible (por ejemplo para venderle acceso a Internet a clientes hogareños). 1) En base a los datos suministrados y la cantidad de PCs necesarias en cada subred completar la siguiente tabla: Subred, PCs, Dirección de red, Máscara, Rango hosts, Broadcast (A 110, B 118, C 126). 2) Indicar para cada router la dirección IP asociada a cada interfaz (excepto para la interfaz WAN de R1). ¿Por qué se decidió por asignar esas direcciones? 3) Cada router será configurado con rutas estáticas. Indicar para cada router cuál debería ser su tabla de ruteo, indicando para cada entrada: Dirección IP destino, Gateway, Máscara, Interfaz."

**Solución:**

> **1.** Topología del dibujo: tres routers en triángulo, R2 con dos LAN, R3 con una y R1 con la salida a Internet.

R1 eth0 --- R2 eth3 R2 eth0 --- Subred A R3 eth0 --- Subred C

R1 eth1 --- R3 eth2 R2 eth1 --- Subred B R3 eth1 --- R2 eth2

R1 WAN --- Internet R2 eth2 --- R3 eth1 R3 eth2 --- R1 eth1

R2 eth3 --- R1 eth0

> **2.** El rango 200.16.0.0 a 200.16.7.255 son 8 x 256 = 2048 direcciones: 200.16.0.0/21 (máscara 255.255.248.0).
>
> **3.** Tamaños. Cada subred necesita PCs + 1 (la interfaz del router) + 2 (red y broadcast):

A: 110 + 1 + 2 = 113 -\> bloque de 128 -\> /25 mascara 255.255.255.128 126 hosts utiles

B: 118 + 1 + 2 = 121 -\> bloque de 128 -\> /25 mascara 255.255.255.128 126 hosts utiles

C: 126 + 1 + 2 = 129 -\> bloque de 256 -\> /24 mascara 255.255.255.0 254 hosts utiles

> **4.** La trampa de C: un /25 tiene 126 direcciones útiles. Las 126 PCs lo llenan y no queda dirección para eth0 de R3, que es el gateway de esas PCs (y el punto 2 pide justamente esa IP). Por eso C va en /24. Escribilo en la hoja.
>
> **5.** Respuesta 1). Se asigna de mayor a menor desde 200.16.0.0 (C, B, A) y después un /30 por cada enlace entre routers:

| **Subred** | **PCs** | **Dirección de red** | **Máscara** | **Rango hosts** | **Broadcast** |
|----|----|----|----|----|----|
| A | 110 | 200.16.1.128 | 255.255.255.128 | 200.16.1.129 a 200.16.1.254 | 200.16.1.255 |
| B | 118 | 200.16.1.0 | 255.255.255.128 | 200.16.1.1 a 200.16.1.126 | 200.16.1.127 |
| C | 126 | 200.16.0.0 | 255.255.255.0 | 200.16.0.1 a 200.16.0.254 | 200.16.0.255 |
| Enlace R1-R2 | 2 | 200.16.2.0 | 255.255.255.252 | 200.16.2.1 a 200.16.2.2 | 200.16.2.3 |
| Enlace R1-R3 | 2 | 200.16.2.4 | 255.255.255.252 | 200.16.2.5 a 200.16.2.6 | 200.16.2.7 |
| Enlace R2-R3 | 2 | 200.16.2.8 | 255.255.255.252 | 200.16.2.9 a 200.16.2.10 | 200.16.2.11 |

> **6.** Lo que queda libre: se usan 256 + 128 + 128 + 12 = 524 direcciones, todas dentro de 200.16.0.0/22. Queda entera la otra mitad, 200.16.4.0/22: máscara 255.255.252.0, hosts 200.16.4.1 a 200.16.7.254 (1022), broadcast 200.16.7.255. Es la subred más grande posible: algo del /21 hay que usar, así que nunca puede quedar libre más de una mitad. Además sobran 200.16.3.0/24, 200.16.2.128/25, 200.16.2.64/26, 200.16.2.32/27, 200.16.2.16/28 y 200.16.2.12/30.
>
> **7.** Respuesta 2), interfaces:

| **Router** | **Interfaz** | **Conecta con** | **Dirección IP** | **Máscara**     |
|------------|--------------|-----------------|------------------|-----------------|
| R1         | eth0         | R2 (eth3)       | 200.16.2.1       | 255.255.255.252 |
| R1         | eth1         | R3 (eth2)       | 200.16.2.5       | 255.255.255.252 |
| R2         | eth0         | Subred A        | 200.16.1.129     | 255.255.255.128 |
| R2         | eth1         | Subred B        | 200.16.1.1       | 255.255.255.128 |
| R2         | eth2         | R3 (eth1)       | 200.16.2.9       | 255.255.255.252 |
| R2         | eth3         | R1 (eth0)       | 200.16.2.2       | 255.255.255.252 |
| R3         | eth0         | Subred C        | 200.16.0.1       | 255.255.255.0   |
| R3         | eth1         | R2 (eth2)       | 200.16.2.10      | 255.255.255.252 |
| R3         | eth2         | R1 (eth1)       | 200.16.2.6       | 255.255.255.252 |

> **8.** Por qué esas direcciones. En cada LAN el router toma la primera dirección útil: es el gateway por defecto de las PCs, es fácil de recordar y deja el resto del rango contiguo para las PCs. Cada enlace entre routers es punto a punto y solo necesita 2 direcciones, por eso un /30, que es lo que menos desperdicia. Los /30 se sacan del primer bloque libre a continuación de las LAN para no fragmentar y dejar intacto 200.16.4.0/22.
>
> **9.** Respuesta 3), tablas de ruteo. "Directa" es una red conectada a esa interfaz (gateway 0.0.0.0). El gateway de una red remota es siempre la IP del router vecino en el enlace compartido.

R1:

| **Dirección IP destino** | **Máscara** | **Gateway** | **Interfaz** |
|----|----|----|----|
| 200.16.2.0 | 255.255.255.252 | directa | eth0 |
| 200.16.2.4 | 255.255.255.252 | directa | eth1 |
| 200.16.1.128 (A) | 255.255.255.128 | 200.16.2.2 | eth0 |
| 200.16.1.0 (B) | 255.255.255.128 | 200.16.2.2 | eth0 |
| 200.16.0.0 (C) | 255.255.255.0 | 200.16.2.6 | eth1 |
| 200.16.2.8 (enlace R2-R3) | 255.255.255.252 | 200.16.2.2 | eth0 |
| 0.0.0.0 (por defecto) | 0.0.0.0 | IP del router del proveedor | WAN |

R2:

| **Dirección IP destino** | **Máscara**     | **Gateway** | **Interfaz** |
|--------------------------|-----------------|-------------|--------------|
| 200.16.1.128 (A)         | 255.255.255.128 | directa     | eth0         |
| 200.16.1.0 (B)           | 255.255.255.128 | directa     | eth1         |
| 200.16.2.8               | 255.255.255.252 | directa     | eth2         |
| 200.16.2.0               | 255.255.255.252 | directa     | eth3         |
| 200.16.0.0 (C)           | 255.255.255.0   | 200.16.2.10 | eth2         |
| 0.0.0.0 (por defecto)    | 0.0.0.0         | 200.16.2.1  | eth3         |

R3:

| **Dirección IP destino** | **Máscara**     | **Gateway** | **Interfaz** |
|--------------------------|-----------------|-------------|--------------|
| 200.16.0.0 (C)           | 255.255.255.0   | directa     | eth0         |
| 200.16.2.8               | 255.255.255.252 | directa     | eth1         |
| 200.16.2.4               | 255.255.255.252 | directa     | eth2         |
| 200.16.1.128 (A)         | 255.255.255.128 | 200.16.2.9  | eth1         |
| 200.16.1.0 (B)           | 255.255.255.128 | 200.16.2.9  | eth1         |
| 0.0.0.0 (por defecto)    | 0.0.0.0         | 200.16.2.5  | eth2         |

Criterio: R2 y R3 mandan todo lo desconocido a R1 (ruta por defecto), que es el que sale a Internet. Entre A/B y C se va por el enlace directo R2-R3. R1 necesita una fila por cada red interna que no tiene conectada, porque su ruta por defecto apunta al proveedor.

> **10.** Si lo piden con comandos de Linux (IP_ISP es la IP del router del proveedor e IFACE_WAN la interfaz WAN de R1: los dos se reemplazan a mano):

\# R1 (eth0 hacia R2, eth1 hacia R3)

sudo ip addr add 200.16.2.1/30 dev eth0

sudo ip addr add 200.16.2.5/30 dev eth1

sudo ip route add 200.16.1.128/25 via 200.16.2.2 dev eth0

sudo ip route add 200.16.1.0/25 via 200.16.2.2 dev eth0

sudo ip route add 200.16.0.0/24 via 200.16.2.6 dev eth1

sudo ip route add 200.16.2.8/30 via 200.16.2.2 dev eth0

sudo ip route add default via IP_ISP dev IFACE_WAN

sudo sysctl -w net.ipv4.ip_forward=1

\# forma vieja de una ruta:

\# sudo route add -net 200.16.0.0 netmask 255.255.255.0 gw 200.16.2.6 dev eth1

\# R2 (eth0 subred A, eth1 subred B, eth2 hacia R3, eth3 hacia R1)

sudo ip addr add 200.16.1.129/25 dev eth0

sudo ip addr add 200.16.1.1/25 dev eth1

sudo ip addr add 200.16.2.9/30 dev eth2

sudo ip addr add 200.16.2.2/30 dev eth3

sudo ip route add 200.16.0.0/24 via 200.16.2.10 dev eth2

sudo ip route add default via 200.16.2.1 dev eth3

sudo sysctl -w net.ipv4.ip_forward=1

\# forma vieja de la ruta por defecto:

\# sudo route add default gw 200.16.2.1 eth3

\# R3 (eth0 subred C, eth1 hacia R2, eth2 hacia R1)

sudo ip addr add 200.16.0.1/24 dev eth0

sudo ip addr add 200.16.2.10/30 dev eth1

sudo ip addr add 200.16.2.6/30 dev eth2

sudo ip route add 200.16.1.128/25 via 200.16.2.9 dev eth1

sudo ip route add 200.16.1.0/25 via 200.16.2.9 dev eth1

sudo ip route add default via 200.16.2.5 dev eth2

sudo sysctl -w net.ipv4.ip_forward=1

\# forma vieja de una ruta:

\# sudo route add -net 200.16.1.0 netmask 255.255.255.128 gw 200.16.2.9

**Demostrar:** las tablas de los puntos 5, 7 y 9 y la justificación del punto 8. Las cuentas se revisan a mano, subred por subred: la red es la primera dirección del bloque, el broadcast la última, los hosts van de la red + 1 al broadcast − 1 y los hosts útiles son el tamaño del bloque menos 2. Tiene que dar esto (red, máscara, primer host, último host, broadcast, hosts útiles):

200.16.1.128/25 255.255.255.128 200.16.1.129 200.16.1.254 200.16.1.255 126

200.16.1.0/25 255.255.255.128 200.16.1.1 200.16.1.126 200.16.1.127 126

200.16.0.0/24 255.255.255.0 200.16.0.1 200.16.0.254 200.16.0.255 254

200.16.2.0/30 255.255.255.252 200.16.2.1 200.16.2.2 200.16.2.3 2

200.16.2.4/30 255.255.255.252 200.16.2.5 200.16.2.6 200.16.2.7 2

200.16.2.8/30 255.255.255.252 200.16.2.9 200.16.2.10 200.16.2.11 2

200.16.4.0/22 255.255.252.0 200.16.4.1 200.16.7.254 200.16.7.255 1022

En la prueba, con los tres routers cargados con el paso 10, una PC de cada subred llega a las otras dos, a todas las interfaces de los routers y a Internet. El camino de una PC de A (200.16.1.130) a una de C (200.16.0.254):

traceroute to 200.16.0.254 (200.16.0.254), 30 hops max, 60 byte packets

1 200.16.1.129

2 200.16.2.10

3 200.16.0.254

**Ojo:**

- C en /25 está mal si contás el router: 126 PCs + la interfaz de R3 son 127 hosts y un /25 tiene 126.

- Si el corrector no cuenta la interfaz del router (tres /25, como en la resolución de alumno que circula): C = 200.16.0.0/25 (hosts 200.16.0.1 a 200.16.0.126, broadcast 200.16.0.127), B = 200.16.0.128/25 (hosts 200.16.0.129 a 200.16.0.254, broadcast 200.16.0.255), A = 200.16.1.0/25 (hosts 200.16.1.1 a 200.16.1.126, broadcast 200.16.1.127), enlaces 200.16.1.128/30, 200.16.1.132/30 y 200.16.1.136/30. Las tablas de ruteo tienen las mismas filas con esos valores. Aclarar que así C admite 125 PCs más el router.

- Lo que sobra no es un bloque único: una subred tiene que ser un bloque alineado a potencia de 2. La subred libre más grande es 200.16.4.0/22 (1022 hosts), con cualquiera de las dos asignaciones.

- "Minimizar direcciones" es usar para cada subred el bloque más chico que alcanza y ponerlos contiguos desde el principio, para no romper la mitad alta del /21.

- En la columna Gateway no va una IP propia del router ni una IP de la red destino: va la IP del router vecino, la del otro extremo del enlace.

- Si hay ida pero no vuelta, falta la ruta de regreso en el otro router. Revisá cada par de subredes en los dos sentidos.

- El broadcast es la última dirección de cada bloque y la dirección de red la primera: ninguna de las dos se asigna a una PC ni a un router.

- Ubuntu no trae un comando simple para comprobar una subred (ipcalc no viene instalado). Si querés comprobar una en la terminal, sirve esta línea, cambiando la red entre comillas; muestra red, máscara, primer host, último host, broadcast y hosts útiles: python3 -c "import ipaddress as i; n=i.ip_network('200.16.1.128/25'); print(n, n.netmask, n\[1\], n\[-2\], n.broadcast_address, n.num_addresses-2)"

*Probado en Ubuntu 24.04 (lab): cuentas con python3 ipaddress y los tres routers armados con namespaces (pings entre las tres subredes, a todas las interfaces y a una IP externa detrás de R1).*

### Subnetting 188.44.55.0/24 (60/12/90 hosts con direcciones obligadas)

**Tomado en:** Parcial 2015 (segundo parcial) · Ejercicio 1.

> **Idea:** La idea es partir 188.44.55.0/24 con VLSM en tres subredes de 60, 12 y 90 hosts y dar máscara, red, broadcast y máximo de hosts de cada una. El truco es ubicar primero la más grande y que cada dirección obligada caiga adentro: C en .128/25, A en .0/26 y B en .64/28, y quedan 48 direcciones sin usar.

**Contexto del parcial:** del segundo parcial de 2015 hay dos tandas de tres ejercicios, numeradas igual. Esta es la tanda en papel; las marcadas "(otra tanda)" son las de máquina y están en Resueltos — Red, "Llegar a una red detrás de un gateway y descubrir sus servicios" (Ejercicio 1 y Ejercicio 3) y en Resueltos — Firewall (iptables), "Filtrar, redirigir y restringir con iptables" (Ejercicio 2).

**Buscar:** subnetting, 188.44.55.0/24, router con 3 interfaces, 3 subredes, lo más óptimo posible, lo mas optimo posible, 60 hosts, 12 hosts, 90 hosts, 188.44.55.40, 188.44.55.70, 188.44.55.200, tiene que pertenecer a la misma, mascara, máscara de red, /26, /28, /25, dirección base, direccion base, red, broadcast, cantidad máxima de hosts, cantidad maxima de hosts, VLSM, direcciones quedan sin usar, direcciones sin usar, diagrama router, mascara de red

**Enunciado:** No hay hoja en las fuentes; es el relato de un alumno: "Te dan un router con 3 interfaces, y la red 188.44.55.0/24, te piden que armes 3 subredes lo más óptimo posible con las siguientes condiciones: La red A tiene 60 hosts y la dirección 188.44.55.40 tiene que pertenecer a la misma. La red B tiene 12 hosts y la dirección 70 tiene que pertenecer a la misma. La red C tiene 90 hosts y la dirección 188.44.55.200 tiene que pertenecer a la misma. a) hacer un diagrama que muestre el router y las redes b) escribir la máscara de red de cada subred d) escribir la dirección base y broadcast de cada red c) escribir la cantidad máxima de hosts que puede haber en las redes e) decir cuántas direcciones quedan sin usar de la red original".

**Solución (VLSM, de la más grande a la más chica):**

- C = 90 hosts → /25 (128 direcciones, 126 útiles). .200 cae en 188.44.55.128/25.

- A = 60 hosts → /26 (64, 62 útiles). .40 cae en 188.44.55.0/26.

- B = 12 hosts → /28 (16, 14 útiles). .70 cae en 188.44.55.64/28 (el hueco entre A y C).

| **Subred** | **Hosts** | **Red** | **Máscara** | **Rango de hosts** | **Broadcast** | **Máx hosts** |
|----|----|----|----|----|----|----|
| A | 60 (tiene .40) | 188.44.55.0 | 255.255.255.192 (/26) | 188.44.55.1 – 188.44.55.62 | 188.44.55.63 | 62 |
| B | 12 (tiene .70) | 188.44.55.64 | 255.255.255.240 (/28) | 188.44.55.65 – 188.44.55.78 | 188.44.55.79 | 14 |
| C | 90 (tiene .200) | 188.44.55.128 | 255.255.255.128 (/25) | 188.44.55.129 – 188.44.55.254 | 188.44.55.255 | 126 |

\(e\) Direcciones sin usar del /24: 256 − 64 (A) − 16 (B) − 128 (C) = 48 (el bloque 188.44.55.80–188.44.55.127).

**Demostrar:** la tabla con máscara, red, rango, broadcast y máx de hosts de cada subred; que cada dirección obligada cae en su subred; y las 48 libres. Se comprueba a mano: una dirección pertenece a la subred si está entre la red y el broadcast.

A 188.44.55.0/26 .40 está entre .0 y .63 -\> sí

B 188.44.55.64/28 .70 está entre .64 y .79 -\> sí

C 188.44.55.128/25 .200 está entre .128 y .255 -\> sí

libres: 256 - 64 - 16 - 128 = 48 (188.44.55.80 a 188.44.55.127)

**Ojo:**

- Ubicá primero la subred más grande (C, /25) y respetá que cada dirección obligada caiga adentro: .200→/25 alto, .40→/26 bajo, .70→/28 en el medio.

- 60 hosts entra justo en /26 (62 útiles), y con la interfaz del router (61) también. 90 hosts necesita /25 (126 útiles), no /26. 12 hosts entra en /28 (14 útiles).

- El broadcast es la última dirección de cada bloque (.63, .79, .255); la red es la primera (.0, .64, .128).

- "Lo más óptimo" = VLSM: cada subred con la máscara justa para sus hosts, no todas iguales.

- Ubuntu no trae un comando simple para comprobar una subred (ipcalc no viene instalado). Si querés comprobar una en la terminal, sirve esta línea, cambiando la red entre comillas; muestra red, máscara, primer host, último host, broadcast y hosts útiles (para B da 188.44.55.64/28 255.255.255.240 188.44.55.65 188.44.55.78 188.44.55.79 14): python3 -c "import ipaddress as i; n=i.ip_network('188.44.55.64/28'); print(n, n.netmask, n\[1\], n\[-2\], n.broadcast_address, n.num_addresses-2)"

*Probado en Ubuntu 24.04 (lab): cuentas verificadas con python3 ipaddress; cada dirección obligada cae en su subred y quedan 48 libres.*

### DHCP (ninguna o discover/offer/request/ack), tabla de ruteo del Host A y config del router

**Tomado en:** Parcial 2015 (segundo parcial) · Ejercicio 2.

> **Idea:** La idea es contestar el multiple choice de DHCP, escribir la tabla de ruteo del Host A (asumiendo /24: dos redes directas más la ruta por defecto a 192.168.1.1) y configurar el router con ip_forward y MASQUERADE. La trampa es que la respuesta es ninguna: 192.168.1.13 es del enlace con el router y el broadcast DHCP nunca llega al Host C.

**Contexto del parcial:** del segundo parcial de 2015 hay dos tandas de tres ejercicios, numeradas igual. Esta es la tanda en papel; las marcadas "(otra tanda)" son las de máquina y están en Resueltos — Red, "Llegar a una red detrás de un gateway y descubrir sus servicios" (Ejercicio 1 y Ejercicio 3) y en Resueltos — Firewall (iptables), "Filtrar, redirigir y restringir con iptables" (Ejercicio 2).

**Buscar:** DHCP, discover, offer, request, ack, DORA, ninguna, multiple choice, múltiple choice, secuencia de mensajes, servidor de dhcp, tabla de ruteo, tabla de routeo completa, Host A, Host B, Host C, Switch, dos interfaces, eth0, eth1, 192.168.1.13, 192.168.1.1, 192.168.2.1, 192.168.2.2, 192.168.2.3, 192.168.2.10, router, default gateway, acceso a internet, configuración que tiene que tener el router, configuracion que tiene que tener el router, ip route, route add, route -n, ip_forward, MASQUERADE, NAT, DHCP relay

**Enunciado:** No hay hoja en las fuentes; es el relato de un alumno: "Te dan un diagrama: El Host A, B, C y un router están conectados a un Switch. El Host A está conectado al mismo router por otra interfaz. El router tiene acceso a internet. a) Había un múltiple choice que preguntaba, cual es la secuencia de mensajes que se mandaban entre el Host A y el Host C (siendo que este último tiene un servidor de dhcp) para asignarle al Host a la ip 192.168.1.13? \[opciones: ninguna / discover, offer, request, ack\] b) Escribir la tabla de routeo completa del Host A c) Escribir la configuración que tiene que tener el router para que el Host A tenga acceso a internet". Datos del diagrama: "El Host A se conecta con el router por la interfaz eth1 con el ip 192.168.1.13, el router tiene ip 192.168.1.1 en esa interfaz. El Host A se conecta con la otra red con la interfaz eth0 con ip 192.168.2.1. El Host B tiene ip 192.168.2.2. El Host C tiene ip 192.168.2.3. El router tiene ip 192.168.2.10 en esa interfaz".

**Solución:**

\(a\) Respuesta: **ninguna**. La 192.168.1.13 es la IP de eth1, el enlace directo entre el Host A y el router. El DHCP Discover es un broadcast que no sale de ese enlace, así que nunca llega al Host C, que está en la red 192.168.2.0 del switch. Además un servidor DHCP ofrece direcciones de la red por donde le llega el pedido (192.168.2.x). La secuencia discover, offer, request, ack entre A y C solo valdría para la IP de eth0 (192.168.2.1), o para la 192.168.1.13 si el router hiciera de DHCP relay.

Dato aparte, la secuencia normal de DHCP cuando cliente y servidor están en la misma red (regla "DORA"):

cliente -- DHCPDISCOVER (broadcast) --\> servidor

cliente \<-- DHCPOFFER (ofrece una IP) -- servidor

cliente -- DHCPREQUEST (pide esa IP) --\> servidor

cliente \<-- DHCPACK (confirma) -- servidor

\(b\) Tabla de ruteo del Host A. El enunciado no da máscaras: asumí /24 y escribilo. Una fila por cada red conectada y la ruta por defecto hacia el router:

Destino Máscara Gateway Interfaz

192.168.1.0 255.255.255.0 0.0.0.0 eth1 (conectada directa)

192.168.2.0 255.255.255.0 0.0.0.0 eth0 (conectada directa)

0.0.0.0 0.0.0.0 192.168.1.1 eth1 (default -\> router)

En Linux se arma así:

\# IPs de las interfaces (forma moderna)

sudo ip addr add 192.168.1.13/24 dev eth1

sudo ip addr add 192.168.2.1/24 dev eth0

\# ruta por defecto hacia el router para salir a internet

sudo ip route add default via 192.168.1.1

\# equivalente viejo

\# sudo ifconfig eth1 192.168.1.13 netmask 255.255.255.0

\# sudo ifconfig eth0 192.168.2.1 netmask 255.255.255.0

\# sudo route add default gw 192.168.1.1

\(c\) El router necesita: una IP en cada red (192.168.1.1 y 192.168.2.10), reenvío de paquetes activado, ruta por defecto hacia el proveedor y NAT en la salida, porque las 192.168.x.x son privadas y no se rutean en Internet. IFACE_WAN es la interfaz del router que va a Internet e IP_ISP la IP del router del proveedor: los dos se reemplazan a mano. El dibujo no los da: en la hoja, escribí un nombre de ejemplo (eth2) y "IP del router del proveedor", y aclaralo. En una máquina, el nombre de la interfaz lo ves con ip -br a. La IP del proveedor, si la máquina ya sale a Internet, la ves con ip route, en la línea default via ...: en ese caso la ruta por defecto ya existe y el ip route add da RTNETLINK answers: File exists, así que salteá ese comando. Ojo: si dejás IFACE_WAN sin cambiar, el iptables no da error y la regla no hace nada; solo falla el ip route. Para ver qué regla quedó cargada, corré sudo iptables -t nat -S POSTROUTING.

\# en el router

sudo sysctl -w net.ipv4.ip_forward=1

sudo iptables -t nat -A POSTROUTING -o IFACE_WAN -j MASQUERADE

sudo ip route add default via IP_ISP dev IFACE_WAN

\# equivalente viejo

\# sudo sh -c 'echo 1 \> /proc/sys/net/ipv4/ip_forward'

\# sudo route add default gw IP_ISP IFACE_WAN

**Demostrar:** marcar ninguna en el multiple choice y justificarlo en una línea (la 192.168.1.13 es del enlace con el router; el Host C está en la otra red y el broadcast no le llega); la tabla del Host A con las dos redes directas y la ruta por defecto; y el router con ip_forward=1 y MASQUERADE hacia la WAN. En la prueba, con el Host C como servidor DHCP en la red del switch:

(Host A pide por eth1: nadie contesta y al Host C no le llega nada)

DHCPDISCOVER on eth1 to 255.255.255.255 port 67 interval 3

DHCPDISCOVER on eth1 to 255.255.255.255 port 67 interval 5

DHCPDISCOVER on eth1 to 255.255.255.255 port 67 interval 10

(Host A pide por eth0: secuencia completa)

DHCPDISCOVER on eth0 to 255.255.255.255 port 67 interval 3

DHCPOFFER of 192.168.2.148 from 192.168.2.3

DHCPREQUEST for 192.168.2.148 on eth0 to 255.255.255.255 port 67

DHCPACK of 192.168.2.148 from 192.168.2.3

Y la tabla del Host A, con ip route y con route -n:

default via 192.168.1.1 dev eth1

192.168.1.0/24 dev eth1 proto kernel scope link src 192.168.1.13

192.168.2.0/24 dev eth0 proto kernel scope link src 192.168.2.1

Destination Gateway Genmask Flags Metric Ref Use Iface

0.0.0.0 192.168.1.1 0.0.0.0 UG 0 0 0 eth1

192.168.1.0 0.0.0.0 255.255.255.0 U 0 0 0 eth1

192.168.2.0 0.0.0.0 255.255.255.0 U 0 0 0 eth0

**Ojo:**

- La trampa del inciso a) es la IP: 192.168.1.13 no es de la red donde está el servidor DHCP. Si la pregunta fuera por la IP de eth0, la respuesta sería discover, offer, request, ack.

- El Host A tiene dos interfaces: la tabla lleva una fila por cada red conectada más la ruta por defecto. Va una sola ruta por defecto; también es válida por 192.168.2.10 en eth0, que es el mismo router.

- Sin NAT en el router el paquete sale a Internet con origen 192.168.1.13 y la respuesta no vuelve.

- Los hosts B y C usan como gateway 192.168.2.10.

*Probado en Ubuntu 24.04 (lab), con namespaces: el Host A con eth1 al router y eth0 al switch, dnsmasq como servidor DHCP en el Host C, y el router con una WAN simulada (sin MASQUERADE el ping a afuera no vuelve; con MASQUERADE sí).*

## Preguntas teóricas

Se contestan por escrito: hay que explicar o justificar, y los comandos van solo como respaldo. De un parcial a otro cambia el tema (saltear un filtro, /etc/hosts, colisiones, verdadero o falso) y, en las dos del filtro .ar, qué puertos bloquea el firewall. Las tres que se contestan con un túnel SSH (Parcial 2016 (segundo parcial) · Ejercicio 1, Parcial 2007 · Ejercicio 2 y Parcial 2007 · Ejercicio 4) tienen ejercicios parecidos para hacer en la máquina en Resueltos — SSH, "Salir a internet o llegar a un servicio interno con túneles".

| **Ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| Cómo navegar sitios .ar desde una red que los filtra | Explicar cómo navegar con el browser sitios .ar desde una red que los filtra, con proxy interno obligatorio para http y el puerto 22 bloqueado | Solo cae el 22: túnel SSH con -D a una máquina externa que escucha en el 443, con el DNS resuelto afuera |
| CSMA/CD: detección y resolución de colisiones | Cómo se detectan y cómo se resuelven las colisiones en un protocolo de enlace (CSMA/CD) | Teoría pura, sin comandos. El alumno lo recordaba como CRID/CD |
| Evitar ads, banners y pop-ups haciendo que ad-up.com y adcomplete.com no resuelvan a su IP | Que ad-up.com (216.92.187.245) y adcomplete.com (63.171.166.66) no resuelvan a su IP en una PC, sin tocar la red, y qué haría el autor de las páginas para esquivarlo | Se resuelve con /etc/hosts apuntando a 127.0.0.1. El autor lo esquiva poniendo la IP en el link |
| ITBA Online manda DNI y clave en texto plano: conectarse sin que puedan capturar la clave, sin instalar un servidor | Conectarse a ITBA Online sin que capturen el DNI y la clave, sin instalar un servidor y como usuario común; si el administrador lo puede impedir; y dejar un servicio de la PC accesible desde casa | Túnel SSH a pampero.itba.edu.ar con -D (o -L hacia proxy.itba.edu.ar:8080) y, para el servicio, túnel remoto -R |
| Verdadero o falso: única IP pública y NAT, VPN red-red con IP-en-IP, firewall y paquetes UDP entrantes | Verdadero o falso con justificación: única IP pública y hosts simultáneos, VPN red-red con IP-en-IP, firewall y paquetes UDP entrantes | a\) falso (NAT con puertos), b) verdadero con la salvedad de que no cifra, c) falso (seguimiento de conexiones) |
| Navegar a sitios .ar desde una red que los filtra, con proxy obligatorio 192.168.0.3 y puertos hasta 1024 bloqueados | Si se puede navegar sitios .ar sin usar SSH desde una red que los filtra, con proxy 192.168.0.3, y qué recursos hacen falta si se usa SSH | El firewall bloquea 80, 8080 y todos los puertos hasta 1024: proxy HTTP externo en el 3128 o SSH en un puerto alto (2222). El 443 no sirve |

### Cómo navegar sitios .ar desde una red que los filtra

**Tomado en:** Parcial 2016 (segundo parcial) · Ejercicio 1.

> **Idea:** La idea es explicar cómo saltear el filtro .ar con un túnel SSH dinámico (ssh -D) hacia una máquina externa, usado como proxy SOCKS en el browser. El truco es que el 22 está bloqueado, así que el sshd externo escucha en el 443, y que el nombre .ar se resuelva afuera (--socks5-hostname o DNS por el proxy).

**Buscar:** .ar, dominio termine en .ar, terminado en .ar, navegar .ar, filtrar todo acceso, filtro, política, politica, IPs privados, instalar nuevo software, servidor proxy interno, proxy obligatorio, por fuera del proxy, firewall, puerto 22, descarta intentos de conexión al puerto 22, forwardeen, Explique en forma detallada, tunel, túnel, ssh, ssh -D, ssh -p 443, SOCKS, socks5, socks5h, --socks5-hostname, DNS remoto, Port 443, sshd_config.d, ssh.socket, teoria, explicar, descarta intentos de conexion al puerto 22

**Enunciado:** "Suponga que se encuentra en las oficinas de une empresa para la que trabaja, la cual tiene como política filtrar todo acceso a cualquier sitio web cuyo dominio termine en .ar, descartándolo. El filtro tiene la inteligencia suficiente para determinar si un determinado IP corresponde a un dominio .ar. La red a la cual pertenece su PC usa IPs privados. Los usuarios de cada PC pueden realizar cambios de configuración o instalar nuevo software en su PC. Para poder acceder a sitios web (con el protocolo http) se debe utilizar un servidor proxy interno, cualquier intento de enviar tráfico http por fuera del proxy es descartado por el firewall (y registrado para estudiar sanciones al usuario que lo intente). El firewall también descarta intentos de conexión al puerto 22. Obviamente no puede solicitar que "forwardeen" un puerto a su host. Pero usted sabe cómo hacer para poder acceder con su browser desde su PC a cualquier sitio web terminado en .ar. Explique en forma detallada cómo lo logrará."

**Solución (respuesta conceptual, con los comandos de respaldo):** el filtro corta por el nombre .ar y por la IP asociada. La idea es que el tráfico salga cifrado hacia una máquina de afuera que sí llega al sitio, y que el nombre .ar se resuelva allá y no en la red de la empresa.

> **1.** Conseguí una máquina externa con acceso libre (tu casa, un VPS) con un servidor SSH. Como el 22 está bloqueado, hacelo escuchar también en un puerto permitido, por ejemplo el 443:

En la máquina externa, hay que hacer sudo nano /etc/ssh/sshd_config.d/puerto443.conf, borrar lo que tenga y pegar lo siguiente:

Port 22

Port 443

Después recargá la configuración y reiniciá el servicio:

sudo systemctl daemon-reload

sudo systemctl restart ssh.socket

\# en sistemas sin ssh.socket (Debian, Ubuntu viejo): sudo systemctl restart ssh

\# viejo: sudo service ssh restart

Fijate que escuche en los dos puertos: corré sudo ss -ltn y en la salida tienen que aparecer estas dos líneas (puede haber otras):

LISTEN 0 4096 0.0.0.0:22 0.0.0.0:\*

LISTEN 0 4096 0.0.0.0:443 0.0.0.0:\*

> **2.** Desde tu PC de la empresa, abrí un proxy SOCKS sobre ese túnel y probalo. Con --socks5-hostname el nombre lo resuelve la máquina externa.

\# deja un proxy SOCKS en localhost:1080 que sale por la maquina externa

ssh -f -N -D 1080 -p 443 USUARIO@MAQUINA_EXTERNA

\# sin -f -N queda una terminal abierta en la maquina externa: sirve igual, no la cierres y proba en otra terminal

Si te pide la clave, escribila: te devuelve el prompt y el túnel queda andando de fondo. Después probalo; tiene que devolver el HTML del sitio:

curl --socks5-hostname localhost:1080 http://unsitio.com.ar/

\# lo mismo: curl -x socks5h://localhost:1080 http://unsitio.com.ar/

> **3.** En el browser: proxy manual, host SOCKS localhost, puerto 1080, SOCKS v5, y tildar la opción de resolver DNS por el proxy (en Firefox, "Proxy DNS when using SOCKS v5"). Así ni el DNS ni el HTTP del .ar pasan por el proxy interno ni por el filtro.

**Demostrar:** explicar por qué funciona: (a) el tráfico sale cifrado, el filtro no ve el nombre ni el contenido; (b) la resolución del .ar ocurre afuera (SOCKS con DNS remoto), así que el filtro nunca ve un nombre ni una IP de .ar; (c) no es tráfico http por fuera del proxy ni va al puerto 22: es una conexión SSH a un puerto permitido. En la prueba, con el 22 y el 80 salientes descartados y un sitio .ar que solo resuelve la máquina externa, salió esto: la primera línea es lo que da ssh USUARIO@MAQUINA_EXTERNA a secas (va al 22, que está bloqueado); la segunda es la línea del puerto 1080 que tiene que aparecer en la salida de ss -ltn con el túnel abierto; la tercera es lo que devuelve el curl del paso 2:

ssh: connect to host MAQUINA_EXTERNA port 22: Connection timed out

LISTEN 0 128 127.0.0.1:1080 0.0.0.0:\*

contenido de unsitio.com.ar

**Ojo:**

- El puerto 22 está bloqueado: el servidor del túnel tiene que escuchar en otro puerto (el 443 suele estar abierto). Con ssh a secas da Connection timed out.

- Resolución remota (--socks5-hostname o socks5h://): con --socks5 a secas el nombre se resuelve en tu PC, el filtro lo ve y te corta. En la prueba dio curl: (97) Could not resolve host: unsitio.com.ar.

- En Ubuntu 24.04 el puerto de sshd lo abre ssh.socket: después de agregar Port hay que hacer daemon-reload y reiniciar ssh.socket, no alcanza con ssh.service.

- "No puede solicitar que forwardeen un puerto" descarta que alguien entre hacia tu PC: por eso el túnel es saliente, desde tu PC.

- Es un ejercicio de explicar. El puntaje está en el razonamiento; los comandos van como respaldo.

*Probado en Ubuntu 24.04 (lab): sshd en el 443 en otra máquina del lab (con ssh.socket y también solo con ssh.service, reiniciando con systemctl restart ssh), el 22 y el 80 salientes descartados con iptables en el cliente, y un sitio .ar que solo se resuelve y se alcanza desde la máquina externa. Sin probar: la configuración del browser (el lab no tiene entorno gráfico).*

### CSMA/CD: detección y resolución de colisiones

**Tomado en:** Parcial 2015 (segundo parcial) · Ejercicio 3.

> **Idea:** La idea es explicar por escrito cómo CSMA/CD detecta y resuelve colisiones: sensa el medio, transmite y sigue escuchando mientras transmite; si choca, corta, manda jam y espera un tiempo aleatorio con backoff exponencial. La trampa es confundirlo con CSMA/CA, que evita colisiones en lugar de detectarlas.

**Contexto del parcial:** del segundo parcial de 2015 hay dos tandas de tres ejercicios, numeradas igual. Esta es la tanda en papel; las marcadas "(otra tanda)" son las de máquina y están en Resueltos — Red, "Llegar a una red detrás de un gateway y descubrir sus servicios" (Ejercicio 1 y Ejercicio 3) y en Resueltos — Firewall (iptables), "Filtrar, redirigir y restringir con iptables" (Ejercicio 2).

**Buscar:** CSMA/CD, CRID/CD, protocolo de enlace, colisiones, cómo se detectan colisiones, como se detectan colisiones, cómo se resuelven las colisiones, como se resuelven las colisiones, detectar colision, resolver colision, sensar el medio, backoff, jam, teoria enlace, Ethernet, CSMA/CA

**Enunciado:** No hay hoja en las fuentes; es el relato de un alumno: "Te dicen que hay un protocolo de enlace con un nombre raro, CRID/CD me parece, y te preguntan: Cómo se detectan colisiones entre Host? Como se resuelven las colisiones?". Por la descripción es CSMA/CD.

**Solución (respuesta teórica):**

- **Cómo se detecta una colisión:** el host que quiere transmitir primero sensa el medio (Carrier Sense). Si está ocupado, espera. Si está libre, transmite y **sigue escuchando el medio mientras transmite**: si lo que lee en el cable difiere de lo que mandó, hubo colisión (Collision Detection).

- **Cómo se resuelve:** al detectar la colisión, el host corta la transmisión y manda una señal de atasco (jam) para que todos la noten. Después espera un tiempo aleatorio antes de reintentar (backoff exponencial binario: tras cada colisión duplica el rango de espera). Pasado ese tiempo vuelve a sensar y reintenta.

**Demostrar:** nombrar los dos mecanismos (Carrier Sense + Collision Detection para detectar; jam + backoff exponencial aleatorio para resolver) con la idea de "sensar, transmitir, escuchar mientras transmito, si choco corto y espero un tiempo random".

**Ojo:**

- Es CSMA/CD (Ethernet cableado clásico). No lo confundas con CSMA/CA (Wi-Fi), que **evita** colisiones (no las detecta).

- El punto clave de "detección" es que escucha mientras transmite; el de "resolución" es el backoff **aleatorio** (si todos esperaran lo mismo, volverían a chocar).

*Sin probar: ejercicio teórico, no hay comando que ejecutar.*

### Evitar ads, banners y pop-ups haciendo que ad-up.com y adcomplete.com no resuelvan a su IP

**Tomado en:** Parcial 2007 · Ejercicio 1.

> **Idea:** La idea es lograr que ad-up.com y adcomplete.com no resuelvan a su IP agregándolos a /etc/hosts apuntando a 127.0.0.1, que se consulta antes que el DNS, y explicar que el autor lo esquiva poniendo la IP en el link en lugar del nombre. Para comprobarlo va getent hosts, porque dig no mira /etc/hosts.

**Buscar:** código molesto, codigo molesto, ads, banners, pop-ups, ad-up.com, 216.92.187.245, adcomplete.com, 63.171.166.66, malware.jsp, no se resuelvan a la IP pública, no se resuelvan a la IP publica, forma sencilla en una PC, sin necesidad de recurrir a cambios en la red, nuevos servidores, /etc/hosts, 127.0.0.1, autor de las páginas, autor de las paginas, medida de seguridad no tenga efecto

**Enunciado:** "Al consultar una página web desde cualquier browser, es posible que la misma tenga referencias (links) a código "molesto" ( ads, banners, pop-ups, etc.). Estos sitios en general son conocidos y se puede encontrar un listado actualizado, por ejemplo ad-up.com (216.92.187.245), adcomplete.com ( 63.171.166.66), etc. Una forma de evitar que se activen esos links es que estos sitios no se resuelvan a la IP pública que corresponde. Por ejemplo, si existiera una referencia a http://ad-up.com/scripts/malware.jsp no intentaría conectarse al servicio http en el host 216.92.187.245. De esta forma evitaríamos resolver referencias no deseadas. a) Encontrar una forma sencilla de lograr esto en una PC, sin necesidad de recurrir a cambios en la red o nuevos servidores. b) Si usted fuera el autor de las páginas con las referencias mencionadas, ¿qué podría hacer para que esta medida de seguridad no tenga efecto?"

**Solución:**

> **1.** a) Agregar esos nombres al archivo /etc/hosts apuntando a la propia máquina. El sistema consulta ese archivo antes que al DNS, así que el browser intenta conectarse a 127.0.0.1 y no al servidor real.

Hay que hacer sudo nano /etc/hosts y agregar al final lo siguiente:

127.0.0.1 ad-up.com adcomplete.com

> **2.** b) Como autor, poner en los links la IP en lugar del nombre: http://216.92.187.245/scripts/malware.jsp. No hay resolución de nombres, así que /etc/hosts no interviene. Otra forma: usar un nombre nuevo que todavía no esté en el listado y que apunte a la misma IP.

**Demostrar:**

getent hosts ad-up.com adcomplete.com

127.0.0.1 ad-up.com adcomplete.com

127.0.0.1 ad-up.com adcomplete.com

**Ojo:**

- dig, host y nslookup le preguntan directo al DNS y no miran /etc/hosts: para comprobar usá getent hosts o ping.

- /etc/hosts no tiene comodines: www.ad-up.com es otro nombre y hay que agregarlo aparte.

- El apunte dice "redirigir todas las ips a localhost": lo que se redirige son los nombres, no las IP.

*Probado en Ubuntu 24.04 (lab).*

### ITBA Online manda DNI y clave en texto plano: conectarse sin que puedan capturar la clave, sin instalar un servidor

**Tomado en:** Parcial 2007 · Ejercicio 2.

> **Idea:** La idea es conectarse a ITBA Online sin exponer DNI y clave con un túnel SSH a pampero (ssh -D 1080, o -L hacia proxy.itba.edu.ar:8080), usando solo el cliente y un puerto local mayor a 1024. El administrador lo puede cortar, por ejemplo bloqueando la salida al 22, y para usar un servicio de la PC desde casa va un túnel remoto -R.

**Buscar:** ITBA Online, DNI, clave, texto plano, sin encriptar, capturen el tráfico, capturen el trafico, robar su identidad, no sea necesario instalar un nuevo servidor, usuario común, usuario comun, no administrador, pampero, pampero.itba.edu.ar, proxy.itba.edu.ar, túnel, tunel, ssh -L, ssh -D, ssh -R, túnel remoto, tunel remoto, puede el administrador de la red impedir, medidas a adoptar, dejar funcionando un servicio en su PC, accederlo en forma remota de su casa, GatewayPorts

**Enunciado:** "Suponga que usted, además de ser alumno del ITBA, trabaja en una empresa, con acceso de usuario común (no administrador) a una PC con Linux (en forma similar a las PCs de LI1 y LI2). Como bien sabe, al conectarse a ITBA Online tanto el DNI del usuario como la clave viajan en forma de texto plano, sin encriptar. Como no quiere que en el trabajo alguien pueda robar su identidad decide conectarse de forma tal que, por más que capturen el tráfico, no puedan obtener su clave. Se pide: a) Describir la forma más simple para realizar esta tarea, de forma tal que no sea necesario instalar un nuevo servidor (sí puede instalar o configurar algún cliente en la PC que le asignaron). b) ¿Puede el administrador de la red impedir que le funcione la solución propuesta? En caso afirmativo, indicar cuál o cuáles serían las medidas a adoptar por el administrador. c) ¿Podría usar la solución anterior, con una leve modificación, para dejar funcionando un servicio en su PC y accederlo en forma remota de su casa? Describir cómo lo haría."

**Solución:**

> **1.** a) Túnel SSH hasta un host del ITBA donde ya tengo cuenta y ya hay un servidor SSH (pampero). Solo uso el cliente ssh, y con un puerto local mayor a 1024 no hace falta ser administrador.

ssh -N -D 1080 USUARIO@pampero.itba.edu.ar

\# alternativa de los apuntes, hacia el proxy HTTP del ITBA:

\# ssh -N -L 8080:proxy.itba.edu.ar:8080 USUARIO@pampero.itba.edu.ar

> **2.** Configuro el browser con proxy SOCKS v5 en 127.0.0.1 puerto 1080 (con la alternativa: proxy HTTP en localhost puerto 8080). Todo lo que sale de la PC de la empresa viaja cifrado hasta pampero; el DNI y la clave van en claro solo entre pampero e ITBA Online, dentro de la red del ITBA.
>
> **3.** b) Sí. Medidas: bloquear en el firewall la salida al puerto 22 (o a la IP de pampero); obligar a salir por un proxy HTTP que no permita CONNECT hacia ese destino; o inspeccionar el tráfico y cortar las conexiones que empiezan con el saludo SSH-2.0-, que viaja sin cifrar.
>
> **4.** c) Sí: cambio el túnel local por uno remoto (-R). En la PC de la empresa, con el servicio escuchando en el puerto 7666:

ssh -N -R 6666:localhost:7666 USUARIO@pampero.itba.edu.ar

> **5.** Pampero queda escuchando en su puerto 6666 y lo que llega ahí vuelve por el túnel hasta el 7666 de la PC de la empresa. Desde mi casa:

ssh -N -L 8666:localhost:6666 USUARIO@pampero.itba.edu.ar

> **6.** En mi casa uso el servicio en localhost:8666. La conexión la abre la PC de la empresa hacia afuera, por eso no necesito que me redirijan ningún puerto.

**Demostrar:** con el túnel del paso 1 abierto (el ssh -N deja esa terminal tomada y sin mostrar nada, es normal: no la cierres), abrí otra terminal, corré ss -ltn y en la salida mirá que aparezca la línea del puerto 1080:

LISTEN 0 128 127.0.0.1:1080 0.0.0.0:\*

Y, en esa misma terminal, probá que se navega por el túnel, cambiando SITIO por el sitio que quieras abrir (tiene que devolver el HTML del sitio):

curl --socks5-hostname localhost:1080 http://SITIO/

**Ojo:**

- Los apuntes escriben ssh -l 8080:proxy.itba.edu.ar:8080 ... con ele minúscula: -l es el nombre de usuario. El túnel local es -L mayúscula.

- El segundo túnel del punto c) hace falta porque el puerto de -R queda solo en el loopback de pampero, salvo que su servidor tenga GatewayPorts yes.

- bind \[127.0.0.1\]:80: Permission denied: un usuario común no puede abrir puertos locales menores a 1024. Usá 1080, 8080, 8666.

- Si el sitio ofreciera HTTPS, esa sería la forma más simple. El enunciado parte de que no lo ofrece.

*Probado en Ubuntu 24.04 (lab). Sin probar: pampero y el proxy del ITBA (los túneles se probaron contra hosts del lab) y las medidas del punto b), que son teoría.*

### Verdadero o falso: única IP pública y NAT, VPN red-red con IP-en-IP, firewall y paquetes UDP entrantes

**Tomado en:** Parcial 2007 · Ejercicio 3.

> **Idea:** La idea es marcar verdadero o falso con una justificación breve: a) falso, porque con NAT con puertos el límite son las conexiones simultáneas y no las IP privadas; b) verdadero, el encapsulado IP-en-IP es transparente para los hosts, con la salvedad de que no cifra; c) falso, porque el seguimiento de conexiones acepta solo los UDP que responden a algo que salió.

**Buscar:** verdadera o falsa, verdadero o falso, justificar brevemente, única IP pública, unica IP publica, cantidad de IPs privadas, acceder a Internet en forma simultánea, simultanea, NAT, NAPT, PAT, MASQUERADE, VPN red-red, IP-en-IP, IP-IP, IPIP, encapsulamiento es transparente, túnel, tunel, IPsec, gateway Linux, firewall, sesión establecida, sesion establecida, paquetes UDP entrantes, conntrack, ctstate, ESTABLISHED, RELATED, state

**Enunciado:** "Para cada una de las siguientes afirmaciones, indicar si es verdadera o falsa. Justificar brevemente. a) Si una organización tiene una única IP pública, la cantidad de hosts de su red interna (TCP/IP v4) que pueden acceder a Internet en forma simultánea está limitada por la cantidad de IPs privadas existentes. b) Una forma sencilla de crear una VPN red-red es utilizando IP-en-IP, haciendo que los routers de ambas redes encapsulen los paquetes cuyo IP destino pertenezcan a la red interna "de la otra punta". Esto es posible porque para cada host el encapsulamiento es transparente. c) Sea una red donde todos los hosts tienen una IP pública pero utilizan un host Linux como gateway que a su vez provee funciones de firewall. El firewall puede filtrar los paquetes TCP que no pertenezcan a una sesión establecida, pero debe dejar pasar todos los paquetes UDP entrantes hacia cualquier host de la red, ya que no puede distinguir si son válidos o no."

**Solución:**

> **1.** a) Falso. Con una sola IP pública se sale con NAT con traducción de puertos (NAPT; MASQUERADE en Linux). Cada conexión ocupa una entrada de la tabla de NAT identificada por protocolo, puerto traducido e IP y puerto de destino. Lo que se agota son los puertos de la IP pública (unos 64 mil por protocolo hacia un mismo destino) y la memoria de la tabla, no las direcciones privadas: solo en 10.0.0.0/8 hay más de 16 millones. El límite es de conexiones simultáneas, no de hosts.
>
> **2.** b) Verdadero en cuanto al mecanismo: cada router encapsula el paquete original (con IP privadas) dentro de otro paquete IP con las direcciones públicas de los dos routers, y el otro extremo lo desencapsula. Los hosts no se enteran: solo le mandan el paquete a su gateway. Salvedad para escribir en la justificación: IP-en-IP no cifra ni autentica, así que la red es virtual pero no privada frente a quien capture en el camino; para eso hace falta IPsec.
>
> **3.** c) Falso. UDP no tiene conexiones, pero el firewall de Linux (netfilter con seguimiento de conexiones) recuerda cada flujo UDP saliente por sus IP y puertos durante un tiempo. Un UDP entrante se acepta solo si responde a un flujo que un host interno inició antes; el resto se descarta. En el gateway alcanza con estas reglas (IFACE_LAN es la interfaz que da a la red interna, se reemplaza a mano: el nombre lo ves con ip -br a; si queda sin cambiar, iptables no avisa y la red interna no sale):

sudo iptables -P FORWARD DROP

sudo iptables -A FORWARD -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

sudo iptables -A FORWARD -i IFACE_LAN -j ACCEPT

\# forma vieja del mismo match:

\# sudo iptables -A FORWARD -m state --state ESTABLISHED,RELATED -j ACCEPT

**Demostrar:** es para escribir: por cada inciso, "Verdadero" o "Falso" y dos o tres líneas de justificación como las de arriba. Las reglas de iptables alcanzan como respaldo del inciso c): con ellas, un UDP que llega de afuera sin pedido previo no pasa, y la respuesta a un UDP que salió de adentro sí.

**Ojo:**

- En b) el apunte responde Falso con el argumento de que sin cifrado no es una VPN. Si el corrector espera ese criterio, la justificación es la misma salvedad: escribí el mecanismo y aclará que no da confidencialidad.

- En c) el apunte lo justifica al revés ("iptables permite filtrar UDP si no mandé antes"); lo correcto es que deja pasar solo las respuestas a lo que salió antes.

- En a) no hay un número máximo de hosts: el tope práctico son las conexiones simultáneas que entran en la tabla de NAT.

*Probado en Ubuntu 24.04 (lab). Sin probar: los incisos a) y b), que son teoría (del c) se ejecutaron las tres reglas en un gateway de prueba).*

### Navegar a sitios .ar desde una red que los filtra, con proxy obligatorio 192.168.0.3 y puertos hasta 1024 bloqueados

**Tomado en:** Parcial 2007 · Ejercicio 4.

> **Idea:** La idea es justificar que sí se puede llegar a los .ar sin SSH, con un intermediario externo que no sea .ar en un puerto mayor a 1024 y distinto de 8080, como un proxy HTTP en el 3128. Con SSH hacen falta un host externo con sshd en un puerto alto (2222) y ssh -D, porque el 443 también queda bloqueado.

**Buscar:** dominio termine en .ar, .ar, filtrar todo acceso, filtro, IPs privados, instalar nuevo software, puerto 80 u 8080, por fuera del proxy, puertos menores o igual a 1024, forwardear, 192.168.0.3, sin usar SSH, recursos necesarios, proxy externo, 3128, VPN, ssh -p, ssh -D, SOCKS, socks5, túnel, tunel

**Enunciado:** "Suponga que se encuentra en las oficinas de cierta organización para la que trabaja, la cual tiene como política filtrar todo acceso a cualquier sitio web cuyo dominio termine en .ar, descartándolo. El filtro tiene la inteligencia suficiente para determinar si un determinado IP corresponde a un dominio .ar. Luego de analizar la red descubre que: a) La red a la cual pertenece su PC usa IPs privados. b) Los usuarios de cada PC pueden realizar cambios de configuración o instalar nuevo software en la misma. c) Cualquier intento de enviar tráfico al puerto 80 u 8080 (los usados habitualmente por http) por fuera del proxy es descartado por el firewall (y registrado para estudiar sanciones al usuario que lo intente). d) El firewall también descarta intentos de conexión a todos los puertos menores o igual a 1024. e) El administrador de la red no acepta pedidos para "forwardear" puertos a los hosts de los usuarios. f) El IP privado del proxy es 192.168.0.3. Pero usted, luego de haber cursado Protocolos de Comunicación, sabe cómo hacer para poder acceder desde su navegador a cualquier sitio web terminado en .ar. Responder con una breve justificación: 1) ¿Es posible realizar la conexión a los sitios web terminados en .ar sin usar SSH? Si la respuesta es sí, indicar cómo lo haría 2) Si la respuesta a la pregunta anterior es no, detallar los recursos necesarios para poder lograrlo."

**Solución:**

> **1.** 1) Sí. El filtro mira el destino (nombre o IP .ar) y el firewall mira los puertos. Alcanza con que el browser hable con un intermediario externo que no sea .ar, en un puerto mayor a 1024 y distinto de 8080, y que ese intermediario pida la página .ar. Opciones: un proxy HTTP externo en el puerto 3128 configurado en el browser, o una VPN contra un servidor externo en un puerto alto (se puede porque el usuario instala software). También sirve, sin instalar nada, un proxy web externo (un sitio que no es .ar y trae la página por vos) usado a través del proxy 192.168.0.3.

curl -x http://HOST_EXTERNO:3128 http://www.SITIO.com.ar/

> **2.** 2) Si se quiere hacer con SSH (o si la respuesta fuera no), recursos: un host externo que no sea .ar, con cuenta y con un servidor SSH escuchando en un puerto mayor a 1024 (el 22 y el 443 están bloqueados), y el cliente ssh en la PC. Túnel dinámico y browser con proxy SOCKS v5 en 127.0.0.1 puerto 1080, con el DNS resuelto por el proxy:

ssh -p 2222 -N -D 1080 USUARIO@HOST_EXTERNO

El ssh -N deja esa terminal tomada y sin mostrar nada (es normal: no la cierres). Para probarlo, abrí otra terminal y corré esto (tiene que devolver el HTML del sitio .ar):

curl --socks5-hostname localhost:1080 http://www.SITIO.com.ar/

> **3.** Por qué pasa los controles: la conexión visible va a un destino que no es .ar, en un puerto mayor a 1024 que no es 80 ni 8080; la inicia la PC desde adentro, así que no hace falta que forwardeen nada ni tener IP pública; y el puerto local 1080 no requiere ser administrador.

**Demostrar:** es para escribir: "Sí" en 1) con el intermediario externo y su puerto, y en 2) la lista de recursos con el comando ssh. Si hay que mostrarlo andando, cualquiera de los dos curl de arriba tiene que devolver el HTML del sitio .ar.

**Ojo:**

- El intermediario no puede ser una PC cuya IP resuelva a un nombre .ar (por ejemplo una conexión hogareña de Argentina): el filtro "tiene la inteligencia suficiente" para detectarlo. Los apuntes proponen la PC de casa sin aclarar esto.

- La misma pregunta está en el Parcial 2016 (segundo parcial) · Ejercicio 1 (Resueltos — En papel, "Preguntas teóricas"). Ahí solo se bloquea el 22 y sirve SSH en el 443; acá el 443 también cae porque es menor a 1024.

- Si el nombre .ar se resuelve en la PC de la oficina, la consulta DNS puede delatar el destino: por eso --socks5-hostname o "DNS por proxy" en el browser.

*Probado en Ubuntu 24.04 (lab). Sin probar: el filtro .ar y el firewall de la organización (es una pregunta teórica; los dos comandos se probaron contra un proxy HTTP en el 3128 y un sshd en el 2222 del lab).*

# Resueltos — Consignas que faltan

## Ejercicios que no están en las fuentes

Son los ejercicios de los que no hay consigna: no tienen enunciado ni solución, y no se inventan. De uno a otro cambia qué rastro quedó en las fuentes y qué ejercicios resueltos conviene mirar en su lugar.

| **Parcial y ejercicio** | **Qué pide** | **Lo particular** |
|----|----|----|
| Parcial 2022-2C · Ejercicio 3 | No se sabe: las fuentes no traen la consigna | El apunte del alumno dice "FALTA EJERCICIO 3" y no hay imagen. Es el único de los cuatro ejercicios que falta |
| Parcial 2021-1C · Ejercicio 4 | No se sabe: el archivo 59235_E04 no está entre las fuentes | Para temas parecidos sirve el Parcial o Recuperatorio 2021-1C (virtual hosts y salida a internet sin NAT por un túnel) |

### Parcial 2022-2C · Ejercicio 3 — no está en las fuentes

**Contexto del parcial:** es el único de los cuatro ejercicios del Parcial 2022-2C que falta. Los otros tres están resueltos: Parcial 2022-2C · Ejercicio 1 (Resueltos — nginx, "Proxy reverso: un dominio sirve el contenido de otro"), Parcial 2022-2C · Ejercicio 2 (Resueltos — SSH, "Seguir un archivo remoto con tail -f por un túnel") y Parcial 2022-2C · Ejercicio 4 (Resueltos — DNS, "Crear una zona en BIND").

**Buscar:** 2022-2C ejercicio 3, falta ejercicio 3

**Enunciado:** Las fuentes no traen la consigna. El apunte del alumno dice literalmente "FALTA EJERCICIO 3" y no hay imagen.

**Solución:** no hay nada para resolver. No se inventa.

*Sin probar: la consigna no está en las fuentes.*

### Parcial 2021-1C · Ejercicio 4 — no está en las fuentes

**Contexto del parcial:** es el único ejercicio del Parcial 2021-1C (archivos 59235 de las fuentes: E01, E02, E03 y E05) que falta. Los otros cuatro están resueltos: Parcial 2021-1C · Ejercicio 1 (Resueltos — nginx, "Proxy reverso: un dominio sirve el contenido de otro"), Parcial 2021-1C · Ejercicio 2 (Resueltos — SSH, "Entrar con una llave privada: passphrase o nombre de archivo por deducir"), Parcial 2021-1C · Ejercicio 3 (Resueltos — curl, "Negociación de contenido: text/plain en español") y Parcial 2021-1C · Ejercicio 5 (Resueltos — Capturas (Wireshark), "Responder preguntas sobre una captura").

**Buscar:** 2021-1C ejercicio 4, 59235_E04, falta ejercicio 4

**Enunciado:** Las fuentes no traen la consigna (el archivo 59235_E04 no existe).

**Solución:** La consigna del Ejercicio 4 del Parcial 2021-1C no figura en los archivos disponibles (59235_E04 no existe entre las fuentes). No se incluye para no inventarla. Si en tu examen aparece algo parecido a "virtual hosts", "salida a internet sin NAT" o "túnel", mirá el otro examen de ese cuatrimestre: Parcial o Recuperatorio 2021-1C · Ejercicio I (virtual hosts; está en Resueltos — nginx, "Virtual hosts: cada dominio con su contenido") y Parcial o Recuperatorio 2021-1C · Ejercicio IV (salida a internet sin NAT por un túnel; está en Resueltos — SSH, "Salir a internet o llegar a un servicio interno con túneles").

*Sin probar: la consigna no está en las fuentes.*

.
