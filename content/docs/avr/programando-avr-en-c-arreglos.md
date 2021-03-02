---
title: 'Programando Avr en C. Arreglos.'
date: Wed, 01 Jul 2020 23:39:42 +0000
draft: false
tags: ['avr', 'c', 'Programación']
---

Los arreglos o arrays son elementos que permiten hacer colecciones de datos del tipo indicado, 
su uso es amplio en prácticamente todos los lenguajes de programación, en C los arreglos se 
declaran con la siguiente sintaxis,

```
tipo_dato nombre_array[TAMAÑO];
```

Por ejemplo para declarar un arreglo de 5 enteros llamado horas:

```
int horas[5];
```

Para declarar un arreglo de 3 boleanos llamado respuestas:

```
#include<stdbool.h>

bool respuestas[3];
```

Los arreglos pueden ser de cualquier tipo de dato. El número entre corchetes (paréntesis cuadrados)
indica la cantidad de elementos que puede almacenar, en el caso del arreglo horas, este puede 
almacenar hasta 5 enteros, la forma de asignar los valores es la siguiente:

```
int horas[5] = {1, 2, 1, 2, 3};

//o también

int horas[5];
horas[0] = 1;
horas[1] = 2;
horas[2] = 1;
horas[3] = 2;
horas[4] = 3;
```

Ambas asignaciones resultaran en los mismos enteros para el arreglo, nótese que cuando asignamos los 
valores, la primera posición es cero y no uno, esto implica que la última posición sea cuatro y no 
cinco, si asignáramos `horas[5] = 4;` obtendríamos un error en tiempo de ejecución, este error es muy 
diferente de el error en tiempo de compilación, de hecho el programa compilara sin error, pero si 
se intenta ejecutar dentro del microcontrolador la ejecución sera errática, probablemente el programa 
se reinicie al tratar de ejecutar la instrucción o tal vez se detenga en ese instante, o tal vez no 
pase nada, depende de la forma en que se organiza la memoria en el micro, por lo tanto hay que tener 
cuidado en no rebasar el indice, el máximo es igual a el tamaño menos uno.

Arreglos y apuntadores.
-----------------------

Los arreglos y apuntadores tienen una relación importante, el nombre de un arreglo es un apuntador 
que apunta a la dirección del primer elemento o elemento cero,

```
int numeros[2] = {1, 4};
int a = *numeros + numeros[1];
//a contendra el valor de 5
```

A pesar de esta equivalencia, prefiero utilizar los corchetes cuando de arreglos se trata, esto me
evita la sobrecargar de conceptos en la cabeza y me permite diferenciar claramente el propósito de
 un arreglo y el de un apuntador, con el arreglo quiero manejar una colección de datos, mientras que 
con los apuntadores quiero manejar el acceso a objetos individuales.
