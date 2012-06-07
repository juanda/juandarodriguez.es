Aplicaciones web
================

.. note:: 

   Este artículo ha sido adaptado de un texto del curso: `Desarrollo de aplicaciones
   web con Symfony2`_ del proyecto `Aula Mentor`_:

La World Wide Web es un sistema de documentos de hipertexto o hipermedios enlazados
y distribuidos a través de Internet. En sus comienzos, la interacción entre los 
usuarios de la *WWW* y sus  servidores era muy reducida: a través de un software
cliente denominado navegador web, el usuario se limitaba a solicitar documentos a
los servidores y estos respondían a aquellos con el envío del documento solicitado.
A estos documentos que circulaban por el “espacio web” se les denominó páginas web.
Cada recurso, conocido como página web, se localiza en este espacio mediante una
dirección única que describe tanto al servidor como al recurso: la *URL*. Cada
página web puede incorporar las *URL's* de otras páginas como parte de su contenido.
De esta manera se enlazan unas con otras.

Han pasado casi 20 años desde la aparición de la Word Wide Web y, aunque en
esencia su funcionamiento, basado en el protocolo *HTTP*, sigue siendo el mismo,
la capacidad de interacción entre usuarios y servidores se ha enriquecido
sustancialmente. De la página web hemos pasado a la aplicación web; un tipo de 
aplicación informática que adopta, de manera natural,  la arquitectura
cliente-servidor de la web. De manera que en las peticiones  al servidor, el 
usuario no sólo solicita un recurso, si no que además puede enviar datos. El 
servidor los procesa y elabora la respuesta que corresponda en función de ellos.
Es decir, el servidor construye dinámicamente la página web que le envía al cliente.

Todo el peso de la aplicación reside en el servidor, mientras que el cliente, esto
es, el navegador web, se limita a presentar el contenido que recibe mostrándolo al
usuario.

Esta evolución comenzó con la aparición de los *CGI's*, que son aplicaciones
escritas en cualquier lenguaje de programación y que pueden ser accedidas por el 
servidor web a petición del cliente, y ha madurado gracias a la aparición de los 
lenguajes de programación del lado del servidor, como *PHP*, *Java* o *Python*,
gracias a los cuales los servidores web (*apache* como ejemplo más conocido y 
usado) han ampliado su funcionalidades; ya no sólo son capaces de buscar, encontrar
y enviar documentos a petición del cliente, si no que también pueden procesar
peticiones (acceder a base de datos, realizar peticiones a otros servidores, 
ejecutar algoritmos, …)  y construir los documentos que finalmente serán enviados
al cliente en función de los datos que este les ha proporcionado.

También es relevante en esta evolución de la web la incorporación de procesamiento
en los navegadores web mediante lenguajes de *“scripting”* como *javascript*, que 
permiten la ejecución de ciertos procesos (casi todos relacionados con la
manipulación de la interfaz gráfica) en el lado del cliente. De hecho, en la 
actualidad existen aplicaciones que delegan gran parte de sus procesos al lado del
cliente, aunque de todas formas, todo el código es proporcionado desde la parte 
servidora la primera vez que se solicita el recurso.

Todo esto ha sido bautizado con el omnipresente y manido término de Web 2.0, que 
en realidad es una manera de referirse a este aumento de la capacidad de 
interacción con el usuario, y que  ha permitido el desarrollo y explosión de las
redes sociales y la blogosfera entre otros muchos fenómenos de la reducida pero
incesante historia de la *World Wide Web*.

El panorama actual se resume en un interés creciente por las aplicaciones web, 
hasta el punto de que, en muchos casos, han desplazado a la madura aplicación de 
escritorio. Son varias las razones que justifican  este hecho y, aunque se trata 
de un tema que por su amplitud no abordaremos en detalle, si que señalaremos 
algunas:

* Se mejora la  mantenibilidad de las aplicaciones gracias a su centralización. Al
  residir la aplicación en el servidor, desaparece el problema de la distribución
  de las mismas. Por ejemplo, los cambios en la interfaz de usuario son realizado
  una sola vez en el servidor y tienen efecto inmediatamente en todos los clientes.
  
* Se aumenta la capacidad de interacción y comunicación entre los usuarios de la 
  misma, así como de su gestión.
  
* Al ser *HTTP* un protocolo de comunicación ligero y “sin conexión” 
  (*conectionless*) se evita  mantener conexiones abiertas con todos y cada uno de
  sus clientes, mejorando la eficiencia de los servidores.
 
* Para utilizar la aplicación, los usuarios tan solo necesitan tener instalado un
  software denominado  navegador web (browser). Esto reduce drásticamente los
  problemas de portabilidad y distribución. También permite que terminales ligeras,
  con poca capacidad de proceso, puedan utilizar “grandes” aplicaciones ya que su
  función se limita a mostrar mediante el navegador los datos que le han sido 
  enviado. 
  
