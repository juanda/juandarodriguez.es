.. codeauthor:: Juan David Rodríguez García <juandavid.rodriguez@ite.educacion.es>

Tutorial: Symfony2 a vista de pájaro
====================================

.. note:: 

   Este tutorial es la adaptación de la unidad 3 del curso `Desarrollo de aplicaciones web con Symfony2`_ del proyecto `Aula Mentor`_:

El objetivo este tutorial es ofrecer una visión panorámica del framework PHP para el
desarrollo de aplicaciones web *Symfony2*. El desarrollo de una sencilla aplicación
aplicación web servirá como elemento vertebrador de este documento.

Para seguir el tutorial necesitarás un entorno con:

* un servidor web con PHP 5.3.x (x>2)

* un servidor MySQL 5

* tu IDE o editor favorito)


Descripción de la aplicación
----------------------------

Vamos a construir una aplicación web para elaborar y consultar un repositorio
de alimentos con datos acerca de sus propiedades dietéticas. Utilizaremos una
base de datos para almacenar dichos datos que consistirá en una sola tabla con
la siguiente información sobre alimentos:

* El nombre del alimento,
* la energía en kilocalorías ,
* la cantidad de proteínas,
* la cantidad hidratos de carbono  en gramos
* la cantidad de fibra en gramos  y
* la cantidad de grasa en gramos,

todo ello por cada 100 gramos de alimento.

Aunque se trata de una aplicación muy sencilla,  cuenta con los elementos
suficientes para mostrar las características básicas de *Symfony2*.

Utilizando algún cliente *MySQL* (*phpMyAdmin*, por ejemplo) crea la siguiente base
de datos para almacenar los alimentos e introduce algunos registros para probar la 
aplicación.

.. code-block:: sql
 
    CREATE TABLE `alimentos` (
      `id` int(11) NOT NULL AUTO_INCREMENT,
      `nombre` varchar(255) NOT NULL,
      `energia` decimal(10,0) NOT NULL,
      `proteina` decimal(10,0) NOT NULL,
      `hidratocarbono` decimal(10,0) NOT NULL,
      `fibra` decimal(10,0) NOT NULL,
      `grasatotal` decimal(10,0) NOT NULL,
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB  DEFAULT CHARSET=utf8;



¿Qué es *Symfony2*?
-------------------

La respuesta rápida, no por ello menos cierta, es que *Symfony2* es un framework
*PHP* para  el desarrollo  de aplicaciones  web. Sin  embargo, los  creadores de
*Symfony2* no la darían por  buena. Posiblemente tampoco aprobasen completamente
lo  que  voy  a  decir  a  continuación  (¡son  unos  programadores,  además  de
excelentes, muy "quisquillosos"!).

El  objetivo principal  de *Symfony2*  no  es tanto  desarrollar otro  framework
*PHP*,  como desarrollar  un conjunto  de componentes  estables, independientes,
fácilmente acoplables  (que no  acoplados) para  formar sistemas  más complejos,
mediante los cuales se puedan  resolver problemas relacionados con el desarrollo
de aplicaciones  web en  *PHP*. Obviamente  no es  necesario utilizar  todos los
componentes  de  *Symfony2*;  gracias  a su  absoluta  independencia  se  pueden
utilizar  únicamente aquellos  que se  requieran  para resolver  el problema  en
cuestión. *Symfony2* se presenta así como una  especie de *Lego* en el mundo del
desarrollo en *PHP*.

Con estos componentes,  uno de los problemas  que se puede resolver es  el de la
creación de  un framework de  desarrollo web. De hecho, uno de los primeros
productos que se han construido con estos componentes ha sido el framework de
desarrollo conocido como *Distribución  Standard de Symfony2*,  o más  brevemente
*Symfony2* sin  más. Y  es precisamente  del estudio de esta distribución de lo que
trata este tutorial.

Por ello, el término  *Symfony2* puede referirse a:

1. Los componentes de *Symfony2*

2. El framework de *Symfony2*, o distribución standard de *Symfony2*.

En el momento en que se escribe este texto, lo más probable es que se trate de lo
segundo.

Los componentes de *Symfony2*, se están  utilizando para otros proyectos [1]_, y
su uso  aumentará cuando dispongan  de una  documentación tan exhaustiva  y bien
elaborada como la  que cuenta en estos momentos el  framework *Symfony2*. Es muy
probable (ojalá) que cuando este curso  vea la luz esta situación haya cambiado [2]_.
Entonces, a lo mejor sería interesante elaborar otro tutorial sobre los componentes
de *Symfony2*, pero sería eso: otro  tutorial. Los componentes son los elementos de
bajo  nivel del  framework *Symfony2*,  y para  utilizar dicho  framework no  es
necesario conocerlos.

De todas formas vamos a aprovechar este apartado para enumerar los componentes
de *Symfony2*, dando una breve descripción de su funcionalidad. Pero, repetimos:
no es necesario conocerlos para utilizar el framework, que es de lo que trata este
tutorial. Damos esta información con el fin de calmar al curioso y de aclarar las 
posibles confusiones que se dan al hablar de *Symfony2* entre los componentes y el
framework. Obviamente, el contexto aclarará de que se habla en cada ocasión.

====================   ======================================================================================================
Componente             Descripción
====================   ======================================================================================================
DependencyInjection_   Automaticación de la Inyección de dependencias
EventDispatcher_       Implementación del patrón observer
HttpFoundation_        Proporciona una capa orientada a objetos para el tratamiento del HTTP
DomCrawler_            Para navegar el DOM de un documento HTML
ClassLoader_           Autoloader que implementa PSR-0 standard (forma standard de autocargar classes con espacios de nombre)
CssSelector_           Para convertir un CSS selector en un XPath
HttpKernel             Proporciona la parte dinámica de la especificación HTTP
BrowserKit             Simula el comportamiento de un Browser
Templating_            Proporciona elementos para construir sistemas de plantillas
Translation            Soporte para las traducciones
Serializer             Para crear arrays a partir de estructuras complejas como gráficos
Validator              Validaciones basadas en JSR-303 Bean Validation specification
Security               Infraestructura para el tratamiento de la autentificación y la autorización
Routing_               Potente sistema para asociar rutas a acciones
Console_               Para desarrollar herramientas CLI 
Process_               Para ejecutar procesos del sistema
Config                 Herramientas para cargar configuraciones de distintas fuentes
Finder_                Para encontrar archivos en el sistema de archivos
Locale_                Para tratar la localización cuando no está disponible la extensión ``intl``
Yaml_                  Para la manipulación de archivos de configuración en formato YAML
Form                   Herramientas para definir formularios, pintarlos y asociarle datos
====================   ======================================================================================================

Y estas son las "tripas" del monstruo *Symfony2*. No hablaremos mucho más acerca
de estos componentes a lo largo del cursos. Pero que sepas que existen.
Puede que te ayuden a resolver tu próximo proyecto, y muy probablemente sean
los ladrillos fundamentales con los que se construyan muchas de las aplicaciones
*PHP* en un futuro no muy lejano.

Instalación y configuración de *Symfony2*
-----------------------------------------

A partir de este momento, y mientras no lo especifiquemos explicitamente, cuando
hablemos de *Symfony2*  nos estamos refiriendo al framework, concretamente a la
edición estándard.

En este apartado vamos a instalar y configurar *Symfony2*, y lo dejaremos listo 
para construir la aplicación de gestión de alimentos sobre él.

Bájate de http://symfony.com/download la última versión de la rama 2.0 de
*Symfony2*. Verás que hay una modalidad normal y otra *without vendors*. Utiliza
la primera. 

.. note:: 
   
   La modalidad normal contiene todas las librerías de terceros (*vendors*) 
   necesarias para comenzar a trabajar con el framework, mientras que la modalidad
   *without vendors*, como su nombre indica, viene sin estas librerías, razón
   por lo que hay que instalarlas posteriormente mediante una herramienta incluida
   con *Symfony2* (``bin/vendors``) que utiliza el sistema de control de versiones
   ``git`` para bajar las últimas versiones desde el repositorio de *github* [3]_,
   donde se encuentra todo el código de *Symfony2*. 

Descompríme el archivo descargado en algún directorio accesible al servidor web,
esto es, dentro de su *Document root*. Para que la aplicación funcione, el 
servidor web debe poder escribir en los directorios ``app/cache`` y ``app/logs``.
Si estás utilizando un sistema operativo tipo *UNIX* (*Ubuntu*, *MacOSX*, 
etcétera), la forma más fácil de dar dichos permisos es:
  
.. code-block:: bash
 
   chmod -R 777 app/cache app/logs

.. note:: 

   Durante toda el tutorial suponemos que has hecho esta operación directamente en 
   el *Document root* del servidor web, de manera que tendrá la siguiente 
   estructura de directorios:

   .. code-block:: bash
    
    /var/www/    (o donde tengas mapeado tu Document root)
        |
        └── Symfony
	    |
	    ├── LICENSE
	    ├── README.md
	    ├── app/
	    ├── bin/
	    ├── deps
	    ├── deps.lock
	    ├── src/
	    ├── vendor/
	    └── web/
   
   Y que tanto el servidor web como el servidor de MySQL están instalado en la
   máquina local.

A continuación comprobamos que nuestro sistema cumple los requisitos mínimos
ejecutando por la interfaz de comandos la siguiente orden:

.. code-block:: bash

   php app/check.php

Si el resultado nos señala algún error, debemos resolverlo antes de continuar. Una
vez que pasemos al menos los requisitos obligatorios (*mandatory requirements*), 
podemos ejecutar la demo que viene incorporada en la distribución standard de
*Symfony2*. Para ello apunta con tu navegador a la siguiente *URL*:

.. code-block:: bash

   http://localhost/Symfony/web/app_dev.php


¡Y juega un poquito!, Por ejemplo, pica en *Run the demo* y navega por los 
distintos enlaces. Fíjate en la pinta que tienen las *URL's*. La demo muestra el
código que genera las páginas de la propia demo. Fíjate en él detenidamente.
Verás que muestra dos partes: el del controlador y el de la plantilla (*template*).
Es decir, dos elementos del patrón de diseño MVC (Modelo - Vista - Controlador).

Ya has visto en acción la primera aplicación construida con *Symfony2*. Ahora 
vamos a describir la manera en que *Symfony2* organiza el código.

*Symfony2* organiza los archivos en dos grupos: los que deben estar directamente 
accesibles al servidor web (*CSS's*, *Javascript*, imágenes y el controlador 
frontal) y los que pueden ser incluidos desde el controlador frontal (librerías 
*PHP* y ficheros de configuración). Los primeros viven en el directorio ``web``,
y los segundos, según su funcionalidad, están repartidos entre los directorios
``app`` , ``src`` y ``vendor``. 

.. note::
   En una instalación en un entorno de producción, el **Document root** del servidor
   web (o del **Virtual host** dedicado para la aplicación), debe coincidir con el 
   directorio ``web``, y el resto de directorios deben ubicarse fuera del
   **Document root**. No obstante, en un entorno de desarrollo podemos relajarnos y,
   para no andar afinando las configuraciones del servidor web, se puede ubicar todo
   el código dentro del **Document root**.

   Para paliar el efecto de posibles despistes o malas prácticas por 
   desconocimiento, pereza y otras fatales causas, los directorios ``src`` y
   ``app``, contienen un fichero ``.htaccess`` que indica al servidor web que
   no debe mostrar su contenido.

Veamos ahora para que se utiliza cada uno de estos directorios.

El directorio web
^^^^^^^^^^^^^^^^^

Poco hay que decir ya de este directorio, aquí encontraremos el controlador
frontal y todos los *assets* de la aplicación: *CSS's*, *Javascipts*, imágenes,
etcétera. 

Esta es la estructura del directorio:

.. code-block:: bash

   web
   ├── app_dev.php
   ├── apple-touch-icon.png
   ├── app.php
   ├── bundles
   │   ├── acmedemo
   │   ├── framework
   │   ├── sensiodistribution
   │   └── webprofiler
   ├── config.php
   ├── favicon.ico
   └── robots.txt

Podemos ver 3 scripts *PHP*: 

* ``config.php`` es un script que asiste en la configuración del framework. No
  es imprescindible. De hecho cuando uno se siente confortable con *Symfony2*,
  es más sencillo realizar la configuración directamente sobre el código
  fuente. Pero para empezar puede servir de ayuda. Si lo utilizas ten en cuenta
  los permisos de los ficheros del directorio ``app/config``, pues este script
  debe poder escribir allí.

* ``app.php`` es el controlador frontal de la aplicación, es decir, es el script
  *PHP* por el que pasan todas las peticiones. Este script decide el flujo que debe
  seguir la aplicación "observando" los parámetros que se hayan  pasado en cada
  petición (*request*). Un conjunto de parámetros determinado se denomina ruta. 
  Veamos algunos ejemplos para aclararlo: en
  
  ``http://tu.servidor.web/app.php/articulo/1``,

  ``app.php``, es el controlador frontal y ``articulo/1`` es una ruta de la aplicación.

* ``app_dev.php`` también es el controlador frontal de la aplicación. ¿Cómo?
  ¿dos controladores frontales? ¡eso no parece encajar con lo que acabamos de decir!.
  Bueno tranquilos, tiene su explicación. Se trata de lo que se denomina en 
  *Symfony2* el controlador frontal de **desarrollo**. En principio pinta lo
  mismo que ``app.php``, pero le añade una barra de depuración que ofrece
  muchísima información sobre todo lo relacionado con la ejecución del *script*.
  Puedes ver la barra de depuración en la demo que has ejecutado hace un momento.
  Se encuentra abajo de la página. Explórala un poco, te asombrarás de la cantidad
  de información que te proporciona. Cuando desarrollamos es muy conveniente
  utilizar este controlador frontal, pero en producción NUNCA debe utilizarse,
  pues  daríamos a los usuario de la web información que podría comprometer
  nuestro sistema.

Por otro lado los *assets*  se ubicarán en el directorio 
``bundles/nombre_bundle``, donde ``nombre_bundle`` es el nombre del *bundle* al
que pertenece el *asset* en cuestión. Vale, ¿y que es un *bundle*?, pues por lo
pronto quedate con que "es la unidad funcional de código que utiliza *Symfony2*".
Algo así como una de las piezas del Lego *Symfony2*. En la sección 
:ref:`bundles` hablaremos de estos "personajes" con más detalle.

El directorio ``app``
^^^^^^^^^^^^^^^^^^^^^

La finalidad de este directorio es alojar a a los scripts *PHP* encargados de
los procesos de carga del framework (lo que se conoce como **bootstraping**) y a
todo lo que tenga que ver con la configuración general de la aplicación. 
Los archivos de este directorio son los encargados de **unir** y dar cohesión a
los distintos componentes del framework.

Son especialmente importantes los ficheros ``autoload.php`` y ``AppKernel.php``,
ya que hay que tocarlos cada vez que extendemos el framework con nuevas 
funcionalidades, es decir cada vez que incorporamos nuevos *bundles* (vamos
poniendo en circulación a esta palabreja que usaremos hasta la saciedad). 

En ``autoload.php`` se mapean los espacios de nombres contra los directorios en
los que residirán las clases pertenecientes a dichos espacios de nombre. De
esa manera el proceso de autocarga de clases sabrá donde tiene que buscar
las clases cuando se *usen* dichos espacios, sin necesidad de incluir 
explicitamente (esto es, usando ``include`` o ``require`` ) los archivos donde se
definen las clases.

En ``AppKernel.php``, se declaran los *bundles* que se utilizarán en la
aplicación.

En el directorio ``config`` se encuentran los archivos de configuración de la
aplicación: ``config.yml``, ``routing.yml`` y ``security.yml``. 

El sistema de configuración de *Symfony2* permite trabajar con distintos
entornos de ejecución. Los más típicos son ``prod``, para producción y
``dev``, para desarrollo. Pero se pueden definir tantos entornos como deseemos. 
En el controlador frontal se indica qué entorno deseamos utilizar en la 
ejecución del script. Fíjate en la línea 22 de ``web/app_dev.php``, o en la
línea 9 del ``web/app.php``:

.. code-block:: php 

   ...
   $kernel = new AppKernel('prod', false);
   ...

El primer argumento decide el entorno de ejecución que se utilizará. ¿Y para que
sirve esto?. *Symfony2* utiliza este dato para saber qué ficheros de 
configuración debe cargar. Supongamos, por ejemplo, que se especifica ``dev`` como
entorno de ejecución. Entonces, si existe el fichero ``config_dev.yml`` lo cargará,
y si no es así cargará ``config.yml``. Lo mismo ocurre con los ficheros 
``routing.yml``, ``security.yml`` y ``services.yml``. Más adelante estudiaremos 
para que sirven cada uno de ellos. Por lo pronto nos conformaremos con saber
la dinámica de funcionamiento. 

Los entornos proporcionan mucha flexibilidad a la hora de desarrollar una
aplicación. Vamos a ilustrar con un ejemplo esta flexibilidad. Un caso que nos
encontramos habitualmente es que la aplicación que estamos construyendo debe 
enviar e-mails. Es bastante molesto tener que disponer de cuentas reales y
gestionarlas para que podamos probar la aplicación mientras desarrollamos. Podemos
utilizar este sistema de configuración para indicar al framework que en el 
entorno de desarrollo se envíen todos los e-mails a una sola cuenta, o incluso
que no se envíen. Otro ejemplo típico podría ser el definir unos parámetros de 
conexión a la base de datos para el entorno de producción y otro para el de 
desarrollo.

Una estrategía muy adecuada para tratar con los ficheros de configuración cuando
queremos que haya partes comunes y partes diferentes en cada entorno, es definir
todos los parámetros comunes en el fichero ``fichconfig.yml`` (donde ``fichconfig``
es ``config``, ``security``, ``routing`` o ``services``), y los particulares de
cada entorno en el fichero ``fichconfig_env.yml`` (donde ``env`` es ``dev``, 
``prod`` o cualquier otro nombre de entorno que usemos). Por último importamos
los primeros (comunes) desde los últimos (particulares) de la siguiente manera:

Inicio del fichero ``fichconfig_env.yml``

.. code-block:: yaml

   imports:
    - { resource: fichconfig.yml }
    ...

Puedes comprobar que esta es la estrategia utilizada por la distribución standard 
de *Symfony2* con los ficheros ``config.yml``, ``config_dev.yml`` y
``config_prod.yml``.

Para acelerar la ejecución de los scripts, la configuración, el enrutamiento y las
plantillas de twig son compiladas y almacenadas en el directorio ``cache``. Por
otro lado, los errores y otra información de interés acerca de eventos que ocurren
cuando se ejecuta el framework, son registrados en archivos que se almacenan en el
directorio ``logs``. Por eso **estos dos directorios deben tener permisos de 
escritura para el servidor web**.

Por último, en este directorio tan "denso", encontramos la navaja suiza de
*Symfony2*, la aplicación ``app/console``. Prueba a ejecutarla sin pasarle 
ningún argumento. Verás una lista con todas las tareas que se pueden lanzar por
línea de comandos.

.. code-block:: bash

   php app/console


El directorio ``vendor``
^^^^^^^^^^^^^^^^^^^^^^^^

Aquí se aloja todo el código funcional que no es tuyo. Es lo que tradicionalmente
se conoce como librerías de terceros. Entre otras cosas, el directorio contiene
los componentes de *Symfony2*, el ORM *Doctrine2* y el sistema de plantillas
*twig*. Cuando amplies tu aplicación con nuevos *bundles* de terceros instalados 
automáticamente con la aplicación ``bin/vendors``, será aquí donde se ubique el
código.

El directorio ``src``
^^^^^^^^^^^^^^^^^^^^^

Es el directorio donde colocarás tu código. Más concretamente: tus *bundles*.
A base de utilizar este palabro acabarás por asimilarlo antes de que te lo
expliquemos :-).

