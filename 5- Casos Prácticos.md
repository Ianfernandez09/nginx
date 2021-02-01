<h1><p align=center> Casos Prácticos </p></h1>

# Versión de Nginx Instalado

![version](https://i.imgur.com/RXDhP3j.png)

# Servicio asociado

``` systemctl status nginx ```

# Ficheros de configuración

Los ficheros de configuración se encuentran bajo la ruta ```/etc/nginx```, siendo el archivo de configuración principal ```/etc/nginx/nginx.conf```

**Añadir imagen 5**

En ```/etc/nginx/modules-enabled/``` se encuentran los archivos de los módulos que se desean configurar.

En ```/etc/nginx/sites-enabled/``` podemos ver los sitios virtuales que tenemos **activados** y en ```/etc/nginx/sites-available/``` encontramos los **disponibles**.

En ```/etc/nginx/conf.d/``` podemos encontrar archivos de configuración adicionales.

# Página web por defecto

La página web por defecto, podemos encontrarla en ```/var/www/html/```, donde podremos editar el archivo **index.html**.

**Añadir imagen 6**

**Añadir imagen 7**

**Añadir imagen 8**

# Virtual Hosting

Tendré 2 sitios web, uno llamado **www.web1.org (```/var/www/web1```)** y otro llamado **www.web2.org (```/var/www/web2```)**

Misma dirección IP y puerto, distintos dominios.

**1-** Creamos las carpetas donde se alojarán las páginas de cada uno.

**Añadir imagen 9**

**2-** Creamos el index en cada una de las carpetas.

``` nano index.html ```

**Añadir imagen 11**

``` nano index.html ```

**Añadir imagen 13**

**3-** Configuramos los nuevos sitios.

Para ello, nos dirigimos al directorio ```/etc/nginx/sites-available/```.

Y creamos primero un archivo llamado **web1.conf** con el siguiente contenido.

**Añadir imagen 14**

Y luego otro para web2 llamado **web2.conf** con el siguiente contenido.

**Añadir imagen 15**

* La directiva server_name especifica el dominio al que asocia el sitio virtual.
* La ruta del contenido web se indica en la directiva root.
* En la directiva index especificamos los nombres de archivos que son considerados índice.
* En el bloque Location aplicamos configuraciones concretas dentro de la ruta del contenido web. Con la directiva try fules indicamos que debe resolver la url como archivo, si falla como directorio y por ultimo con el error 404.
* Las dos últimas directivas hacen referencia a los eventos de acceso y errores.

Por último tenemos que crear un enlace simbólico dentro de sites-enabled para que los sitios se activen, ya que ahora mismo solo están disponibles.

**Añadir imagen 16 y 17**

Y recargamos nginx.

**Añadir imagen 18**

Ahora, desde un cliente configurando el archivo ```/etc/hosts``` (ya que estamos trabajando en entorno local), realizamos la comprobación desde el navegador.

**Añadir imagen 19,20 y 21**

# Autenticación, Autorización y Control de Acceso

## Voy a dejar accesible web1 desde externa e interna, pero web2 va a ser accesible solo desde la red interna.

Para ello, me voy al archivo ```/etc/nginx/sites-available/web2.conf```, y en la directiva **location**, voy a añadir las siguientes líneas.

**Añadir imagen 22**

Luego de esto, realizamos un systemctl restart nginx

Y ahora comprobamos desde 2 clientes, uno mediante la red que tiene acceso a internet y la otra que solo tiene acceso a la red local.

**Añadir imagen 23**

Cliente01 tiene ip 192.168.3.x y cliente02 tiene ip 192.168.2.x

## Web1 va a contener un directorio privado, con autenticación básica y solo pueden acceder usuarios válidos

Para ello, primero voy a crear un directorio dentro de la carpeta contenedora de la página de web1.

**Añadir imagen 24**

Ahora necesitaremos instalar el paquete apache2-utils.

``` apt install apache2-utils ```

En él podremos encontrar la herramienta .htpasswd. Ejecutamos el siguiente comando, para insertar en el archivo claves.txt un usuario con su contraseña cifrada.

**Añadir imagen 26**

Ahora editaremos el fichero de web1.conf para añadir las siguientes líneas.

**Añadir imagen 27**

Hacemos ```systemctl reload nginx```

Cuando intentemos acceder desde un equipo que pueda acceder a la web de **wwww.web1.org**, al añadir a la dirección **/privado**, nos pedirá las credenciales.

**Añadir imagen 28**

Pero si no añadimos el usuario y contraseña adecuados, no nos dejará entrar y nos pedirá de nuevo la contraseña.

**Añadir imagen 29**

Si ponemos el usuario y la contraseña adecuada.

**Añadir imagen 30 y 31**

## A través de la red interna no pide autenticación para entrar a www.web1.org/privado, pero a través de la red pública sí.

Editamos el fichero web1.conf y añadimos las siguientes líneas.

**Añadir imagen 32**

Hacemos ```systemctl reload nginx```

Ahora intentamos entrar dede la red pública.

**Añadir imagen 33**

Pero desde un equipo de la red interna, nos deja acceder directamente.

**Añadir imagen 34**

## Configurar el sitio virtual www.web1.org con acceso seguro

Para ello, primero crearemos los certificados, en mi caso voy a usar **openssl**, ejecutamos el siguiente comando.

``` openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out nginx-certificate.crt -keyout nginx.key ```

Añadimos todos los campos que nos piden. Una vez termine, se generarán 2 archivos. Esos archivos los metemos en alguna carpeta, en mi caso la siguiente.

**Añadir imagen 36**

Ahora, nos dirigimos al archivo web1.conf y lo editamos de la siguiente manera.

**Añadir imagen 37**

Si nos dirigimos al  navegador y colocamos ``` https://www.web1.org ```, nos aparecerá de la siguiente manera.

**Añadir imagen 38**

Y accederá a la página, en la cual si miramos los detalles del certificado, veremos los atributos que le hemos colocado a la hora de generarlo.

**Añadir imagen 39**
