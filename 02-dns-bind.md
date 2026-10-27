# DNS, dig y BIND

> Cheatsheet de PROTOS (72.07, ITBA) para el parcial práctico. Repaso del tema y, al final, el paso a paso de todos los ejercicios de parciales que caen acá.

---

## Cheatsheet

### dig

```
dig <dominio>  # registro A
dig MX <dominio>  # servidores de mail (+ prioridad)
dig SOA <dominio>  # serial, refresh, retry, expire
dig <tipo> <dominio> @localhost  # preguntarle a TU bind (verificar la zona)
dig +short <dominio>  # solo la respuesta
dig AXFR <zona> @<master>  # pedir la transferencia completa
dig -x <ip>  # resolución inversa (PTR): IP → nombre
dig +trace <dominio>  # resolución completa desde los root servers
dig TXT <dominio>  # ver el SPF; whois <dominio>: dueño y NS
```

### Tipos de registro

| Tipo | Para qué | Ojo |
|---|---|---|
| A | nombre → IPv4 | — |
| NS | servidor autoritativo de la zona | necesita su propio A, si no la zona no carga |
| MX | servidor de mail + prioridad | menor número = más prioridad |
| CNAME | alias → nombre canónico | no puede ir en la raíz (@) |
| TXT | SPF, verificación | las comillas forman parte del valor |
| SOA | datos de la zona | regla: `Expire > Refresh + Retry` |

### Zona master

En `/etc/bind/named.conf.local`: `zone "foo.pdc.lab" { type master; file "/etc/bind/foo.pdc.lab"; };`. El archivo de zona (el TTL por registro va entre el nombre y la clase):

```
$ORIGIN foo.pdc.lab.
$TTL 1m

@ IN SOA ns.foo.pdc.lab. admin.pdc.lab. (
    2026102501   # Serial YYYYMMDDnn — SUBIRLO en cada cambio
    7d 1d 10d 1m )   
# Refresh Retry Expire TTL-negativo

@        IN NS  ns
ns       IN A   <IP de tu bind>
@   1h   IN A   123.123.123.123
www 7d   IN CNAME @
@   7d   IN MX 10 mx1.otro.dominio.
@        IN TXT "v=spf1 ip4:1.2.3.4 -all"
```

- Todo nombre externo termina en **punto** (`mx1.otro.dominio.`); sin punto se le pega el `$ORIGIN`. `@` = la raíz.
- El mail del SOA: la `@` se cambia por `.`; un punto propio del mail (`example.admin@`) se escapa como `\.`.
- TTL: `1w`=`7d`=`604800`, `1h`=`3600` (dig lo muestra en segundos).

```
named-checkconf  # valida named.conf (sin salida = OK)
named-checkzone foo.pdc.lab /etc/bind/foo.pdc.lab  # valida la zona
systemctl restart bind9  # recién después de validar
dig SOA foo.pdc.lab @localhost
```

> **Servidor secundario / slave (lo que probablemente se tome)**
>
> **Master**: autorizar la transferencia y avisar.
> 
> ```
> zone "protos.foo" {
>     type master; file "/etc/bind/protos.foo";
>     allow-transfer { <IP slave>; };
>     also-notify { <IP slave>; };
> };
> ```
> 
> **Slave**: `type slave`, de dónde copiar, y el archivo en `/var/cache/bind/` (donde bind puede escribir), no en `/etc/bind`.
> 
> ```
> zone "protos.foo" {
>     type slave; masters { <IP master>; };
>     file "/var/cache/bind/protos.foo";
> };
> ```
> 
> Verificás: `dig AXFR protos.foo @master` (si da `Transfer failed`, falta el `allow-transfer`), y que master y slave den el **mismo serial** con `dig SOA... +short`. Propagar un cambio = **subir el serial**. Con el master caído, el slave responde hasta que vence el **expire**. La transferencia va por **TCP/53**.

### Diagnóstico

| Síntoma | Causa |
|---|---|
| SERVFAIL, sin flag `aa` | la zona no cargó: sintaxis mala o path mal |
| NXDOMAIN con flag `aa` | la zona cargó, pero ese registro no existe |
| los cambios no se reflejan | faltó subir el serial o recargar |

Errores clásicos: falta el punto final en un nombre externo; un NS o MX que apunta a un nombre sin su A; el serial sin subir. Log: `journalctl -u named -n 30`. `/etc/hosts` se consulta antes que el DNS (sirve para probar nginx sin DNS).

### Que tu bind resuelva también internet

Por defecto tu bind solo sabe de su zona. Para que resuelva el resto, en `/etc/bind/named.conf.options`:

```
forwarders {
    1.1.1.1;
    8.8.8.8;
};
```

---

## Ejercicios paso a paso

### Ejercicio 1: Servidor DNS secundario para protos.foo con transferencia de zona

_[Probable 2C 2026]_

**Enunciado**

> La cátedra administra el dominio protos.foo y necesita montarlo con redundancia sobre dos máquinas de la red 192.168.108.0/24: ns1.protos.foo (192.168.108.10), que va a ser el servidor primario, y ns2.protos.foo (192.168.108.20), que va a ser el servidor secundario de respaldo. La zona debe contener, como mínimo, los dos registros NS con sus respectivos A, un registro A para www.protos.foo apuntando a 192.168.108.80 y un MX de prioridad 10 hacia mail.protos.foo (192.168.108.25). El archivo de zona se edita únicamente en ns1: en ns2 no se permite escribir el archivo de zona a mano, la información tiene que llegarle desde ns1. Se pide: (a) que ns2 responda las consultas de protos.foo con autoridad, igual que ns1; (b) que la transferencia de zona entre ns1 y ns2 funcione, indicando qué protocolo de transporte y qué puerto usa y por qué; (c) que se demuestre, con la salida de los comandos correspondientes, que un cambio hecho en la zona de ns1 (agregar intranet.protos.foo con IP 192.168.108.90) se propaga a ns2; y (d) que se demuestre que ns2 sigue respondiendo con autoridad por protos.foo con ns1 caído, aclarando hasta cuándo lo va a hacer y qué campo del SOA lo determina. Entregar los archivos de configuración de ambos servidores y los comandos de verificación usados.