El directorio ``bin``
^^^^^^^^^^^^^^^^^^^^^

El nombre de este directorio es un clásico en el mundo *UNIX*. En él se colocan
archivos ejecutables. La distribución standard solo trae el ejecutable ``vendors``
que se utiliza, en combinación con el fichero ``deps`` (dependencias), para 
instalar componentes de terceros (*vendors*).


Y con esto acabamos la descripción de los directorios de *Symfony2*. Ha llegado
el momento de hablar de los *bundles*, esos grandes desconocidos (¡por ahora!).

.. _bundles:

Los Bundles: Plugins de primera clase
-------------------------------------

Si los creadores de *Symfony2* hubieran elegido la palabra *plugin* en lugar de
*bundle*, es probable que te hubieses hecho una idea más concreta de lo que es
un *bundle*. Pues bien, por lo pronto, piensa que un *bundle* es un *plugin*, por
que no es ni más ni menos que eso.

Cualquier framework que se precie debe ofrecer un mecanismo de extensión que 
permita ampliar la aplicación sin compromenter la escalabilidad. Para ello las
piezas que se añaden al sistema deben ser bloques prácticamente autónomos y con
una interfaz sencilla para engancharlos (*to plug*, en inglés) al sistema. A estos
bloques se les conoce a lo largo y ancho de la galaxia con el nombre de *plugin* 
(o complemento, en castellano). ¿Por qué los creadores de *Symfony2* han decidido
llamarles *bundles* en su lugar? Lo mismo hay alguna razón teórica que se me 
escapa. Pero de lo que si estoy seguro es de que hay una razón histórica:

El antecesor de *Symfony2*, el fantástico  *symfony 1.x* organiza el código en 
*aplicaciones*, que a su vez están formadas por *módulos* con la implementación
de las acciones. Además ofrece un mecanismo de extensión basado en *plugins*, los 
cuales también organizan el código en *módulos* con sus acciones. Pero a pesar 
de este paralelismo las aplicaciones son "más importantes" que los *plugins*. De 
hecho, las aplicaciones pueden usar módulos de los plugins, pero lo contrario no
tiene sentido tal y como está concebido *symfony 1.x*. Con el tiempo los 
desarrolladores se dieron cuenta de que era más fácil de mantener y organizar los plugins, ya que son bloques de código autónomos y fácilmente acoplables a la 
aplicación. Este hecho llevó de forma natural  a reorganizar la aplicación
colocando todo el código funcional en los *plugins*. Las aplicaciones se quedaban 
prácticamente vacías de código y tan solo contenían ficheros de configuración.

Así pues, en *Symfony2* decidieron olvidarse del concepto de aplicación (en el 
sentido de *symfony 1.x*), y obligar a que todo el código funcional se organizase
en *plugins*. Es como hacer a los *plugins* ciudadanos de primera clase del 
framework. Finalmente, para evitar cualquier confusión y dirimir la diferencia 
entre *plugin* y aplicación, decidieron usar la palabra *bundle*. Y eso es todo.
Si no conoces *symfony 1.x*, seguro que hubieras preferido llamarle *plugin*. Y si lo conoces
es probable que también.

En fin, lo que realmente debes saber:

.. important::

   Un *bundle* no es más que un directorio que aloja todo aquello relativo a una 
   funcionalidad determinada. Puede incluir clases *PHP*, plantillas, 
   configuraciones, *CSS's* y *Javascript*.

La aplicación *gestión de alimentos* en *Symfony2*
--------------------------------------------------

Y llegó el momento de ponerse a cocinar código.

Generación de un *Bundle*
^^^^^^^^^^^^^^^^^^^^^^^^^
La primera idea que debe quedar clara, expresada de manera simplista, es que 
*"todo es un bundle"* en *Symfony2*. Por tanto, si queremos desarrollar una
aplicación necesitaremos, por lo menos, tener un *bundle* para alojar el código de
la misma. Comencemos por ahí. El siguiente comando de *Symfony2* nos ayuda a 
generar el esqueleto de un bundle de manera interactiva:

.. code-block:: bash
 
   php app/console generate:bundle
   
A cada pregunta que nos hace le acompaña una pequeña ayuda. En primer lugar nos
pregunta por el espacio de nombre que compartiran las clases del *bundle*. La 
recomendación, como se dice en el texto de ayuda del comando, es que comience por
el nombre del fabricante del *bundle*, el nombre del proyecto o del cliente, 
seguido, opcionalemente, por una o más categorías, y finalizar con el nombre del
*bundle* seguido del sufijo *Bundle*. Es decir el nombre completo del espacio
de nombres del *bundle* debe seguir el siguiente patrón:

.. code-block:: bash

   Fabricante/categoria1/categoria2/../categoriaN/nombrebundleBundle

Ilustremos esto con varios ejemplos de nombres de *bundles* válidos:

