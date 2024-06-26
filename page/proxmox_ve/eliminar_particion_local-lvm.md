# Eliminar partición local-LVM

Al instalar Proxmox se crea 2 particiones por defecto llamadas `local` y `local-lvm` en esta ultima solo se guardan los discos de las VM por lo que eliminaremos esa partición y la crearemos para poder almacenar las iso, backup, plantillas, etc.

Lo primero es eliminar la partición `lvm-thin` que es la que nos interesa para ello desde la consola ejecutaremos los siguientes comandos: 

```bash
$ lvremove /dev/pve/data
Do you really want to remove and DISCARD active logical volumen pve/data? [y/n]: y
  Logical volume "data" successfully removed
```
 
Verificar el espacio libre con `vgdisplay` y fijarnos en el espacio libre en `Free PE / Size`: 

```bash 
$ vgdisplay
 --- Volume group ---
 [...]
 Alloc PE / Size       7680 / 30.00 GiB
 Free  PE / Size       17791 / <69.50 GiB
 VG UUID               4Wpf9c-oAXk-OGmD-ndsZ-BTbg-npwB-F45oTl
 ```

Crear el nuevo volumen con un tamaño un poco mas pequeño (69.49G): 

```bash
$ lvcreate -L 69.49G -n data pve
Rounding up size to full physical extent 250.24 GiB
Logical volume "data" created.
```

Formatear el nuevo volumen:

```bash
$ mkfs.ext4 /dev/pve/data
[...]

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (131072 blocks): done
Writing superblocks and filesystem accounting information: done
```

Modificar el archivo `fstab` y agregar al final los parámetros para que se monte el volumen al iniciar el sistema:

```bash
$ echo "/dev/pve/data /media/hdd ext4 rw 0 2" >> /etc/fstab
```

Crear carpeta de montaje y probar a montar:

```bash
$ mkdir /media/hdd
$ mount -a
```

Comprobar que se a montado:

```bash
$ df -lh
Filesystem             Size      Used  Avail Use% Mounted on

[...]
/dev/mapper/pve-data    68G       24K    65G   1% /media/hdd
```

Eliminar partición `local-lvm` que aparece en la interfaz web del proxmox y agregar el nuevo volumen creado, puede ser por la web o por consola:

Archivo: `/etc/pve/storage.cfg`

```bash
dir: local
        path /var/lib/vz
        content iso,vztmpl,backup
 
#lvmthin: local-lvm
#        thinpool data
#        vgname pve
#        content rootdir,images 
 
dir: hdd
        path /media/hdd
        content backup,images,iso,rootdir,snippets,vztmpl
        maxfiles 4
        shared 0
```
        
$\color{#50fa7b}{\textsf{FIN}}$