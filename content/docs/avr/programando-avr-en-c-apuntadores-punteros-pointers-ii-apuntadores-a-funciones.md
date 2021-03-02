---
title: 'Programando Avr en C.- Apuntadores, punteros, pointers II. Apuntadores a funciones.'
date: Wed, 20 May 2020 03:44:43 +0000
draft: false
tags: ['avr', 'c', 'Programación']
---

Como ya he dicho un apuntador es una variable que apunta a direcciones de memoria, también hemos mencionado que los apuntadores deben ser especificados sobre el tipo, cuando las funciones se compilan se les asigna una dirección en memoria, ¿existirán apuntadores a funciones?, la respuesta es afirmativa, la sintaxis es un poco rara,

```
tipo (*nombre_apuntador)(argumento1, argumento2, ..., argumento_n);
```

al igual que los apuntadores comunes, los apuntadores a funciones deben ser especificados con el tipo de dato que regresan, entre parentesis se coloca el operador \* seguido del nombre del apuntador, finalmente se especifican los parámetros)

```
int (\*operacion)(int a, int b);

int suma(int a, int b)
{
        return a + b;
}
int resta(int a, int b)
{
        return a - b;
}

int a = 5;
int b = 10;

if(condicion)
        operacion = suma;
else
        operacion = resta;

int c = operacion(a , b);

/\* Sí condición es verdadera c contendrá 15 de
 \* lo contrario c contendrá -5
 \*/

```

gracias a los apuntadores a funciones, también es posible pasar funciones como argumentos de otras funciones,

```
int operadorBinario(int (\*operation)(int a, int b), int c, int d)
{
        return operation(c,d);
}

int resultado = operadorBinario(suma, 5, 10);
//resultado es igual a 15
resultado = operadorBinario(resta, 5, 10);
//resultado = -5;
```

Esta característica nos ayudara a mejorar la calidad del código.