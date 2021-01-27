# Casos Prácticos
## Versión de Nginx Instalado

**Añadir imagen 4**

## Servicio asociado

**Añadir imagen 3**

## Ficheros de configuración

Los ficheros de configuración se encuentran bajo la ruta /etc/nginx, siendo el archivo de configuración principal /etc/nginx/nginx.conf

**Añadir imagen 5**

En **/etc/nginx/modules-enabled/** se encuentran los archivos de los módulos que se desean configurar.

En **/etc/nginx/sites-enabled/** podemos ver los sitios virtuales que tenemos **activados** y en /etc/nginx/sites-available/ encontramos los **disponibles**.

En **/etc/nginx/conf.d/** podemos encontrar archivos de configuración adicionales.

## Página web por defecto

La página web por defecto, podemos encontrarla en **/var/www/html/**, donde podremos editar el archivo **index.html**.

**Añadir imagen 6**

**Añadir imagen 7**

**Añadir imagen 8**

## Virtual Hosting

Tendré 2 sitios web, uno llamado **www.web1.org (/var/www/web1)** y otro llamado **www.web2.org (/var/www/web2)**

Misma dirección IP y puerto, distintos dominios.

1- Creamos las carpetas donde se alojarán las páginas de cada uno.

**Añadir imagen 9**

2- Creamos el index en cada una de las carpetas.

**Añadir imagenes 10,11,12 y 13**

3- Configuramos los nuevos sitios.

Para ello, nos dirigimos al directorio **/etc/nginx/sites-available/**.

Y creamos primero un archivo llamado **web1.conf** con el siguiente contenido.

**Añadir imagen 14**

Y luego otro para web2 llamado **web2.conf** con el siguiente contenido.

**Añadir imagen 15**

* La directiva server_name especifica el dominio al que asocia el sitio virtual
* La ruta del contenido web se indica en la directiva root
* En la directiva index especificamos los nombres de archivos que son considerados índice
* En el bloque Location aplicamos configuraciones concretas dentro de la ruta del contenido web. Con la directiva try fules indicamos que debe resolver la url como archivo, si falla como directorio y por ultimo con el error 404
* Las dos últimas directivas hacen referencia a los eventos de acceso y errores

Por último tenemos que crear un enlace simbólico dentro de sites-enabled para que los sitios se activen, ya que ahora mismo solo están disponibles.

**Añadir imagen 16 y 17**

Y recargamos nginx.

**Añadir imagen 18**

Ahora, desde un cliente configurando el archivo */etc/hosts* (ya que estamos trabajando en entorno local), realizamos la comprobación desde el navegador.

**Añadir imagen 19,20 y 21**
