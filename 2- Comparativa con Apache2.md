<h1><p align=center> Nginx vs Apache </p></h1>

## Seguridad
En el sentido de la seguridad, ambos tienen buenas políticas de seguridad. Publican parches de seguridad con frecuencia para evitar ataques DDos.
Tienen desarrollos bastante activos y tienen actualizaciones constantes.

## Rendimiento
A la hora de tener un servidor web,lo ideal es que sea lo más rápido posible.De lo contrario, la lentitud causaría colapsos en el servidor y fallas ante los visitantes.

En este sentido, Nginx responde mejor a las solicitudes de los clientes gracias a la gestión de subprocesos. Añadiendole el plus de que está basado en eventos, solo el servidor responde con solicitudes
lo que hace que no gaste memoria innecesariamente. También consume menos RAM y procesa mejor las solicitudes múltiples.

## Flexibilidad
Apache es un servidor web muy flexible. Admite más de 60 módulos diferentes, lo que amplía aún mas su funcionalidad. También admite la personalización de conexiones a través de la herramienta **.htaccess** mientras que Nginx no.
Entonces podemos decir que Apache es mucho más flexible y personalizable.

## ¿Qué usar?
Todo depende de lo que necesites. En términos generales, Apache es ideal si tienes que personalizar mucho las conexiones, mientras que si tienes que recibir mucho tráfico
quizás lo ideal sea usar Nginx.