* El desarrollo de dispositivos móviles con conectividad a redes expande el 
  dominio de uso de las aplicaciones web y abre nuevos mercados. 
  
* Se puede acceder a la aplicación desde cualquier punto con acceso a la red donde
  preste servicio la aplicación. Si se trata de Internet, desde cualquier parte del
  mundo, si se trata de una intranet desde cualquier parte del mundo con acceso a
  la misma. Todo ello sin necesidad de instalar nada más que el navegador en la 
  computadora cliente (punto anterior).
  
* Los lenguajes utilizados para construir las aplicaciones web son relativamente
  fáciles de aprender. Además algunos de los más utilizados ,como *PHP* y *Python*,
  se distribuyen con licencias libres y existe una gran cantidad de documentación 
  de calidad disponible en la propia red Internet.
  
* Recientemente  han aparecido en escena varias plataformas y frameworks de 
  desarrollo (por ejemplo *Zend Framework*, *CakePHP*, *symfony*, *Symfony2*) que 
  facilitan la construcción de las aplicaciones web, reduciendo el tiempo de 
  desarrollo y mejorando la calidad.

Obviamente no todo son ventajas; incluso algunas de las ventajas que hemos señalado
pueden convertirse en desventajas desde otros puntos de vista. Por ejemplo:

* el hecho de que los cambios realizados en una aplicación web sean efectivos
  inmediatamente en todos los clientes que la usan, puede dejar sin servicio a un
  gran número de usuarios si este cambio provoca un fallo (intencionado si se trata 
  de un ataque, o no intencionado si se trata de una modificación que no ha sido
  debidamente probada). Esto repercute en la necesidad de aumentar las precauciones
  y la seguridad por parte de los responsables técnicos que mantienen la aplicación.

* La disponibilidad de la aplicación es completamente dependiente de la 
  disponibilidad de la red. Así la aplicación web será útil en entornos donde se
  garantice la estabilidad de la red. Los programadores necesitan dominar las
  distintas tecnologías y conceptos que, en estrecha colaboración, conforman la
  aplicación (*HTTP*, *HTML*, *XML*, *CSS*, *javascript*, lenguajes de scripting
  del lado del servidor como *PHP*, *Java* o *Python*, …)
  
* La triste realidad de las incompatibilidades entre navegadores.

No obstante, la realidad demuestra que el interés por las aplicaciones web es un 
hecho consumado, lo cual seduce a los programadores de todo el mundo a formarse en
las tecnologías y estrategias que permiten desarrollarlas. Este sitio tiene como 
objetivo presentar algunas de las más exitosas como *symfony 1.4* y  *Symfony2*.

Desarrollo rápido y de calidad de  aplicaciones web
---------------------------------------------------

La experiencia adquirida tras muchos años de construcción de aplicaciones 
informáticas de escritorio, dio lugar a la aparición de entornos y frameworks de
desarrollo que no solo hacían posible construir rápidamente  las aplicaciones, si
no que además cuidaban la calidad de las mismas. Es lo que técnicamente se conoce
como *Desarrollo Rápido de Aplicaciones*. 

Sin embargo, el desarrollo de aplicaciones web es muy reciente, por lo que estas 
herramientas de desarrollo rápido y de calidad no han aparecido en el mundo de la
web hasta hace bien poco. De hecho la construcción de una aplicación web de
calidad no ha estado exenta de dificultades debido a esta carencia. Afortunadamente
contamos desde hace unos pocos años con frameworks de desarrollo de aplicaciones
web que facilitan el desarrollo de las mismas y están haciendo que el concepto de 
*Desarrollo Rápido de Aplicaciones* en este campo sea una realidad.

Desarrollar con  ayuda de frameworks hace más sencillo la construcción de aplicaciones
web que satisfagan las siguientes características, deseables en cualquier tipo de
aplicación informática profesional al margen de sus requisitos específicos.

* Fiabilidad. La aplicación debe responder de forma que sus resultados sean 
  correctos y podamos fiarnos de ellos. También implica que los datos que 
  introducimos como entrada sean debidamente validados para asegurar un 
  comportamiento correcto. 
  
* Seguridad. La aplicación debe garantizar la confidencialidad y el acceso a la
  misma a usuarios debidamente autentificados y autorizados. En el caso de las 
  aplicaciones web esto es especialmente importante puesto que residen en
  computadores que, al pertenecer a una red, son accesibles a una gran cantidad de
  personas. Lo que significa que inevitablemente están expuestas a ser atacadas
  con fines maliciosos. Por ello deben incorporar mecanismos de protección ante 
  conocidas técnicas de ataque web como puede ser el *Cross Site Scripting (XSS)*.
  
