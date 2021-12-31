---
title: 'Variables y tipos'
date: Fri, 14 Jun 2019 02:32:35 +0000
draft: false
tags: ['avr', 'c', 'variables']
---
# Variables y tipos primitivos

El lenguaje de programación C, es un lenguaje de tipado estático, esto quiere
decir que nosotros debemos indicar el tipo de dato que guarda una variable. 

¿Qué es una variable?, puede decirse que una variable es un espacio en memoria
que guarda un valor determinado, y ¿qué es un tipo?, por tipo de se entiende 
a una indicación hacia el compilador de como se utilizará el dato. En C existen
los siguientes tipos, también llamados tipos primitivos:

*   __int__, enteros almacenan valores enteros, como: 1, 2, -100, 0b11111, 0x32, ...
*   __float__ o __double__, de punto Flotante, cantidades con decimales
como: 1.111, 3.141598, -1.4142, ...
*   __char__, Caracter. 'a', 'y', ' ', '\\n', ...
*   \*, Apuntador, almacena direcciones de memoria.

Existen tipos adicionales pero son variantes de las anteriores, como dijimos 
para declarar una variable debemos especificar su tipo, esto se hace con la 
siguiente sintaxis,

```C
tipo nombre_de_variable;
//Una variable entera llamado numero
int numero;
//Una variable de tipo carácter llamada letra
char letra;
//Una variable de tipo punto flotante llamada cantidad
double cantidad;
//Un apuntador de tipo entero llamado signal
int * signal;
```

¿Fácil no?, pero en la vida nada es fácil :D.

## El tamaño de los enteros, stdint.h

Un problema importante es que el tamaño de los enteros depende del compilador
y de otros factores como la arquitectura del microcontrolador, en el caso
de los microcontroladores avr de 8 bits, el tamaño de los enteros declarados
con _int_ es 2 bytes, sin embargo en microcontroladores cortex-m suelen ser
de 4 bytes, esto provoca que el código sea difícil de portar entre una 
plataforma y otra, afortunadamente podemos hacer el código más portable con
la biblioteca _stdint.h_, con ella podemos especificar el tamaño de los enteros:
```C
#include <stdint.h>

//entero de 8 bits llamado ocho
int8_t ocho;
int16_t dieciseis;
int32_t treintaydos;
```
Con esto tenemos la seguridad de que los enteros tendrán el mismo tamaño en
ambas arquitecturas o en cualquier otra que soporte la biblioteca(la mayoría).

```C
8 bits pueden almacenar desde -128 a 127
16 bits pueden almacenar desde -32768 a 32767
32 bits pueden almacenar desde -2147483648 a 2147483647
64 bits pueden almacenar desde -9,223,372,036,854,775,808 a 9,223,372,036,854,775,807
```
Nosotros evitaremos utilizar la declaración común en favor de la declaración 
provista por la biblioteca stdint.h cuando de manipulación de registros se trate.

## El tipo char

El tipo char sirve para guardar caracteres tiene un tamaño de 8 bits, es muy 
común ver programas donde un tipo char guarda el valor de un registro:
```C
//variable de tipo char llamada registro que guarda el valor de DDRB;
char registro = DDRB;
```
Es preferible guardar el valor de los registros en variable enteras de
8 bits, principalmente por motivos de legibilidad, pero también por portabilidad.

### ¿Y las palabras?
En C no existe el tipo string como en otros lenguajes, ciertamente el manejo de
strings o mejor dicho de cadenas de caracteres es más doloroso.

Los strings se representan como arreglos de caracteres (los arreglos se detallan
en otra entrada), los arreglos son espacios conjuntos de memoria, un string por
lo tanto no es más que una serie de caracteres que son declarados de forma 
contigua:

```C
char palabra[] = "Hola mundo";
//otra forma
char * otra = "Adios mundo";
```

El tipo char puede almacenar los caracteres descritos por el código ASCII, 
incluidos los de control, por ejemplo el carácter nulo '\\0', o el tabulador 
'\\t', 
el uso de caracteres suele estar limitado a los dispositivos capaces de 
mostrarlos, como pantallas por ejemplo.```

Algunas personas utilizan el puerto serial para enviar comandos por medio de 
caracteres, sin embargo es un método ineficiente, imagina que queremos enviar 
el número 200 desde un microcontrolador a una PC, podrías usar el puerto serial
y enviar un '2', '0' y '0', cada uno es un carácter y tiene un tamaño de 8 bits,
en total son 3x8 bits, sin embargo el número 200 puede almacenarse sin problemas
en solo 8 bits, ahora supongamos que queremos enviar el número 2,000,000,000, 
enviarlo carácter por carácter requeriría 8x10 bits, 80 bits, cuando 
perfectamente ese número puede ser almacenado en un entero de 32 bits.

## Double y float
El tipo double almacena números con punto decimal, por medio de una representación
de punto flotante. En el lenguaje C moderno para arquitecturas de 64 bits 
float esta en desuso, sin embargo en microcontroladores avr de 8 bits float 
y double se implementan igual, ambas tienen un tamaño de 32 bits.

La aritmetica de punto floate es costosa y se realiza por software, otras 
arquitecturas como los cortex-m4 tienen hardware dedicado para punto flotante,
sin embargo es pronto para preocuparse por el rendimiento, sugiero no 
preocuparse tanto por el comentario anterior.

## Apuntadores
Los apuntadores son la herramienta más poderosa del lenguaje C y también la más
peligrosa, haré una entrada dedicada a Apuntadores.
