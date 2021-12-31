---
title: 'Control de flujo (if, while, for)'
date: Tue, 09 Jul 2019 09:06:50 +0000
draft: false
tags: ['avr', 'booleanos', 'c', 'Programación']
---

# Control de flujo

El control de flujo es el pilar de la programación estructurada, a pesar de 
que el paradigma de la programación estructurada pueda considerarse anticuada 
para algunos, lo cierto es que la solución de los algoritmos siempre requiere
control de flujo; condicionales y repeticiones, antes de adentrarnos en el tema,
revisemos el tipo de dato booleano.

## Tipo de dato Booleano 
Otro tipo de datos básico es el tipo booleano, aunque por raro que parezca en C,
no existía este tipo de dato originalmente, actualmente se requiere incluir
la biblioteca;
```C
#include <stdbool.h>
```
El tipo de dato `bool` puede contener únicamente dos valores, verdadero(true) o
falso(false), la sintaxis es:
```C
#include<stdbool.h>
//Para declarar una variable
bool un_nombre;
//Para definir un valor 
un_nombre = true;
un_nombre = false;
```
Para asumir la carencia del tipo de dato bool, C que cualquier valor diferente
de `0`o `NULL` es verdadero, sin embargo es mejor utilizar explícitamente los 
valores true o false para denotar valores booleanos, por razones de legibilidad.

## Operadores booleanos
Como el nombre sugiere, los operadores booleanos operan sobre tipos de dato 
booleanos y dan como resultado un booleano.

El operador **and** (&&) probablemente es el más utilizado,
```C
true && true //Resulta true
true && false //Resulta false
false && true //Resulta false
false && false //Resulta false
```
El operador **or** (||)
```C
true || true //Resulta true
true || false //Resulta true
false || true //Resulta true
false || false //Resulta false
```
El operador de negación **(!)**
```C
!false //Resulta true
!true //Resulta false
```

## Operadores de comparación

Los operadores de comparación, comparan dos expresiones y evalúan el resultado
como falso o verdadero.

Igualdad:
```C
10 == 10 //true
'a' == 'b' //false
```
Diferencia:
```C
10 != 10 //false
'a' != 'b' //true
```
Menor que:
```C
10 < 11 //true
10 < 10 //false
```
Mayor que:
```C
10 > 11 //false
11 > 10 //true
```
Menor o igual que:
```C
10 <= 11 //true
10 <= 10 //true
```
Mayor o igual que:
```C
10 >= 11 //false
10 >= 10 //true
```
## Condicionales, if, else
El termino control de flujo se deriva de los diagramas de flujo, una técnica
gráfica de diseño de programas estructurados, en ellos existe un bloque
llamado `if`, el cual tiene la capacidad de decidir la siguiente acción en 
base a una condición, en C:
```C
bool condicional;
if(condicional){
	//Ejecuta este código si condicional es verdadero
}
```
el complemento de `if` es `else`:
```C
bool condicional;
if(condicional){
	//Ejecuta este código si condicional es verdadero
}else{
	//Ejecuta este código si condicional es falso
}
```
también es posible hacer comparaciones intermedias:
```C
if(condicion1){
	//Ejecuta este código si condicion1 es verdadera
}else if(condicion2{
	//Ejecuta este código si condicion1 es falso y si condicion2 es verdadera
}else if(condicion3){
	//Ejecuta este código si condicion1 y condicion2 son falsos y condicion3
	//es verdadera
}else{
	//Ejecuta este código si todas las condiciones son falsas
}
```
Debe resaltarse que la evaluación de las condiciones se detiene cuando se
encuentra la primera condición verdadera.
## Ciclos, while, for
La repetición de acciones se logra de varias formas, pero lo más común es 
utilizar los ciclos `while` y `for`.
### While
El ciclo while repite el código dentro de sus llaves mientras una condición sea
verdadera:
``` C
bool condicion = true;
while(condicion){
	//Repite estas lineas de código
	//mientras condición sea verdadera
}
``` 
Simple y fácil de entender, otra forma de hacer repeticiones es por
medio del ciclo for.
### For

