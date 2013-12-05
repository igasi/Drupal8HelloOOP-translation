Drupal 8: Hello OOP, Hello world!
=================================

Introducción
------------

_Enviado por effulgentsia el 2 de agosto de 2013 a las 06:50:32 PM_
Hola mundo, Este es mi primera entrada de blog. Para las personas que no me conoces, soy [effulgentsia]: https://drupal.org/user/78040 en drupal.org. Trabajo en [Acquia's OCTO group]:http://buytaert.net/announcing-the-office-of-the-cto-at-acquia, colaboro bastante en el CORE de Drupal, escribiendo y revisando parches.

Trabajando en Drupal 8 y asistiendo a varios eventos Drupal, eh conocido a un buen número de desarrolladores de módulos Drupal 7 con la inquietud acerca de que se necesita aprender para ser unos desarrolladores de módulos Drupal 8 existosos.

Varias personas de la comunidad de Drupal ah empezado a escribir entradas de blog sobre este tema, una de ellas a principios de esta semana por Joe Shindelar sobre "Cómo escribir un módulo Hello World". ([writing a Hello World module]: http://drupalize.me/blog/201307/drupal-8-writing-hello-world-module)

En esta entrada de blog, me gustaría profundizar un poco más, en lo primero que probablemente se dará cuenta al ver el video y escribir el módulo: que ahora estas _namespaced PHP classes_ en lugar de funciones globales, inlcuso por cosas muy simples. Si primero aprendiste PHP en los últimos 2 años, o has trabajado en cualquier otro proyecto moderdo de PHP orientado a objetos, todo esto puede ser un segundo lenguage natural. Sin embargo, si eres como yo, y sólo aprendiste PHP para desarrollar para Drupal 7 o una versión anterior, podría tardar un poco para aprender y adaptarse a las mejores prácticas de la programación orientada a objetos en PHP. Pero bueno, de aprender y adaptarse es de lo que se trata la programación. Basta con ver lo mucho que han cambiado las mejores prácticas en HTML, CSS y JS en los últimos 3 años. ¿Por qué debe quedarse estancado el lado del servidor?

Para empezar, echemos un vistazo a lo que un hello.module vería en Drupal 7:

hello.info
----------

```php
name = Hello
core = 7.x
```

hello.module
------------

```php
<?php

function hello_menu() {
  return array(
    'hello' => array(
      'title' => 'Hello',
      'page callback' => 'hello_page',
      'access callback' => 'user_access', 
      'access arguments' => array('access content'),
    ), 
  ); 
} 

function hello_page() { 
  return array( 
    '#type' => 'markup', 
    '#markup' => t('Hello.'), 
  ); 
} 

?>
```

Bastante simple hasta ahora, ¿cierto? Existe un archivo .info que le permite a Drupal saber sobre el módulo, y dentro del archivo hello.module, se implmenta hook_menu(), lo cual especifica que se desea un enlace en el menú (que aparece en el menú de navegación por defecto) en la URL "hello", cuyo títilu del enlace es "Hello". Visitando la URL (ya sea dando clic en el vínculo o al escribirla en la barra de direccónes del navegador) debe devolver el contenido de la función hello_page(), pero sólo los usuarios con permisos para acceder al contenido.

Sin embargo, hay dos cosas aquí que necesitan ser mejoradas aún para Drupal 7. La primera es que al tener hello_page() en hello.module, PHP necesita cargar esa función en la memoría para cada solicitud de página única, a pesar que la mayorñia de las solicitudes probablemente sean por alguna URL diferente de /hello. Una función adicional para cargar no sta mal, pero en un sitio con muchos módulos, si cada módulo hace las cosas de esta manera, lo haría muy lento. Por lo tanto, es mejor trasladar la función a un archivo que se puede cargar sólo cuando sea necesario:

hello.info
----------
```php
name = Hello 
core = 7.x 
```

hello.module
------------
```php
<?php

function hello_menu() {
  return array(
    'hello' => array( 
      'title' => 'Hello', 
      'page callback' => 'hello_page', 
      'access callback' => 'user_access', 
      'access arguments' => array('access content'), 
      'file' => 'hello.pages.inc', 
    ), 
  ); 
} 
?>
```

hello.pages.inc
---------------
```php
<?php

function hello_page() {
  return array(
    '#type' => 'markup', 
    '#markup' => t('Hello.'), 
  ); 
} 
?>
```

