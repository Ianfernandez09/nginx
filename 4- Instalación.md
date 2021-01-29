<h1><p align=center> Instalación </p></h1>
Antes de nada, lo primero será actualizar la lista de paquetes.

``` apt update ```

Después de esto instalamos el paquete de nginx.

``` apt -y install nginx ```

Al hacer esto, se nos iniciará un servicio automáticamente llamado nginx.service, comprobamos que está iniciado y funciona correctamente.

``` systemctl status nginx ```
