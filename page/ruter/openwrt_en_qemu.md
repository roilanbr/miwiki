# Crear VM en QEMU

## Descargamos el firmware y decomprimimos y renombramos:

> [!NOTE]
> Se usara la versión estable del firmware `generic-squashfs-combined.img`


Vamos a [https://downloads.openwrt.org/releases/](https://downloads.openwrt.org/releases/) y descarganos la ultima version, en este momento la `23.05.3`

```bash
$ mkdir openwrt && cd openwrt

$ wget https://downloads.openwrt.org/releases/23.05.3/targets/x86/generic/openwrt-23.05.3-x86-generic-generic-squashfs-combined.img.gz

$ gunzip openwrt-23.05.3-x86-generic-generic-squashfs-combined.img.gz
gzip: openwrt-23.05.3-x86-generic-generic-squashfs-combined.img.gz: decompression OK, trailing garbage ignored

$ mv openwrt-23.05.3-x86-generic-generic-squashfs-combined.img openwrt.img
```

### KVM/QEMU

Si está utilizando la virtualización KVM/QEMU.

```bash
$ qemu-img resize -f raw openwrt.img 300M
Image resized.
```

### libvirt+KVM/QEMU

Si está utilizando KVM/QEMU con el marco de administración libvirt. Importe la imagen tal como está y detenga el primer inicio en la pantalla de GRUB. Luego expanda la partición de datos desde la consola de la siguiente manera y continúe con el arranque.

```bash
$ virsh blockresize openwrt vda 300M
$ virsh vol-resize openwrt 300M default
```

> [!NOTE]
> Si está utilizando otros sistemas de virtualización como Proxmox, VirtualBox, también es posible ampliar la unidad, pero tendrá que consultar su propia documentación para obtener más información.

## Ejemplo de iniciar desde la consola

```bash
qemu-system-x86_64 -enable-kvm -m 64 -M q35 \
-drive file=openwrt.img,id=d0,if=none \
-device ide-hd,drive=d0,bus=ide.0 \
-drive file=data.qcow2,id=d1,if=none \
-device ide-hd,drive=d1,bus=ide.1 \
-soundhw ac97 \
-netdev bridge,br=virbr0,id=hn0 \
-device e1000,netdev=hn0,id=nic1 \
-netdev user,id=hn1 \
-device e1000,netdev=hn1,id=nic2
```

```bash
qemu-system-x86_64 -m 64 -M q35 \
-drive file=openwrt.img,id=d0,if=none,bus=0,unit=0 \
-device ide-hd,drive=d0,bus=ide.0
```

El firmware UEFI requiere el paquete ovmf instalado. 

```bash
qemu-system-x86_64 \
-enable-kvm -m 1G -drive if=pflash,format=raw,readonly,file=/usr/share/edk2-ovmf/x64/OVMF_CODE.fd \
-drive if=pflash,format=raw,file=my_uefi_vars.fd
```

## Configuración de la red

QEMU tiene varias opciones para proporcionar conectividad de red a imágenes emuladas, ver todas -net opciones en qemu(1). Aunque esta opción se considera obsoleta desde QEMU 0.12, sigue funcionando aunque lo esté. La nueva sintaxis utiliza -nic para emular una placa integrada particular, o utiliza las dos opciones -netdev y -device, consulte la [Documentación oficial](https://wiki.qemu.org/Documentation/Networking)

## Proporcionar acceso a Internet a OpenWrt

El modo de red predeterminado para QEMU es “user mode network stack”.

En este modo, qemu Actúa como proxy para conexiones TCP/UDP salientes . También proporciona servicios DHCP y DNS al sistema emulado.

Para proporcionar acceso a Internet al sistema OpenWrt emulado, utilice (el ejemplo utiliza un sistema armvirt, ajuste según su configuración): 

```bash
qemu-system-arm -net nic,vlan=0 -net nic,vlan=1 -net user,vlan=1 \
-nographic -M virt -m 64 -kernel lede-17.01.0-r3205-59508e3-armvirt-zImage-initramfs
```

Aquí, configuramos dos tarjetas de red dentro del sistema OpenWrt emulado:

* eth0, usado como LAN en OpenWrt (no conectado a nada aquí)
* eth1, utilizado como WAN en OpenWrt y conectado a qemu que representará todas las conexiones TCP/UDP hacia Internet

