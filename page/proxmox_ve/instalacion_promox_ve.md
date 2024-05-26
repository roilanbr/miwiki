# Instalción
Priero descargamos el iso, para ello vamos a la web de Promox [https://www.proxmox.com/en/downloads](https://www.proxmox.com/en/downloads) y descargamos la última versión del iso.

Al insertar el CD-ROM o USB con Proxmox nos sale la pantalla de binvenida.

Bienbenida a Proxmox Virtual Environment
<img src="../../media/proxmox_ve/pve-00-grupiso.jpg" alt="pve gruiso" width="75%" />

Aceptamos la licencia:
<img src="../../media/proxmox_ve/pve-01-licencia.jpg" alt="pve gruiso" width="75%" />

Escogemos el HDD donde instalar y damos click al botón `Option`:
<img src="../../media/proxmox_ve/pve-02-disk.jpg" alt="pve gruiso" width="75%" />

Configuramos Las particiones:
<img src="../../media/proxmox_ve/pve-02-disk-1.jpg" alt="pve gruiso" width="75%" />

Escogemos la localidad, zona horaria y teclado:
<img src="../../media/proxmox_ve/pve-03-time.jpg" alt="pve gruiso" width="75%" />

Ponemos la contraseña y email:
<img src="../../media/proxmox_ve/pve-04-passwd.jpg" alt="pve gruiso" width="75%" />

Configuramos la red:
<img src="../../media/proxmox_ve/pve-05-red.jpg" alt="pve gruiso" width="75%" />

Verificar que los datos llenados son los deseados. Y damos click en `Install`. Al terminar la instalación click en `Reboot`
<img src="../../media/proxmox_ve/pve-06-resumen.jpg" alt="pve gruiso" width="75%" />


Al reiniciar escogeremos la primera opcion del GRUB: `Proxmox VE GNU/Linux`\
<img src="../../media/proxmox_ve/pve-08-grup.png" alt="pve gruiso" width="75%" />

Consola de Proxmox, en la misma pantalla aparecera la `URL` para administrar el Proxmox via web.\
<img src="../../media/proxmox_ve/pve-07-consola.png" alt="pve gruiso" width="75%" />

# Interfaz Web de Proxmox

En un navegador firefox abrimos la URL [https://192.168.0.2:8006](https://192.168.0.2:8006).

Advertencia del certificado autofirmado, damos click en `Avanzado` y luego en `Aceptar el riesgo y continuar`.\
<img src="../../media/proxmox_ve/pve-09-cerificado.jpg" alt="pve gruiso" width="75%" />

Ventana para autenticarnos a la Aministración web del Proxmox.\
<img src="../../media/proxmox_ve/pve-10-autenticacion.jpg" alt="pve gruiso" width="75%" />

Interfaz Web del Proxmox\
<img src="../../media/proxmox_ve/pve-11-interfaz_web.jpg" alt="pve gruiso" width="75%" />

$\color{#50fa7b}{\textsf{FIN}}$