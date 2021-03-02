---
title: '¿Qué son los registros en un microcontrolador?'
date: Sat, 08 Jun 2019 15:46:28 +0000
draft: false
tags: ['atmega328p', 'avr', 'c']
---

Hemos hablado acerca de los registros en anteriores entradas, vamos a profundizar un poco en el tema.

Para entender un registro, definamos que es un bit, la palabra tiene varias acepciones, para nuestro propósito un bit es la representación de un valor binario, este puede ser 1 o 0. Entonces un _registro es sencillamente un conjunto de bits_, en nuestro microcontrolador atmega328p, existen registros de 8bits, en otras arquitecturas como los cortex-m los registros son de 32 bits, algunos como el msp de texas instruments, tienen registros de 16 bits.

¿Es mejor 32 que 8 bits?, no realmente, la diferencia radica en que un sistema de 32 bits requiere un bus de 32 bits, un bus podría definirse como un conjunto paralelo de cables que comunica los registros con algún otro elemento, en otras palabras un bus de 32 bits comunica mayor información por cada ciclo, a priori esto es bueno, sin embargo las instrucciones que también viajan por este bus, no necesariamente ocupan los 32 bits, esto deriva en programas más grandes para las arquitecturas de 32 bits. Hoy por hoy las arquitecturas de 32 bits son muy populares, siendo incluso más baratas y eficientes que sus contrapartes de 8 bits, la gran ventaja de los 8 bits radica en su facilidad y simpleza.

Siguiendo con los registros, programar un microcontrolador se resume a modificar los registros, ya comentamos el registro DDRx, donde x es un puerto determinado (haremos una serie especifica sobre puertos), modificar dicho registro nos permite establecer el comportamiento de cada uno de los pines pertenecientes a dicho puerto. A lo largo de las entradas iremos revisando varios de los registros y explicaremos las implicaciones que se tienen al modificarlos. En realidad DDRx es un nemotécnico no un registro, cuando escribimos DDRB, realmente estamos escribiendo algo similar a \*(0xfa020121029120), una vez que estudiemos los apuntadores detallaremos que es lo anterior, adelanto que el número entre paréntesis es la dirección del registro).

El tema de los registros tiene una gran exposición cuando se programa en ensamblador, el ensamblador es un lenguaje muy estimulante de aprender y el ensamblador de avr es muy limpio y sencillo, quizá en el futuro expliquemos un poco.