.. code-block:: bash

   AulasMentor/AlimentosBundle
   AulasMentor/Tutorial/AlimentosBundle
   AulasMentor/CursoSf2/Tutorial/AlimentosBundle
   Jazzyweb/AlulasMentor/AlimentosBundle

Nos quedaremos con el último de los nombres para el *bundle* que vamos a 
construir. Con este nombre se quiere expresar algo así como que el *bundle*
``AlimentosBundle`` ha sido creado por ``Jazzyweb`` (una empresa ficticia) para
el cliente ``AulasMentor``. Como ves, cualquier nombre vale siempre que contenga
un nombre de fabricante (*vendor name*) y un nombre de *bundle*. En medio podemos
poner lo que queramos para organizar nuestro trabajo.

Introduce ``Jazzyweb/AulasMentor/AlimentosBundle`` como espacio de nombres del 
*bundle*. A continuación nos pregunta por el nombre del *bundle*. Y nos ofrece
una recomendación que es el mismo nombre del espacio de nombres anterior pero sin
los separadores ``/``. El nombre del *bundle* es importante pues, en ocasiones, 
hay que referirse al *bundle* por este nombre.

Presiona ``enter`` para aceptar la sugerencia.

El próximo paso es asignarle una ubicación en la estructura de directorios del 
proyecto. La flexibilidad de *Symfony2* permite que lo coloques donde quieras.
Pero es muy recomendable que lo coloques en el directorio ``src``, ya que está 
pensado para alojar nuestro código. Si lo haces así, te ahorrarás tener que 
incluir una línea de código en el fichero ``app/autoload.php`` para registrar el
espacio de nombre en el sistema de autocarga de clases. Esto último es así porque
en dicho fichero ya se ha contemplado que todas las clases que se aloje en ``src``
sean autocargadas asignándole como espacio de nombre raíz el mismo nombre que
la estructura de directorios computada desde ``src``. 

Presiona ``enter`` para aceptar la sugerencia. Cuando termines de generar el 
*bundle* verás como se ha creado en ``src`` el directorio 
``Jazzyweb/AlulasMentor/AlimentosBundle``, es decir un directorio que tiene la
misma estructura que el espacio de nombres que hemos asignado al *bundle*. Esto
es lo que se quiere decir de manera genérica en el párrafo anterior.

Los *bundles* llevarán asociados algo de configuración. Como mínimo será necesario
configurar las rutas que mapean las *URL's* en acciones del *bundle*. *Symfony2*
admite 4 formas de representar las configuraciones: con ficheros *XML*, *YML* o 
*PHP*, y mediante anotaciones, que es una manera de expresar parámetros de 
configuración en el propio código funcional aprovechando para ello los comentarios
de *PHP*.

Más adelante tendremos ocasión de utilizar las anotaciones y las entenderás mejor.
Llegados a este punto hemos de decir que la elección es una cuestión de gusto; 
discutir con alguien acerca de cual es la mejor opción sería una pérdida de tiempo.
Para el caso de la configuración de los *bundles* (prácticamente para definir 
rutas como veremos después) hemos elegido los fichero *YAML* como formato para
la configuración.

Selecciona (escribe) ``yml`` como formato de configuración.

Por último contesta ``yes`` a la pregunta de si quieres generar la estructura 
completa. Esta opción generará algunos directorios y archivos extra que siguen
las recomendaciones de *Symfony2* para alojar código. Es posible que no los 
utilices, pero no hacen "daño" y sugieren como debe organizarse el código. No
obstante el programador tiene bastante libertad a la hora de organizar los 
archivos del *bundle* como quiera.

Confirma la generación del código. Una vez generado, el asistente te realizará
dos preguntas más. Primera pregunta: ¿quieres actualizar automáticamente el Kernel?
y segunda pregunta ¿quieres actualizar directamente el *routing*? Contesta a las
dos afirmativamente. Vamos a ver con más detalle las consecuencias de estas 
actualizaciones automáticas.

Por una parte el *bundle*, como ya hemos explicado, es un bloque desacoplado y
reutilizable de código que agrupa a una serie de funcionalidades. Si queremos 
utilizarlo en nuestro proyecto debemos "notificarlo" al framework. Es decir,
hemos de "engancharlo". Esto se hace registrándolo en el archivo 
``app/AppKernel.php``. La primera actualización automática ha realizado dicho
registro. Abre ese archivo y fíjate como al final del método ``registerBundles()``
aparece la siguiente línea:

.. code-block:: php

   ...
   new Jazzyweb\AulasMentor\AlimentosBundle\JazzywebAulasMentorAlimentosBundle(),
   ...

Dicha línea ha sido insertada automáticamente como consecuencia de haber respondido
afirmativamente a la primera pregunta. El cometido de la línea es registrar el 
*bundle* recien creado en el framework para poder hacer uso del mismo.

La segunda actualización automática "enlaza" la tabla enrutamiento general de 
la aplicación con la tabla de enrutamiento particular del *bundle*. La tabla de
enrutamiento es la responsable de indicar al framework como deben mapearse las 
*URL's* en acciones *PHP*. Para ver como se ha realizado este enlace mira el
fichero ``app/config/routing.yml``:

.. code-block:: yaml

   JazzywebAulasMentorAlimentosBundle:
    resource: "@JazzywebAulasMentorAlimentosBundle/Resources/config/routing.yml"
    prefix:   /

Estas líneas han sido introducidas automáticamente como consecuencia de contestar
afirmativamente a la segunda pregunta. Observa que el apartado *resource* es la
dirección en el sistema de ficheros de la tabla de enrutamiento propia del *bundle*
que acabamos de crear. *Symfony2* sabe convertir ``@JazzywebAulasMentorAlimentosBundle`` 
en la ubicación del *bundle* pues está debidamente registrado. 

Es importante que conozcas como se acopla un *bundle* a la aplicación, pues si
falla la actualización automática del ``KernelApp.php`` y/o del ``routing.yml``,
debes realizarlas manualmente.

Ahora puedes echarle un vistazo al fichero ``routing.yml`` del *bundle* 
(``src/Jazzyweb/AulasMentor/AlimentosBundle/Resources/config/routing.yml``). Verás que
existe una ruta mapeada contra una acción. Después explicaremos los detalles de la
ruta. Esta última ruta sirve para probar el *bundle*. Así que accede desde tu 
navegador web a la siguiente *URL* (que es la que se corresponde con esta ruta de 
prueba)

.. code-block:: bash
   
   http://localhost/Symfony/web/app_dev.php/hello/alberto
   
Si todo va bien, obtendrás como respuesta un saludo. Puedes cambiar el nombre
del final de la ruta.

Resumiendo: Para desarrollar nuestra aplicación hemos de contar al menos con un
*bundle* para escribir el código. Según la complejidad de la aplicación será
más o menos adecuado organizar el código en varios *bundles*. El criterio a seguir
es el de agrupar en cada *bundle* funcionalidades similares o del mismo tipo.
Los bundles son bloques desacoplados y tienen asociado un espacio de nombre. Para
acoplar un bundle al framework hay que :

