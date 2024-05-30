# Notas y consejos

En esta URL estan las versiones de Openwrt: [https://downloads.openwrt.org/releases/](https://downloads.openwrt.org/releases/)

Nos dirigimos al directorio de la versión más actulizada: `/23.05.3/targets/x86/generic/ `

| Image for your Device               | sha256sum    | File size   | Date                     |
| ----------------------------------- | ------------ | ------------| ------------------------ |
generic-ext4-combinado-efi.img.gz    | 08daa3980... |	10448,5KB  | Sat Mar 23 08:35:30 2024 |
generic-ext4-combinado.img.gz 	      | 02f57bd81... |	10283,2 KB | Sat Mar 23 08:35:30 2024 |
generic-ext4-rootfs.img.gz 	      | 3643f0ca8... | 	4508,3KB   | Sat Mar 23 08:35:29 2024 |
kernel-genérico.bin 	              | 4b6ff1f3b... | 	5377,7KB   | Sat Mar 23 08:35:04 2024 |
generic-squashfs-combinado-efi.img.gz | 36a7f5fb9... | 	9693,1KB   | Sat Mar 23 08:35:30 2024 |
generic-squashfs-combined.img.gz 	  | 20cfec267... | 	9528,9KB   | Sat Mar 23 08:35:30 2024 |
generic-squashfs-rootfs.img.gz 	  | ef121e3c9... | 	3755,7KB   | Sat Mar 23 08:35:27 2024 |
rootfs.tar.gz                         | 15c3e494c... |	4048.9 KB  | Sat Mar 23 08:35:06 2024 |


Ahora verá diferentes archivos ofrecidos tanto para `EFI` como para `MBR`:

* Las imágenes que contienen `ext4` en su nombre utilizan el sistema de archivos ext4.

* Las imágenes que contineen `squashfs` utilizan un sistema de archivos comprimido de solo lectura, diseñado para sistemas embebidos.

* Las imágenes que contienen `combined` suelen contener tanto el kernel como el sistema de archivos raíz en un solo archivo

* Las imágenes que contienen `rootfs` contienen solo el sistema de archivos raíz.


Ej: **generic-squashfs-combined.img.gz**

Esta imagen de disco utiliza el diseño tradicional de OpenWrt, un sistema de archivos raíz squashfs de solo lectura y una partición de lectura y escritura donde se almacenan las configuraciones y los paquetes que instala. Debido a cómo se ensambla esta imagen, tendrá solo 230 MB de espacio para almacenar paquetes y configuraciones adicionales, y Extroot no funciona. El Restablecimiento de fábrica estará disponibles.

Ej: **generic-ext4-combinado.img.gz**

Esta imagen de disco utiliza una única partición ext4 de lectura-escritura sin sistema de archivos raíz squashfs de solo lectura, lo que permite ampliar la partición. Las funciones como el Modo a prueba de fallos o el Restablecimiento de fábrica no estarán disponibles, ya que necesitan una partición squashfs de solo lectura para funcionar.