* Disponibilidad. La aplicación debe prestar servicio cuando se le solicite. Es 
  importante, por tanto, que los cambios requeridos por operaciones relacionadas 
  con el mantenimiento (actualizaciones, migraciones de datos, migraciones de la 
  aplicación a otros servidores, etcétera) sean sencillos de controlar. De esa
  manera se evitarán largas temporadas de inactividad. La disponibilidad es una de
  las características más valoradas en las aplicaciones web, ya que el 
  funcionamiento de la misma no depende, por lo general, de sus usuarios si no de
  los responsables técnicos del sistema donde se encuentre alojada. Hay que pensar
  en ellos y ponérselo fácil cuando necesiten realizar este tipo de tarea. 
  También es importante que los errores de funcionamiento debidos a errores de 
  programación (*bugs*) sean rápidamente diagnosticados y resueltos para mejorar 
  tanto la disponibilidad como la fiabilidad de la aplicación.
  
* Mantenibilidad. A medida que se usa una aplicación, aparecen nuevos requisitos y
  funcionalidades que se desean ofrecer. Un sistema mantenible permite ser
  extendido sin que ello suponga un coste muy alto, minimizando la probabilidad de
  introducir errores en los aspectos que ya estaban funcionando antes de emprender
  la implementación de nuevas característcas.
  
* Escalabilidad, es decir, que la aplicación pueda ampliarse sin perder calidad en
  los servicios ofrecidos, lo cual se consigue diseñándola de manera que sea 
  flexible y modular.

Qué conocimientos se requieren para desarrollar aplicaciones web
----------------------------------------------------------------

En primer lugar, y como es obvio, hay que tener ciertos conocimientos sobre programación
de aplicaciones informáticas, siendo especialmente importante manejarse con soltura con 
la programación orientada a objetos.

Por otro lado, las aplicaciones web surgen como un puzzle que combina piezas de distintas 
tecnologías más o menos relacionadas pero diferentes:

* El protocolo *HTTP*  y los servidores web, 

* Los lenguajes de marcado *HTML* y *XML*,

* Las hojas de estilo *CSS's*,

* Javascript como lenguaje de script del lado del cliente,

* Los lenguajes de script del lado del servidor (*PHP*, Java, Python fundamentalmente),

* Los fundamentos de la programación orientada a objetos,

* Los fundamentos de las bases de datos relacionales y los sistemas gestores de 
  base de datos.
  
No hay que ser un *ninja* experto en cada uno de estas tecnologías, pero sí es importante
conocerlas hasta el punto de saber cual es el papel que desempeña cada una y como se relacionan
entre sí.

Qué le pedimos a una aplicación web profesional (es decir, en condiciones)
--------------------------------------------------------------------------

Cuando finalices el curso habrás adquirido suficiente conocimiento para desarrollar
aplicaciones web mediante el empleo del framework de desarrollo en *PHP* *Symfony2*.
Ello significa a grandes rasgos que serás capaz de construir aplicaciones web que:

* Son altamente modulares, extensibles y escalables.

* Separan claramente la lógica de negocio de la presentación, permitiendo que el
  trabajo de programación y de diseño puedan realizarse independientemente.
  
* Incorporaran un sistema sencillo, flexible y robusto garantizar la seguridad a
  los niveles de autentificación y autorización.
  
* Acceden a las bases de datos a través de una capa de abstracción que permite 
  cambiar de sistema gestor de base de datos sin más que cambiar un parámetro de 
  configuración. No es necesario tocar ni una sola línea de código para ello.
  
* Cuentan con un flexible sistema de configuración mediante el que se puede cambiar
  gran parte del comportamiento de la aplicación sin tocar nada de código. Esto 
  permite, entre otras cosas, que se puedan ejecutar en distintos entornos: 
  de producción, de desarrollo y de pruebas, según la fase en la que se encuentre
  la aplicación.
  
* Pueden ofrecer el resultado final en varios formatos distintos (*HTML, XML, JSON,
  RSS, txt, …*) gracias al avanzado sistema de generación de vistas,

* Cuentan con un potente sistema de gestión de errores y excepciones, especialmente
  útil en el entorno de desarrollo.

* Implementan un sistema de caché que disminuye los tiempos de ejecución de los
  scripts.
  
* Incorpora por defecto mecanismos de seguridad contra ataques XSS y CSRF.

* Pueden ser internacionalizadas con facilidad, aunque la aplicación no se haya 
  desarrollado con la internacionalización como requisito.
  
* Incorporan un sistema de enrutamiento que proporciona URL's limpias, compuestas 
  exclusivamente por rutas que ocultan detalles sobre la estructura de la
  aplicación.

* Cuentan con un avanzado sistema de autentificación y autorización.
  
Los materiales que se incluyen en este sitio tratan de aportar conocimientos y *know how*
que ayuden a construir aplicaciones web con las características que acabamos de enumerar.

.. _`Desarrollo de aplicaciones web con Symfony2`: https://centrovirtual.educacion.es/Symfony14/mentor.php/areasCursosWeb/mostrarInfoCurso/idCurso/958
.. _`Aula Mentor`: http://www.aulamentor.es