**Pista:** El secundario no se configura escribiendo la zona: se configura diciéndole a bind de quién la tiene que pedir y dónde la puede guardar, y el único campo del SOA que dispara esa transferencia es el serial.

**Resolución paso a paso**

**1. Preparar las dos máquinas y verificar que se ven entre sí**

La transferencia de zona viaja por TCP/53 desde ns2 hacia ns1. Si no hay conectividad, o si ns1 solo escucha en loopback, el slave va a fallar sin que nada de la config de bind esté mal, y vas a perder el parcial buscando el error en el lugar equivocado. Chequear esto primero cuesta treinta segundos.

```
# en las dos máquinas
sudo apt install bind9 bind9-utils
sudo -i
ip addr show        # confirmar 192.168.108.10 en ns1 y .20 en ns2

# desde ns2, probar que llega a ns1
ping -c2 192.168.108.10
```

_Verificación:_ El ping responde y cada máquina tiene la IP que le corresponde. Si tenés que agregarla: ip addr add 192.168.108.20/24 dev enp0s9

**2. En ns1: declarar la zona como master, con allow-transfer y notify**

Por defecto BIND9 NO permite transferencias de zona a nadie: sin allow-transfer, ns1 contesta REFUSED a todo pedido de AXFR y ns2 nunca va a cargar la zona. notify yes y also-notify son los que hacen que el cambio del punto (c) se propague en segundos en vez de esperar el refresh completo.

```
# /etc/bind/named.conf.local
zone "protos.foo" {
    type master;
    file "/etc/bind/db.protos.foo";
    allow-transfer { 192.168.108.20; };   # solo ns2, nunca any
    notify yes;
    also-notify { 192.168.108.20; };
};
```

_Verificación:_ named-checkconf no imprime nada. Si imprime algo, es un punto y coma o una llave faltante.

**3. En ns1: escribir el archivo de zona con el SOA y los DOS registros NS**

La zona tiene que declarar a ns1 y a ns2 como NS, cada uno con su registro A. Sin el NS de ns2, bind no sabe a quién mandarle NOTIFY y ningún resolver se entera de que existe un backup: todas las consultas siguen yendo a ns1 y la redundancia del punto (a) no existe en la práctica. Los tiempos del SOA tienen que cumplir expire > refresh + retry, para que el slave tenga al menos una oportunidad de refresh y un reintento antes de dar la zona por muerta.

```
# /etc/bind/db.protos.foo
$ORIGIN protos.foo.
$TTL 1m

@ IN SOA ns1.protos.foo. admin.protos.foo. (
        2026102101  ; Serial - incrementar en CADA cambio
        1h          ; Refresh
        15m         ; Retry
        7d          ; Expire  (7d > 1h + 15m, se cumple)
        1m          ; Negative TTL
)

@      IN NS  ns1
@      IN NS  ns2
ns1    IN A   192.168.108.10
ns2    IN A   192.168.108.20

www    IN A   192.168.108.80
@      IN MX  10 mail
mail   IN A   192.168.108.25
```

_Verificación:_ named-checkzone protos.foo /etc/bind/db.protos.foo tiene que decir 'OK' y mostrar el serial cargado. Si dice 'has no NS records' o 'not loaded due to errors', no sigas: recargás y bind se queda con la zona anterior o con ninguna.

**4. Levantar ns1 y comprobar que responde con autoridad por su cuenta**

Antes de meter al slave en la ecuación hay que saber que el master está sano. Si ns1 devuelve SERVFAIL sin el flag aa, la zona no cargó y cualquier cosa que hagas en ns2 va a fallar por una causa que no está en ns2.

```
named-checkconf && systemctl reload bind9
systemctl status bind9

dig @localhost protos.foo SOA
dig @localhost www.protos.foo A
dig @localhost protos.foo NS
```

_Verificación:_ Las tres respuestas traen el flag aa en la línea 'flags:' y status: NOERROR. El NS tiene que listar ns1 y ns2.

**5. Desde ns2: pedir la transferencia a mano con dig AXFR**

Este es el paso que ahorra media hora. Pedir el AXFR con dig, antes de configurar nada en ns2, aisla el problema: si acá ves la zona completa, allow-transfer y la conectividad están bien y todo lo que falte después es config del slave. Si ves 'Transfer failed' o REFUSED, el problema es allow-transfer en ns1. Además es la demostración directa del punto (b): dig abre una conexión TCP al puerto 53, no manda un datagrama UDP, porque la respuesta es grande, de tamaño desconocido de antemano y tiene que llegar completa y en orden.

```
# desde ns2
dig @192.168.108.10 protos.foo AXFR

# opcional, para mostrar que va por TCP/53
ss -tn state established '( dport = :53 )'
```

_Verificación:_ dig imprime la zona entera: el SOA, los dos NS, los A y el MX, y cierra repitiendo el SOA. Si sale 'Transfer failed.' revisá allow-transfer.

**6. En ns2: declarar la zona como slave, con file en /var/cache/bind**

Acá no se escribe ningún archivo de zona: el file es donde bind va a GUARDAR la copia que reciba, para seguir teniéndola si se reinicia sin poder hablar con ns1. Tiene que estar en un directorio escribible por el usuario bind. /etc/bind es root:bind con solo lectura para el grupo, así que ahí el guardado falla con permission denied; /var/cache/bind es bind:bind y es el único lugar que AppArmor le permite escribir.

```
# /etc/bind/named.conf.local  en ns2
zone "protos.foo" {
    type slave;
    masters { 192.168.108.10; };
    file "/var/cache/bind/db.protos.foo";
};

named-checkconf && systemctl reload bind9
```

_Verificación:_ ls -l /var/cache/bind/db.protos.foo tiene que existir y pertenecer a bind:bind pocos segundos después del reload. Si no aparece, el archivo o el directorio no son escribibles.

**7. Verificar en ns2 que la transferencia se completó y que responde con autoridad**

Que el servicio arranque no quiere decir que la zona haya llegado. El log es el único lugar donde dice con precisión qué pasó, y el flag aa en la respuesta de ns2 es exactamente lo que pide el punto (a). Comparar los seriales de los dos lados es el chequeo de consistencia más rápido que existe.

