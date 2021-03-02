---
title: 'Programando Avr en C.- Tipo de dato Booleano, operadores booleanos y de comparación.'
date: Tue, 09 Jul 2019 09:06:50 +0000
draft: false
tags: ['avr', 'booleanos', 'c', 'Programación']
---

#### Tipo de dato Booleano

Otro tipo de datos básico es el tipo booleano, este tipo de dato puede contener unicamente dos valores, verdadero(true) o Falso(false), para tener acceso a este tipo de dato debemos incluir la biblioteca **stdbool.h**, la sintaxis es:

```
#include<stdbool.h>
//Para declarar una variable
bool un\_nombre;
//Para definir un valor 
un\_nombre = true;
un\_nombre = false;
```

Es común asumir en C que cualquier valor diferente de cero o NULL es verdadero, sin embargo pienso que es mejor utilizar explícitamente los valores true o false para denotar valores booleanos, por razones de legibilidad.

#### Operadores booleanos

Como el nombre sugiere, los operadores booleanos operan sobre tipos de dato booleanos. El operador **and**(&&) probablemente es el más utilizado,

```
a && b
```

el resultado de la operación previa dará verdadero solo si ambos, **a** y **b** son verdaderos, resulta falso de lo contrario. La regla es

```
false and false  //es igual a false
true and false  //es igual a false
false and true  //es igual a false
true and true //es igual a true
```

El operador **or**(||)

```
a || b
```

el resultado de la operación previa dará verdadero si **a** o **b**, o ambos son verdaderos, resulta falso de lo contrario. La regla or es

```
false or false //es igual a false
true or false //es igual a true
false or true //es igual a true
true or true //es igual a true
```

El operador de negación **(!)**

```
!a
```

resulta verdadero si **a** es falso, resulta falso si **a** es verdadero.

```
!false //es igual a true
!true //es igual a false
```

#### Operadores de comparación

Los operadores de comparación, comparan dos expresiones y evalúan el resultado como falso o verdadero, por ejemplo el operador de igualdad (==)

```
a = 10; //entero
b = 10; //entero
c = (a == b); //boleano verdadero
d = ((a+1) == b); //¿11 es igual a 10? falso
```

la variable **c** contendrá verdadero, mientras que **d** sera falso pues 11 no es igual a 10.

otros operadores de comparación se resumen a continuación

\==

Igual que

!=

Diferente de

<

menor que

\>

mayor que

<=

menor o igual que

\>=

mayor o igual que