---
title: 'Arduino'
date: Wed, 05 Jun 2019 19:07:30 +0000
draft: false
tags: ['Arduino', 'atmega328p']
---

# Arduino
Arduino se compone tanto de software como de hardware, miremos un poco el 
hardware.

## Hardware
El Arduino Uno (placa de desarrollo), cuenta con un puerto usb, por el que se 
alimenta la placa; enviamos y recibimos datos, este puerto esta conectado a un 
circuito integrado FTDI que no es más que un convertidor serial-usb, igualmente 
este convertidor se conecta a unos pines específicos del 
microcontrolador. Lo importante a destacar es que esta conexión ya esta hecha y
es perfectamente funcional, permitiendo la conexión entre la placa y pc sin 
mayores complicaciones, esta es sin duda la gran característica que dio paso 
al éxito, 
una plataforma lista para usarse sin necesidad de componentes adicionales.

El convertidor serial varia dependiendo de la versión de tu tarjeta, pero su 
comportamiento es similar en todas las versiones.  Ahora nos enfocamos en 
conocer el microcontrolador,

![atmega328p](/eleckia/img/atmega328p.jpg)

El atmega328p es un microcontrolador avr de 8 bits con arquitectura RISC, no
entraremos en detalles sobre la arquitectura.
Quizá el parámetro más importante de un microcontrolador es la capacidad de memoria 
con la que cuenta, la asombrosa cantidad de 32KB de flash o memoria de programa
y 2KB de ram, teniendo en cuenta que en este momento contamos con PCs 
regularmente de 8GB de ram y mayores de 1TB de almacenamiento, la memoria del 
atmega328p parecería poco, sin embargo se pueden hacer muchas cosas con ella y 
cuando digo muchas es muchas, adicionalmente cuenta con 1KB de memoria eeprom, 
en la cual podemos guardar datos y evitar que se pierdan cuando se corta la 
alimentación.

Otro parámetro importante es la frecuencia a la que trabaja, siendo hasta 20MHz
el máximo posible, internamente cuenta con un oscilador que puede ser 
configurado hasta 8MHz, si queremos frecuencias mayores podemos conseguirlas 
conectado un oscilador externo, usualmente un reloj de cuarzo, la regla es que 
a mayor frecuencia mayor capacidad de procesamiento, pero mayor gasto de energía.
En el caso del Arduino(tarjeta), tiene un reloj de 16MHz ya conectado, por lo 
que el entorno esta desarrollado con este reloj en mente.

Cuenta con 23 pines programables en la versión DIP y hasta 32 en la versión de 
montaje superficial.

Los periféricos con los que cuenta

*   Timer con output compare
*   ADC
*   USART
*   SPI
*   I2C
*   Y varias fuentes de interrupciones

Describiré con mucho detalle los periféricos en posteriores entradas.

Adicionalmente la tarjeta cuenta con un par de hileras de pines donde podemos 
conectar componentes externos, un jack de alimentación por el cual podemos 
conectar hasta 9V, finalmente un LED conectado al pin 13.

## Software
En cuanto al software, Arduino es un conjunto de bibliotecas que nos permiten
programar el microcontrolador de una manera simple y fácil, hay bibliotecas
para muchos dispositivos, muy probablemente la solución a una de tus necesidades
o al menos la solución parcial se encuentre en forma de biblioteca ya programada
por alguien más.

## Sin Arduino
Mi objetivo es exponer al
microcontrolador per se más que al ambiente Arduino en general, tan solo 
sacare provecho de la tarjeta.
