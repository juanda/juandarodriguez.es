.. codeauthor:: Juan David Rodríguez García <juandavid.rodriguez@ite.educacion.es>

Tutorial: Injección de Dependencias con Symfony2
================================================

Injección de Dependencia (*Dependency Injection*), Inversión de Control (*Control
Inversion*), Contenedor de Servicios (*Service Container*) y Contenedor
de Dependencias (*Dependency Container*) son palabros  que te vas a encontrar
continuamente cuando te sumerges en el mundo de *Symfony2*. Todas hacen 
referencia a una misma cosa: un patrón de diseño, en el *"que se suministran 
objetos a una clase en lugar de ser la propia clase quien cree los objetos"* 
(definición encontrada en la *Wikipedia* y bastante acertada para lo cortita
que es).

Este patrón es muy popular en el mundo de *Java* y ha sido muy bien explicado
por un famoso gurú del desarrollo de software: *Martin Fowler* [1]_. Los creadores
de *Symfony2* han comprendido su eficacia para organizar sistemas compuestos 
por muchos objetos que presentan dependencias entre ellos y han decidido
diseñar la arquitectura del framework *Symfony2* alrededor de este patrón.

Y ya está bien de palabritas de presentación. Vamos a contarlo. Lo haremos 
directamente con ejemplos, aquí se aplica al cien por cien aquello de que 
*una imagen vale más que mil palabras*.

En un sistema software orientado a objetos, como puede ser un framework de 
desarrollo web, se llevan a cabo tareas de distinta índole: interpretar la 
petición *HTTP*, construir una respuesta *HTTP*, manipular la sesión, controlar
la autentificación y autorización, persistir los datos en una base de datos,
contruir formularios, validar datos, crear documentos *HTML*, *JSON*, *XML* y 
de otros tipos, enviar *e-mails*, mapear las *URL's* en acciones, y otras tareas
que pueden ser incorporadas por el desarrollador de la aplicación. Cada una de 
estas tareas se delega en distintos objetos atendiendo al principio de separación 
de ámbitos (*Separation Of Concern*). Este tipo de objetos que realizan algún tipo
de tarea "global" en el sistema se denominan *Servicios*, para diferenciarlos de 
los objetos que tienen utilidad en partes concretas de la aplicación, esto es 
que se crean y se destruyen en el momento en que han realizado su labor. Estos 
últimos objetos son más propios de la lógica de negocio de la aplicación
(por ejemplo un objeto *Documento* en un aplicación de gestión documental).

Es muy usual, prácticamente lo normal, que los servicios dependan de parámetros
de configuración e incluso de otros servicios que a su vez dependen de 
parámetros de configuración y, posiblemente, de otros servicios. Por ejemplo,
un servicio de persistencia de datos va a depender de los parámetros de conexión
a la base de datos, un servicio de *mailing* dependerá de los parámetros de 
conexión al servidor *SMTP*, un servicio diseñado para registrar usuarios puede
depender del servicio de persistencia y del servicio de *mailing*. Y así hasta
que construyamos nuestro sistema completo.

.. _inyeccion:

Primer paso: inyección de dependencias
--------------------------------------

El primer paso en la aplicación del patrón consiste en diseñar los servicios
de manera que NO construyan ellos mismos los servicios de los cuales dependen, 
sino que los servicios dependientes se pasen debidamente construidos a través
del constructor. Es decir si un servicio ``S2`` depende de otro servicio ``S1``,
lo siguiente no cumple el patrón:

