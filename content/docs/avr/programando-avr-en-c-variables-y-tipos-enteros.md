---
title: 'Programando Avr en C.- Variables y tipos.- Enteros.'
date: Fri, 14 Jun 2019 02:32:35 +0000
draft: false
tags: ['atmega328p', 'avr', 'c', 'variables']
---

El lenguaje de programación C, es un lenguaje de tipo; tipado estático, esto quiere decir que nosotros debemos indicar el tipo de dato que guarda una variable. ¿Qué es una variable?, puede decirse que una variable es un espacio en memoria que guarda un valor determinado, y ¿qué es un tipo', en C los tipos de variables básicos (mejor llamados primitivos) son,

*   Enteros, almacenan valores enteros, como: 1, 2, -100, 0b11111, 0x32, ...
*   De punto Flotante, cantidades con decimales como: 1.111, 3.141598, -1.4142, ...
*   Caracter. 'a', 'y', ' ', '\\n', ...
*   Boleanos, true, false.

Existen tipos adicionales pero son variantes de las anteriores, como dijimos para declarar una variable debemos especificar su tipo, esto se hace con la siguiente sintaxis,

```
tipo nombre\_de\_variable;
//Una variable entera llamado numero
int numero;
//Una variable de tipo carácter llamada letra
char letra;
//Una variable de tipo punto flotante llamada cantidad
double cantidad;
//Una variable boleana llamada condicion
bool condicion;
```

¿Fácil no?, pero en la vida nada es fácil :D, pero si sencilla, existen modificadores que nos permiten agregar o mejor dicho establecer propiedades a las variables.

#### Signed y Unsigned

Por defecto las variables de tipo entero pueden guardar enteros positivos y negativos, a costa de utilizar un bit para guardar el signo, de manera que si declaramos una variable como unsigned (sin signo), podemos utilizar ese bit para guardar información adicional, pero perdemos la capacidad de almacenar números negativos.

```
//Suponiendo que los enteros son binarios de 8 bits
int numero1; //Puede guardar valores desde -128 a 127
//Por defecto las variables son de tipo signed
signed int numero1; //Puede guardar valores desde -128 a 127
unsigned int numero2; //Puede guardar valores de 0 a 255
```

Al final la cantidad de información guardada no cambia, tan solo cambiamos la forma en como se interpreta, sin embargo el utilizar unsigned generalmente lleva a un camino lleno de dolor, pues es una fuente de errores aritméticos, así que utilizaremos este modificador en situaciones bien definidas. El resto de tipos de variables limitaremos el uso de unsigned a las variables de tipo entero(int).

#### El tamaño de los enteros, stdint.h

En el ejemplo anterior asumimos que un entero guarda números de 8 bits, esto es poco probable, realmente a priori no sabemos que tamaño tiene un entero(el número de bits), el tamaño es determinado por el compilador y es dependiente de la arquitectura, muy probablemente un entero en Arduino sea de 16 bits pero en un cortex-m tal vez sera de 32, y una PC sea de 64 y puede variar entre compiladores en la misma plataforma, sería muy riesgoso asumir un tamaño por defecto, afortunadamente existe una biblioteca que nos permite declarar variables con el tamaño que nosotros indiquemos, basta con incluir la biblioteca,

```
#include<stdint.h>
```

es importante declarar las variables con un tamaño predeterminado cuando de operaciones con registros se trate, con aras de mejorar la portabilidad de nuestro código. ¿Como declaramos un entero con la anterior biblioteca?,

```
#include<stdint.h> // Standard Int

int8\_t numero; //Variable entera de 8 bits
//Podemos cambiar el 8 por 16, 32 o 64 siendo esto el
//numero de bits
uint8\_t numero; //variable entera de 8 bits unsigned
```

de esta forma nos aseguramos que la variable numero sera una variable entera de 8 bits en cualquier ambiente donde nuestro código se compile y ejecute, a manera de resumen

```
8 bits pueden almacenar desde **\-128 a 127**
16 bits pueden almacenar desde **\-32768 a 32767**
32 bits pueden almacenar desde **\-2147483648 a 2147483647**
64 bits pueden almacenar desde −9,223,372,036,854,775,808 a 9,223,372,036,854,775,807
```

nosotros evitaremos utilizar la declaración común en favor de la declaración provista por la biblioteca stdint.h cuando de manipulación de registros se trate.