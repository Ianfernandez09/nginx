<h1><p align=center> Casos Prácticos </p></h1>

# Versión de Nginx Instalado

![version](https://i.imgur.com/RXDhP3j.png)

# Servicio asociado

``` systemctl status nginx ```

# Ficheros de configuración

Los ficheros de configuración se encuentran bajo la ruta ```/etc/nginx```, siendo el archivo de configuración principal ```/etc/nginx/nginx.conf```

![ficheros](https://i.imgur.com/LnX5BwW.png)

En ```/etc/nginx/modules-enabled/``` se encuentran los archivos de los módulos que se desean configurar.

En ```/etc/nginx/sites-enabled/``` podemos ver los sitios virtuales que tenemos **activados** y en ```/etc/nginx/sites-available/``` encontramos los **disponibles**.

En ```/etc/nginx/conf.d/``` podemos encontrar archivos de configuración adicionales.

# Página web por defecto

La página web por defecto, podemos encontrarla en ```/var/www/html/```, donde podremos editar el archivo **index.html**.

![defecto](https://i.imgur.com/q9GtFyJ.png)

![index](https://i.imgur.com/r9flyPH.png)

![muestra](https://i.imgur.com/doYLk5q.png)

# Virtual Hosting

Tendré 2 sitios web, uno llamado **www.web1.org (```/var/www/web1```)** y otro llamado **www.web2.org (```/var/www/web2```)**

Misma dirección IP y puerto, distintos dominios.

**1-** Creamos las carpetas donde se alojarán las páginas de cada uno.

![carpetas](https://i.imgur.com/bdolnqw.png)

**2-** Creamos el index en cada una de las carpetas.

``` nano index.html ```

![web1](https://i.imgur.com/WqiBEhw.png)

``` nano index.html ```

![web2](https://i.imgur.com/NfAhwLn.png)

**3-** Configuramos los nuevos sitios.

Para ello, nos dirigimos al directorio ```/etc/nginx/sites-available/```.

Y creamos primero un archivo llamado **web1.conf** con el siguiente contenido.

![web1](https://i.imgur.com/gQwWJLx.png)

Y luego otro para web2 llamado **web2.conf** con el siguiente contenido.

![web2](https://i.imgur.com/c1j2SKM.png)

* La directiva server_name especifica el dominio al que asocia el sitio virtual.
* La ruta del contenido web se indica en la directiva root.
* En la directiva index especificamos los nombres de archivos que son considerados índice.
* En el bloque Location aplicamos configuraciones concretas dentro de la ruta del contenido web. Con la directiva try fules indicamos que debe resolver la url como archivo, si falla como directorio y por ultimo con el error 404.
* Las dos últimas directivas hacen referencia a los eventos de acceso y errores.

Por último tenemos que crear un enlace simbólico dentro de sites-enabled para que los sitios se activen, ya que ahora mismo solo están disponibles.

![enlace](https://i.imgur.com/Q5G8whl.png)

![enlace](https://i.imgur.com/hpdCatL.png)

Y recargamos nginx.

```systemctl reload nginx```

Ahora, desde un cliente configurando el archivo ```/etc/hosts``` (ya que estamos trabajando en entorno local), realizamos la comprobación desde el navegador.

![hosts](https://i.imgur.com/AqGy8i7.png)

![web1](https://i.imgur.com/xXtq9VB.png)

![web2](https://i.imgur.com/UmI6eA0.png)

# Autenticación, Autorización y Control de Acceso

## Voy a dejar accesible web1 desde externa e interna, pero web2 va a ser accesible solo desde la red interna.

Para ello, me voy al archivo ```/etc/nginx/sites-available/web2.conf```, y en la directiva **location**, voy a añadir las siguientes líneas.

![web2](https://i.imgur.com/oPBwoKl.png)

Luego de esto, realizamos un systemctl restart nginx

Y ahora comprobamos desde 2 clientes, uno mediante la red que tiene acceso a internet y la otra que solo tiene acceso a la red local.

![comprobacion](https://i.imgur.com/EEYHC0e.png)

Cliente01 tiene ip 192.168.3.x y cliente02 tiene ip 192.168.2.x

## Web1 va a contener un directorio privado, con autenticación básica y solo pueden acceder usuarios válidos

Para ello, primero voy a crear un directorio dentro de la carpeta contenedora de la página de web1.

![privado](https://i.imgur.com/fG2BNu0.png)

Ahora necesitaremos instalar el paquete apache2-utils.

``` apt install apache2-utils ```

En él podremos encontrar la herramienta .htpasswd. Ejecutamos el siguiente comando, para insertar en el archivo claves.txt un usuario con su contraseña cifrada.

![clave](https://i.imgur.com/7jM8IOZ.png)

Ahora editaremos el fichero de web1.conf para añadir las siguientes líneas.

![auth](https://i.imgur.com/P8VtCH1.png)

Hacemos ```systemctl reload nginx```

Cuando intentemos acceder desde un equipo que pueda acceder a la web de **wwww.web1.org**, al añadir a la dirección **/privado**, nos pedirá las credenciales.

![web1auth](https://i.imgur.com/CjPVCgB.png)

Pero si no añadimos el usuario y contraseña adecuados, no nos dejará entrar y nos pedirá de nuevo la contraseña.

Si ponemos el usuario y la contraseña adecuada.

![auth](https://i.imgur.com/AWLgSfb.png)

![entrada](https://i.imgur.com/74Qzv2m.png)

## A través de la red interna no pide autenticación para entrar a www.web1.org/privado, pero a través de la red pública sí.

Editamos el fichero web1.conf y añadimos las siguientes líneas.

![fichero](https://i.imgur.com/FQylF2w.png)

Hacemos ```systemctl reload nginx```

Ahora intentamos entrar dede la red pública.

![publicauth](https://i.imgur.com/orrQ3IX.png)

Pero desde un equipo de la red interna, nos deja acceder directamente.

![privateauth](https://i.imgur.com/wwYpeYW.png)

## Configurar el sitio virtual www.web1.org con acceso seguro

Para ello, primero crearemos los certificados, en mi caso voy a usar **openssl**, ejecutamos el siguiente comando.

``` openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out nginx-certificate.crt -keyout nginx.key ```

Añadimos todos los campos que nos piden. Una vez termine, se generarán 2 archivos. Esos archivos los metemos en alguna carpeta, en mi caso la siguiente.

[ssl1](https://i.imgur.com/k3FJbe6.png)

Ahora, nos dirigimos al archivo web1.conf y lo editamos de la siguiente manera.

![ssl2](https://i.imgur.com/OSCqQ1E.png)

Si nos dirigimos al  navegador y colocamos ``` https://www.web1.org ```, nos aparecerá de la siguiente manera.

![ssl3](https://i.imgur.com/yFqkojd.png)

Y accederá a la página, en la cual si miramos los detalles del certificado, veremos los atributos que le hemos colocado a la hora de generarlo.

![ssl4](https://i.imgur.com/LjFoHcs.png)
