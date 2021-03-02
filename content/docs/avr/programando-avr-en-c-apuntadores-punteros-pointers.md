---
title: 'Programando Avr en C.- Apuntadores, punteros, pointers I.'
date: Sat, 16 May 2020 21:27:18 +0000
draft: false
tags: ['apuntadores', 'avr', 'c', 'Programación']
---

Los apuntadores (pienso que esta es la traducción más acertada), también conocidos como punteros o pointers en ingles, es un tipo de dato característico de lenguajes de bajo nivel y algunos de nivel medio, C por supuesto cuenta con tal tipo de dato.

Un apuntador es una variable cuyo tipo de dato almacena direcciones de memoria, tan simple como eso. Aunque una dirección de memoria puede ser interpretada como un entero, es mejor pensar que una dirección de memoria es un tipo de dato diferente, un tipo de dato de tipo apuntador.

Los apuntadores son temidos y exaltados, por buenas razones, el problema es que los apuntadores sirven para gran cantidad de aplicaciones y da la impresión de que debes conocerlas todas, por supuesto no es así, se puede usar apuntadores de forma efectiva sin tener todo el bagaje técnico alrededor, e ir aprendiendo gradualmente los pormenores. El secreto esta en auto limitarse y ser disciplinado.

la sintaxis para declarar un apuntador es la siguiente

```
tipo_a_donde_apunta * nombre_apuntador;
```

un apuntador que apunta a una variable de tipo entero es diferente a un apuntador que apunta a una variable de tipo carácter,

```
char a;
int b;

int * apuntador1; // solo puede apuntar hacia b, apuntar hacia a implica un error
```

#### Operador addressOff **&**

Para almacenar la dirección de una variable primero debemos obtener su dirección, para ello contamos con el operador **&**,

```
int b = 0;
int * apuntador = &b;
//apuntador ahora contiene la dirección de memoria de b
```

#### Operador de indirección **\***

¿Para que se usa un apuntador?, como mencione las aplicaciones son amplias, pero la base es la misma, se trata de acceder al contenido guardado en una dirección de memoria de manera indirecta. Una vez establecido un apuntador, usamos el operador de indirección para acceder al contenido a donde apunta.

```
int b = 0;
int * apuntador = &b;
*apuntador = 10; //ahora b contiene el valor 10 y no 0.
```

Una de las aplicaciones más importantes es el paso de parámetros a funciones, cuya intensión sea modificar tales parámetros, por ejemplo

```
int a = 5;
int b= 3;
//Se pasan dos apuntadores
intercambiar(&a, &b);
//pasar &a, es equivalente a crear un apuntador 
//int *apuntador = &a y escribir intercambiar(apuntador, &b);

//ahora a = 3 y b = 5;

void intercambiar(int * v1, int * v2)
{
        int tmp = *v1;//tmp = valor guardado en la 
        //dirección a donde apunta v1
        *v1 = *v2;
        *v2 = tmp;
}
```

Usaremos ampliamente la forma de la función intercambiar, por ahora es suficiente con este conocimiento sobre apuntadores, por supuesto más adelante veremos otras aplicaciones.