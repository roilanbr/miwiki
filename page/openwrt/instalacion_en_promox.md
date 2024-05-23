# Crear VM en Proxmox

En Promox creamos una VM con el boton `Create VM` con las siguientes opciones:

* General
  - Nodo: `name_pve`
  - ID VM: `105`
  - Nombre: `router`
* OS
  - No usar algún medio
* Sistema
  - (Opciones por defecto) / Siguiente
* Disco Duro
  - (Opciones por defecto, despues se borrara el HDD)
* CPU
  - (Opciones por defecto) / Siguiente
* Memoria
  - Memoria (MiB): `64`
* Red
   - Puente: `vmbr0(interfaz_lan)`
   - Modelo: `VirtIO`
* Confirmar
   - Siguiente

Agregamos otra interfaz de red que será la WAN, para ello seleccionamos la VM `105`,  la opcion `Hardware >> Agregar >> Dispositivo de red`:
* Red
  - Puente: `vmbr1(interfaz_wan)`
  - Modelo: `VirtIO`

Desconectamos y eliminamos el disco de 32 Gb que se añade por defecto al crear la maquina virtual:

```bash
qm unlink 105 --idlist scsi0 --force 1
update VM 105: -delete scsi0 -force 1
  Logical volume "vm-100-disk-0" successfully removed
```


# Descargamos el firmware

> Nota:\
>Se usara la versión estable del firmware `combined-ext4.img.gz`

Descargamos el firmware y decomprimimos:

Vamos a [https://downloads.openwrt.org/releases/](https://downloads.openwrt.org/releases/) y descarganos la ultima version, en este momento la `23.05.3`

```bash
mkdir openwrt && cd openwrt

wget https://downloads.openwrt.org/releases/23.05.3/targets/x86/generic/openwrt-23.05.3-x86-generic-generic-ext4-combined.img.gz



gunzip openwrt-23.05.3-x86-generic-generic-ext4-combined.img.gz
```

Importamos la imagen del firmware a la maquina virtual 105 como unidad de disco:

```bash
qm importdisk 105 openwrt-23.05.3-x86-generic-generic-ext4-combined.img local-lvm
importing disk 'openwrt-19.07.7-x86-64-combined-ext4.img' to VM 105 ...
  Rounding up size to full physical extent 276.00 MiB
[...]
transferred 0.0 B of 272.5 MiB (0.00%)
transferred 4.0 MiB of 272.5 MiB (1.47%)
transferred 8.0 MiB of 272.5 MiB (2.94%)
transferred 12.0 MiB of 272.5 MiB (4.40%)
transferred 16.0 MiB of 272.5 MiB (5.87%)
[...]
transferred 272.5 MiB of 272.5 MiB (100.00%)
Successfully imported disk as 'unused0:hdd:vm-105-disk-0'
```

Asignar disco importado como disco principal de arranque:

```bash
qm set 105 --scsi0 local-LVM:vm-105-disk-0 
update VM 1xx: -scsi0 hdd:vm-105-disk-0

qm set 105 --boot order=scsi0
update VM 105: -boot order=scsi0
```


# Iniciar el router virtual y configuración inicial

Por el Promox damos click derecho en la `VM >> Iniciar`, luego iniciar la consola web de VM. Presionamos `Enter` en la consola de la VM, con esto ya estariamos en el terminal del router. La configuración de red por defecto del router es la estandar por lo que debemos configurar en cada caso. 

> Nota:\
>La IP por defecto de OPenwrt de la red LAN es ''192.168.1.1/24'' por lo que deberemos cambiarla para que este en la misma subred que nuestra LAN.

```nash
uci set network.lan.ipaddr=tu_ip_router_lan

uci commit

reboot
```

Aprovechamos y ponemos contraseña a root:

```bash
passwd root
changing password for root
New password:
```

Una ves reiniciado, en otra PC abrimos en un navegador la URL [http://tu_ip_router_lan](http://tu_ip_router_lan) para administrar el router.

$\color{#50fa7b}\textsf{FIN}$
