.. code-block:: php
   :linenos:
   
   <?php 
   
   class S1 {
   
   public __construct($p11,$p12, ..., $p1n)
   {...}
   
   ...
   }
   
   class S2 {
   
   public __construct($p21,$p22, ..., $p2m)
   {
    ... 
    $s2 = new S1($p11,$p12, ..., $p1n); 
    ...
   }

En su lugar las clases anteriores se escribirían así:

.. code-block:: php
   :linenos:
   
   <?php 
   
   class S1 {
   
   public __construct($p11,$p12, ..., $p1n)
   {...}
   
   ...
   }
   
   class S2 {
   
   public __construct(S1 $s1 $p21,$p22, ..., $p2m)
   {... }
   

Esto es, para instanciar un objeto ``S2``, hay que crear previamente un objeto
``S1`` y, entonces pasarselo como argumento en la construcción del objeto ``S2``.
Esta manera de tratar las dependencias, mediante *inyección* en los objetos, 
redunda en un **sistema más desacoplado**. Si en un momento dado tenemos que 
cambiar, la clase ``S1`` por ``S1bis``, en el primer plantemiento tenemos que 
tocar el código de la clase ``S2``, mientras que en el segundo, si la clases 
``S1bis`` mantiene la misma interfaz que ``S1`` todo seguiría funcionando sin
más que pasarle al constructor de ``S2`` un objeto de ``S1bis`` en lugar de uno
de ``S1``.

Esta es la parte fácil de patrón. De hecho, es muy probable que hayas usado más de
una vez esta manera de construir los objetos sin haber oído nunca hablar de la 
"inyección de dependencias"

Segundo paso: el contenedor de dependencias
-------------------------------------------

No obstante, cuando el sistema contenga un número considerable de servicios, las
relaciones de dependencia pueden llegar a ser bastante complejas. Entonces, lo que
hemos ganado en flexibilidad al separar las tareas en distintos objetos o 
servicios, lo perdemos en complejidad a la hora de instanciarlos, pues hay que 
tener en cuenta todas las dependencias para instanciar correctamente un servicio.

La siguiente figura ilustra una imagen gráfica de lo que acabamos de decir. Los
servicios (objetos) se han representado con un círculo, y los parámetros de 
configuración con un cuadrado. Observese que un mismo parámetro de configuración
puede ser utilizado por varios servicios, y un mismo servicio puede ser 
utilizado por varios servicios.

.. figure:: ../recursos/dependency_injection.png
   :alt: Sistema de servicios/objetos dependientes

   Sistema de servicios/objetos dependientes

Si en alguna parte de la aplicación necesitamos utilizar el servicio ``S4``
en *PHP* haríamos algo así:

.. code-block:: php
   :linenos:

   <?php
    ...
   
    $params = array(
        'p1' => 'v1',
        'p2' => 'v2',
        'p3' => 'v3',
        'p4' => 'v4',
        'p5' => 'v5',
        'p6' => 'v6',
     );
     
    $s1 = new S1($params['p1'], $params['p2']);
    
    $s2 = new S2($params['p3']);
    
    $s4 = new S4($s1, $s2, $params['p6']);
    
    // y ya podemos usar $s4

Como puedes comprobar resulta un poco engorroso; tenemos que conocer las 
relaciones de dependencias entre los servicios para realizar una instancia 
correcta. ¿Qué podemos hacer para seguir gozando de la flexibilidad ofrecida 
por un conjunto de objetos desacoplados y evitar, a la vez, tener que instanciar
todas las dependencias de un objeto cada vez que lo necesitamos?

La respuesta a esta pregunta es la segunda parte del patrón, y es lo que, en 
nuestra opinión, lo hace realmente útil. Se trata de elaborar un objeto 
*"inteligente"* que conozca las dependencias de los servicios y sea capaz de
construir cualquier servicio que le pidamos y entregarnóslo bien configurado, listo
para su uso, sin que nos tengamos que preocupar de instanciar e "inyectar"sus
dependencias. Este objeto tan fantástico y listo se denomina *"Injector de
Dependencias"*, *"Contenedor de Dependencias"*, *"Contenedor de Servicios"* o, 
simplemente *"Contenedor"*. Nosotros utilizaremos preferentemente el término:
"Contenedor de Servicios". Si contásemos con un " Contenedor de Servicios" 
implementado, por ejemplo, en una clase llamada ``Container``, el código anterior
quedaría así.

.. code-block:: php
   :linenos:

    <?php
    ...
    
    $container = new Container('/ruta/a/fichero/de/configuracion');
    
    $s4 = $container->get('S4');
    
    // y ya podemos usar $s4

Es decir, creamos un "Contenedor de Servicios" y le pedimos una instancia del 
servicio ``S4`` que es el que vamos a usar en ese momento. ¡Y ya está!. El 
contenedor se encarga de construir los objetos necesarios con los parámetros de
configuración correctos. 

.. important::

   Con la aplicación de este patrón, **se ha separado la configuración de los
   servicios de su uso**.  Reflexiona acerca de esta última frase; resume bastante
   bien la finalidad del patrón *Inyección de Dependencias*

Bueno, en realidad esto suena muy bien pero no hemos hablado del principal problema:
¿cómo diseñamos y elaboramos un objeto tan magnífico e inteligente?. El problema
no es nada sencillo. Lo que está claro es que ese objeto debe conocer todas las
dependencias de los servicios de la aplicación. Por eso hemos pasado como 
argumento a nuestro hipotético contenedor un, también hipotético, fichero de 
configuración donde estarían definidas dichas dependencias.

No vamos a invertir tiempo en contar el diseño y construcción de un "Contenedor
de Servicios". Aunque es un ejercicio realmente interesante e instructivo, el 
tutorial va de *Symfony2*, así que contaremos, directamente, como funciona el que
trae incorporado este framework a través de su componente *DependencyInjection*.
Mostraremos su uso integrando la clase ``Model`` de la aplicación de gestión de
alimentos como un servicio.

.. note::

   La aplicación a la que nos referimos es la que se desarrolla en este otro tutorial:

   http://juandarodriguez.es/tutoriales/tutorial-de-symfony2/
 
   Si quieres probar todo lo que se dice en los siguientes apartados, al menos deberías 
   copiar el código del tutorial sobre *Symfony2* del enlace anterior. No obstante, para 
   comprender  el funcionamiento de la inyección de dependencias en *Symfony2* basta con 
   que eches un vistazo a la clase ``Model`` (puedes verla en  http://juandarodriguez.es/tutoriales-2/tutorial-de-symfony2/#implementamos-el-resto-de-la-aplicacion) y leas detenidamente
   este tutorial.

Integración de la clase ``Model`` como un servicio de *Symfony2*
----------------------------------------------------------------

La clase ``Model`` de nuestra aplicación realiza un tipo de tarea "global" en 
el framework; conectarse a la base de datos *MySQL* para realizar operaciones
sobre sus datos. Por tanto se trata de un servicio en el sentido que hemos explicado
anteriormente. Para convertirla en un servicio de *Symfony2* no hay que tocar nada
de su código, ya que todas sus dependencias son pasadas (inyectadas) a través del
constructor, tal y como se ha explicado en el punto anterior. Tan sólo debemos
"decirle" al Contenedor de Servicios  *Symfony2* que la añada al conjunto de 
servicios que es capaz de manipular. Y es lo que haremos. 

.. note::
   
   Para que el *Contenedor de Dependencias* pueda crear instancias de la clase
   que vamos a integrar como servicio, dicha clase debe construirse como se ha
   indicado en el apartado :ref:`inyeccion`, es decir, los objetos que requiera
   la clase no deben ser instanciados por ella, sino pasados ya construidos a
   través de su constructor.

Todos los *bundles* construidos con el generador de *bundles* de *Symfony2*, 
proporcionan el fichero ``Resources/config/services.yml``. Échale un vistazo. 
Trae algunas líneas comentadas para que sirvan como ejemplo. En este fichero se
describen los servicios del *bundle* y sus dependencias. El fichero tiene dos 
secciones; ``parameters`` y ``services``. La primera sirve para declarar 
parámetros de configuración, y la segunda para declarar los servicios (los nombres
lo dicen todo). Los parámetros declarados en la sección ``parameters``, pueden ser
referenciados en cualquier fichero de configuración del framework rodeando su 
nombre con el caracter ``%``. Para incorporar la clase ``Model`` como servicio de 
*Symfony2* debemos añadir al fichero ``services.yml`` el siguiente código.

``src/Jazzyweb/AulasMentor/AlimentosBundle/Resources/config/services.yml``

.. code-block:: yaml

    parameters:
      jamab.database_name: alimentos
      jamab.database_user: root
      jamab.database_password: root
      jamab.database_host: localhost
      jamab.model.class: Jazzyweb\AulasMentor\AlimentosBundle\Model\Model
    
    services:
      jamab.model:
         class: %jamab.model.class%
         arguments: [%jamab.database_name%, %jamab.database_user%, %jamab.database_password%, %jamab.database_host%] 
    
La sección ``parameters`` es autodescriptiva. El nombre de los parámetros de 
configuración puede ser cualquiera. Hemos antepuesto el prefijo ``jamab`` para 
evitar posibles colisiones con otros *bundles*. Observa que hemos parametrizado
incluso el nombre de la clase. La idea es que la definición de todo lo que sea
parametrizable, es decir, susceptible de ser cambiado, se encuentre juntito y 
fácilmente localizable.

En la sección ``services``, cada servicio es declarado con un nombre único. 
Podemos elegir el que queramos siempre que no coincida con otro que ya exista
en el sistema. Por eso es siempre una buena práctica usar un prefijo que haga
referencia al *bundle*. En nuestro caso hemos llamado al servicio: ``jamab.model``.

A continuación hay que indicar qué clase implementa el servicio y qué argumentos
necesita dicha clase para crear objetos de su tipo. Las directivas ``class`` y 
``arguments`` recogen dicha información. Fíjate la forma de referenciar los 
parámetros de configuración con el carácter ``%``.

Esta información es todo lo que necesita *Symfony2* para incorporar la clase 
``Model`` como servicio del framework. Ahora podrás obtener instancias de la misma
a través del "Contenedor de Servicios" de *Symfony2*.

El Contenedor de Servicios de *Symfony2*
----------------------------------------

La clase ``Symfony\Bundle\FrameworkBundle\Controller\Controller`` del framework, 
proporciona en un atributo público llamado ``container``,  una instancia del
contenedor de dependecias. Por tanto, desde cualquier clase derivada de 
``Symfony\Bundle\FrameworkBundle\Controller\Controller`` se puede obtener una
instancia del Contenedor de Dependencias así:

::
   
   $c = $this->container;

Y una vez que lo tengamos podemos instanciar cualquier servicio existente a través
del método ``get()`` del Contenedor de Servicios. A este método se le pasa como
argumento una *string* con el nombre que se le ha dado a dicho servicio en la 
declaración del mismo. Por ejemplo para obtener una instancia del servicio que
acabamos de crear con la clase ``Model``, haríamos lo siguiente:

::
   
   $c = $this->container->get('jamab.model');
   
O más sencillo aún:

::
   
   $c = $this->get('jamab.model');

Ya que el método ``get()`` de la clase ``Symfony\Bundle\FrameworkBundle\Controller\Controller``,
es un ``wrapper``, es decir llama directamente al método ``get()`` del Contenedor
de Dependencia de *Symfony2*.

Como nuestro controlador ``DefaultController`` extiende a la clase
``Symfony\Bundle\FrameworkBundle\Controller\Controller``, podemos obtener instancias
de la clase ``Model`` a través del Contenedor de Servicios de *Symfony2*. El 
código de la clase ``DefaultController`` quedaría así:

``src/Jazzyweb/AulasMentor/AlimentosBundle/Controller/DefaultController.php``

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
    
            return $this->render('JazzywebAulasMentorAlimentosBundle:Default:index.html.twig', $params);
        }
    
        public function listarAction()
        {
            $m = $this->get('jamab.model');
    
            $params = array(
                'alimentos' => $m->dameAlimentos(),
            );
    
            return $this->render('JazzywebAulasMentorAlimentosBundle:Default:mostrarAlimentos.html.twig',
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
    
            $m = $this->get('jamab.model');
    
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
    
            return $this->render('JazzywebAulasMentorAlimentosBundle:Default:formInsertar.html.twig',
             $params);
        }
    
        public function buscarPorNombreAction()
        {
            $params = array(
                'nombre' => '',
                'resultado' => array(),
            );
    
            $m = $this->get('jamab.model');
    
            if ($_SERVER['REQUEST_METHOD'] == 'POST') {
                $params['nombre'] = $_POST['nombre'];
                $params['resultado'] = $m->buscarAlimentosPorNombre($_POST['nombre']);
            }
    
            return $this->render('JazzywebAulasMentorAlimentosBundle:Default:buscarPorNombre.html.twig',
             $params);
        }
    
        public function verAction($id)
        {
            $m = $this->get('jamab.model');
            
            $alimento = $m->dameAlimento($id);
    
            if (!$alimento) {
                throw new \Symfony\Component\HttpKernel\Exception\AccessDeniedHttpException();
            }
    
            $params = $alimento;
    
            return $this->render('JazzywebAulasMentorAlimentosBundle:Default:verAlimento.html.twig',
             $params);
        }
    
    }

Observa (líneas 22, 43, 75 y 88) que ya no tenemos que pasar parámetros de
configuración para obtener una instancia de la clase ``Model``, puesto que el 
Contenedor de Servicios sabe hacerlo y lo hace por nosotros. Como consecuencia,
ya no es necesario la clase ``Jazzyweb\AulasMentor\AlimentosBundle\Config\Config``,
puesto que los parámetros de configuración han sido definidos en el archivo
``src/Jazzyweb/AulasMentor/AlimentosBundle/Resources/config/services.yml``. Así
que puedes borrarla (¡menos mal! era una clase que "chirriaba" demasiado).

.. note::

   Si no has seguido el tutorial http://juandarodriguez.es/tutoriales/tutorial-de-symfony2/, echa un vistazo al código del ``DefaultController.php`` que se encuentra en el apartado: http://juandarodriguez.es/tutoriales-2/tutorial-de-symfony2/#implementamos-el-resto-de-la-aplicacion para comprobar lo que se dice en el apartado anterior.

¿Y qué hemos ganado con todo esto?
----------------------------------

Es muy probable que estes preguntándote qué hemos ganado con todo esto de la 
inyección de dependencias. Y es normal, por que el concepto del Contenedor de
Servicios es una nueva forma de entender la programación orientada a objetos.

En esta sección vamos a indicar algunas de las cosas que hemos ganado al convertir
en servicio de *Symfony2* nuestra clase ``Model``. Después seguiremos mostrando
las ventajas de Contenedor de Servicios implementando nuevos servicios para la
aplicación de gestión de alimentos.

La ventaja más inmediata es el hecho de poder instanciar un objeto ``Model`` sin
tener que pasarle nada ni conocer nada sobre sus dependencias, tan solo hay que 
pedírselo al contenedor.  

Otra cosa buena es que los parámetros de configuración son declarados en un fichero
de configuración y son accesibles desde cualquier otro fichero de configuración 
usando el carácter ``%`` alrededor del nombre del parámetro. También se puede 
obtener el valor de un determinado parámetro de configuración desde un controlador 
utilizando el método ``getParameter()``.

.. code-block:: php
  
   ...
   $database = $this->getParameter('jamab.database_name');
   ...

En ocasiones resulta muy útil cambiar la clase que implementa un servicio.
Por ejemplo, si queremos realizar pruebas aisladas  sobre la clase 
``DefaultController`` para comprobar que la lógica de control funciona como se
espera, es decir, pruebas en las que no interfiera la base de datos,
podemos utilizar en lugar de la clase ``Model``, una clase "tonta" con la misma
interfaz pero independiente de la conexión a la base de datos. Por ejemplo la 
siguiente:

``src/Jazzyweb/AulasMentor/AlimentosBundle/Model/ModelMock.php``

.. code-block:: php
   :linenos:
   
    <?php
    
     namespace Jazzyweb\AulasMentor\AlimentosBundle\Model;
     
     class ModelMock
     {
         protected $conexion;
    
         public function __construct($dbname,$dbuser,$dbpass,$dbhost)
         {   
            
         }
    
         public function dameAlimentos()
         {
             $alimentos = array(
                 array(
                     'id' => 1,
                     'nombre' => 'pera',
                     'energia' => '90', 
                     'proteina' => '80',
                     'hidratocarbono' => '78',
                     'fibra' => '89',
                     'grasatotal' => '98',
                 ),
                 array(
                     'id' => 2,
                     'nombre' => 'manzana',
                     'energia' => '94', 
                     'proteina' => '60',
                     'hidratocarbono' => '38',
                     'fibra' => '83',
                     'grasatotal' => '48',
                 ),
             );
             
    
             return $alimentos;
         }
    
         public function buscarAlimentosPorNombre($nombre)
         {
             return $this->dameAlimentos();
         }
    
         public function dameAlimento($id)
         {
            $alimento = array(
                'id' => 1,
                'nombre' => 'manzana',
                'energia' => '94', 
                'proteina' => '60',
                'hidratocarbono' => '38',
                'fibra' => '83',
                'grasatotal' => '48',
            );
    
             return $alimento;
    
         }
    
         public function insertarAlimento($n, $e, $p, $hc, $f, $g)
         {
         }
    
     }

Fíjate que la clase ``ModelMock`` clase no requiere una conexión a la base de datos, 
pero mantiene la misma interfaz que  ``Model``. Puedes probar a cambiar en el 
archivo de configuración de los servicios del *bundle*, el nombre de la clase
que implementa el servicio ``jamab.model``:

``src/Jazzyweb/AulasMentor/AlimentosBundle/Resources/config/services.yml``

.. code-block:: yaml
   :linenos:
   
    parameters:
      jamab.database_name: alimentos
      jamab.database_user: root
      jamab.database_password: root
      jamab.database_host: localhost
      jamab.model.class: Jazzyweb\AulasMentor\AlimentosBundle\Model\ModelMock
    
    services:
      jamab.model:
         class: %jamab.model.class%
         arguments: [%jamab.database_name%, %jamab.database_user%, %jamab.database_password%, %jamab.database_host%] 
    
Si ejecutas la aplicación ahora puedes comprobar que todo sigue funcionando, aunque
los alimentos que devuelve son los especificados a "capón" en los arrays de la 
clase ``ModelMock``. Esto nos puede servir, como ya hemos dicho antes, para 
construir *tests* con *PHPUnit* que comprueben la funcionalidad del controlador
``DefaultController`` sin necesidad de "contaminar" las pruebas con posibles fallos
con la conexión a la base de datos. Todo esto es posible gracias al uso de objetos
(servicios) absolutamente desacoplados y que son "juntados" entre sí mediante la 
inyección de dependencias.

Imáginemos ahora que, después de que nuestra aplicación de gestión de alimentos ha
crecido un montón, por "exigencias del guíon" nos han migrado los datos a un 
sistema gestor de base de datos *Postgresql*. O que nos exigen hacerla compatible 
también con este sistema. En tal caso podríamos crear una clase ``ModelPostgresql``,
con la misma interfaz que ``Model``, pero con los métodos adaptados para atacar
una base de datos *Postgresql*. Hecho esto, bastaría con cambiar en la 
configuración la clase que implementa el servicio ``jamab.model``.

Terminamos la sección "symfonizando" un poco más nuestro *bundle*. La distribución 
standard de *Symfony2* proporciona el archivo ``app/config/parameters.ini``,
con el objetivo de que se declaren ahí los parámetros globales de la aplicación.
Si lo abres verás que propone como tales a los parámetros de conexión a una base
de datos y a un servidor de correo. Por tanto deberíamos utilizar estos parámetros
como argumentos de nuestro servicio ``jamab.modle``. Para ello basta con realizar
el cambio en el fichero ``services.yml`` del *bundle*:

``src/Jazzyweb/AulasMentor/AlimentosBundle/Resources/config/services.yml``

.. code-block:: yaml
   :linenos:
   
    parameters:
      jamab.model.class: Jazzyweb\AulasMentor\AlimentosBundle\Model\ModelMock
    
    services:
      jamab.model:
         class: %jamab.model.class%
         arguments: [%database_name%, %database_user%, %database_password%, %database_host%] 
    
Ya no necesitamos los parámetros ``jamab.database_name``, etcétera. Obviamente
debes colocar los valores correctos para la conexión a la base de datos en el
fichero de configuración global ``parameters.ini``:

``app/config/parameters.ini``

.. code-block:: yaml
   :linenos:
  
   [parameters]
    database_driver   = pdo_mysql
    database_host     = localhost
    database_port     =
    database_name     = alimentos
    database_user     = root
    database_password = root

    mailer_transport  = smtp
    mailer_host       = localhost
    mailer_user       =
    mailer_password   =

    locale            = en

    secret            = ThisTokenIsNotSoSecretChangeIt

¡*Servicios* al poder! 
----------------------

Continuamos mostrando la potencia de los servicios. Ahora vamos a construir un
servicio que combinará el que acabamos de crear ``jamab.model``, y el servicio de 
*mailing* que viene incluido en *Symfony2*. Denominaremos al nuevo servicio
``jamab.infosender``, y su cometido será enviar por correo electrónico la 
información que tenemos en nuestro sistema sobre un determinado alimento.

En primer lugar vamos a crear la clase que implementará el servicio. La llamaremos
``InfoSender`` y la colocaremos en el espacio de nombres 
``Jazzyweb\AulasMentor\AlimentosBundle\Model``, se alojará por tanto en el 
directorio ``src/Jazzyweb/AulasMentor/AlimentosBundle/Model``. 

Dicha clase, para que cumpla el patrón y pueda integrarse como servicio, debe 
recibir en su constructor un objeto ``jamab.model`` y otro ``mailer`` . Con el
primero buscará información sobre alimentos en el sistema, y con el segundo
enviará el e-mail con dicha información. La clase que nos hemos inventado para
este menester es la siguiente:

``src/Jazzyweb/AulasMentor/AlimentosBundle/Model/InfoSender.php``

.. code-block:: php
   :linenos:
   
    <?php
    
    namespace Jazzyweb\AulasMentor\AlimentosBundle\Model;
    
    class InfoSender
    {
    
        protected $model;
        protected $mailer;
    
        public function __construct($model, $mailer)
        {
            $this->model = $model;
            $this->mailer = $mailer;
        }
    
        public function send($terminoBusqueda, $direccionEmail)
        {
            $alimentos = $this->model->buscarAlimentosPorNombre($terminoBusqueda);
    
            $texto = '';
            foreach ($alimentos as $alimento)
            {
                $texto = implode(',', $alimento);
                $texto .= PHP_EOL;
            }
    
            $message = \Swift_Message::newInstance()
                    ->setSubject('Información sobre alimentos')
                    ->setFrom('noreplay@aulasmentor.com')
                    ->setTo($direccionEmail)
                    ->setBody($texto)
            ;
            
            $this->mailer->send($message);
        }
    }
    
El constructor de la clase (líneas 11-15) recibe los objetos ``$model`` y
``$mailer`` (o dicho de otra forma; los servicios ``jamab.model`` y ``mailer``) 
ya creados y bien configurados, y simplemente los asigna como atributos para que
puedan utilizarlos los métodos que se irán añadiendo al servicio.

El método ``send()`` del servicio (líneas 17-36), recibe como parámetros un 
término de búsqueda y una dirección de correos. Entonces, utiliza el servicio
``jamab.model`` (línea 19) para realizar una busqueda de alimentos en el sistema. 
El resultado es usado para construir una cadena que servirá como cuerpo del e-mail
(líneas 21-26). A continuación se prepara un mensaje de e-mail (líneas 28-33) con 
los campos correspondientes; *subject*, *from*, *to* y *body*, y se usa el
servicio de *mailing* de *Symfony2* (línea 35) para enviar el mensaje.

.. note::
   
   El servicio de *mailing* incorporado con *Symfony2* esta basado en la librería
   *SwiftMailer*, y para el envío de mensaje utiliza el método ``send()`` cuyo
   argumento debe ser una instancia de ``Swift_Message``. La creación de esta
   instancia es lo que se realiza en las líneas 28-33.
   
Algo importante y que hemos de resaltar. Durante el proceso de construcción 
de la clase ``InfoSender`` no nos hemos preocupados por detalles de la configuración
de los servicios, simplemente sabíamos que existian y conocíamos sus funciones, las
hemos utilizado y se acabó. Es la consecuencia de haber separado la configuración
del uso. Gracias a ello, escribir un servicio que dependa de otro, o simplemente
utilizar un servicio es realmente sencillo.

El próximo paso es integrar la clase que acabamos de construir como servicio del
framework. Si has estado atento a todo lo que llevamos dicho en esta unidad, sabrás
que esto se hace añadiéndola al fichero de configuración ``services.yml`` del 
*bundle*.


``src/Jazzyweb/AulasMentor/AlimentosBundle/Resources/config/services.yml``

.. code-block:: yaml
   :linenos:
   
   parameters:
      jamab.model.class: Jazzyweb\AulasMentor\AlimentosBundle\Model\Model
      jamab.infosender.class: Jazzyweb\AulasMentor\AlimentosBundle\Model\InfoSender
    
   services:
      jamab.model:
         class: %jamab.model.class%
         arguments: [%database_name%, %database_user%, %database_password%, %database_host%] 
      
      jamab.infosender:
         class: %jamab.infosender.class%
         arguments: [ @jamab.model, @mailer ]

La única explicación que merece el código anterior es que hay que tener en cuenta 
que los argumentos que son servicios se representan anteponiendo el carácter ``@``
a su nombre.

El último paso es asegurarnos de que todos los servicios que estamos usando están 
bien configurados. Los de nuestra factura; ``jamab.model`` y ``jamab.infosender``, 
obviamente lo están (véase el código anterior). Nos falta comprobar el servicio
de *mailing*. Este servicio se configura en el archivo de configuración global
``app/config/config.yml``, en la sección ``swiftmailer``.Echale un vistazo y fíjate
que hace referencia a los parámetros ``%mailer_transport%``, ``%mailer_host%``,
``%mailer_user%`` y ``%mailer_password%``. ¿Adivinas donde se definen tales 
parámetros? Espero que así sea, significa que has estado leyendo la unidad con
atención. Se hace en el archivo ``src/config/parameters.ini``, aunque podríamos 
colocar los valores concretos directamente en el fichero ``config.yml``, los 
administradores de sistemas que van a instalar nuestra aplicación agredecerán
muchísimo que todos los parámetros de configuración que tengan que ver con los
servicios del sistema se encuentren juntitos en un sólo fichero (cuidado que ahora
la palabra *servicio* la estamos usando en un contexto de sistemas de información,
esto es, nos referimos a servicios de base da datos, de correo, etcétera).

Hemos utilizado una cuenta de *Gmail* para enviar emails. Para configurarla es
preciso añadir dos parámetros más: el tipo de encriptación y el modo de 
autenticación (esto lo dice la documentación de *Gmail*). Por otro lado, en la
documentación oficial de *Symfony2* se dice que estos datos son mapeados por
*swiftmailer* a través de los parámetros ``encryption`` y ``auth_mode``. La 
sección ``swiftmailer`` del archivo de configuración global ``config.yml`` queda
así:

``app/config/config.yml``

.. code-block:: yaml

    ...
    swiftmailer:
        transport:  %mailer_transport%
        username:   %mailer_user%
        password:   %mailer_password%
    ...

Y la declaración de los parámetros:

``app/config/parameters.ini``

.. code-block:: yaml
   :linenos:
    
    ...
    mailer_transport  = gmail
    mailer_user       = tuusername
    mailer_password   = tupassword
    ...

Y ya tenemos el servicio de *mailing* bien configurado y listo para ser usado.
Y ahora toca probarlo. Construiremos una ruta y una acción asociada para ello.

En ``src/Jazzyweb/AulasMentor/AlimentosBundle/Resources/config/routing.yml``
añadimos la ruta:

.. code-block:: yaml
 
  JAMAB_testinfosender:
    pattern:  /testinfosender
    defaults: { _controller: JazzywebAulasMentorAlimentosBundle:Default:testInfoSender }

Y en el ``DefaultController`` implementamos la acción correspondiente:

``src/Jazzyweb/AulasMentor/AlimentosBundle/Controller/DefaultController.php``

::

  <?php
  ...
  public function testInfoSenderAction()
    {
        $infosender = $this->get('jamab.infosender');

        $infosender->send('%naranja%', 'juandalibaba@gmail.com');

        return new \Symfony\Component\HttpFoundation\Response(
                '<html><body><h2>Se ha enviado información a 
                juandalibaba@gmail.com</h2></body></html>');
    }
  ...
  
Lanza esta ruta en tu navegador y se enviará información sobre el alimento 
*naranja* a la dirección de e-mail que hayas elegido.

.. note::
   
   Sustituye ``tuusername``, ``tupassword`` y ``quientuquieras@dondequieras.com``
   por los valores propioso de tu cuenta de e-mail y por la dirección de correo
   contra la que deseas hacer pruebas.
   
.. note::

   Observa que no hemos construido una plantilla para pintar la acción. Como es
   una prueba sencilla, hemos decidido construir directamente un objeto ``Response``.

El uso del servicio ``jamab.infosender``, como todos los servicios de *Symfony2*
no puede ser más sencillo. Le dices al *Contenedor de Servicios*: *"dame el 
servicio fulanito"*, él te lo devuelve educadamente, sin hacerte preguntas, y tu
lo usas en tu aplicación. Que quieres usar otro servidor para enviar los mensajes.
No tienes que cambiar ni una línea de tu código. Cambias la configuración del
servicio de *mailing* y todo sigue funcionando. Que ahora resulta que no quieres
enviar mails mientras estás haciendo pruebas. Añades a la configuración de 
*swiftmailer* el parámetro: ``disable_delivery:  true`` y se deshabilita el envío.
Son posibles situaciones prácticas que se te pueden dar en el desarrollo de tus
aplicaciones y que, gracias a la inyección de dependencias, se resuelven de una
manera muy sencilla y segura en el sentido de que realizas grandes cambios y todo
sigue funcionando, nada se rompe.

Más servicios aún
-----------------

No te apures ya hemos terminado, tan sólo queremos recalcar que *Symfony2* está
compuesto por muchos servicios como los que hemos estudiado en esta unidad. Y 
que, por supuesto, los *bundles* de terceros más famosos utilizan extensiva e
intensivamente la inyección de dependencia. Comprender bien este concepto te 
facilitará el aprendizaje y tu experiencia con *Symfony2*. 

.. note::

   Si quieres saber todos los servicios que vienen con *Symfony2* ejecuta este
   comando:

   .. code-block:: bash

      php app/console container:debug


Es verdad que se pueden desarrollar aplicaciones con *Symfony2* sin necesidad de
"echar mucha cuenta" a la inyección de dependencias. Basta con conocer los 
servicios más importantes proporcionados por *Symfony2*, instanciarlos en 
las acciones del controlador cuando los necesitas y utilizarlos. Sin embargo, si
no prestas suficiente atención a este concepto y lo dejas un poco de lado, 
sentirás que no consigues controlar del todo al framework y en ocasiones se te
escapa. Comprender la *inyección de dependencia*, el concepto de  *servicio* y
de *contenedor de servicios*, es fundamental para tomar las riendas del framework
y cabalgar sobre él confortablemente. Este es el motivo esta unidad y de su 
ubicación a la mitad del curso, justo antes de comenzar a desarrollar una
aplicación más compleja que servirá de vehículo vertebrador para aprender a 
utilizar *Symfony2*.

En las unidades anteriores ya hemos utilizado los servicios de *Symfony2* sin 
haberlos mencionados. Cuando renderizamos los resultados de una acción con una
plantilla, estamos haciendo uso del servicio ``templating`` a través del *wrapper*
``render()`` de la clase ``Controller``. De hecho hacer esto:

::
 
  return $this->render('JazzywebAulasMentorAlimentosBundle:Default:index.html.twig', $params);

es equivalente a esto:

::

  $t = $this->get('templating');
        
  return $t->renderResponse('JazzywebAulasMentorAlimentosBundle:Default:index.html.twig', $params);
 
Pero más cortito. 
 
En las próximas unidades estudiaremos los servicios más importantes que ofrece
*Symfony2*. Con ellos se puede hacer casi de todo. No obstante a veces tendremos
que ampliar el framework con nuevas funcionalidades. Y esto, si queremos hacerlo
bien, debemos hacerlo usando la inyección de dependencias y los servicios.

El tutorial en chuletas
-----------------------

* *Servicio*. Un tipo de objetos que realizan algún tipo de tarea “global” en el 
  sistema.

* Las clases que se quieran integrar como servicios, se definen en el archivo
  ``Resources/config/services.yml`` del *bundle*.

* Ese archivo tiene dos secciones: ``parameters`` y ``services``. La primera sirve
  para definir parámetros de configuración del *bundle* (que pueden ser utilizados
  por los servicios o en otras partes del *bundle*), y la segunda para declarar los
  servicios.
  
* Las clases que vayan a incorporarse como servicios deben "inyectar" sus 
  dependencias (parámetros y otros servicios) a través de su constructor o de
  algún *setter*.

Ejemplo:

.. code-block:: yaml

    parameters:
      jamab.database_name: alimentos
      jamab.database_user: root
      jamab.database_password: root
      jamab.database_host: localhost
      jamab.model.class: Jazzyweb\AulasMentor\AlimentosBundle\Model\Model
    
    services:
      jamab.model:
         class: %jamab.model.class%
         arguments: [%jamab.database_name%, %jamab.database_user%, %jamab.database_password%, %jamab.database_host%]

* Desde un controlador (que extienda la clase ``Symfony\Bundle\FrameworkBundle\Controller\Controller``, 
  se puede obtener cualquier servicio usando el método ``get()``:
  
::

    $servicio = $this->get('nombre_del_servicio');

* Desde ese controlador también se puede obtener los valores de los parámetros
  usando el método clase ``getParameter()``:

::

    $parametro = $this->getParameter('nombre_del_parametro');
    
* Los argumentos que son servicios se especifican colocando el carácter ``@`` 
  delante del nombre:
  
.. code-block:: yaml

   ...
   services:
     jamab.infosender:
        class: %jamab.infosender.class%
        arguments: [ @jamab.model, @mailer ]
   ...


Comentarios
-----------

Aquí puedes enviar comentarios, dudas y sugerencias. Utiliza la barra de *scroll* para
recorrer todos los mensajes. El formulario de envío se encuentra al final.

.. raw:: html

   <object type="text/html" width="100%" height="800" data="commentator/index.php?page=unidad4"></object> 

----------

 .. [1] http://martinfowler.com/
 
----------

.. raw:: html

   <div style="background-color: rgb(242, 242, 242); text-align: center; margin: 20px; padding: 10px;">
   <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/"><img alt="Licencia Creative Commons" style="border-width:0" src="http://i.creativecommons.org/l/by-nc-sa/3.0/88x31.png" /></a>
   <br />
   <span xmlns:dct="http://purl.org/dc/terms/" href="http://purl.org/dc/dcmitype/Text" property="dct:title" rel="dct:type">Desarrollo de Aplicaciones web con Symfony2</span> por <span xmlns:cc="http://creativecommons.org/ns#" property="cc:attributionName">Juan David Rodríguez García (juandavid.rodriguez@ite.educacion.es)</span>
   <br/>
   se encuentra bajo una Licencia <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Reconocimiento-NoComercial-CompartirIgual 3.0 Unported</a>.
   </style>
   </div>
