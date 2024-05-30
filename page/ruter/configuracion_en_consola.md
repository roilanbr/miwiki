# Configuración en consola

Varias configuraciones que se pueden hacer por la consola. Las configuraciones se guardan en `/etc/config/`

```bash
ls /etc/config/

dhcp        firewall    network     system      uhttpd
dropbear    luci        rpcd        ucitrack    
```

Lo primero es cambiar la contraseña de `root`

```bash
passdw

Changing password for root
New password: 
Retype password: 
```


## Uso del comando `uci`

El comando `uci` es usado para configurar OpenWRT por consola

>[!TIP]
>Tambien puedes configurar OpenWRT con la interfaz web `luci`


```bash
# Ver configuración
uci show [<config>.[<section>[.<option>]]]

# Obtener configuración
uci get <config>.<section>[.<option>]

# Configuración opciones
uci set <config>.<section>[.<option>]=<value>

# Guardar la configuración 
uci commit <config>

# Reiniciar el servicio de dos formas distintas
service <servicio> restart
/etc/init.d/<servicio> restart
```


## Configurar la red por la consola

```bash
# Configuración LAN
uci set network.lan.ipaddr='192.168.0.1'
uci set network.lan.netmask='255.255.255.0'

# Configuración WAN
uci network.wan.device='eth1'
uci network.wan.proto='static'
uci set network.wan.ipaddr='172.16.0.2'
uci set network.wan.netmask='255.255.255.252'
uci set network.wan.gateway='172.16.0.1'

# Guardar configuración
uci commit network

# Reiniciar el servicio de la red
service firewall restart
```

# Usando uci para configurar el firewall

```bash
# Ver las configuracion de firewall
uci show firewall

# Agregar una nueva regla, esto mostrará un serial que 
# se asignara a la variable '@rule[<No>]'. 
uci add firewall rule
cfg0c92bd

# Puedes usar el serial cfg0c92bd o @rule[5] para configurar las regla.
# Nombre de la regla
uci set firewall.cfg0c92bd.name=<Nombre de la regla>

# Protocolo 'tcp' 'udp' 'icmp' 'igmp' 'esp', si no se espesifica es para # todos los protocolos, para varios protocolos separado por espacio
uci set firewall.@rule[5].proto=<protocolo>

# Zona de origen de la conexión se puede espesificar 'lan, wan, *', 
# si no se espesifica es desde el propio dispositivo.
uci set firewall.@rule[5].src=<zona>

# IP de origen, puede ser más de una separado por espacio
uci set firewall.@rule[5].src_ip=<ip_uno> <rango_ip/24>

# IP de destino
uci set firewall.@rule[5].dest_ip=<ip_destino>

# Puerto de destino
uci set firewall.@rule[5].dest_port=<port>

# Acción 'ACCEPT' o 'REJECT'
uci set firewall.@rule[5].target=<accion>

# Version IPv4 o IPv6, si no se espesifica es para todos
uci set firewall.@rule[5].family=<ip_version>
```


### Ejemplos

Abrir el puerto `22` del route para acceder desde la WAN. Primeros mostramos las reglas creadas y nos fijamos en el id de la última regla creada.

```bash
uci show firewall

[...]
firewall.@rule[4].family='ipv4'
```
En este caso `@rule[4]`, con esto ya sabemos que id poner a la siguiente regla: `@rule[5]` .


```bash
uci add firewall rule
uci set firewall.@rule[5].name='Allow SSH from WAN'
uci set firewall.@rule[5].proto='tcp'
uci set firewall.@rule[5].src='wan'
uci set firewall.@rule[5].dest_port='22'
uci set firewall.@rule[5].target='ACCEPT'
uci set firewall.@rule[5].family='ipv4'
uci commit firewall
service firewall restart
```

Permitir desde la ip `192.168.0.21` hacia la `WAN` por el puerto `5222`

```bash
uci add firewall rule
uci set firewall.@rule[6].name='Allow XMPP --> WAN'
uci set firewall.@rule[6].proto='tcp'
uci set firewall.@rule[6].src='lan'
uci set firewall.@rule[6].src=_ip'192.168.0.21'
uci set firewall.@rule[6].dest_ip='*'
uci set firewall.@rule[6].dest_port='5222'
uci set firewall.@rule[6].target='ACCEPT'
uci commit firewall
service firewall restart
```

## Creando reglas editando el archivo `firewall`

Permitir la salida desde la `LAN` hacia la `WAN`. Primero hacemos una copia del archivo y luego editamos.

```bash
cp /etc/config/firewall /etc/config/firewall.bak
```

Archivo: `/etc/config/firewall`

```bash
# Al final del archivo agregamos lo siguiente
config rule
	option name 'Allow FTP --> WAN'
	option proto 'tcp'
	option src 'lan'
	option dest '*'
	option dest_port '5222'
	option target 'ACCEPT'
```
Por ultimo reiniciar el servicio

```bash
service firewall restart
```

## Configurar proxy al gestor de paquete `opkg`

Editar: `/etc/opkg.conf`

```bash
option ftp_proxy ftp://proxy.example.org:2121/
option http_proxy http://proxy.example.org:8080/
option http_proxy http://proxy.example.org:8080/

# Usar autenticación:
option proxy_user tu
option proxy_passwd xxxx
```

>[!TIP]
Alternativamente, se puede proporcionar un nombre de usuario y contraseña como parte de la URL si `busybox wget` está instalado:
>```bash
>opción http_proxy http://usuario:contraseña@proxy.example.org:8080/
>opción ftp_proxy http://usuario:contraseña@proxy.example.org:2121/
>```


## Uso del gestor del paquete `opkg`

```bash
# Actualizar los repositorios
opkg update

# Actualizar los paquetes
opkg upgrade <pkgs>

# Instalar paquetes
opkg install <pkgs>

# Desintalar paquetes
opkg remove <pkgs>

# Listar paquetes válidos
opkg list <pkg>

# Listar los paquetes actulisable
opkg list-upgradable

# Actualizar todos los paquetes
opkg list-upgradable | cut -f 1 -d ' ' | xargs opkg upgrade

# Para mas ayuda ejecute
opkg --help
```

Instalar alguos paquetes 
* `luci-i18n-base-es` Idioma español para LUCI
* `nano` Editor de texto en consola


```bash
opkg install luci-i18n-base-es nano
```