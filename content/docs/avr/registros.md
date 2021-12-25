---
title: "Registros"
date: 2021-12-25T11:04:48-06:00
draft: false
---
# Registros y Acceso

## Registros
¿Qué son los registros?, _un registro es un espacio en memoria que guarda
información_, tan simple como eso. En el mundo de los microcontroladores
un registro guarda la información que define el comportamiento del 
microcontrolador.

Cada periférico cuenta con varios registros, listos para ser modificados y 
leídos para adaptarlos a la solución de un problema en particular.

En la arquitectura avr de 8 bits, los registros son de 8 bits, algunos
registros se presentan como de 16 bits, pero en realidad son 2 registros
de 8 bits, el tamaño lo indica el bus de datos que en avr es de 8 bits,
en otras arquitecturas donde el bus es
más grande los registros son de otros tamaños.

La programación de sistemas embebidos mucho tiene que ver con modificar 
registros, esta es la principal diferencia aunque no la única con respecto a
programa en software de alto nivel.

## Acceso
Entendido lo que es un registro, veamos como modificarlo.

Ya hemos utilizado registros, por ejemplo _DDRB_, data direction register del 
puerto B, establece a los pines como entrada o salida:
```C
//Establece el pin 5 del puerto B como salida y el resto como entradas
DDRB = 0b00100000;
```
sin embargo la sintaxis _0bxxxx_ no es estándar, únicamente esta disponible
via avr-libc, que es la biblioteca principal utilizada por avr-gcc, lo que 
implica que nuestro código no sea portable si usamos esta sintaxis.

Otra forma de abordar la asignación es por medio de la notación hexadecimal,
```C
//0b0010 0000;
//  2    0	
DDRB = 0x20;
```
El número binario se divide en dos cuartetos y cada uno representa un 
número en hexadecimal, esto es cierto debido a las propiedades de las
bases binarias y hexadecimal, es una forma estandarizada de asignación
y es ampliamente utilizada, operaciones con datos de 8 bits son fáciles
pero cuando los registros son de 32 bits por ejemplo, las conversiones
son más complicadas y se suelen cometer errores, existe otra forma de
abordar el problema

### bitwise Corrimiento a la izquierda

El operador __<<__ corrimiento a la izquierda es un gran aliado para 
resolver este problema:
```C
//0b0010 0000;
DDRB = 1<<5;
```
Recorre el número a la izquierda del operador el número de veces indicado a
la derecha del operador, en este caso recorre el número 1, 5 veces a la izquierda,
el corrimiento se hace en la forma binaria del número a la izquierda:
```C
DDRB = 3<<1;
//Daria como resultado 0b110 (6 en decimal) no 30 en decimal

DDRB = 1<<PB5; //Lo que nos intersa es que el 1 quede en la posición PB5
```
Sin embargo el problema de modificar el resto de pines del registro persiste,
veamos la solución a este problema.

### Establecer un bit
El operado binario or __|__ es perfecto para solucionar el problema,
el operador |, opera en dos números binarios con la regla:
```Bash
0 or 0 = 0
1 or 0 = 1
0 or 1 = 1
1 or 1 = 1
```
Por ejemplo, para los números binarios, 100 y 010, el resultado de hacer un or 
bit a bit es

```Bash
bit2 bit1 bit0
   1    0    0
   or   or   or
   0    1    0
--------------
   1    1    0  Resultado
```

Asumiendo que el registro DDRB contiene el valor 0000 0111, es decir los 3 
primeros bits están configurados como salidas, requerimos hacer que el bit 
correspondiente a PB5 sea 1, sin modificar el resto, para ello usamos la 
sintaxis,
```C
DDRB = DDRB | (1<<PB5);
//Es igual a 
DDRB = 0b00000111 | 0b00100000;
//despues de hacer or, resulta
DDRB = 0b00100111;
//DDRB queda con el bit PB5 en 1 y el resto sin modificar
```
Una notación más compacta y equivalente es:
```C
DDRB |= 1<<PB5;
```
También es posible encadenar varios pines:
```C
DDRB |= 1<<PB5 | 1<<PB0 | 1<<PB3;
```

### Limpiar un bit
Con limpiar un bit me refiero a ponerlo en cero, para ello el operador 
binario and __&__ es el indicado, el operado & opera con dos números
binarios con la regla:
```bash
0 and 0 = 0
0 and 1 = 0
1 and 0 = 0
1 and 1 = 1
```
Por ejemplo en los siguientes números binarios el resultado es,
```bash
bit2 bit1 bit0
   1    0    0
       and
   0    1    0
-------------- 
   0    0    0 Resultado
```
Otro operador (en este caso unario) importante es el complemento a uno **~**, 
cambia el valor de 1 a 0 y de 0 a 1, por ejemplo:
```Bash
1010
  ~
-----
0101 Resultado
```

Asumiendo que el registro DDRC contiene el número 0b0010 0011, requerimos 
configurar el pin PC0 como entrada, asignar DDRC = 0 no es aceptable (colocaría
todos los pines como entrada), en su lugar usaremos el siguiente código
```C
DDRC = DDRC & ~(1<<PC0);
//Primero se realiza (1<<PC0), resulta
DDRC = DDRC & ~0b00000001;
//Ahora se aplica el operador complemento, resulta
DDRC = DDRC & 0b11111110;
//Operador And bit a bit,
DDRC = 0b00100011 & 0b11111110;
//Resulta
DDRC = 0b00100010;
```
De esta forma logramos establecer el pin requerido sin afectar el resto. Por
supuesto también se pueden limpiar varios pines:
```C
DDRC &= ~(1<<PC0 | 1<<PC1 | 1<<PC5);
```
