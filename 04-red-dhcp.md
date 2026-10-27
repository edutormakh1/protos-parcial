# Red: rutas, nmap y DHCP

> Cheatsheet de PROTOS (72.07, ITBA) para el parcial práctico. Repaso del tema y, al final, el paso a paso de todos los ejercicios de parciales que caen acá.

---

## Cheatsheet

### route -n vs ip route

| Qué | Clásico | Moderno |
|---|---|---|
| Ver la tabla | `route -n` | `ip route` |
| Ruta a una red | `route add -net 10.0.0.0/24 gw 192.168.1.3` | `ip route add 10.0.0.0/24 via 192.168.1.3` |
| Ruta default | `route add default gw 192.168.1.1` | `ip route add default via 192.168.1.1` |
| Qué ruta usa una IP | — | `ip route get 10.0.0.5` |

En `route -n`: `Destination 0.0.0.0` = default; `Gateway 0.0.0.0` (o `*`) = red conectada directa; gana la máscara más específica.

### Receta: descubrir y escanear un servidor detrás de un gateway

El ejercicio que más se repite (2024 ej3, 2025 ej2). Siempre los mismos 6 pasos:

```
ip addr  # 1. mi IP (en VM: adaptador en modo Bridge)
ping -c 3 192.168.50.250
sudo ip route add 10.0.0.0/24 via 192.168.50.250  # 2. ruta a la red remota
ip route get 10.0.0.10  # 3. → via 192.168.50.250
sudo nmap -sn 10.0.0.0/24  # 4. quién está vivo (gateway + servidor)
sudo nmap -sV -Pn -p- 10.0.0.X  # 5. qué servicios expone
curl -v http://10.0.0.X:PUERTO/  # 6a. entregable: el HTTP (puerto de nmap)
traceroute 10.0.0.X  # 6b. entregable: los routers (1er salto = 192.168.50.250)
```

| Síntoma | Causa |
|---|---|
| `Network is unreachable` | el gateway no está en tu red: revisá tu IP y el Bridge |
| nmap -sn no encuentra nada | falta la ruta, o ICMP filtrado → `-PS22,80,443` |
| traceroute sin 192.168.50.250 primero | la ruta no se aplica (`ip route get`) |
| curl colgado | puerto equivocado: usá el de nmap |

### nmap — flags que caen

```
sudo nmap -sn 10.0.0.0/24  # host discovery (sin puertos)
sudo nmap -sV -Pn -p- <ip>  # servicio+versión, aunque no responda ping, todos los puertos
sudo nmap -sS <ip>  # SYN scan (rápido, necesita sudo); sin sudo: -sT
sudo nmap --script broadcast-dhcp-discover  # buscar un servidor DHCP
```

Estados: **open** (algo escucha), **closed** (RST), **filtered** (sin respuesta, firewall).

### Servidor DHCP con reserva (2025 ej1)

```
# /etc/dhcp/dhcpd.conf
subnet 192.168.100.0 netmask 255.255.255.0 {
    range 192.168.100.10 192.168.100.50;
    option routers 192.168.100.1;
    option domain-name-servers 8.8.8.8;

    host maquina_h {  # IP fija por MAC
        hardware ethernet aa:bb:cc:dd:ee:ff;
        fixed-address 192.168.100.5;
    }
}
```

1. La interfaz que mira a los clientes con IP fija de esa subred: `ip addr add 192.168.100.1/24 dev enp0s8`. Si NetworkManager la pisa, `systemctl stop NetworkManager`.
2. El bloque `subnet` coincide con esa red.
3. En `/etc/default/isc-dhcp-server`: `INTERFACESv4="enp0s8"`.
4. Validar `sudo dhcpd -t`, después `systemctl restart isc-dhcp-server`. Cliente: `sudo dhclient -v enp0s8`.

### NAT (lo justo)

```
sysctl net.ipv4.ip_forward=1  # que el host rutee
iptables -t nat -A POSTROUTING -o <if> -j MASQUERADE  # SNAT: salida enmascarada
iptables -t nat -A PREROUTING -p tcp --dport <p> -j DNAT --to <ip>:<p>  # DNAT: exponer un servicio
```