```
journalctl -u named -n 40 --no-pager | grep -i transfer
# o: grep named /var/log/syslog | tail -40

dig @192.168.108.20 www.protos.foo A
dig @192.168.108.10 protos.foo SOA +short
dig @192.168.108.20 protos.foo SOA +short
```

_Verificación:_ En el log: "transfer of 'protos.foo/IN' from 192.168.108.10#53: Transfer completed". El dig a ns2 trae flag aa y la IP correcta. Los dos SOA +short muestran el MISMO serial (2026102101).

**8. Demostrar la propagación: agregar el registro en ns1 e INCREMENTAR el serial**

Es el punto (c), y es donde se cae la mayoría. El disparador de la transferencia es el serial: si agregás intranet y recargás sin tocar el serial, ns1 responde bien, no manda NOTIFY, y ns2 cuando chequee va a concluir que ya está al día. El cambio nunca se propaga y el síntoma parece un problema del slave.

```
# en ns1, en /etc/bind/db.protos.foo:
#   agregar     intranet  IN A  192.168.108.90
#   y cambiar el serial a 2026102102

named-checkzone protos.foo /etc/bind/db.protos.foo
rndc reload protos.foo

# en ns2, si no querés esperar el NOTIFY:
rndc retransfer protos.foo
```

_Verificación:_ dig @192.168.108.20 intranet.protos.foo A devuelve 192.168.108.90 con flag aa, y los dos dig SOA +short muestran 2026102102. En el log de ns2 aparece el NOTIFY recibido seguido de otro 'Transfer completed'.

**9. Demostrar la redundancia: bajar ns1 y verificar que ns2 sigue respondiendo**

Es el punto (d) y es un comando, pero hay que mostrarlo explícitamente o no cuenta. También es la prueba de que armaste un secundario y no un forwarder: un forwarder, con ns1 caído, dejaría de responder en cuanto se le venciera el TTL de lo cacheado y nunca pondría el flag aa.

```
# en ns1
systemctl stop bind9

# en ns2 (o desde un tercer host)
dig @192.168.108.20 www.protos.foo A
dig @192.168.108.20 intranet.protos.foo A
dig @192.168.108.10 www.protos.foo A    # este tiene que fallar
```

_Verificación:_ ns2 sigue devolviendo NOERROR con flag aa para los dos nombres, mientras el dig a ns1 da 'connection refused' o timeout. En la respuesta hay que aclarar que ns2 va a seguir así durante el expire (7d) contado desde el último contacto exitoso con ns1, y que vencido ese plazo abandona la zona y empieza a devolver SERVFAIL sin aa.

**Errores típicos**

- Poner el file del slave en /etc/bind/. El directorio es root:bind con solo lectura para el grupo y named corre como usuario bind, así que el guardado falla con 'dumping master file: /etc/bind/tmp-XXXX: open: permission denied'. Peor aún, la zona puede responder mientras el proceso vive y el síntoma recién aparece al reiniciar: zona vacía y SERVFAIL. Va en /var/cache/bind/.
- Modificar la zona en ns1 sin incrementar el serial. El master no manda NOTIFY y el slave, al chequear, concluye que está al día: el cambio no se propaga nunca. Diagnóstico de dos comandos: dig @ns1 SOA +short y dig @ns2 SOA +short; si los seriales coinciden, el problema está en ns1, no en ns2.
- Olvidar allow-transfer en ns1. Por defecto BIND9 no autoriza transferencias a nadie: ns1 responde REFUSED al AXFR y en el log de ns2 aparece 'failed while receiving responses: REFUSED'. El síntoma es que el archivo en /var/cache/bind nunca se crea.
- Resolver el ejercicio con un forwarder en vez de un secundario. El forwarder no tiene la zona: responde sin flag aa y deja de responder cuando se le vence el TTL de lo cacheado, así que no cumple ni (a) ni (d).
- Escribir a mano el archivo de zona en ns2, con type master en los dos servidores. Resuelve igual y parece andar, pero no hay transferencia ninguna y el punto (c) falla: el cambio en ns1 no llega a ns2. El enunciado lo prohíbe explícitamente.
- Declarar un solo NS en la zona (solo ns1). La zona carga, pero ns1 no tiene a quién notificar y ningún resolver descubre a ns2, así que la redundancia no existe aunque la transferencia funcione. Además, cada NS necesita su propio registro A dentro de la zona.

_Fuente: reconstruido a partir de indicios sobre el próximo parcial_

---

### Ejercicio 2: Zona BIND con TTL por registro, A heredado y MX copiado de otro dominio

_[2022 2C]_

**Enunciado**

> Ejercicio
>
> Instale un servidor de nombres BIND y realice los cambios necesarios para servir la zona demiloo.com.ar considerando que:
>
> 1. su servidor de DNS será la autoridad para estas zonas
> 2. la dirección de contacto con la persona responsable por la zona es demiloo@leak.com.ar
> 3. demiloo.com.ar debe tener un registro A apuntando a la dirección IP de foo.leak.com.ar. El registro tendrá una validez de 1 hora.
> 4. el subdominio www debe ser registros CNAME apuntando a la raíz de la zona. Los registros tendrán una validez de 2 semanas.
> 5. la zona debe poder recibir correos electrónicos. El correo electrónico será atendido por los mismos servidores de correo que atienden los del dominio proto.leak.com.ar (manteniendo el orden). Estos registros tendrán una validez de 1 semana.
>
> Deberá demostrar la correcta configuración utilizando dig.

**Pista:** Tres de los cinco puntos te piden ir a buscar un dato a OTRO dominio con dig antes de poder escribir tu zona: la IP de foo.leak.com.ar (punto 3) y los MX de proto.leak.com.ar con sus prioridades (punto 5). Y 'validez' significa TTL por registro, no el TTL global.

**Resolución paso a paso**

**1. Leé la consigna y separá lo que sabés de lo que tenés que ir a buscar**

