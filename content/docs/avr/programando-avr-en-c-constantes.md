---
title: 'Programando Avr en C.- Constantes'
date: Wed, 10 Jul 2019 13:23:09 +0000
draft: false
tags: ['avr', 'c', 'constantes', 'Programación']
---

Las constantes son de gran utilidad en la programación, a diferencia de las variables, las constantes no pueden cambiar su valor.

Las dos formas más comunes de definir constantes son con la palabra reservada **const** y con la directiva **define**.

#### La palabra const

Agregando la palabra const a la declaración de una variable, esta se convierte en constate, sin embargo también debe asignarle el valor al momento de la declaración;

```
uint8_t const NUMERO = 10; //Declaración de una constante
NUMERO = 1; //error, no se puede reasignar
```

Sin embargo el uso más cotidiano que tiene la palabra const es en los argumentos de funciones,

```
int8_t suma(int8_t const sumando1, int8_t const sumando2);
```

#### Directiva define

Entre otras cosas, la directiva define puede ser usada para definir constantes;

```
#define NUMERO 10
```

cuando el programa se compilar, la palabra NUMERO, se reemplaza por el número 10,

```
#define PRIMERO 5
int8_t = 5 + PRIMERO;
```

al compilar, sera reemplazado por

```
int8_t = 5 + 5;
```

#### Enum

Los enum son un tipo de dato que nos facilitá la creación de colecciones de constantes, su sintaxis es

```
enum NOMBRE{CONSTANTE1 = 0, CONSTANTE2, CONSTANTE3 = 5, CONSTANTE4};
```

de esta forma CONSTANTE1 es una variable de tipo enum NOMBRE y contiene el valor de 0, CONSTANTE2 contiene el valor 1 y CONSTANTE3 tiene el valor de 5 y CONSTANTE4 tiene el valor de 6.

```
enum numeros{CERO, UNO, DOS};
a = CERO + UNO + DOS; //a contiene el número 3
```

#### ¿Por qué usar constantes?

Hay dos conceptos al utilizar constantes, ser constante y querer ser constante.

Ser constante se refiere a declarar una constante explícitamente por ejemplo el número PI, SQRT2, etc.

Querer ser constante, se refiere a evitar que las variables cambien por accidente su valor, por ejemplo las funciones suele recibir parámetros constantes, se busca evitar que los parámetros cambien su valor, esto tomara sentido cuando comentemos los apuntadores en entradas posteriores.

Muchos lenguajes de programación nuevos, definen a las variables como constantes (inmutable) y es el usuario quien explícitamente declara una variable como variable (mutable), la razón principal como se menciona es reducir la posibilidad de añadir errores de programación.