SNAT/MASQUERADE en POSTROUTING (después del ruteo), DNAT en PREROUTING (antes).

### VirtualBox: modos de red

| Modo | Qué da |
|---|---|
| Bridge (Puente) | la VM es una máquina más de la red, con su propia IP. El que piden para los ejercicios de red |
| NAT | sale a internet con la IP del host; no accesible desde afuera |
| Internal | solo las VMs entre sí, sin host ni internet |
| Host-Only | red privada host ↔ VMs, sin internet |

Si el SO te pisa la config de red: `sudo systemctl stop NetworkManager`. Subnetting: hosts usables = `2^n − 2` (/24=254, /26=62, /27=30, /28=14, /30=2). Privadas: `10/8`, `172.16–31`, `192.168/16`; loopback `127/8`.

---

## Ejercicios paso a paso

### Ejercicio 1: Escanear servicios de un host detrás de un gateway y trazar la ruta

_[2024 2C]_

**Enunciado**

> Ejercicio III
>
> El host en 192.168.1.3 es el gateway de la red 10.0.0.0/24. Descubra los servicios que tiene expuesto el host 10.0.0.1 y, de existir, muestre el resultado de acceder vía HTTP a dicho servidor. Muestre también todos los routers que intervienen en esta comunicacion.
>
> Nota: Recomendamos utilizar una VM para resolver este ejercicio, pero no es necesario de poder hacerlo en su Host. De usar una VM, el adaptador de red debe estar en modo Bridge (Puente) conectado a la red abierta Lab.

**Pista:** Tu máquina no tiene ninguna interfaz en 10.0.0.0/24: ¿cómo sabe el kernel por dónde mandar esos paquetes?

**Resolución paso a paso**

**1. Poné la VM en modo Bridge y confirmá que llegás al gateway**

Con el adaptador en NAT la VM no ve la red 192.168.1.x del laboratorio y nada de lo que sigue funciona. El ping al gateway es el chequeo más barato de que estás en la red correcta antes de perder tiempo tocando rutas.

```
ip addr show
ping -c 3 192.168.1.3
```

_Verificación:_ Tiene que haber respuesta del 192.168.1.3 y tu IP tiene que ser 192.168.1.x

**2. Agregá la ruta hacia 10.0.0.0/24 por el gateway**

Este es el paso que decide el ejercicio. Sin la ruta, el kernel no sabe por dónde salir hacia 10.0.0.x: los manda por la default gateway (que no conoce esa red) o devuelve 'Network is unreachable', y ningún nmap ni curl posterior va a andar.

```
sudo ip route add 10.0.0.0/24 via 192.168.1.3
# equivalente clásico:
# sudo route add -net 10.0.0.0/24 gw 192.168.1.3
```

_Verificación:_ ip route | grep 10.0.0.0 → tiene que aparecer '10.0.0.0/24 via 192.168.1.3'

**3. Escaneá los servicios de 10.0.0.1 con nmap**

-Pn es imprescindible: nmap por defecto hace un ping previo y, si el host tiene ICMP bloqueado, lo da por caído y no escanea ni un puerto. -sV además te dice qué servicio y versión corre en cada puerto abierto, que es literalmente lo que pide la consigna.

```
sudo nmap -sS -Pn -sV 10.0.0.1
# si no aparece nada en los 1000 puertos por defecto:
sudo nmap -sS -Pn -sV -p- 10.0.0.1
```

_Verificación:_ Una tabla PORT/STATE/SERVICE/VERSION con al menos un puerto open

**4. Accedé por HTTP al puerto que encontró nmap**

La consigna pide mostrar el resultado del acceso HTTP, no solo que el puerto esté abierto. Ojo que el HTTP puede no estar en el 80: si nmap lo encontró en 8080 u otro, hay que poner el puerto explícito en la URL.

```
curl -v http://10.0.0.1
# o con el puerto que haya reportado nmap:
curl -v http://10.0.0.1:<puerto>
```

_Verificación:_ Un HTTP/1.1 200 OK con su cuerpo; guardá la salida para mostrarla