Este ejercicio es tramposo porque tres puntos dependen de datos que NO están en el enunciado: la IP real de foo.leak.com.ar y los servidores de mail de proto.leak.com.ar. Si te ponés a escribir la zona de una sin esos datos, te la vas a tener que rehacer. Primero identificá qué tenés que resolver con dig y recién después tocás el archivo.

```
Punto 2 → el mail de contacto va en el SOA, con el @ convertido en punto
Punto 3 → A de demiloo.com.ar = la IP que devuelva  dig A foo.leak.com.ar
Punto 4 → www CNAME a la raiz (@), TTL 2 semanas
Punto 5 → copiar los MX de proto.leak.com.ar con sus mismas prioridades, TTL 1 semana
```

**2. Instalá BIND y buscá los datos que faltan con dig**

Necesitás la IP de foo.leak.com.ar porque el punto 3 pide un registro A (una IP), no un CNAME: en el apex de una zona no se puede poner CNAME, y además te piden que apunte a 'la dirección IP de', o sea que resuelvas vos el nombre y copies el número. Y necesitás los MX de proto.leak.com.ar con sus prioridades para poder copiarlos 'manteniendo el orden'.

```
sudo apt install bind9 bind9utils

# La IP para el registro A (punto 3)
dig +short A foo.leak.com.ar

# Los MX con su prioridad para copiarlos tal cual (punto 5)
dig +short MX proto.leak.com.ar
#   -> ej:  10 smtp20.proto.leak.com.ar.
#           20 smtp30.proto.leak.com.ar.
```

_Verificación:_ Los dos dig tienen que devolver algo. Si el MX devuelve dos líneas con prioridades distintas, anotá las dos con su número, porque 'mantener el orden' quiere decir respetar esas prioridades.

**3. Declará la zona en named.conf.local**

BIND no sabe que sos autoridad de demiloo.com.ar hasta que se lo decís. 'type master' es lo que te hace la autoridad (punto 1): respondés con el flag aa, no sos un caché ni un forwarder. El 'file' apunta al archivo de zona que vas a crear en el paso siguiente.

```
# en /etc/bind/named.conf.local
zone "demiloo.com.ar" {
    type master;
    file "/etc/bind/demiloo.com.ar";
};
```

**4. Escribí el archivo de zona con el SOA y el TTL global**

El $TTL es el TTL por defecto que se aplica a todo registro que NO tenga uno propio. Como acá CASI TODOS los registros tienen su propia validez, el $TTL global casi no se usa, pero igual tiene que estar o BIND se queja. En el SOA, el segundo campo es el mail de contacto (punto 2): demiloo@leak.com.ar se escribe demiloo.leak.com.ar. con el @ cambiado por punto y el punto final, porque en un archivo de zona el @ tiene otro significado (es la raíz de la zona).

```
# /etc/bind/demiloo.com.ar
$ORIGIN demiloo.com.ar.
$TTL 1h

@ IN SOA ns.demiloo.com.ar. demiloo.leak.com.ar. (
        2022010101 ; serial (subilo en cada cambio)
        7d         ; refresh
        1d         ; retry
        14d        ; expire
        1h )       ; negative TTL
```

_Verificación:_ El mail de contacto NO lleva @. Si escribiste demiloo@leak.com.ar. adentro del SOA, está mal: va demiloo.leak.com.ar.

**5. Agregá los NS y el A del apex con TTL de 1 hora**

La zona necesita al menos un registro NS que diga quién es su servidor de nombres, y ese NS necesita su propio A para que se pueda resolver (si el NS apunta a un nombre sin A, la zona queda coja). El registro A del punto 3 va en el apex (@) y lleva la IP que sacaste con dig, con TTL de 1h escrito ANTES de la clase: el formato es 'nombre TTL IN tipo valor'. Por eso el A pide 1 hora de validez.

```
@    IN NS  ns
ns   IN A   1.2.3.4            ; la IP de tu servidor DNS

; punto 3: A del apex con la IP de foo.leak.com.ar, validez 1h
@    1h  IN A   76.76.21.241   ; <- la que devolvio dig +short A foo.leak.com.ar
```

_Verificación:_ named-checkzone demiloo.com.ar /etc/bind/demiloo.com.ar debería decir 'OK'. Si tira 'has no address records (A or AAAA)' en el NS, te falta el A de ns.

**6. Agregá el CNAME de www (2 semanas) y los MX (1 semana)**

El www es un CNAME a la raíz (punto 4): se escribe con @ como valor, que acá significa demiloo.com.ar. El TTL de 2 semanas va como 2w. Los MX (punto 5) se copian de proto.leak.com.ar MANTENIENDO el orden, o sea las mismas prioridades que te devolvió el dig; el número menor es el de mayor prioridad. TTL de 1 semana = 1w. Ojo: el valor de un MX es un NOMBRE, nunca una IP.

```
; punto 4: www CNAME a la raiz, validez 2 semanas
www  2w  IN CNAME  @

; punto 5: mismos MX que proto.leak.com.ar, mismo orden, validez 1 semana
@    1w  IN MX  10 smtp20.proto.leak.com.ar.
@    1w  IN MX  20 smtp30.proto.leak.com.ar.
```

_Verificación:_ Fijate que las prioridades (10, 20) sean las MISMAS que devolvió dig +short MX proto.leak.com.ar. Si las invertís, cambiás cuál servidor se intenta primero y perdés el punto.

**7. Validá y recargá**

named-checkzone te atrapa los errores de sintaxis ANTES de reiniciar, así no te quedás con el servicio caído sin darte cuenta. Si validás después de reiniciar, podés estar sirviendo una zona rota. Primero checkzone, después reload.

```
sudo named-checkconf
sudo named-checkzone demiloo.com.ar /etc/bind/demiloo.com.ar
sudo systemctl reload bind9   # o named, segun la distro
```

_Verificación:_ named-checkzone tiene que terminar en 'OK'. systemctl status bind9 tiene que decir active (running), no failed.

**8. Demostrá con dig cada punto de la consigna**

La consigna pide explícitamente demostrarlo con dig, así que esto NO es opcional: es parte del puntaje. Preguntás a tu propio servidor (@localhost) cada registro y verificás no solo el valor sino el TTL, porque los TTL por registro son medio punto del ejercicio.

