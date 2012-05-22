Contenedores de atributos en *symfony 1.4*
==========================================

En symfony 1.4 los *parameter holders* son objetos de una clase denominada 
``sfParameterHolder``, que se utilizan para almacenar y organizar parámetros. Se pueden
utilizar allá donde el programador lo considere oportuno. Pongamos un ejemplo para ilustrar
este concepto. Supongamos que estamos parseando un fichero que contiene información acerca
de distintos aspectos de los planetas y queremos recoger tales datos almacenándolos de 
forma ordenada. Podríamos utilizar *parameter holders* para solucionar este problema:

.. code-block:: php
   
    <?php
    ...
    $latierra = new sfParameterHolder();
    $mercurio = new sfParameterHolder();
    $venus = new sfParameterHolder();
    
    $latierra->set('diametro', 1);
    $latierra->set('masa', 1);
    $latierra->set('periodo_orbital', 1);
    
    $mercurio->set('diametro', 0.382);
    $mercurio->set('masa', 0.055);
    $mercurio->set('periodo_orbital', 0.24);
    
    $venus->set('diametro', 0.949);
    $venus->set('masa', 0.815);
    

Cada *parameter holder* representaría un planeta y (aunque con el fin de ilustrar en el
código anterior los hemos usado directamente) el proceso de *parsing* se encargaría de 
utilizar el parameter holder adecuado para añadir cada parámetro parseado del fichero de
datos. Posteriormente podríamos utilizarlos en nuestra aplicación  para:

* saber si tenemos un parámetro determinado de algún planeta:

* obtener un parámetro de un planeta

* serializar el parameter holder para, por ejemplo, almacenarlo en una base de datos o enviarlo como respuesta de un servicio web.

* borrar sus datos,

* y más cosas.

.. code-block:: php

    <?php
    ...
    $venus->has('periodo_orbital');  // false
    $diametro_venus = $venus->get('diametro'); //obtiene el diámetro de venus
    $venus_serialized = $venus->serialize();   // serialización
    $venus-> remove('periodo_orbital');        // elimina el parámetro periodo_orbital
    $venus->clear();                           // borra todos los parámetros

Y ahora viene lo realmente importante: en symfony 1.4, tanto los atributos de la sesión 
como los de la *request*, son *parameter holders*, lo cual permite organizar los datos de
la sesión y de la *request* según nuestras necesidades. En el caso de la sesión puede ser
especialmente útil organizar los atributos que vamos almacenando a lo largo de la ejecución
de la aplicación. Además de mejorar la organización puede evitar la colisión entre
parámetros de sesión. Expliquemos esto último:

Supongamos que en una parte de la aplicación hemos creado una variable de sesión denominada
``var`` (``$this->getUser()->set('var','valor1');``) , y en otra parte que no tiene nada que ver 
con la anterior se ha vuelto a definir una variable de sesión denominada también ``var`` 
(``$this->getUser()->set('var','valor2');``).  Esto puede suceder fácilmente si han sido 
personas distintas las que han elaborado cada parte de la aplicación. Pues bien, en ese
caso puede producirse un comportamiento anómalo si, por ejemplo ocurre lo siguiente: El 
usuario pasa por la primera parte de la aplicación y se crea la variable de sesión ``var``
con valor ``valor1``. Posteriormente pasa por la segunda parte de la aplicación y se 
modifica el valor de dicha variable por ``valor2``. Y, por último, se vuelve a utilizar una
funcionalidad de la parte 1 que espera el valor de la variable var definido en principio.
Como ha sido cambiado en el segundo paso, la aplicación fallará de alguna manera inesperada
y posiblemente se trataría de un fallo de dificil detección.

Los *parameter holders* resuelven este problema de la colisión entre variables de sesión. 
Bastaría tener la precaución de definir las variables en *parameter holders* con nombres 
relacionado con cada parte de la aplicación (por ejemplo con el nombre del plugin, o del
módulo o de la aplicación o de lo que fuese cada parte abstracta que estamos suponiendo).
Así, aunque las variables se llamen igual, al pertenecer a distintos *parameter holders*,
no colisionarían. ¿Cómo se hace esto en la práctica?.

En la parte 1 de la aplicación se definiría la variable de sesión así:

.. code-block:: php

    <?php
    ...
    $this->getUser()->set('var', null, 'nombre_parameter_holder1');

Y en la parte 2:

.. code-block:: php

    <?php 
    ...
    $this->getUser()->set('var', null, 'nombre_parameter_holder2');

(el segundo parámetro del método set es el valor por defecto de la variable si lo hubiera)

Y para recuperarlos en donde haga falta:

.. code-block:: php

   <?php
   ...
   this->getUser()->get('var',  'nombre_parameter_holder1');

o

.. code-block:: php

   <?php 
   ...
   this->getUser()->get('var',  'nombre_parameter_holder2');
   
según queramos uno u otro.

En aplicaciones pequeñas, aunque no hacen daño, puede que no sean tan precisos, pero cuando
la aplicación alcanza cierto tamaño son imprescindibles. Y son muy adecuados si queremos 
dejar que nuestra aplicación crezca sana.