**5. Mostrá los routers que intervienen con traceroute**

traceroute lista hop por hop los routers que atraviesa el paquete, que es la segunda cosa que pide la consigna. El primer hop tiene que ser 192.168.1.3: si no lo es, la ruta que agregaste no se está usando.

```
traceroute 10.0.0.1
# si ICMP está filtrado, probá por TCP contra el puerto abierto:
sudo traceroute -T -p 80 10.0.0.1
```

_Verificación:_ La lista de hops, con 192.168.1.3 como primer salto

**Errores típicos**

- Saltear el ip route add y escanear directo: 'Network is unreachable', o nmap reporta todo filtered sin razón aparente.
- Correr nmap sin -Pn contra un host con ICMP bloqueado: nmap dice 'Host seems down' y no escanea nada.
- Dejar la VM en modo NAT en vez de Bridge: nunca ves la red 192.168.1.x del laboratorio.
- Asumir que el HTTP está en el puerto 80 y no probar el puerto que efectivamente reportó nmap.
- Mostrar solo el nmap y olvidarse del traceroute: la consigna pide explícitamente los routers que intervienen.

_Fuente: 2024_2c_practico.md_

---

### Ejercicio 2: Servidor DHCP con reserva por MAC y DNS de Google

_[2025 2C]_

**Enunciado**

> Ejercicio I
>
> Usted administra la red de una pequeña oficina (192.168.100.0/24) que utiliza un servidor DHCP para la asignación de direcciones IP. Configure el servidor para que cumpla con los siguientes requisitos
>   1. Una impresora de red crítica, con la dirección MAC 0B:08:27:AE:C8:6A, debe tener siempre la dirección IP estática 192.168.100.8 para que los scripts de impresión funcionen correctamente.
>   2. Se desea que todos los clientes tengan un servidor DNS siempre disponible, y que el proveedor sea confiable. Deben utilizar los servidores DNS de Google (8.8.8.8 y 8.8.4.4).
>   3. Se desea usar el rango de direcciones de 10-200 para asignar. El resto queda reservado para uso futuro.
>
> Verifique la configuracion con el siguiente comando, que le permite simular DHCP Request:
> sudo nmap --script broadcast-dhcp-discover \
>         --script-args "broadcast-dhcp-discover.mac=AA:BB:CC:DD:EE:FF"
>
> Aviso: No resuelva el ejercicio sobre una interfaz conectada a la red WIFI del Laboratorio. Hagalo en una red interna o interfaz virtual.
>
> [La red del enunciado se lee 192.168.100.0/24 en la imagen; la resolución del .md la trabaja como 192.168.108.0/24. El procedimiento es idéntico, solo cambia el tercer octeto: usá el que diga tu enunciado.]

**Pista:** El cliente manda un broadcast, no un paquete dirigido: ¿por qué interfaz lo escucha isc-dhcp-server y qué IP tiene esa interfaz?

**Resolución paso a paso**

**1. Pará NetworkManager e instalá isc-dhcp-server**

NetworkManager reconfigura las interfaces por su cuenta y te va a pisar la IP que le pongas a mano al rato, dejando el servidor escuchando en una interfaz sin dirección en la subred. Pararlo primero evita ese sabotaje silencioso.

```
sudo systemctl stop NetworkManager.service
sudo apt install isc-dhcp-server
```

_Verificación:_ systemctl is-active NetworkManager → inactive

**2. Dale una IP de la subred a la interfaz interna**

isc-dhcp-server se niega a servir una subnet declarada si ninguna interfaz tiene una dirección dentro de ella: arranca y muere con 'Not configured to listen on any interfaces'. Además hacelo sobre una interfaz virtual o de red interna, nunca sobre el WiFi del laboratorio, porque le repartirías IPs a toda la clase.

```
ip addr show
sudo ip addr add 192.168.100.1/24 dev enp0s9
```

_Verificación:_ ip addr show enp0s9 → tiene que figurar inet 192.168.100.1/24

**3. Escribí /etc/dhcp/dhcpd.conf con el pool y las opciones**

