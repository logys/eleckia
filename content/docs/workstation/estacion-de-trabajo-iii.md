---
title: 'Estación de trabajo III'
date: Wed, 05 Jun 2019 03:56:02 +0000
draft: false
tags: ['Arduino', 'Fedora', 'Herramientas']
---

Ya casi estamos listos, damos click en **Actividades** y abrimos una terminal, la terminal puede ser intimidante, pero creeme una vez te acostumbres a ella la amaras.

En los sistemas linux tenemos diferentes tipos de usuarios cada uno de ellos con diferentes privilegios, el usuario con más privilegios es el usuario root, puede hacer y deshacer el sistema, nuestro usuario por defecto tiene pocos privilegios para evitar que el usuario pueda romper el sistema por accidente. Al abrir la terminal tendremos algo similar a esto

```
[eleckia@tuxin-lp ~]$
```

donde eleckia es el nombre de usuario que se eligió, y tuxin-lp es el nombre que se le dio a la pc, delante del signo $, podemos escribir.

DNF es el manejador de paquetes de Fedora, es una herramienta que nos permite actualizar, instalar y remover los programas y/o bibliotecas instaladas en nuestro sistema, para utilizarlo simplemente tecleamos

```
[eleckia@tuxin-lp ~]$ dnf
```

nos desplegara una enorme lista de las opciones disponibles para este comando, el que nos interesa por ahora es **update**, esta opción nos actualiza todos los paquetes a la última versión disponible, tecleamos

```
[eleckia@tuxin-lp ~]$ dnf update
Error: Este comando debe ejecutarse como usuario root.
```

el error nos indica que requerimos privilegios altos, la forma de escapar de la tiranía el usuario root, es anteponer la palabra **sudo**, es decir

```
[eleckia@tuxin-lp ~]$ sudo dnf update
```

nos pedirá introducir la contraseña que creamos al iniciar Fedora por primera vez, la introducimos y nos desplegará la lista de actualizaciones disponibles, nos pedirá confirmación a lo que responderemos **s**. La primera vez que actualicemos, habrá muchas actualizaciones, por lo que se tardara un poco, al terminar de actualizar, reiniciaremos el sistema y abriremos nuevamente una terminal, una vez hecho esto instalaremos Arduino por fin. Para instalarlo tan fácil como escribir lo siguiente:

```
[eleckia@tuxin-lp ~]$ sudo dnf install arduino
```

terminada la descarga e instalación, ya tenemos disponible el software Arduino, hacemos click en actividades y lo ejecutamos, nos indicara que debemos agregar nuestro usuario a los grupos dialout y lock, le decimos que si e introducimos la contraseña del sistema, tenemos finalmente todo listo para comenzar a programar, tan solo nos falta conectar nuestro Arduino a un puerto usb.

Anteriormente mencione que trabajaremos con Arduino uno, sin embargo debo confesar que cualquier Arduino de 8 bits es valido, de hecho ni siquiera es necesario instalar Fedora, puedes instalar Arduino en casi cualquier sistema operativo, la razón de instalar Fedora es que nos ofrece todas las herramientas de manera simple e inmediata, al estar dentro de una máquina virtual no debemos preocuparnos de dañar el sistema accidentalmente, yo haré todo desde Fedora, por que es mi sistema instalado, pero sientete libre de utilizar lo que más te acomode, eso si, si tienes algún problema con tu sistema no podre ayudarte puesto que yo no lo uso (windows). Lo mismo va para la tarjeta Arduino que utilices, funciona cualquiera, pero los registros de cada microcontrolador varian mucho, sobre todo de una arquitectura a otra, los microcontroladores de 8 bits no tiene nada que ver con los micros de 32 o 16 bits.

No es de mi interés centrarme en Arduino, pero es la opción de aprendizaje más adecuada y sería tonto no empezar con ella.

Los lugares más comunes para adquirir la tarjeta es ebay, amazon y mercadolibre, es tu elección, también requerimos algunos dispositivos como leds, resistencias, transistores, capacitores, etc, en el siguiente post daremos algunos detalles adicionales.