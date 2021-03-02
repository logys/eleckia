---
title: 'Programando Avr en C.- Apuntadores, punteros, pointers III. Apuntadores Dobles.'
date: Wed, 20 May 2020 04:19:41 +0000
draft: false
tags: ['avr', 'c', 'Programación']
---

Los apuntadores suenan bastante complejos, nuestro cerebro amplifica su complejidad, pero al igual que cualquier otra variable su funcionamiento es simple,

```
tipo ** nombre_apuntador;
```

a que me refiero con apuntador doble, a un apuntador que guarda direcciones de otros apuntadores de su mismo tipo,

```
char caracter1 = 'a';
char caracter2 = 'b';
char \* letra = &caracter1;
char \*\* apuntador\_doble = &letra;
\*apuntador\_doble = &caracter2;
//ahora letra apunta a la dirección de caracter2,
//\*letra es 'b'
```

el propósito evidente de los apuntadores dobles, es cambiar el valor a donde apuntan apuntadores simples,

```
int \* pointer1=NULL;
int \* pointer2=NULL;
void updatePointers(int \*\* a, int \*\* b)
{
        \*a = direction\_1;
        \*b = direction\_2;
}
//ahora los apuntadores apuntan a las 
//direcciones en direction\_n
```

Esta aplicación es muy sencilla, la usaremos con frecuencia.