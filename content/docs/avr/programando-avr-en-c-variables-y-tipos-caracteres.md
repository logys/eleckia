---
title: 'Programando Avr en C.- Variables y tipos, caracteres.'
date: Fri, 14 Jun 2019 16:18:27 +0000
draft: false
tags: ['avr', 'c', 'variables']
---

El tipo de variable **char**, sirve para guardar caracteres, generalmente el tamaño de este tipo es de 8 bits, por ello es común encontrar este tipo de dato en una gran cantidad de programas de 8 bits para almacenar resultados de operaciones con registros, sin embargo es preferible usar uint8\_t, para un código consistente. El tipo char lo usaremos únicamente para lo que es, es decir para guardar un carácter, por ejemplo;

```
char letra = 'A';
char numero = '1';
//Ambas variables guardan caracteres,
uint8\_t resultado = numero + numero;
//Esto dará resultados erróneos, pues estamos sumando dos
//caracteres y nos dos números.
```

Para representar una palabra utilizaremos arreglos de caracteres, un arreglo es un conjunto contiguo de variables, los arreglos serán analizados a detalle en otra entrada.

El tipo char puede almacenar los caracteres descritos por el código ASCII, incluidos los de control, por ejemplo el carácter nulo '\\0', o el tabulador '\\t', el uso de caracteres suele estar limitado a los dispositivos capaces de mostrarlos, como pantallas por ejemplo.

Algunas personas utilizan el puerto serial para enviar comandos por medio de caracteres, sin embargo es un método ineficiente, imagina que queremos enviar el número 200 desde un microcontrolador a una PC, podrías usar el puerto serial y enviar un '2', '0' y '0', cada uno es un carácter y tiene un tamaño de 8 bits, en total son 3x8 bits, sin embargo el número 200 puede almacenarse sin problemas en solo 8 bits, ahora supongamos que queremos enviar el número 2,000,000,000, enviarlo carácter por carácter requeriría 8x10 bits, 80 bits, cuando perfectamente ese número puede ser almacenado en un entero de 32 bits, cuando comentemos el periférico UART esto tomara sentido.