El bloque subnet define qué repartir: range acota el pool al 10-200 que pide el punto 3 y deja el resto libre, y option domain-name-servers es lo que resuelve el punto 2 (los clientes lo reciben en el DHCPOFFER y lo escriben en su resolv.conf).

```
subnet 192.168.100.0 netmask 255.255.255.0 {
    range 192.168.100.10 192.168.100.200;
    option domain-name-servers 8.8.8.8, 8.8.4.4;
    option subnet-mask 255.255.255.0;
    option routers 192.168.100.1;
    option broadcast-address 192.168.100.255;
    default-lease-time 20;
    max-lease-time 600;
}
```

**4. Agregá el bloque host para reservar la IP de la impresora**

El punto 1 no se resuelve sacando la .8 del range: hace falta un binding fijo. El bloque host asocia la MAC con fixed-address, así la impresora recibe siempre la misma IP aunque se reinicie o expire el lease. Ojo que la fixed-address puede estar dentro o fuera del range, pero conviene fuera para que nadie más la tome.

```
host impresora {
    hardware ethernet 0B:08:27:AE:C8:6A;
    fixed-address 192.168.100.8;
    option host-name "impresora";
}
```

**5. Declará la interfaz en /etc/default/isc-dhcp-server y reiniciá**

Si no le decís en qué interfaz escuchar, el demonio puede elegir la equivocada (o ninguna) y nunca ve los DHCPDISCOVER, que son broadcasts de capa 2 y no se rutean. journalctl es donde aparece el error real cuando el systemctl restart falla sin explicación.

```
sudo sed -i 's/^INTERFACESv4=.*/INTERFACESv4="enp0s9"/' /etc/default/isc-dhcp-server
sudo systemctl restart isc-dhcp-server
sudo systemctl status isc-dhcp-server
sudo journalctl -xeu isc-dhcp-server
```

_Verificación:_ status active (running) y en los logs 'Listening on LPF/enp0s9/...'

**6. Probá con nmap: una MAC cualquiera y la de la impresora**

Con la MAC random comprobás que el pool entrega una IP dentro de 10-200 y que viajan los DNS de Google. Con la MAC de la impresora comprobás la reserva: tiene que contestar 192.168.100.8 y no una del pool. Si solo probás una de las dos, podés aprobar medio ejercicio sin darte cuenta.

```
sudo nmap --script broadcast-dhcp-discover \
    --script-args "broadcast-dhcp-discover.mac=AA:BB:CC:DD:EE:FF"

sudo nmap --script broadcast-dhcp-discover \
    --script-args "broadcast-dhcp-discover.mac=0B:08:27:AE:C8:6A"
```

_Verificación:_ Primera corrida: IP entre .10 y .200 + Domain Name Server 8.8.8.8, 8.8.4.4. Segunda: IP Offered 192.168.100.8

**Errores típicos**

- No pararle NetworkManager: te pisa la IP de la interfaz y el servicio queda escuchando donde no debe.
- Declarar la subnet pero no tener ninguna IP de esa subred en una interfaz: isc-dhcp-server no arranca.
- Resolverlo sobre la interfaz del WiFi del laboratorio, que el enunciado prohíbe explícitamente: le repartís IPs a toda la red.
- Intentar la IP fija solo achicando el range en vez de usar un bloque host con hardware ethernet + fixed-address.
- Olvidarse de reiniciar isc-dhcp-server después de editar dhcpd.conf: el demonio sigue con la config vieja en memoria.

_Fuente: 2025_2c_practico.md_

---

### Ejercicio 3: Descubrir un servidor desconocido en una red remota y escanear sus servicios

_[2025 2C]_

**Enunciado**

> Ejercicio II
>
> El host en 192.168.50.250 es el gateway de la red 10.0.0.0/24. Sabe que existe un servidor distinto al gateway en esta red, pero no esta seguro de que direccion IP tiene asignada. Descubra la direccion del servidor, y los servicios que tiene expuesto el host.
>
> Debe mostrar:
>   - El resultado de acceder via HTTP a dicho servidor.
>   - Los routers que intervienen en esta comunicacion.
>
> Nota: Recomendamos utilizar una VM para resolver este ejercicio, pero no es necesario de poder hacerlo en su Host. De usar una VM, el adaptador de red debe estar en modo Bridge (Puente) conectado a la red abierta.
>
> [El prefijo de la red aparece tachado y corregido a mano en la imagen; se lee 10.0.0.0/24.]