1. Registrar el espacio de nombre en el sistema de autocarga (fichero 
   ``app/autoload.php``. Este paso no es necesario si ubicamos al *bundle* en el
   directorio ``src``.

2. Registrar al bundle en el fichero ``app/AppKernel.php``. Esta operación se
   puede hacer automáticamente a través del generador interactivo de *bundles*,
   pero si fallase por alguna razón (por ejemplo que los permisos de dicho archivo
   no estén bien definidos). Habría que hacerlo a mano.

3. Importar las tablas de enrutamiento del *bundle* en la tabla de enrutamiento
   de la aplicación.

Anatomía de un *Bundle*
^^^^^^^^^^^^^^^^^^^^^^^

Si has seguido las indicaciones que hemos dado en este tutorial, debes tener en tu
directorio ``src`` dos directorios: ``Jazzyweb`` y ``Acme`` . El primero se 
corresponde con el *bundle* que acabamos de crear, y el segundo es un ejemplo
que viene de serie con la distribución standard de *Symfony2* y que contiene el
código de la demo con la que has jugado hace un rato. Vamos a utilizar este 
último para realizar la *disección* de un *bundle*, ya que está más rellenito de
código  que nuestro recien horneado y esquelético *bundle*. 

.. code-block:: bash

    Acme/
    └── DemoBundle
	├── AcmeDemoBundle.php
	├── Controller
	│   ├── DemoController.php
	│   ├── SecuredController.php
	│   └── WelcomeController.php
	├── ControllerListener.php
	├── DependencyInjection
	│   └── AcmeDemoExtension.php
	├── Form
	│   └── ContactType.php
	├── Resources
	│   ├── config
	│   │   └── services.xml
	│   ├── public
	│   │   ├── css
	│   │   │   └── demo.css
	│   │   └── images
	│   │       ├── blue-arrow.png
	│   │       ├── field-background.gif
	│   │       ├── logo.gif
	│   │       ├── search.png
	│   │       ├── welcome-configure.gif
	│   │       ├── welcome-demo.gif
	│   │       └── welcome-quick-tour.gif
	│   └── views
	│       ├── Demo
	│       │   ├── contact.html.twig
	│       │   ├── hello.html.twig
	│       │   └── index.html.twig
	│       ├── layout.html.twig
	│       ├── Secured
	│       │   ├── helloadmin.html.twig
	│       │   ├── hello.html.twig
	│       │   ├── layout.html.twig
	│       │   └── login.html.twig
	│       └── Welcome
	│           └── index.html.twig
	├── Tests
	│   └── Controller
	│       └── DemoControllerTest.php
	└── Twig
	    └── Extension
		└── DemoExtension.php

* ``AcmeDemoBundle.php`` es una clase que extiende a 
  ``Symfony\Component\HttpKernel\Bundle\Bundle`` y que define al *bundle*.
  Se utiliza en el proceso de registro del mismo (recuerda, en el fichero 
  ``app/AppKernel.php``). Todos los *bundles* deben incorporar esta clase (bueno,
  el nombre cambiará según el nombre del *bundle*)

* ``Controller`` es el directorio donde se deben colocar los controladores con las
  distintas acciones del *bundle*. Las acciones son las funciones (o métodos)  Lo lógico y recomendado, es crear una clase
  ``Controller`` por cada grupo de funcionalidades. Pero no es una exigencia, si
  quieres puedes colocar todas tus acciones en el mismo controlador. Cuando se
  genera un *bundle* se crea el controlador *DefaultController*.

* ``Dependency Injection``. Una de las características más sobresaliente de
  *Symfony2* es el uso intensivo que hace de la *Inyección de Dependencias*, un
  potente patrón de diseño mediante el que se facilita la creación y configuración
  de objetos que prestan servicios en una aplicación gracias a la gestión 
  automática de sus dependencias. Contribuye a crear un código más desacoplado y
  coherente. Presentaremos este concepto en un tutorial dedicado en exclusiva a
  la Inyección de Dependencias en *Symfony2*. Aunque no es un patrón complicado, es
  dificil de explicar con precisión y  claridad. 
  
  *Symfony2* nos ofrece dos maneras de "cargar" la configuración de las 
  dependencias y los servicios creados. Una más sencilla y directa, y otra más
  elaborada y apropiada para el caso en que nuestro *bundle* vaya a ser
  distribuido con la intención de que se utilice en otros proyectos *Symfony2*. En
  este directorio se ubican las  clases relacionadas con este segundo método de
  gestionar las dependencias.

* ``Resources``, es decir, recursos. Entendemos por recursos: los ficheros de
  configuración del *bundle* (directorio ``config``), los *assets* que requiere
  el *bundle* para enviar en sus respuestas (directorio ``public``) y las 
  plantillas con las que se *renderizan* (pintan) el resultado de las acciones de
  los controladores (directorio ``view``). Fíjate como en este *bundle*,
  las plantillas están  organizadas en tres directorios (``Demo``, ``Secured`` y 
  ``Welcome``) cuyos nombres coinciden con los de los controladores.   

* ``Test``, es el directorio donde viven las pruebas unitarias y funcionales del
  *bundle*.

Estos son los directorios más típicos de cualquier *bundle*, de hecho son los que
se generan automáticamente con el comando ``app/console generate:bundle``. Sin
embargo un *bundle* puede tener muchos más directorios y ficheros, organizados
como su creador crea conveniente. En el caso del *bundle* ``AcmeDemoBundle``,
puedes ver los siguientes "extras":

* ``Form`` es el directorio donde se colocarán las clases que definen los 
  formularios de la aplicación.

* ``ControllerListener.php``  describe un *event listener* que es un mecanismo muy
  adecuado de extender y alterar el flujo del framework sin  tener que tocar el
  código original del componente del framework que utiliza dicho sistema. Se trata
  de una característica avanzada de *Symfony2* raramente utilizada cuando uno se
  esta iniciando.

* ``Twig`` es un directorio propio de este *bundle*, en el que se ha implementado
  una extensión del sistema de plantillas.
  
Ahora ya nos encontramos con un mínimo bagaje para emprender el desarrollo del
*bundle* ``JazzywebAulasMentorAlimentosBundle`` y, por tanto de la aplicación.

Flujo básico de creación de páginas en *Symfony2*
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

La creación de páginas web con *Symfony2* involucra tres pasos:

1. Creación de la ruta que mapea la *URL* de la página en una acción de algún 
   controlador. Dicha ruta se registra en el archivo 
   ``config/Resources/routing.yml`` del *bundle*, que a su vez debe estar
   correctamente importado en el archivo de rutas general de la aplicación 
   ``app/config/routing``.

2. Creación de dicha acción en el controlador correspondiente. La acción, haciendo
   uso del modelo, realizará las operaciones necesarias y obtendrá los datos crudos
   (raw), es decir sin ningún tipo de formato, que facilitará a una plantilla para
   ser pintados (renderizados). El código de los controladores debe ubicarse en
   el directorio ``Controllers`` del *bundle*.
   
3. Creación de dicha plantilla. Esto se hace en el directorio ``Resources/view``.
   Con el fin de organizar bien las plantillas, es recomendable crear un 
   directorio con el nombre de cada controlador. También es muy recomendable
   utilizar *Twig* como sistema de plantillas, aunque también se puede utilizar
   *PHP*.


Estos pasos son, por supuesto, una guía general y mínima que debemos seguir en
la creación de las páginas de nuestra aplicación. No obstante, en muchos casos
tendremos que realizar otras operaciones que se salen de este flujo y que tienen
que ver más con la construcción del modelo de la aplicación.

Definición de las rutas del *bundle*
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Ya hemos visto que en *Symfony2* todas las peticiones a la aplicación se realizan a
través de un script *PHP* que se llama controlador frontal (``app.php``).
Este script "sabe" lo que debe devolver como respuesta al usuario "mirando" la *ruta*
que lo acompaña en cada petición. 

Cada ruta en *Symfony2* consiste en un conjunto de parámetros separados por el 
caracter ``/``. Ejemplos de *URL's* con rutas de *Symfony2*  serían:

.. code-block:: bash

   http://tu.servidor/app.php
   http://tu.servidor/app.php/listar
   http://tu.servidor/app.php/ver/4

Las rutas correspondientes serían:

.. code-block:: bash

   /
   /listar
   /ver/4

Es decir, los parámetros que siguen al controlador frontal. Esta forma de pasar 
parámetros a través de la *URL* mejora en varios aspectos a la clásica *query string* 
del tipo:

.. code-block:: bash

   ?param1=val1&param2=val2&...&paramN=valN

En primer lugar el usuario que utiliza el navegador “siente” que la *URL* que aparece
en la barra de direcciones forma parte de la aplicación que está utilizando. Por tanto,
cualquier *URL* llena de carácteres extraños y demasiado larga redunda en una 
degradación estética. En segundo lugar y más allá de cuestiones estéticas, cuando utilizamos query strings clásicas estamos dando información innecesaria al usuario, ya que
el nombre de los parámetros (``paramX``) es algo que tiene sentido únicamente para la 
aplicación en el servidor. Esta información extra, además de dar lugar a *URL’s* 
horribles, supone un problema de seguridad, ya que el usuario podría utilizarlas para
sacar conclusiones acerca de la aplicación y utilizarla para comprometerla.

El aspecto de las *URL's* puede mejorar aún más si utilizamos el módulo ``Rewrite``
del servidor web, ya que también podemos eliminar el nombre del controlador frontal
(``app.php``). Así además de mejorar el estilo de la *URL*, ocultamos al usuario
información acerca del lenguaje de programación que estamos utilizando en el servidor.

Nos quedarían *URL's* de este tipo:

.. code-block:: bash

   http://tu.servidor/
   http://tu.servidor/listar
   http://tu.servidor/ver/4

¡Mucho más legibles y elegantes!

.. note::

   En el directorio ``web`` existe un fichero ``.htaccess`` con el siguiente contenido:
   
   .. code-block:: bash
   
      <IfModule mod_rewrite.c>
        RewriteEngine On
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteRule ^(.*)$ app.php [QSA,L]
      </IfModule>
   
   La función de dicho fichero es, precisamente, reescribir las rutas anteponiendo
   ``app.php``, de manera que no sea necesario especificar el controlador frontal
   en la *URL*. Para que esto funcione es necesario que el servidor web tenga
   instalado el módulo ``Rewrite``, y permita el cambio de directivas a través
   de ficheros ``.htaccess``.

La siguiente tabla muestra las rutas que definiremos en nuestra aplicación y la 
acción que deben disparar.

========== =======================
URL        Acción
========== =======================
/          mostrar pantalla inicio
/listar    listar alimentos
/insertar  insertar un alimento
/buscar    buscar alimentos
/ver/x     ver el alimento *x*
========== =======================

En *Symfony2* las rutas se definen en el archivo ``app/config/routing.yml``. Para
que los *bundles* no pierdan la autonomía que debe caracterizarlos, las rutas que 
se mapean en un controlador de un determinado *bundle* deberían definirse dentro
del propio *bundle*. Concretamente en el archivo ``Resources/config/routing.yml``
del *bundle*. Y para hacerlas disponibles a la aplicación, se importa este último
fichero en ``app/config/routing.yml``.

.. note::

   Aunque el sitio recomendado para ubicar el fichero ``routing.yml`` de un 
   *bundle* es ``Resources/config``, *Symfony2* no lo exige, ya que en el archivo 
   ``app/config/routing.yml``, que es el que realmente define las rutas, puedes
   indicar la ruta concreta de los archivos que se quieren importar.
   
Abre el archivo ``src/Jazzyweb/AulasMentor/AlimentosBunle/Resources/config/routing.yml``
y borra las siguientes líneas:

.. code-block:: yaml

   JazzywebAulasMentorAlimentosBundle_homepage:
    pattern:  /hello/{name}
    defaults: { _controller: JazzywebAulasMentorAlimentosBundle:Default:index }

Las líneas que acabas de borrar definían la ruta de la acción de ejemplo que se
crea automáticamente al generar el bundle. Fíjate en la estructura de la definición
de una ruta; consisten en un identificador de la ruta
(``JazzywebAulasMentorAlimentosBundle_homepage``), 
que puede ser cualquiera siempre que sea único en todo el framework, el patrón
de la ruta (``pattern: /hello/{name}``), que describe la estructura de la ruta, y 
la declaración del controlador sobre el que se mapea la ruta
(``defaults: { _controller: JazzywebAulasMentorAlimentosBundle:Default:index }``).

Creamos nuestra primera ruta añadiendo al archivo anterior lo siguiente:

.. code-block:: yaml

   JAMAB_homepage:
    pattern:  /
    defaults: { _controller: JazzywebAulasMentorAlimentosBundle:Default:index }
   
Como el nombre de la ruta debe ser único en toda la aplicación, es una buena 
práctica nombrarlas anteponiendo un prefijo con el nombre del *bundle*, o con algo
que lo identifique. Como el nombre de nuestro *bundle* es muy largo, hemos optado
por usar como prefijo las siglas ``JAMAB``.

Una vez definida la ruta debemos implementar la acción del controlador especificada
en la misma, es decir ``JazzywebAulasMentorAlimentosBundle:Default:index``. 

.. note::

   Fíjate en el patrón que se utiliza para especificar la acción del controlador:
   ``JazzywebAulasMentorAlimentosBundle:Default:index``. A esto se le llama en
   *Symfony2* un nombre lógico. Está compuesto por el nombre del *bundle*, el
   nombre del controlador, y el nombre de la acción separados por el caracter
   ``:``. En este caso, el nombre lógico hace referencia a el método
   ``indexAction()`` de una clase *PHP* llamada
   ``Jazzyweb\AulasMentor\AlimentosBundle\Controller\DefaultController``.
   Es decir, hay que añadir el sufijo ``Controller`` al nombre del controlador, y
   el sufijo ``Action`` al nombre de la acción.
 
Creación de la acción en el controlador
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  
Editamos el fichero ``src/Jazzyweb/AulasMentor/AlimentosBundle/Controller/DefaultController.php``,
y reescribimos el método ``indexAction()``:

.. code-block:: php
   :linenos:
   
    <?php
    
    namespace Jazzyweb\AulasMentor\AlimentosBundle\Controller;
    
    use Symfony\Bundle\FrameworkBundle\Controller\Controller;
    
    class DefaultController extends Controller
    {
        
        public function indexAction()
        {
            $params = array(
                'mensaje' => 'Bienvenido al curso de Symfony2',
                'fecha' => date('d-m-yyy'),
            );
            
            return $this->render('JazzywebAulasMentorAlimentosBundle:Default:index.html.twig',
            $params);
        }
    }
   
Analicemos  el código  anterior.  La clase  ``DefaultController``  "vive" en  el
espacio de  nombres ``Jazzyweb\AulasMentor\AlimentosBundle\Controller``,  por lo
que               su               nombre              completo               es
``Jazzyweb\AulasMentor\AlimentosBundle\Controller\DefaultController``.  La clase
extiende  de  ``Symfony\Bundle\FrameworkBundle\Controller\Controller``, la  cual
forma parte de  *Symfony2* y, aunque no es necesario  que nuestros controladores
deriven de dicha clase, si lo hacemos nos facilitará mucho  la vida, ya que esta
clase base cuenta con potentes herramientas  para trabajar  con *Symfony2*.  
Posiblemente  la más  útil sea  el  *Contenedor de  Dependencias* tambien  
conocido como *Contenedor  de Servicios*, con el que podemos obtener  fácilmente 
instancias bien configuradas de cualquier servicio del framework, tanto de los
incluidos en la distribución estándard, como de  los que  nosotros creemos  o de 
los  que se  añadan en  las extensiones  de terceros (*vendors*)  que podamos 
instalar. Quédate tranquilo  con esto  de los servicios pues será un tema que
abordaremos más adelante. Por lo pronto es suficiente con que sepas que los servicios
son objetos  ofrecidos por el  framework para realizar determinadas  tareas (como 
por ejemplo enviar  emails o  manipular una base de datos).

.. note::
    
    **Sobre los espacios de nombre de PHP 5.3**
    Si en la línea 7 se utiliza únicamente el nombre ``Controller`` en lugar del
    nombre completo ``Symfony\Bundle\FrameworkBundle\Controller\Controller``,
    es por que previamente, en la línea 5, se ha indicado en el archivo que se  
    va a utilizar la clase ``Controller`` de dicho espacio de nombre.

El método ``indexAction()`` es una *acción* , es decir, un método que está mapeado
en una *URL* a través de una ruta. Dichas rutas se definen en un fichero, que 
utilizarás intensivamente cuando desarrollas aplicaciones con *Symfony2*, denominado
``routing.yml``. La  acción ``indexAction()`` define un  array asociativo con los
datos "crudos" (raw) ``mensaje`` y ``fecha``, y se los pasa a una plantilla para
que  los  pinte. Esto  último  se  hace en  la  línea  17 utilizando  el  método
``render``  de  la  clase padre
``Symfony\Bundle\FrameworkBundle\Controller\Controller``. Este método recibe dos
argumentos,  el  primero es  el  nombre  lógico de  la  plantilla  que se  desea
utilizar,   y   el   segundo   es   un   array   asociativo   con   los   datos.
Las acciones terminan con la devolución de un objeto ``Response``. Precisamente,
el método ``render`` convierte una plantilla en un objeto de este tipo.

El  método ``render``  es uno  de los  servicios disponibles  en el  framework y
accesible       desde       cualquier       clase      que       derive       de
``Symfony\Bundle\FrameworkBundle\Controller\Controller``.  Es  un  servicio  que
usaremos hasta  la saciedad. El  nombre lógico de  una plantilla, es  similar al
nombre lógico  de un controlador;  está compuesto por  el nombre del  bundle, el
nombre   del  directorio   que   aloja   a  la   plantilla   en  el   directorio
``Resources/view`` (que suele  coincidir con el nombre del  controlador, en este
caso ``Default``), y el nombre del  archivo que implementa la plantilla (en este
caso  ``index.html.twig``).    Es     decir    que     el    nombre     lógico:
``JazzywebAulasMentorAlimentosBundle:Default:index.html.twig``,  hace referencia
al                                                                       archivo
``src/Jazzyweb/AulasMentor/AlimentosBundle/Resources/view/Default/index.html.twig``.

Creación de la plantilla
^^^^^^^^^^^^^^^^^^^^^^^^

Siguiendo los pasos  para la creación de una página  en *Symfony2*, lo siguiente
que   tenemos   que   hacer   es   crear  la   plantilla.   Edita   el   fichero
``src/Jazzyweb/AulasMentor/AlimentosBundle/Resources/view/Default/index.html.twig`` con el siguiente contenido:

.. code-block:: html+jinja
   :linenos:

   <h1>Inicio</h1>
   <h3> Fecha: {{fecha}}  </h3>
   {{mensaje}}
   
Aunque *Symfony2*  permite el uso de  *PHP* como sistema de  plantillas, en este
tutorial  utilizaremos  *Twig*,  que  es lo  recomendado  oficialmente.  El  código
anterior es una plantilla  *twig*.

En  *twig*, el  contenido dinámico,  es  decir, los  datos "crudos"  que le  son
pasados desde el controlador (segundo argumento del método ``render`` en la 
acción ``indexAction()``), se referencian con dobles llaves (``{{ dato }}``). 
En el ejemplo anterior ``{{ fecha }}`` hace referencia al elemento ``fecha`` del
array construido en  el controlador, y ``{{ mensaje }}``,  como ya has deducido,
al elemento ``mensaje`` de dicho array.

Pues  con  esto hemos  terminado.  Vamos  a probar  lo  que  acabamos de  hacer.
Introduce en la barra de direcciones  de tu navegador la *URL* correspondiente a
la  ruta  que   acabamos  de  crear.  Utiliza  el   controlador  de  desarrollo:

.. code-block:: bash
   
   http://localhost/Symfony/web/app_dev.php/

¡Vaya! parece que nada de lo que hemos hecho ha funcionado. Vuelve a aparecer la
aplicación demo de *Symfony2*.

Ahora prueba con el controlador de producción:

.. code-block:: bash
   
   http://localhost/Symfony/web/app.php/

¡Ahora si! Vemos la pantalla de  inicio de nuestro *bundle*. Pero entonces, ¿qué
está pansando?  las rutas tienen  distinto sentido según el  controlador frontal
que usemos.  ¿Por qué?. La respuesta  a este comportamiento se  encuentra en las
distintas configuraciones  que se  cargan en función  del entorno  de ejecución.
Cuando utilizamos el controlador frontal de desarrollo ``app_dev.php``, se carga
el fichero de  routing ``app/config/routing_dev.php``. Si le echas  un vistazo al
fichero verás que comienza con la siguiente ruta:

.. code-block:: yaml

    _welcome:
	pattern:  /
	defaults: { _controller: AcmeDemoBundle:Welcome:index }

La cual colisiona con la que nosotros hemos  creado, ya que el patrón de la ruta
es el  mismo: ``/``. El sistema  de enrutamiento de *Symfony2*  va leyendo todas
las rutas  y cuando encuentra una  que coincide con  la *URL* que se  ha pedido,
ejecuta la acción asociada. No sigue leyendo  más rutas. Por eso, si en un mismo
proyecto hay dos rutas, o más precisamente, dos patrones de rutas que coincidan,
se ejecutará la primera que se encuentre. Atención por que no se producirá ningún
error. Esto hay que tenerlo muy en cuenta cuando se desarrolla  con *Symfony2* 
para evitarnos  algún que  otro dolor  de cabeza.

En el  caso del controlador frontal  de producción, el framework  lee el fichero
``routing.yml``, ya que no existe ``routing_prod.yml``. Mira el fichero y podrás
comprobar que no hay  ninguna ruta que colisione con la  que nosotros hemos 
definido. Por tanto todo está bien y se ejecuta la acción correcta.

Una vez que sabemos las causas del problema, si queremos que el controlador de
desarrollo cargue la ruta de nuestro *bundle*, cualquier solución que propongamos
pasa por evitar la colisión entre rutas. Y para ello podemos hacer varias cosas:

1. Deshabilitar el plugin *AcmeDemoBundle* y sus rutas.

2. Cambiar el patrón de las rutas del plugin *AcmeDemoBundle*, anteponiendole
   a todas ellas un prefijo (``acme``, por ejemplo)

3. Cambiar el patrón de las rutas del   *Jazzyweb/AulasMentorAlimentosBundle*,   anteponiendole a todas ellas un prefijo (``alimentos``, por ejemplo)

Con el fin  de ilustrar una carácteristica del sistema  de routing, hemos optado
por la 3ª solución. Podemos añadir un prefijo a todas las rutas del *bundle* sin
más  que cambiar  el parámetro  ``prefix`` en  la ruta  importada en  el archivo
``app/config/routing.yml``:

.. code-block:: yaml

   JazzywebAulasMentorAlimentosBundle:
     resource: "@JazzywebAulasMentorAlimentosBundle/Resources/config/routing.yml"
     prefix:   /alimentos
 
Ahora, para ver la página de inicio de nuestro *bundle*, apuntamos nuestro
navegador a:

.. code-block:: bash

   http:://localhost/app_dev.php/alimentos/

Y ya está! A partir de ahora todas las rutas de nuestro *bundle* llevarán el 
prefijo ``alimentos`` delante.

.. attention::
 
   Como hemos cambiado un fichero de configuración, para que el cambio se haga
   efectivo en el entorno de producción hay que borrar la caché con el siguiente
   comando:
   
   .. code-block:: bash
   
      # app/console  cache:clear --env=prod
  

Decoración de la plantilla con un layout
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Te habrás dado cuenta que hemos pintado un bloque *HTML* incompleto. Si no te 
has percatado de ello mira el código fuente *HTML* que llega al navegador.
Nos falta someter a la plantilla al proceso de decoración, mediante el cual se le
añade funcionalidad. En el caso de la aplicación de *gestión de alimentos* hay que
añadir la cabecera con el menú, el pie de página y los estilos.

.. note::
   
   Sobre el proceso de decoración:

   En una aplicación web,  muchas de las páginas tienen elementos comunes. Por
   ejemplo, un caso típico es la cabecera (donde se coloca el mensaje de bienvenida),
   el menú y el pie de página. Este hecho, y la aplicación del conocido principio de
   buenas prácticas de programación *DRY* (*Don't Repeat Yourself*, No Te Repitas),
   lleva a que cualquier sistema de plantillas que se utilice para implementar la
   vista utilice un conocido patrón de diseño: El *Decorator*, o Decorador.
   Aplicado a la generación de vistas la solución que ofrece dicho patrón es la de
   añadir funcionalidad adicional a las plantillas. Por ejemplo, añadir el menú y el
   pie de página a las plantillas que lo requieran, de manera que dichos elementos
   puedan reutilizarse en distintas plantillas. Literalmente se trata de *decorar*
   las plantillas con elementos adicionales reutilizables.

El sistema de plantillas *twig*, está provisto de un mecanismo de herencia gracias
al cual la decoración de plantillas resulta de una flexibilidad y versatilidad
total. Podemos hacer cualquier cosa que nos imaginemos, como por ejemplo 
fragmentar la vista en distintas plantillas organizadas por criterios funcionales,
y combinarlas para producir la vista completa. Podemos colocar en una un menú, en
otra un pie de página, en otra la estructura básica del documento *HTML*, otra 
puede pintar un listado de *twits*, etcétera.

La herencia es un mecanismo típico de la programación orientada a objetos mediante
el que un componente software hereda todas las funcionalidades de otro y puede 
extenderlas y/o cambiarlas. Es exactamente esto lo que ocurre cuando una plantilla
*twig* hereda de otra.

En *twig* la herencia se implementa mediante el concepto de bloque. En las plantillas
podemos delimitar *bloques* que comienzan con un ``{% block nombre_bloque %}`` y
finalizan con ``{% endblock %}``. Las plantillas heredan todas las funcionalidades
de las plantillas que extienden y pueden cambiar el código de los bloques 
heredadados. Como siempre un ejemplo vale más que mil palabras.

Fíjate en el fichero ``app/Resources/view/base.html.twig`` que viene de serie
en la distribución standard de *Symfony2*:

``app/Resources/view/base.html.twig``

.. code-block:: html+jinja
   :linenos:

   <!DOCTYPE html>
   <html>
	 <head>
	    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
	    <title>{% block title %}Welcome!{% endblock %}</title>
	        {% block stylesheets %}{% endblock %}
	    <link rel="shortcut icon" href="{{ asset('favicon.ico') }}" />
	 </head>
	 <body>
	    {% block body %}{% endblock %}
	    {% block javascripts %}{% endblock %}
	 </body>
   </html>

Representa la estructura básica de un documento *HTML*. Y presenta varios 
bloques: ``title``, ``stylesheets``, ``body`` y ``javascripts``. Esta plantilla
es ofrecida por *Symfony2* para que sirva de ejemplo. Pero puede utilizarse 
como plantilla básica de casi cualquier aplicación web. 

Vamos a modificar nuestra plantilla ``index.html.twig`` para que la herede (o para
que la extienda, son dos maneras de decir lo mismo):

``src/Jazzyweb/AulasMentor/Resources/view/Default/index.twig.html``

.. code-block:: html+jinja
   :linenos:

   {% extends '::base.html.twig' %}

   {% block body %}
	
	<h1>Inicio</h1>
	<h3> Fecha: {{fecha}}  </h3>
	{{mensaje}}
	
   {% endblock %}

En la línea 1 se indica la herencia de la plantilla base. Esto significa que la
plantilla ``JazzywebAulasMentorAlimentosBundle:Default:index.twig.html`` asume 
todo el contenido de la plantilla ``::base.html.twig``. Pero además se modifica el
contenido del bloque ``body`` con las líneas 5-7.

Si además queremos modificar el bloque ``title``, no tenemos más que añadirlo
en nuestra plantilla ``index.html.twig``: 

``src/Jazzyweb/AulasMentor/Resources/view/Default/index.twig.html``

.. code-block:: html+jinja
   :linenos:

   {% extends '::base.html.twig' %}

   {%  block title %}
     Bienvenido a la aplicación alimentos
   {% endblock %}

   {% block body %}
	
	<h1>Inicio</h1>
	<h3> Fecha: {{fecha}}  </h3>
	{{mensaje}}
	
   {% endblock %}

Ahora, en la sección ``<title>`` del documento se pintará: ``Bienvenido a la
aplicación alimentos`` en lugar de ``Welcome``.

Puedes probar a recargar la página a través de la *URL*:

.. code-block:: bash
  
   http://localhost/Symfony/web/app_dev.php/alimentos/

Aunque el aspecto de la página es el mismo que antes, si ves el código fuente
*HTML* en el navegador, comprobarás que el documento está completo, es decir,
con todas sus etiquetas *HTML*. También puedes comprobar que, al utilizar el
controlador frontal de desarrollo, aparece en la parte de abajo de la página la
barra de depuración de *Symfony2*. 

.. note::

   Recuerda el concepto de nombre lógico de una plantilla. Y fíjate en el nombre
   lógico de la plantilla ``::base.html.twig``. Como no pertenece a ningún
   *bundle* (es común a la aplicación), y está úbicada directamente en el 
   directorio ``view``, no lleva nada ni antes del primer ``:`` ni del segundo.

La herencia de plantillas puede llevarse a cabo a varios niveles, esto es, una
plantilla puede heredar de otra plantilla que a su vez hereda de otra plantilla,
etcétera. No obstante no se recomienda llevar a cabo muchos niveles de herencia,
ya que puede llegar a ser bastante confuso e incontrolable. La estrategia que 
recomiendan los creadores de *Symfony2* es usar tres niveles de herencia: 

* en el primer nivel se colocan la estructura básica del documento *HTML*, se
  corresponde con lo que hace la plantilla ``::base.html.twig``,

* en el segundo se colocan los elementos específicos de cada sección del sitio,
  por ejemplo el menú de la sección,

* y en el tercero se reserva para los elementos propios de la acción, se 
  corresponde con nuestra plantilla  
  ``JazzywebAulasMentorAlimentosBundle:Default:index.twig.html``

Tan sólo nos falta incluir los menús que serán comunes a todas las páginas de
la aplicación. Seguiremos la estrategia de tres niveles de herencia que acabamos
de exponer. Creamos la plantilla genéral
``JazzywebAulasMentorAlimentosBundle::layout.html.twig``. Según la lógica de los
nombres lógicos, esta se debe ubicar en:

``src/Jazzyweb/AulasMentor/Resources/view/layout.twig.html``

.. code-block:: html+jinja
   :linenos:

   {% extends '::base.html.twig' %}

   {% block body %}
   <div id="cabecera">
     <h1>Información de alimentos</h1>
   </div>

   <div id="menu">
   <hr/>
     <a href="{{ path('JAMAB_homepage')}}">inicio</a> |
     <a href="#">ver alimentos</a> |
     <a href="#">insertar alimento</a> |
     <a href="#">buscar por nombre</a> |
     <a href="">buscar por energia</a> |
     <a href="">búsqueda combinada</a>
   <hr/>
   </div>

   <div id="contenido">
   {% block contenido %}

   {% endblock %}
   </div>

   <div id="pie">
   <hr/>
   <div align="center">- pie de página -</div>
   </div>

   {% endblock %}

.. note::

   En la línea 10 hemos usado la función ``path`` de *twig* para construir
   la *URL's* del menú. Está función recibe como argumento el nombre de la ruta
   cuya *URL* se desea calcular. Únicamente la hemos usado en el primer enlace
   del menú, pués, por ahora, es la única ruta que hemos definido.
   
Ahora es esta plantilla la que extiende a la plantilla base, por tanto, habrá
que cambiar la plantilla
``JazzywebAulasMentorAlimentosBundle:Default:index.twig.html`` para que extienda
de ``JazzywebAulasMentorAlimentosBundle::layout.twig.html``, y para que redefina
el bloque ``contenido`` de esta última. Quedaría así:

.. code-block:: html+jinja
   :linenos:

   {% extends 'JazzywebAulasMentorAlimentosBundle::layout.html.twig' %}

   {% block contenido %}
   
   <h1>Inicio</h1>
   <h3> Fecha: {{fecha}}  </h3>
   {{mensaje}}

   {% endblock %}

Vuelve a probar la página. Ya sólo nos falta incorporarle estilos *CSS's*.

Instalación de los *assets* de un *bundle*
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Ya hemos dicho que un *bundle* es un directorio que aloja todo aquello relativo
a una funcionalidad determinada. Puede incluir clases *PHP*, plantillas,
configuraciones, *CSS’s* y *javascripts*.

Cuando los *bundles* incluyen *assets*, es decir archivos que no son procesados
por *PHP* y son servidos directamente por el servidor web (*CSS's*,
*javascripts* e imágenes son los *assets* más habituales), estos deben ser copiados dentro del directorio ``web`` del proyecto o enlazados desde dicho directorio, ya que es ahí únicamente donde el servidor web puede acceder en busca de archivos (suponiendo que lo hemos configurado correctamente para un entorno de producción).

Por otro lado en un *bundle* los *assets* deben ser ubicados en el directorio
``Resources/public``. Si lo examinas verás que tiene la siguiente estructura:

.. code-block:: bash

    Resources
    └─ public
       ├── css
       ├── images
       └── js

Se ha reservado un directorio para cada tipo de *asset*. Copia el siguiente código
*CSS's* en el archivo ``Resources/public/css`` del *bundle*.

.. code-block:: css
   
    body {
      padding-left: 11em;
      font-family: Georgia, "Times New Roman",
            Times, serif;
      color: purple;
      background-color: #d8da3d }
    ul.navbar {
      list-style-type: none;
      padding: 0;
      margin: 0;
      position: absolute;
      top: 2em;
      left: 1em;
      width: 9em }
    h1 {
      font-family: Helvetica, Geneva, Arial,
            SunSans-Regular, sans-serif }
    ul.navbar li {
      background: white;
      margin: 0.5em 0;
      padding: 0.3em;
      border-right: 1em solid black }
    ul.navbar a {
      text-decoration: none }
    a:link {
      color: blue }
    a:visited {
      color: purple }
    address {
      margin-top: 1em;
      padding-top: 1em;
      border-top: thin dotted }
    #contenido {
      display: block;
      margin: auto;
      width: auto;
      min-height:400px;
    }


Para que el servidor web la pueda cargar, se utiliza el siguiente comando de consola:

.. code-block:: bash
  
  php app/console assets:install web --symlink

La función de este comando es realizar una copia o un enlace simbólico (si se 
especifica la opión ``--symlink``, aunque en la plataforma *Windows* esto 
último no es posible) del contenido de los directorios ``Resouces/public`` de
todos los *bundles* que se encuentren registrados en el framework. El comando 
requiere un argumento (``web`` en nuestro caso), que especifica el directorio
donde se realizará la copia o el enlace simbólico. 

Dicha copia o enlazado se organiza de la siguiente manera:

.. code-block:: bash
   
    web
    ├─ nombre_bundle_1
    |  ├── css
    |  ├── images
    |  └── js
    ├─ nombre_bundle_2
    |  ├── css
    |  ├── images
    |  └── js
    ...
    └─ nombre_bundle_N
       ├── css
       ├── images
       └── js

Ya sólo falta incluir una referencia en el código *HTML* a la *CSS* que acabamos
de incorporar. Aunque es posible incluir el enlace a la *CSS* directamente en 
la plantilla ``::base.html.twig``, el lugar correcto es en la plantilla
``JazzywebAulasMentosAlimentosBundle::layout.html.twig``. Teniendo en cuenta
lo que hemos explicado acerca del mecanismo de herencia, habría que añadir
un bloque ``stylesheets`` (heredado de la plantilla padre ``::base.html.twig``),
en el que se haga referencia al archivo *CSS*.

``src/Jazzyweb/AulasMentor/AlimentosBundle/Resources/view/layout.html.twig``

.. code-block:: html+jinja

   ...
   {% block stylesheets %}
    <link href="{{ asset('bundles/jazzywebaulasmentoralimentos/css/estilo.css') }}" type="text/css" rel="stylesheet" />
   {% endblock %} 
   ...
 
En este código hemos utilizado la función de *twig* ``asset``, la cual crea la
*URL* correcta que apunta al *asset* en cuestion. La ruta que toma como argumento
la función *asset* se especifica tomando como raíz el directorio ``web``.

.. note::

   Puedes colocar el bloque ``stylesheets`` delante o detrás del bloque ``body``.

Recarga la página y la verás con los estilos aplicados.

Implementamos el resto de la aplicación
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Siguiendo estos tres pasos: enrutar, crear código de la acción (controlador) y
crear la plantilla, podemos completar lo que nos falta de la aplicación. No 
obstante, en las acciones que faltan, se necesita acceder a la base de datos
para recuperar, modificar y crear alimentos. La distribución standard de
*Symfony2*  proporciona un potente servicio para el acceso a los datos
persistentes, es decir, los que se almacenan en algún tipo de base de datos. Pero
no obliga a utilizarlo. No solo eso, tampoco forma parte del núcleo de
*Symfony2*, es decir, no es un componente. Por ello es una decisión del 
desarrollador utilizarlo o no. Es en ese sentido que *Fabien Potencier*, lider
del proyecto *Symfony2*, proclama que este último no es un framework *MVC*,
ya que no dice nada sobre como debes construir tu modelo.

Aunque lo recomendable es utilizar *Doctrine2* (que es el servicio de 
persistencia que viene incorporado en la distribución standard), o *Propel*, 
en este tutorial no los vamos a utilizar por que ya llevamos muchos conceptos
introducidos y no queremos sobrecargarlo. Además queremos ilustrar como podemos 
construir el modelo a nuestro antojo.

Así pues, antes de implentar el resto de las acciones que componen la
aplicación, vamos a elaborar el modelo.

Crea un directorio denominado ``Model`` (el nombre puede ser cualquiera), y 
crea ahí un fichero ``Model.php`` con el siguiente código: 

``src/Jazzyweb/AulasMentor/AlimentosBundle/Model/Model.php``

.. code-block:: php
   :linenos:

    <?php

    namespace Jazzyweb\AulasMentor\AlimentosBundle\Model;

    class Model
    {
	protected $conexion;

	 public function __construct($dbname,$dbuser,$dbpass,$dbhost)
     {   
         $mvc_bd_conexion = mysql_connect($dbhost, $dbuser, $dbpass);

         if (!$mvc_bd_conexion) {
             die('No ha sido posible realizar la conexión con la base de datos: '
             . mysql_error());
         }
         mysql_select_db($dbname, $mvc_bd_conexion);

         mysql_set_charset('utf8');

         $this->conexion = $mvc_bd_conexion;
     }


	public function bd_conexion()
	{
	    
	}

	public function dameAlimentos()
	{
	    $sql = "select * from alimentos order by energia desc";

	    $result = mysql_query($sql, $this->conexion);

	    $alimentos = array();
	    while ($row = mysql_fetch_assoc($result))
	    {
		$alimentos[] = $row;
	    }

	    return $alimentos;
	}

	public function buscarAlimentosPorNombre($nombre)
	{
	    $nombre = htmlspecialchars($nombre);

	    $sql = "select * from alimentos where nombre like '" . $nombre . "' order
	     by energia desc";

	    $result = mysql_query($sql, $this->conexion);

	    $alimentos = array();
	    while ($row = mysql_fetch_assoc($result))
	    {
		$alimentos[] = $row;
	    }

	    return $alimentos;
	}
	
	public function dameAlimento($id)
	{
	    $id = htmlspecialchars($id);
	    
	    $sql = "select * from alimentos where id=".$id;
	    
	    $result = mysql_query($sql, $this->conexion);

	    $alimentos = array();
	    $row = mysql_fetch_assoc($result);
	    
	    return $row;
	    
	}

        public function insertarAlimento($n, $e, $p, $hc, $f, $g)
	{
	    $n = htmlspecialchars($n);
	    $e = htmlspecialchars($e);
	    $p = htmlspecialchars($p);
	    $hc = htmlspecialchars($hc);
	    $f = htmlspecialchars($f);
	    $g = htmlspecialchars($g);

	    $sql = "insert into alimentos (nombre, energia, proteina, hidratocarbono, 
	    fibra, grasatotal) values ('" .
		    $n . "'," . $e . "," . $p . "," . $hc . "," . $f . "," . $g . ")";

	    $result = mysql_query($sql, $this->conexion);

	    return $result;
	}

    }

El próximo paso es completar el código del controlador con el resto de las acciones que
se han mapeado en las rutas definidas anteriormente. El código del controlador
``DefaultController`` quedaría así:

``src/Jazzyweb/AulasMentor/AlimentosBundle/Controller/DefaultController.php``

.. code-block:: php
   :linenos:

    <?php

    namespace Jazzyweb\AulasMentor\AlimentosBundle\Controller;

    use Symfony\Bundle\FrameworkBundle\Controller\Controller;
    use Jazzyweb\AulasMentor\AlimentosBundle\Model\Model;
    use Jazzyweb\AulasMentor\AlimentosBundle\Config\Config;

    class DefaultController extends Controller
    {

        public function indexAction()
        {
            $params = array(
            'mensaje' => 'Bienvenido al curso de Symfony2',
            'fecha' => date('d-m-yyy'),
            );
    
            return
             $this->render('JazzywebAulasMentorAlimentosBundle:Default:index.html.twig',
             $params);
        }
    
        public function listarAction()
        {
            $m = new Model(Config::$mvc_bd_nombre, Config::$mvc_bd_usuario,
                            Config::$mvc_bd_clave, Config::$mvc_bd_hostname);
    
            $params = array(
            'alimentos' => $m->dameAlimentos(),
            );
    
            return
             $this->render('JazzywebAulasMentorAlimentosBundle:Default:mostrarAlimentos.html.twig', 
             $params);
        
        }
    
        public function insertarAction()
        {
            $params = array(
            'nombre' => '',
            'energia' => '',
            'proteina' => '',
            'hc' => '',
            'fibra' => '',
            'grasa' => '',
            );
    
            $m = new Model(Config::$mvc_bd_nombre, Config::$mvc_bd_usuario,
             Config::$mvc_bd_clave, Config::$mvc_bd_hostname);
    
            if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    
            // comprobar campos formulario
            if ($m->insertarAlimento($_POST['nombre'], $_POST['energia'], 
             $_POST['proteina'], $_POST['hc'], $_POST['fibra'], $_POST['grasa'])) {
                $params['mensaje'] = 'Alimento insertado correctamente';
            } else {
                $params = array(
                'nombre' => $_POST['nombre'],
                'energia' => $_POST['energia'],
                'proteina' => $_POST['proteina'],
                'hc' => $_POST['hc'],
                'fibra' => $_POST['fibra'],
                'grasa' => $_POST['grasa'],
                );
                $params['mensaje'] = 'No se ha podido insertar el alimento. Revisa el formulario';
            }
            }
    
            return
             $this->render('JazzywebAulasMentorAlimentosBundle:Default:formInsertar.html.twig', 
             $params);
        
        }
    
        public function buscarPorNombreAction()
        {
            $params = array(
            'nombre' => '',
            'resultado' => array(),
            );
    
            $m = new Model(Config::$mvc_bd_nombre, Config::$mvc_bd_usuario,
                            Config::$mvc_bd_clave, Config::$mvc_bd_hostname);
    
            if ($_SERVER['REQUEST_METHOD'] == 'POST') {
            $params['nombre'] = $_POST['nombre'];
            $params['resultado'] = $m->buscarAlimentosPorNombre($_POST['nombre']);
            }
    
            return 
             $this->render('JazzywebAulasMentorAlimentosBundle:Default:buscarPorNombre.html.twig',
             $params);
        
        }
    
        public function verAction()
        {
            if (!isset($_GET['id'])) {
            throw new Exception('Página no encontrada');
            }
    
            $id = $_GET['id'];
    
            $m = new Model(Config::$mvc_bd_nombre, Config::$mvc_bd_usuario,
                            Config::$mvc_bd_clave, Config::$mvc_bd_hostname);
    
            $alimento = $m->dameAlimento($id);
    
            $params = $alimento;
    
            return 
             $this->render('JazzywebAulasMentorAlimentosBundle:Default:verAlimento.html.twig',
            $params);
        
        }

    }

Para que podamos utilizar la clase ``Model`` en el controlador sin necesidad de 
referirnos a ella por su nombre completo,
``Jazzyweb\AulasMentor\AlimentosBundle\Model\Model``, hemos utilizado (línea 6) la
directiva ``use`` de *PHP 5.3* en dicho fichero. Así podemos utilizar la clase
``Model`` directamente en el controlador.

En la clase ``Model`` puedes observar que,  para el acceso a la base de datos, se hace
referencia a unos parámetros de configuración a través de la clase estática ``Config``.
Crea dicha clase en el fichero ``src/Jazzyweb/AulasMentor/AlimentosBundle/Config/Config.php``, con el siguiente código:

``src/Jazzyweb/AulasMentor/AlimentosBundle/Config/Config.php``

.. code-block:: php

    <?php

    namespace Jazzyweb\AulasMentor\AlimentosBundle\Config;
    
    class Config
    {
        static public $mvc_bd_hostname = "localhost";
        static public $mvc_bd_nombre   = "alimentos";
        static public $mvc_bd_usuario  = "root";
        static public $mvc_bd_clave    = "root";
        static public $mvc_vis_css     = "estilo.css";
    }
    
Esta forma de especificar los parámetros de configuración no es la más "symfónica", 
pero es suficiente para los propósitos de este tutorial. En otro tutorial explicaremos
como usar la inyección de dependencias para declarar los parámetros en el *Symfony2
way*.

Como puedes ver hemos comenzado por el 2º paso del flujo básico de desarrollo
de páginas con *Symfony2* es decir, escribir el controlador. En realidad el 
orden no importa mucho; al final hay que tener los tres pasos resueltos antes
de que funcione. Así que vamos a por el primer paso: definir las rutas. Esto
lo hacemos editando el fichero 
``src/Jazzyweb/AulasMentor/AlimentosBundle/Resources/config/routing.yml`` y
plasmando ahí la tabla de rutas. Recuerda:

========== =======================
URL        Acción
========== =======================
/          mostrar pantalla inicio
/listar    listar alimentos
/insertar  insertar un alimento
/buscar    buscar alimentos
/ver/x     ver el alimento *x*
========== =======================

El archivo ``src/Jazzyweb/AulasMentor/Resources/config/routing.yml`` queda así:

.. code-block:: yaml
   
    JAMAB_homepage:
      pattern:  /
      defaults: { _controller: JazzywebAulasMentorAlimentosBundle:Default:index }

    JAMAB_listar:
      pattern:  /listar
      defaults: { _controller: JazzywebAulasMentorAlimentosBundle:Default:listar }

    JAMAB_insertar:
      pattern:  /insertar
      defaults: { _controller: JazzywebAulasMentorAlimentosBundle:Default:insertar }
      
    JAMAB_buscar:
      pattern:  /buscar
      defaults: { _controller: JazzywebAulasMentorAlimentosBundle:Default:buscarPorNombre }

    JAMAB_ver:
      pattern:  /ver/{id}
      defaults: { _controller: JazzywebAulasMentorAlimentosBundle:Default:ver }

La última ruta (``JAMAB_ver``) utiliza una funcionalidad muy interesante del
sistema de *Routing* de *Symfony2* que se utiliza continuamente. Se trata de
introducir en la propia ruta los parámetros que se pasarán por *GET* al servidor
web. Los valores encerrados entre llaves, en nuestro caso ``{id}``, se 
denominan *placeholders*. El sistema de *Routing* parsea las *URL's* 
que coincidan con la ruta y asigna el valor que venga en la posición de cada 
*placeholder* a una variable denominada con el nombre especificado entre las
llaves. Veámoslo con un ejemplo. La siguiente ruta:

.. code-block:: bash
 
   http://localhost/Symfony/web/app_dev.php/alimentos/ver/5

Coincide con la ruta ``JAMAB_ver`` (recuerda que a todas las rutas del *bundle*
les hemos colocado el prefijo ``alimentos``). El sistema de *Routing*, al 
parsearla, asignará al objeto *Request* de *Symfony2* una variable denominada
``id``, con un valor ``5``. Además, esta variable se pasará como argumento
al controlador especificado en la ruta, en nuestro caso a 
``JazzywebAulasMentorAlimentosBundle:Default:ver``. Se consigue, además de 
usar *URL's* elegantes en la que sólo se utiliza el caracter ``/``, eliminar
el nombre de las variables de la *query string*, ocultando información que no
es necesaria para el cliente.

*Symfony2* mapea esta ruta en una acción llamada ``verAction($id)`` a la que se
le pasa el argumento ``id``. Vamos a cambiar la acción ``verAction()`` para que
su código sea más correcto y *symfónico*:

``src/Jazzyweb/AulasMentor/AlimentosBundle/Controller/DefaultController.php``

.. code-block:: php
   :linenos:
 
   <?php
   ...
   public function verAction($id)
   {                
	   $m = new Model(Config::$mvc_bd_nombre, Config::$mvc_bd_usuario,
                        Config::$mvc_bd_clave, Config::$mvc_bd_hostname);

	   $alimento = $m->dameAlimento($id);
	    
	   if(!$alimento)
	   {
	     throw new \Symfony\Component\HttpKernel\Exception\AccessDeniedHttpException();
	   }

	   $params = $alimento;

	   return $this->render('JazzywebAulasMentorAlimentosBundle:Default:verAlimento.html.twig', $params);
	
    }   
    ...

En la línea 3 hemos introducido un argumento para recoger la variable creada 
por el sistema de *Routing*, y en las líneas 9-12 hemos utilizado las 
excepciones de *Symfony2* para tratar el caso de que el registro no exista.
Fíjate que de esta manera no necesitamos utilizar la variable superglobal ``$_GET``
de *PHP*. 

.. note::
  
   En lugar del nombre completo ``\Symfony\Component\HttpKernel\Exception\AccessDeniedHttpException()``,
   puedes utilizar ``AccessDeniedHttpException()``, si referencias el espacio
   de nombre al principio del fichero mediante la directiva ``use``.

.. note:: 
   
   Las acciones ``buscarPorNombreAction`` y ``insertarAction``, hacen uso de
   la variable global de *PHP* ``$_POST``. Esto es una mala práctica en 
   *Symfony2*, ya que en su lugar se debe utilizar el objeto ``Request`` del
   framework, que es una abstracción de la petición (*request*) *HTTP* en la que
   se han "limpiado" los valores de sus atributos de posibles cadenas potencialmente
   peligrosas (código malicioso). Será la primera y ultima vez que haremos esto. 
   Sirva como ejemplo de que  el hecho de utilizar un framework ayuda pero no es 
   suficiente para generar un código de calidad. Es el programador quien, conociendo
   y aplicando las buenas prácticas de programación, produce un buen código.

Y ahora a por las plantillas. 

``src/Jazzyweb/AulasMentor/AlimentosBundle/Resources/view/Default/verAlimento.html.twig``

.. code-block:: html+jinja
   :linenos:
 
     {% extends 'JazzywebAulasMentorAlimentosBundle::layout.html.twig' %}

     {% block contenido %}
    
     <h1>{{ nombre }}</h1>
     <table border="1">
    
         <tr>
             <td>Energía</td>
             <td>{{ energia }} </td>
         </tr>
         <tr>
             <td>Proteina</td>
             <td>{{ proteina }}</td>
         </tr>
         <tr>
             <td>Hidratos de Carbono</td>
             <td>{{ hidratocarbono }}</td>
         </tr>
         <tr>
             <td>Fibra</td>
             <td>{{ fibra }}</td>
         </tr>
         <tr>
             <td>Grasa total</td>
             <td> {{grasatotal}} </td>
         </tr>
     </table>
    
    {% endblock %}


``src/Jazzyweb/AulasMentor/AlimentosBundle/Resources/view/Default/mostrarAlimentos.html.twig``

.. code-block:: html+jinja
   :linenos:

    {% extends 'JazzywebAulasMentorAlimentosBundle::layout.html.twig' %}
    
     {% block contenido %}
    
     <table>
         <tr>
             <th>alimento (por 100g)</th>
             <th>energía (Kcal)</th>
             <th>grasa (g)</th>
         </tr>
         {% for alimento in alimentos %}
         <tr>
             <td><a href="{{ path('JAMAB_ver', {'id': alimento.id}) }}">{{alimento.nombre}}</a></td>
             <td>{{ alimento.energia }}</td>
             <td>{{ alimento.grasatotal }}</td>
         </tr>
         {% endfor %}
    
     </table>
    
     {% endblock %}    


En esta última plantilla hemos introducido tres elementos nuevos del sistema 
*twig*:

* La navegación por un array. Fíjate que la acción  que utiliza esta plantilla,
  ``listarAction()``, le pasa como parámetros una colección (array) de alimentos
  devueltos por el método ``dameAlimentos`` del modelo. Las colecciones, es 
  decir los arrays indexados (no asociativos), pueden ser iterados en una 
  plantilla *twig* mediante la construcción ``{% for dato in datos %}`` -
  ``{% endfor %}``,  donde ``datos`` es el array que llega a la plantilla.

* Por otro lado, cada elemento del array ``alimentos`` es un array asociativo.
  Sus elementos pueden ser accedido mediante la notación ``dato.propiedad``. 
  Una característica interesante de esta notación es que se puede utilizar no
  solo con arrays asociativos, sino con objetos provistos de *getters* sobre sus
  propiedades. Este hecho se utiliza intensa y extensamente en *Symfony2*.
  
* Por último se utiliza la función ``path()`` de *twig*, que sirve para calcular
  la *URL* correcta a partir del nombre de la ruta. Así cuando cambiemos la 
  aplicación de servidor o de ubicación, la ruta será calculada correctamente.
  Los argumentos de la ruta se pasan a la función ``path`` usando la sintaxis
  de un objeto *JSON*, es decir: ``{ 'param1': val1, ..., `paramN': valN }``.
  Esta función será otro de los elementos omnipresentes en cualquier aplicación
  web construida con *Symfony2* y *twig*.
  
``src/Jazzyweb/AulasMentor/AlimentosBundle/Resources/Default/formInsertar.html.twig``
  
.. code-block:: html+jinja

    {% extends 'JazzywebAulasMentorAlimentosBundle::layout.html.twig' %}
    
     {% block contenido %}
    
    {% if mensaje is defined %}
    <b><span style="color: red;">{{ mensaje }}</span></b>
    {% endif %}
    <br/>
    <form name="formInsertar" action="{{ path('JAMAB_insertar') }}" method="POST">
        <table>
            <tr>
                <th>Nombre</th>
                <th>Energía (Kcal)</th>
                <th>Proteina (g)</th>
                <th>H. de carbono (g)</th>
                <th>Fibra (g)</th>
                <th>Grasa total (g)</th>
            </tr>
            <tr>
                <td><input type="text" name="nombre" value="{{ nombre }}" /></td>
                <td><input type="text" name="energia" value="{{ energia }}" /></td>
                <td><input type="text" name="proteina" value="{{ proteina }}" /></td>
                <td><input type="text" name="hc" value="{{ hc }}" /></td>
                <td><input type="text" name="fibra" value="{{ fibra }}" /></td>
                <td><input type="text" name="grasa" value="{{ grasa }}" /></td>
            </tr>
    
        </table>
        <input type="submit" value="insertar" name="insertar" />
    </form>
    * Los valores deben referirse a 100 g del alimento
    
    {% endblock %}
  
En esta plantilla hemos introducido otro elemento nuevo; la construcción
``{% if data is defined %}`` - ``{% endif %}``, que como puedes deducir,
comprueba si la variable ``data`` ha sido definida. A lo largo del curso
veremos más expresiones lógicas utilizadas en los bloques ``if - endif``.

También hemos vuelto a utilizar la función ``path``  para escribir el parámetro
``action`` del formulario *HTML*.

Llegados a este punto hemos de aclarar que *Symfony2* proporciona un potente
servicio para la construcción de formularios que estudiaremos en su momento.
Por lo pronto nos quedamos con esta manera sencilla y directa de crear 
formularios.
  
Vamos a por la siguiente plantilla:

``src/Jazzyweb/AulasMentor/AlimentosBundle/Resources/Default/buscarPorNombre.html.twig``

.. code-block:: html+jinja
   :linenos:
   
    {% extends 'JazzywebAulasMentorAlimentosBundle::layout.html.twig' %}
    
     {% block contenido %}
    
    <form name="formBusqueda" action="{{ path('JAMAB_buscar') }}" method="POST">
    
         <table>
             <tr>
                 <td>nombre alimento:</td>
                 <td><input type="text" name="nombre" value="{{ nombre }}">(puedes utilizar '%' como comodín)</td>
    
                 <td><input type="submit" value="buscar"></td>
             </tr>
         </table>
    
         </table>
    
     </form>
    
     {% if resultado %}
     {% include 'JazzywebAulasMentorAlimentosBundle:Default:_tablaAlimentos.html.twig' with {'alimentos': resultado} %}
     {% endif %}
    
    {% endblock %}

Otro elemento nuevo; la inclusión de plantillas en otras plantillas. Esto lo 
hacemos en la línea 21 mediante la función ``include`` de *twig*, la cual 
requiere como argumento el nombre lógico de la plantilla que se desea incluir.
Los parámetros que necesita la plantilla incluida se pasan en un array con
sintaxis *JSON* despues del token ``with``.

Este mecanismo de inclusión combinado con la herencia proporciona una gran
flexibilidad al programador, otorgándole las herramientas necesarias para elaborar
un código bien organizado y reusable.

La plantilla incluida es la siguiente:

 ``src/Jazzyweb/AulasMentor/AlimentosBundle/Resources/Default/_tablaAlimentos.html.twig``
  
.. code-block:: html+jinja
   :linenos:

    <table>
         <tr>
             <th>alimento (por 100g)</th>
             <th>energía (Kcal)</th>
             <th>grasa (g)</th>
         </tr>
         {% for alimento in alimentos %}
         <tr>
             <td>{{ alimento.nombre }}</td>
             <td>{{ alimento.energia }}</td>
             <td>{{ alimento.grasatotal }}</td>
         </tr>
         {% endfor %}
    
     </table>

Y, por último, utilizando esta última plantilla que pinta un listado de alimentos,
podemos simplificar la plantilla ``mostrarAlimentos.html.twig`` evitando la
repetición de código innecesariamente.

``src/Jazzyweb/AulasMentor/AlimentosBundle/Resources/view/Default/mostrarAlimentos.html.twig``

.. code-block:: html+jinja
   :linenos:

    {% extends 'JazzywebAulasMentorAlimentosBundle::layout.html.twig' %}
    
    {% block contenido %}
    
    {% include 'JazzywebAulasMentorAlimentosBundle:Default:_tablaAlimentos.html.twig' with {'alimentos': alimentos} %}
    
    {% endblock %}   
    
Y con esto ya tenemos la aplicación de gestión de alimentos terminada y 
construida en *Symfony2*. Aún puede hacerse de un modo más symfónico, 
utilizando los servicios de persistencia de datos (*Doctrine*), de creación
de formularios y de validación de datos. Pero la intención de este tutorial es
mostrar los elementos básicos para la creación de páginas en *Symfony2*, y por
tanto vamos a dar por buena la aplicación tal y como está.

Únicamente faltaría usar la función ``path()`` de *twig* para completar los
enlaces de los menús. Pero eso vamos a dejar que lo hagas tú.


El tutorial en chuletas
-----------------------

Generar un *bundle*
^^^^^^^^^^^^^^^^^^^

.. code-block:: bash
  
   php app/console generate:bundle

Registrar un *bundle*
^^^^^^^^^^^^^^^^^^^^^

Se hace en el archivo ``app/KernelApp.php``, de la siguiente manera:

.. code-block:: php

   ...
   new Jazzyweb\AulasMentor\AlimentosBundle\JazzywebAulasMentorAlimentosBundle(),
   ...
   
Enlazar el *routing* de un *bundle* con el *routing* general de la aplicación
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Se hace añadiendo al archivo ``app/config/routing.yml`` (o ``routing_{env}.yml``):

.. code-block:: yaml

   JazzywebAulasMentorAlimentosBundle:
   resource: "@JazzywebAulasMentorAlimentosBundle/Resources/config/routing.yml"
   prefix:   /

Pasos para acoplar un *bundle* al framework
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. Registrar el espacio de nombre en el sistema de autocarga. Este paso no es 
   necesario si ubicamos al *bundle* en el directorio ``src``.

2. Registrar al bundle en el fichero ``app/AppKernel.php``. Esta operación se
   puede hacer automáticamente a través del generador interactivo de *bundles*,
   pero si fallase por alguna razón (por ejemplo que los permisos de dicho archivo
   no estén bien definidos). Habría que hacerlo a mano.

3. Importar las tablas de enrutamiento del *bundle* en la tabla de enrutamiento
   de la aplicación.
   
Flujo para la creación de páginas en *Symfony2*
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. Creación de la ruta en ``config/Resources/routing.yml`` del *bundle*, o
   directamente en ``app/config/routing.yml``.

2. Creación de la acción en el controlador correspondiente en una clase que debe
   ubicarse en un fichero del directorio ``Controllers`` del *bundle*.
   
3. Creación de una plantilla en el directorio ``Resources/view``.

Nombres lógicos de acciones
^^^^^^^^^^^^^^^^^^^^^^^^^^^

``NombreBundle:NombreControlador:NombreAcción``.

Ejemplo:

``AcmeDemoBundle:Secured:login`` se mapea en la acción ``loginAction()`` de la
clase ``Acme\DemoBundle\Controller\SecuredController`` definida (normalmente) en
``src/Acme/DemoBundle/Controller/SecuredController.php``.

Sintaxis básica de *twig*
^^^^^^^^^^^^^^^^^^^^^^^^^

``{{ parametro }}`` -> pinta el valor de la variable ``parametro``.

``{% comando %} ... {% endcomando %}`` -> ejecuta la acción expresada por 
``comando`` en el bloque definido desde su declaración hasta ``{% endcomando%}``.

Herencia en plantilla *twig*
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Esta plantilla hereda de ``JazzywebAulasMentorAlimentosBundle::layout.html.twig``, y
modifica el bloque ``contenido`` que allí se declara.

.. code-block:: html+jinja
   :linenos:

   {% extends 'JazzywebAulasMentorAlimentosBundle::layout.html.twig' %}

   {% block contenido %}
   
   <h1>Inicio</h1>
   <h3> Fecha: {{fecha}}  </h3>
   {{mensaje}}

   {% endblock %}
   
Función ``path`` de *twig*
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: html+jinja

   {{ path('JAMAB_listar', {'id': alimento.id}) }}
   
Iterar una colección (array) de datoso en *twig*
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: html+jinja
   
   {% for alimento in alimentos %}
     <tr>
         <td><a href="{{ path('JAMAB_listar', {'id': alimento.id}) }}">{{alimento.nombre}}</a></td>
         <td>{{ alimento.energia }}</td>
         <td>{{ alimento.grasatotal }}</td>
     </tr>
   {% endfor %}
   
Código condicional en *twig*
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: html+jinja
    
   {% if data is defined %} 
    ...
   {% endif %}
   
Inclusión de plantillas en *twig*
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: html+jinja

   {% include 'JazzywebAulasMentorAlimentosBundle:Default:_tablaAlimentos.html.twig' with {'resultado': resultado} %}
 

Estructura básica de una ruta
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: yaml

    nombre_unico_de_la_ruta:
      pattern:  /el/patron/de/la/ruta/{param1}/{param2}/.../{paramN}
      defaults: { _controller: NombreLogico:del:Controlador }
      
    Ejemplo:
    
    JAMAB_ver:
      pattern:  /ver/{id}
      defaults: { _controller: JazzywebAulasMentorAlimentosBundle:Default:ver }
  
-----------
 
.. [1] Drupal 8, phpBB4, Silex, son el nombre de algunos de los proyectos que
        han optado por utilizar los componentes de *Symfony2*.

.. [2] Algunos de los componentes ya cuentan con documentación: http://symfony.com/doc/current/components

.. [3] https://github.com/symfony

.. [4] Los especios de nombres son una nueva característica de PHP 5.3. Gracias a
       ellos se evitan problemas comunes en aplicaciones grandes como la colisión
       de nombres de clases. Si no los conoces deberías echarle un vistazo a 
       http://php.net/manual/en/language.namespaces.php, ya que *Symfony2* hace
       un uso intensivo de los ellos.

.. _ClassLoader: http://symfony.com/doc/current/components/class_loader.html
.. _Console: http://symfony.com/doc/current/components/console.html
.. _CssSelector: http://symfony.com/doc/current/components/css_selector.html
.. _DependencyInjection: http://symfony.com/doc/current/components/dependency_injection.html
.. _EventDispatcher: http://symfony.com/doc/current/components/event_dispatcher.html
.. _DomCrawler: http://symfony.com/doc/current/components/dom_crawler.html
.. _Finder: http://symfony.com/doc/current/components/finder.html
.. _HttpFoundation: http://symfony.com/doc/current/components/http_foundation.html
.. _Locale: http://symfony.com/doc/current/components/locale.html
.. _Process: http://symfony.com/doc/current/components/process.html
.. _Routing: http://symfony.com/doc/current/components/routing.html
.. _Templating: http://symfony.com/doc/current/components/templating.html
.. _Yaml: http://symfony.com/doc/current/components/yaml.html
.. _`Desarrollo de aplicaciones web con Symfony2`: https://centrovirtual.educacion.es/Symfony14/mentor.php/areasCursosWeb/mostrarInfoCurso/idCurso/958
.. _`Aula Mentor`: http://www.aulamentor.es
------------

.. raw:: html

   <div style="background-color: rgb(242, 242, 242); text-align: center; margin: 20px; padding: 10px;">
   <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/"><img alt="Licencia Creative Commons" style="border-width:0" src="http://i.creativecommons.org/l/by-nc-sa/3.0/88x31.png" /></a>
   <br />
   <span xmlns:dct="http://purl.org/dc/terms/" href="http://purl.org/dc/dcmitype/Text" property="dct:title" rel="dct:type">Desarrollo de Aplicaciones web con Symfony2</span> por <span xmlns:cc="http://creativecommons.org/ns#" property="cc:attributionName">Juan David Rodríguez García (juandavid.rodriguez@ite.educacion.es)</span>
   <br/>
   se encuentra bajo una Licencia <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Reconocimiento-NoComercial-CompartirIgual 3.0 Unported</a>.
   </style>
   </div>