```
dig @localhost demiloo.com.ar SOA
dig @localhost demiloo.com.ar A      ; validez 3600 (1h)
dig @localhost www.demiloo.com.ar    ; CNAME -> demiloo.com.ar, 1209600 (2w)
dig @localhost demiloo.com.ar MX     ; dos MX, 604800 (1w), prioridades 10 y 20
```

_Verificación:_ En la columna TTL de cada respuesta tienen que aparecer: 3600 para el A, 1209600 para el CNAME, 604800 para los MX. Y la respuesta tiene que venir con el flag 'aa' (authoritative answer) en la sección de flags, que confirma el punto 1.

**Errores típicos**

- Poner un CNAME en el apex (@) para el punto 3 en vez de un A: en el apex no se puede CNAME, y además te piden la IP de foo.leak.com.ar, que tenés que resolver vos con dig y copiar como número.
- Escribir el mail de contacto del SOA como demiloo@leak.com.ar: adentro del archivo de zona el @ es la raíz, así que el mail va con punto: demiloo.leak.com.ar.
- Poner el TTL por registro en el lugar equivocado: el formato es 'nombre TTL IN tipo valor', el TTL va entre el nombre y la clase IN.
- Copiar los MX con prioridades inventadas o invertidas: 'manteniendo el orden' significa usar las mismas prioridades que devuelve dig MX proto.leak.com.ar.
- Olvidarse de verificar los TTL en la salida de dig: el ejercicio los pide explícitos (1h, 2w, 1w) y es ahí donde está la mitad del puntaje.
- Apuntar el MX a una IP: el valor de un MX siempre es un nombre, que después se resuelve con su propio A.

_Fuente: Práctico 2022 2C, recopilación de ejercicios prácticos (PDF del alumno), zona demiloo.com.ar_

---

### Ejercicio 3: Depurar un archivo de zona BIND9 roto y acelerar su propagación

_[2025 2C]_

**Enunciado**