**Pista:** A diferencia del ejercicio típico, acá no te dan la IP del servidor: ¿qué escaneo barre una /24 entera sin tocar puertos?

**Resolución paso a paso**

**1. Conectate a la red del laboratorio y verificá el gateway**

Con el adaptador en NAT la VM no ve la red 192.168.50.x y nada funciona. El ping al gateway confirma en dos segundos que estás del lado correcto antes de empezar a agregar rutas a ciegas.

```
ip addr show
ping -c 3 192.168.50.250
```

_Verificación:_ Respuesta del 192.168.50.250 y tu IP en 192.168.50.x

**2. Agregá la ruta a 10.0.0.0/24 por el gateway**

Tu máquina no tiene ninguna interfaz en 10.0.0.0/24, así que sin esta ruta el kernel manda esos paquetes a la default gateway (que no conoce la red) o directamente responde 'Network is unreachable'. Todo el resto del ejercicio depende de esto.

```
sudo ip route add 10.0.0.0/24 via 192.168.50.250
```

_Verificación:_ ip route | grep 10.0.0.0 → '10.0.0.0/24 via 192.168.50.250'

**3. Barré la red con un host discovery para encontrar la IP del servidor**

Acá no te dan la IP: hay que encontrarla. -sn hace solo descubrimiento de hosts sin escanear puertos, así que barre los 254 hosts en segundos en vez de minutos. Lo que buscás es la IP viva que no sea la del gateway.

```
sudo nmap -sn 10.0.0.0/24
# si ICMP está filtrado y no aparece nada, probá descubrimiento por TCP:
sudo nmap -sn -PS22,80,443 10.0.0.0/24
```

_Verificación:_ Una lista de 'Nmap scan report for 10.0.0.X'; anotá la que no sea el gateway

**4. Escaneá los puertos y versiones de esa IP**

Ya sabés quién es, ahora qué expone. -Pn evita que nmap lo descarte por tener ICMP bloqueado, -sV identifica servicio y versión (lo que pide la consigna) y -p- cubre los 65535 puertos por si el HTTP no está en un puerto típico.

```
sudo nmap -sS -Pn -sV -p- 10.0.0.<X>
```

_Verificación:_ Tabla PORT/STATE/SERVICE/VERSION con al menos un puerto open

**5. Accedé por HTTP y mostrá la respuesta**

Es uno de los dos entregables explícitos. Usá el puerto que reportó nmap, no asumas el 80: en estos ejercicios el HTTP suele estar en 8080 o parecido.

```
curl -v http://10.0.0.<X>
# o con el puerto detectado:
curl -v http://10.0.0.<X>:<puerto>
```

_Verificación:_ HTTP/1.1 200 OK con el cuerpo de la respuesta

**6. Mostrá los routers con traceroute**

El segundo entregable. Confirma además que los paquetes están saliendo por el gateway correcto: el primer hop tiene que ser 192.168.50.250, y si no lo es, la ruta que agregaste no se está aplicando.

```
traceroute 10.0.0.<X>
# con ICMP filtrado:
sudo traceroute -T -p 80 10.0.0.<X>
```

_Verificación:_ Lista de hops con 192.168.50.250 primero

**Errores típicos**

- Escanear con -sV -p- toda la /24 de entrada: tarda muchísimo. Primero -sn para encontrar la IP, después el escaneo profundo de ese host solo.
- Olvidar la ruta por el gateway: nmap reporta todos los hosts como down y parece que la red está vacía.
- Confundir el gateway con el servidor: el enunciado aclara que el servidor es distinto al gateway.
- Correr el escaneo de puertos sin -Pn contra un host con ICMP bloqueado: nmap no escanea nada.
- Entregar solo el nmap: faltan los dos ítems que la consigna pide mostrar, el HTTP y los routers.

_Fuente: 2025_2c_practico.md_

---