> Ejercicio III
>
> Usted es el administrador de un servidor DNS que utiliza BIND9 y es autoritativo para la zona it.itba.edu.ar. Recientemente, tras una serie de cambios, los clientes han reportado que la resolución de nombres para todos los hosts dentro de esa zona (como pampero.it.itba.edu.ar) ha dejado de funcionar. Sus navegadores arrojan problemas al resolver nombres, y administradores de DNS de los ISP lo han contactado por problemas en la zona.
>
> Al revisar los registros del sistema, observa que el servicio BIND9 se queja de múltiples errores al intentar cargar el archivo de zona, ubicado en /etc/bind/db.it.itba.edu.ar. (Disponible en http://protos.foo/zona)
>
> Inspeccione el archivo de zona, identifique y corrija todos los errores de sintaxis y de configuración lógica que impiden su correcto funcionamiento. Una vez corregidos los errores, haga los cambios necesarios en el registro SOA para asegurar que los cambios se propaguen de manera correcta y rapido.
>
> Archivo de zona entregado:
>
> $TTL 604800
> @ IN SOA ns1.it.itba.edu.ar. admin@it.itba.edu.ar. (
>         2025060500     ; Serial
>         604800         ; Refresh
>         86400          ; Retry
>         2419200        ; Expire
>         604800         ; Negative Cache TTL
>     )
> ; Name Servers
> @      IN NS ns1.it.itba.edu.ar.
>
> ; Mail
> @      IN MX 10 mail
>
> ; Hosts
> ns1.it.itba.edu.ar      IN A 192.168.70.1
> pampero                 IN A 192.168.70.10
>
> ; Aliases
> www          IN A pampero
> correo       IN CNAME pampero
>
> [En la captura los comentarios ';' del SOA aparecen desfasados una línea respecto de sus valores y el paréntesis de cierre se lee ambiguo (puede ser '}' en lugar de ')'); si en tu hoja figura '}', ese es un error de sintaxis más a corregir.]

**Pista:** Un registro A tiene que apuntar a una dirección IP: fijate qué está apuntando a un nombre, y qué nombre quedó sin ningún A que lo resuelva.

**Resolución paso a paso**

**1. Bajá el archivo y hacé que BIND te liste los errores**

named-checkzone parsea la zona igual que el demonio pero sin tocar el servicio, y te dice línea por línea qué está mal. Es muchísimo más rápido que leer el archivo a ojo, y además te garantiza que no te quedó ninguno suelto al final.

```
curl -o /etc/bind/db.it.itba.edu.ar http://protos.foo/zona
named-checkzone it.itba.edu.ar /etc/bind/db.it.itba.edu.ar
named-checkconf
```

_Verificación:_ Al principio va a tirar varios errores; al final tiene que decir 'OK'

**2. Arreglá el SOA: el mail con '@' y el $ORIGIN faltante**

En un archivo de zona el '@' es un metacaracter que significa 'el origen de la zona', así que admin@it.itba.edu.ar se parsea mal. La convención es reemplazar la arroba del mail por un punto. Y sin $ORIGIN declarado no queda explícito a qué dominio se expanden los nombres relativos: BIND usa el nombre de zona del named.conf, así que técnicamente anda, pero es mala práctica y se presta a confusión.

```
$ORIGIN it.itba.edu.ar.
$TTL 604800

@ IN SOA ns1.it.itba.edu.ar. admin.it.itba.edu.ar. (
        2025060500     ; Serial
        604800         ; Refresh
        86400          ; Retry
        2419200        ; Expire
        604800         ; Negative Cache TTL
)
```

_Verificación:_ named-checkzone ya no se queja del SOA

**3. Corregí www: un A no puede apuntar a un nombre**

El registro A mapea nombre → dirección IPv4, y 'pampero' no es una IP. Para que un nombre apunte a otro nombre el tipo correcto es CNAME. Este es el error que, junto con el del SOA, rompe la carga entera de la zona: si BIND no carga el archivo, *ningún* nombre de la zona resuelve, que es exactamente el síntoma que describe el enunciado.

```
; Aliases
www          IN CNAME pampero
correo       IN CNAME pampero
```

_Verificación:_ dig www.it.itba.edu.ar @localhost → CNAME a pampero y después el A

**4. Agregá el A que le falta al destino del MX**

El MX apunta a 'mail', pero no hay ningún registro que resuelva mail.it.itba.edu.ar a una IP. Un MX tiene que apuntar a un nombre con registro A (y nunca a un CNAME): sin eso, ningún servidor de mail del mundo sabe a qué IP entregar el correo de la zona, y por eso te llamaron los ISP.

```
; Mail
@      IN MX 10 mail
mail   IN A 192.168.70.20    ; la IP real del servidor de mail
```

_Verificación:_ dig MX it.itba.edu.ar @localhost → el ADDITIONAL SECTION tiene que traer el A de mail

**5. Arreglá ns1 en la sección Hosts: le falta el punto final**

Un nombre sin punto final es relativo y BIND le concatena el $ORIGIN, así que 'ns1.it.itba.edu.ar' se convierte en ns1.it.itba.edu.ar.it.itba.edu.ar. Resultado: el NS declarado arriba (que sí tiene punto, o sea es absoluto) queda sin registro A que lo resuelva. Se arregla poniendo el punto, o directamente escribiendo solo 'ns1'.

```
; Hosts
ns1.it.itba.edu.ar.     IN A 192.168.70.1
; o, más simple y menos propenso a error:
; ns1                   IN A 192.168.70.1
pampero                 IN A 192.168.70.10
```

_Verificación:_ dig ns1.it.itba.edu.ar @localhost → 192.168.70.1, sin el dominio duplicado

**6. Tocá el SOA para que los cambios se propaguen rápido**

Esto es la segunda consigna, y se olvida seguido. Los slaves solo se enteran de que hay cambios si el serial subió: con el mismo serial, el archivo corregido nunca se transfiere. Y con Refresh en 604800 (una semana) el slave recién preguntaría dentro de siete días: bajándolo (y bajando el $TTL) los caches se renuevan en minutos en vez de días.

```
$TTL 300            ; antes 604800 (1 semana) → 5 min

@ IN SOA ns1.it.itba.edu.ar. admin.it.itba.edu.ar. (
        2026072301     ; Serial: subido (formato YYYYMMDDnn)
        300            ; Refresh: 5 min, antes 1 semana
        300            ; Retry
        2419200        ; Expire
        300            ; Negative Cache TTL
)
```

_Verificación:_ dig SOA it.itba.edu.ar @localhost → serial nuevo y refresh bajo

**7. Recargá BIND y verificá cada tipo de registro**

Editar el archivo no cambia nada hasta que el demonio lo relee. Y hay que verificar registro por registro: que la zona cargue no garantiza que el MX o el CNAME estén bien, solo que la sintaxis pasa.

```
named-checkzone it.itba.edu.ar /etc/bind/db.it.itba.edu.ar
systemctl reload bind9   # o: rndc reload it.itba.edu.ar

dig SOA it.itba.edu.ar @localhost
dig A pampero.it.itba.edu.ar @localhost
dig CNAME www.it.itba.edu.ar @localhost
dig MX it.itba.edu.ar @localhost
dig A mail.it.itba.edu.ar @localhost
```

_Verificación:_ Todas las consultas con status: NOERROR y una ANSWER SECTION no vacía

**Errores típicos**

- Dejar 'www IN A pampero': un A exige una IP, el alias va con CNAME.
- Corregir la sintaxis pero no agregar el registro A de mail: el MX queda apuntando a un nombre que no resuelve.
- No notar el punto final faltante en ns1.it.itba.edu.ar: queda ns1.it.itba.edu.ar.it.itba.edu.ar y el NS no resuelve.
- Corregir todo y olvidarse de subir el serial: los slaves nunca transfieren la zona corregida.
- Dejar el Refresh y el $TTL en 604800: aunque esté todo bien, la propagación tarda una semana y la consigna pide que sea rápida.

_Fuente: 2025_2c_practico.md_

---

### Ejercicio 4: Zona foo.example.org en BIND: A copiado de otro host, MX copiados de otro dominio y SPF

_[2026 1C]_

**Enunciado**

> EJERCICIO I
> Instale un servidor de nombres bind y realice los cambios necesarios para servir la zona foo.example.org considerando que:
> - su servidor dns será la autoridad para estas zonas
> - la dirección de contacto con la persona responsable por la zona es example.admin@foo.com.ar
> - fooo.example.org debe tener un registro A apuntando a la dirección ip de bar.protos.foo. El registro tendrá una validez de 1 hora.
> - el subdominio www debe ser un registro CNAME apuntando a la raíz de la zona. Los registros tendrán una validez de 1 semana.
> - la zona debe poder recibir correos electrónicos. El correo electrónico será atendido por los mismos servidores de correo que atienden los del dominio mail.protos.foo (manteniendo el orden). Estos registros tendrán una validez de 1 semana.
> - El servidor con la IP 1.2.3.4 es el único autorizado a enviar correos para esta zona.
> Se deben cumplir todas las condiciones de forma simultánea.
>
> [Nota: "fooo.example.org" es casi seguro un typo de la consigna por foo.example.org, la raíz de la zona. fooo.example.org literal ni siquiera pertenece a la zona foo.example.org, así que no podrías crearlo ahí. Si te pasa en el examen, preguntá; si no, poné el A en la raíz (@) y aclaralo por escrito.]

**Pista:** Dos de los valores no te los dan: te dicen de dónde copiarlos. ¿Qué consulta te devuelve la IP de bar.protos.foo y cuál los servidores de correo de mail.protos.foo con su orden?

**Resolución paso a paso**

**1. Declará la zona en named.conf.local**

BIND no descubre archivos de zona solo: hasta que no hay un bloque zone que le diga el nombre y la ruta del archivo, el servidor no es autoritativo para nada y responde REFUSED. type master significa que este servidor tiene la copia original, no una transferida de otro.

```
// /etc/bind/named.conf.local
zone "foo.example.org" {
    type master;
    file "/etc/bind/foo.example.org";
};
```

_Verificación:_ named-checkconf → sin salida significa que está OK

**2. Escribí el encabezado del archivo de zona: $ORIGIN, $TTL y SOA**

$ORIGIN fija a qué dominio se expanden los nombres relativos, y $TTL es el TTL por defecto de los registros que no traen uno propio. La consigna no pide un TTL por defecto: cualquier valor razonable sirve (el repo usa 1m), porque los registros que sí tienen TTL pedido lo llevan explícito. En el mail del responsable la '@' se reemplaza por '.', pero como el mail ya tiene un punto antes de la arroba (example.admin@), ese punto se escapa con '\.' para que BIND no lo tome como separador de labels.

```
; /etc/bind/foo.example.org
$ORIGIN foo.example.org.
$TTL 1m

@ IN SOA ns.foo.example.org. example\.admin.foo.com.ar. (
        2026052801   ; Serial (YYYYMMDDnn)
        7d           ; Refresh: cada cuánto el slave le pregunta al master
        1d           ; Retry: si falla el refresh, cada cuánto reintenta
        10d          ; Expire: cuándo el slave deja de responder si no contacta al master
        1m           ; Negative TTL: cuánto se cachea un NXDOMAIN
)
```

**3. Averiguá los valores que la consigna te manda a copiar**

La consigna no te da ni la IP del A ni los nombres de los MX: te dice 'la IP de bar.protos.foo' y 'los mismos servidores que mail.protos.foo, manteniendo el orden'. Eso se resuelve preguntándole al DNS real, no inventando. Para los MX, 'manteniendo el orden' significa copiar también las prioridades tal cual: son las que definen qué servidor se intenta primero.

```
dig +short A bar.protos.foo
# → anotá la IP (en la solución del repo aparece 123.123.123.123)

dig MX mail.protos.foo
# → copiá cada MX con su prioridad exacta
# (en la solución del repo: 10 mx1.protos.foo. y 5 mx2.protos.foo.)
```

_Verificación:_ Tenés anotada una IP y una lista de MX con sus números. Si dig no responde, revisá /etc/resolv.conf antes de seguir: sin esos datos no podés completar la zona

**4. Agregá el NS y su registro A**

'Su servidor dns será la autoridad' se traduce en un registro NS que apunta a tu servidor, más el A de ese nombre. Ojo: la IP del A de ns tiene que ser la de TU máquina (la que corre BIND), no la 1.2.3.4. La 1.2.3.4 de la consigna es la del que manda mail (va en el SPF); la solución del repo reusa 1.2.3.4 para ns y eso mezcla dos cosas distintas. Declarar el NS sin su A deja la zona sin forma de llegar al servidor autoritativo, y named-checkzone te avisa con un warning.

```
@   IN NS ns
ns  IN A  <IP de tu VM>      ; ip -4 addr para verla; en un lab cerrado 127.0.0.1 también sirve
```

_Verificación:_ dig NS foo.example.org @localhost → ns.foo.example.org. y el A en ADDITIONAL

**5. Agregá los MX copiados de mail.protos.foo con TTL de 1 semana**

Ponés exactamente los mismos servidores y prioridades que te devolvió dig MX mail.protos.foo: eso es 'manteniendo el orden'. El TTL por registro va entre el nombre y la clase (7d = 604800 s) y pisa el $TTL global. En MX el número menor es la prioridad más alta. Los nombres terminan en punto porque son externos a la zona: sin el punto se les pega foo.example.org. al final.

```
@   7d IN MX 10 mx1.protos.foo.
@   7d IN MX 5  mx2.protos.foo.
```

_Verificación:_ dig MX foo.example.org @localhost → los dos MX con TTL 604800 y prioridades 5 y 10

**6. Agregá el A de la raíz, el CNAME de www y el TXT con el SPF**

El A de la raíz lleva la IP de bar.protos.foo con TTL 1h. Copiás la IP, no ponés un CNAME a bar.protos.foo: la consigna pide un registro A, y además en la raíz de una zona no puede ir un CNAME porque convive con SOA, NS y MX. www sí es CNAME a la raíz (@) con TTL 7d. El TXT con v=spf1 ip4:1.2.3.4 -all dice que solo esa IP manda mail por el dominio; '-all' es estricto (rechazar lo demás), '~all' sería solo marcarlo como sospechoso. El TTL del TXT no está pedido, así que usa el $TTL por defecto.

```
@      1h  IN A     <IP de bar.protos.foo>
www    7d  IN CNAME @

@          IN TXT   "v=spf1 ip4:1.2.3.4 -all"
```

**7. Validá la zona y reiniciá BIND**

named-checkzone parsea el archivo sin tocar el servicio: si hay un paréntesis sin cerrar o un punto de más, te lo dice acá en vez de dejarte el demonio caído. Recién después reiniciás, porque editar el archivo no cambia nada hasta que el demonio lo relee.

```
named-checkconf
named-checkzone foo.example.org /etc/bind/foo.example.org
systemctl restart bind9
systemctl status bind9
```

_Verificación:_ named-checkzone imprime 'OK' y el servicio queda active (running)

**8. Verificá cada tipo de registro con dig**

Que la zona cargue solo prueba que la sintaxis es válida, no que los valores sean los pedidos. Consultando tipo por tipo contra @localhost confirmás TTLs, prioridades y destinos, que es lo que efectivamente se corrige.

```
dig SOA   foo.example.org @localhost
dig A     foo.example.org @localhost
dig A     bar.protos.foo            # la IP tiene que coincidir con la de arriba
dig NS    foo.example.org @localhost
dig CNAME www.foo.example.org @localhost
dig MX    foo.example.org @localhost
dig MX    mail.protos.foo           # mismos servidores, mismas prioridades
dig TXT   foo.example.org @localhost
```

_Verificación:_ Todas con status: NOERROR. El A de la raíz igual al de bar.protos.foo con TTL 3600; MX idénticos a los de mail.protos.foo con TTL 604800; www con TTL 604800

**Errores típicos**

- Inventar la IP del A o los MX en vez de consultarlos: la consigna dice 'la IP de bar.protos.foo' y 'los mismos que mail.protos.foo'; lo que se corrige es que coincidan con dig.
- Cambiar las prioridades de los MX al copiarlos: 'manteniendo el orden' es copiar los números tal cual.
- Poner un CNAME en la raíz apuntando a bar.protos.foo: la consigna pide un A, y un CNAME no puede convivir con SOA/NS/MX en el apex.
- Usar la 1.2.3.4 como IP del servidor de nombres: esa IP es la del que manda mail (SPF), no la de tu BIND.
- No escapar el punto del mail del SOA: sin '\.' se interpreta como el usuario 'example' en el dominio admin.foo.com.ar.
- Olvidar el punto final en los nombres de los MX: BIND les concatena el $ORIGIN y quedan mx1.protos.foo.foo.example.org.
- Editar el archivo de zona y no reiniciar/recargar bind9: el demonio sigue sirviendo la versión en memoria.

_Fuente: Enunciado real: parcial práctico 2026 1C (PDF). Solución de referencia: Parcial 2026 1C.md del repo_

---


### Ejercicio extra: restaurar una zona DNS desde un PCAP

Cachito borró todos los archivos de configuración de la zona y el cronjob de backups no corre desde 2018. Por suerte quedó una captura PCAP con queries y replies del namemaster, descargable de `http://192.168.0.238:8000/backup.pcapng`. Hay que restaurar la configuración original con bind9 y agregar un TXT que diga "cachito tiene prohibido editar este archivo". (Aclaración de la cátedra: si ya tenés un BIND configurado es más fácil; el puerto es el 8000.)

**La idea:** DNS no guarda historia, así que la única fuente para reconstruir la zona son las respuestas **autoritativas** que quedaron en la captura (flag `aa`): vienen del namemaster, no de una caché, por lo que sus TTL son los configurados. Si hay un AXFR (transferencia de zona), la zona entera está en esa única transferencia.

**1. Bajar la captura**

```
mkdir -p ~/Documents/dns/cachito && cd ~/Documents/dns/cachito
curl -O http://192.168.0.238:8000/backup.pcapng
```

**2. Sacar los registros de la captura**

Primero fijate si hay transferencia de zona (AXFR): si está, tenés la zona completa en un solo stream.

```
# ¿hay AXFR? (tipo 252)
tshark -r backup.pcapng -Y 'dns.qry.type == 252'
# si aparece, seguí el stream TCP:
tshark -r backup.pcapng -Y 'dns.qry.type == 252' -z follow,tcp,ascii,0 -q
```

Si **no** hay AXFR, mirá las respuestas autoritativas del namemaster y desplegá cada paquete:

```
tshark -r backup.pcapng -Y 'dns.flags.response == 1 && dns.flags.authoritative == 1' -V | less
```

(En Wireshark gráfico: filtro `dns.flags.response == 1 && dns.flags.authoritative == 1`, y leés Answers, Authoritative nameservers y Additional records en el panel del medio.)

Anotá de cada registro **nombre, TTL, tipo y valor**:
- **SOA:** sale de la respuesta a una consulta SOA y de la sección Authority de las respuestas NXDOMAIN. De ahí salen el primary NS, el mail del responsable, serial, refresh, retry, expire y minimum.
- **NS** y la IP de cada NS (en Additional).
- **A, AAAA, CNAME, MX** (con su preferencia), **TXT, SRV**.
- **PTR:** si hay consultas `x.x.x.x.in-addr.arpa`, también restaurá la zona inversa.

Usá los TTL de las respuestas autoritativas: las que pasaron por una caché vienen con el TTL ya descontado.

**3. Escribir el archivo de zona**

```
sudo cp /etc/bind/db.local /etc/bind/db.ZONA   # plantilla; db.cachito.com ya no existe
sudo nano /etc/bind/db.ZONA
```

```
$TTL <TTL más común en la captura>
$ORIGIN ZONA.
@ IN SOA <primary NS>. <mail con . en vez de @>. (
  <serial original + 1>
  <refresh>
  <retry>
  <expire>
  <minimum> )
@   <ttl> IN NS ns1.ZONA.
ns1 <ttl> IN A <ip>
@   <ttl> IN A <ip>
www <ttl> IN CNAME @
@   <ttl> IN MX 10 mail.ZONA.
mail <ttl> IN A <ip>
; un registro por cada uno que aparezca en la captura
@ IN TXT "cachito tiene prohibido editar este archivo"
```

Los nombres absolutos terminan en punto (sin el punto BIND les agrega el `$ORIGIN`). En el mail del SOA la `@` se escribe como punto, y si el usuario tiene un punto se escapa con `\.`. Los valores del SOA son los originales, salvo el **serial, que sube en uno** porque se agregó el TXT.

**4. Declarar la zona y levantar BIND**

```
sudo nano /etc/bind/named.conf.local
```

```
zone "ZONA" {
  type master;
  file "/etc/bind/db.ZONA";
  allow-transfer { 127.0.0.1; };
};
```

```
sudo named-checkconf
sudo named-checkzone ZONA /etc/bind/db.ZONA
sudo systemctl restart named   # si falla: journalctl -u named -n 30
```

**5. Verificar**

```
dig @127.0.0.1 ZONA AXFR
dig @127.0.0.1 ZONA TXT +short
dig @127.0.0.1 www.ZONA +noall +answer
```

Consultá los mismos nombres que aparecen en la captura y compará valor y TTL. Las respuestas tienen que traer el flag `aa`.

**Errores típicos**

- `sudo cp /etc/bind/db.cachito.com ...`: ese archivo lo borró Cachito, no existe. Partí de `db.local`.
- Contar con Wireshark gráfico: la VM del lab puede no tenerlo o no tener entorno gráfico. `tshark -V` hace lo mismo desde la terminal.
- Inventar registros que no están en la captura: si no hay AXFR, solo podés recuperar los nombres que alguien consultó. Restaurá lo que aparece y nada más.
- No subir el serial: los secundarios no se enteran del cambio (el TXT agregado).
- Tomar TTL de respuestas sin `aa`: vienen de caché, con el TTL descontado; no son los configurados.

_Fuente: enunciado de parcial (restaurar zona desde PCAP). Relacionado: Zona master (arriba), Ejercicio 2 y Ejercicio 4, y "Resueltos — Capturas (Wireshark)" en 07-machete-parciales-resueltos.md._
