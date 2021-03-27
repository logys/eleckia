---
title: 'Periféricos atmega328p.- Puertos de entrada/salida de propósito general (GPIO)'
date: Thu, 13 Jun 2019 19:18:09 +0000
draft: false
tags: ['atmega328p', 'avr', 'GPIO', 'Programación']
---

Puertos de entrada/salida de propósito general.
-------------------------------------------------

Cando pensamos en programar microcontroladores, lo que queremos es hacer que el 
mundo se mueva, controlar un motor, un relevador, una lampara, altavoz, etc.
Para ello los microcontroladores cuentan con una serie de periféricos.
A grandes rasgos un periférico puede definirse como: una interfaz que permite 
al microcontrolador comunicarse con el exterior, en esta entrada describiremos 
los puertos de entrada/salida de propósito general; GPIOx.

Según la hoja de datos el atmega328p tiene hasta 23 pines programables, cada 
uno con la posibilidad de ser programado individualmente, Arduino Uno tiene 20
pines disponibles, pues 2 están destinados al reloj y el otro está destinado al
pin de reset.

Los pines están agrupados en puertos, en el atmega328 contamos con tres puertos 
disponibles, PORTB, PORTC, PORTD, de manera genérica los llamamos PORTx,  cada 
uno de estos puertos cuentan con tres registros asociados, DDRx, PORTx, PINx, 
donde "x" es el puerto(B, C, D), estos nueve registros nos permiten configurar 
y acceder a los valores de cada uno de los pines pertenecientes al 
microcontrolador.

Los pines en el microcontrolador tienen un nombre del tipo Pxn, donde x es el 
puerto y n es él número de pin, el pin PB0 y el pin PD7, son el pin 0 y el pin 7
del puerto B y del puerto D respectivamente.

Configurar un Pin como Salida.
------------------------------

El acceso a los pines individuales no es posible, estamos limitados a 
a operaciones que involucran 8 bits como mínimo, pues este es el
tamaño del bus de datos y de los registros. Por lo tanto si requerimos escribir 
el estado del pin PB3, necesariamente debemos escribir todo el puerto B,
desde PB0 hasta PB7 (el atmega328p no tiene disponibles todos lo pines PBn).

Suponiendo que requerimos encender un led que esta conectado al pin PB4,
podemos hacer una asignación al registro **DDRB** (Direction Data Register) 
como la siguiente

```
DDRB = (1<<PB4);
//En binario es igual a
DDRB = 0b00010000;
```

Esto efectivamente coloca al pin 4 como salida, sin embargo coloca al resto de 
los pines como entrada, para resolver este problema haremos uso del operador
binario **or**. El operador or compara **bit a bit** y **posición por posición**
con la regla,

```
0 or 0 = 0
1 or 0 = 1
0 or 1 = 1
1 or 1 = 1
```

Por ejemplo, para los números binarios, 100 y 010, el resultado de hacer un or 
bit a bit es

```
bit2 bit1 bit0
   1    0    0
   or   or   or
   0    1    0
--------------
   1    1    0  Resultado
```

Asumiendo que el registro DDRB contiene el valor 0000 0111, es decir los 3 
primeros bits están configurados como salidas, requerimos hacer que el bit 
correspondiente a PB4 sea 1, sin modificar el resto, para ello usamos la 
sintaxis,

```
DDRB = DDRB | (1<<PB4);
```

El símbolo **|**, es el operador binario or, significa: or binario entre DDRB y 
el resultado de la operación `(1<<PB4)`,

```
DDRB = DDRB | (1<<PB4);
//Es igual a 
DDRB = 0b00000111 | 0b00010000;
//Ahora se hacer or, resulta
DDRB = 0b00010111;
//DDRB queda con el bit 5 en 1 y el resto sin modificar
```

Configurar un Pin como Entrada.
-------------------------------

Para poder hacer acciones más complejas requerimos que nuestro microcontrolador 
pueda "sentir" el mundo, los pines de nuestro microcontrolador pueden 
configurarse para que se pueda interpretar el estado de un pin, establecido por 
un elemento externo (como un botón pulsador). Para ello también se utiliza el 
registro DDRx, pero en esta ocasión el pin a ser configurado se establece como 
un cero.

El operador binario and **&**, similar al operador or realiza la operación and bit a bit, con la siguiente regla,

```
0 and 0 = 0
0 and 1 = 0
1 and 0 = 0
1 and 1 = 1
```

Por ejemplo en los siguientes números binarios el resultado es,

```
bit2 bit1 bit0
   1    0    0
       and
   0    1    0
-------------- 
   0    0    0
```

Otro operador (en este caso unario) importante es el complemento a uno **~**, 
cambia el valor de 1 a 0 y de 0 a 1, por ejemplo:

```
1010
  ~
-----
0101
```

Asumiendo que el registro DDRC contiene el número 010 0011, requerimos 
configurar el pin PC0 como entrada, asignar DDRC = 0 no es aceptable (colocaría
todos los pines como entrada), en su lugar usaremos el siguiente código

```
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

De esta forma logramos establecer el pin requerido sin afectar el resto.

Establecer el estado cuando el pin es una salida.
-------------------------------------------------
Cuando un pin es configurado como salida, puede entregar/recibir potencia, 
poquita, pero suficiente para mover al mundo, para ello se requiere establecer 
el estado del pin, asumiendo que tenemos conectado un led al pin PB4 y que dicho
pin esta configurado como salida en el registro DDRB, entonces podemos 
establecer el estado del pin en el registro **PORTB**, donde un cero es nivel 
bajo(0 V) y un uno es nivel alto (5 V).

```
//Led encendido
PORTB = PORTB | (1<<PB4);
//Led apagado 
PORTB = PORTB & ~(1<<PB4);
```
Por supuesto lo mismo aplica para los otros puertos.

Resistencia de Pull up.
-------------------------
¿Qué pasa cuando 
escribimos los registros PORTx cuando DDRx es configurado como entrada?, la 
respuesta es que con un uno se conecta una resistencia interna llamada de 
Pull Up, mientras que con un cero se desconecta, la resistencia de pull up es
de gran utilidad cuando se censan entradas, un pin configurado como entra,
tiene la característica de ser sensible al ruido electromagnético  del 
ambiente, cuando se activa la resistencia de pull up se produce una corriente 
que pasa por el pin, disminuyendo la sensibilidad al ruido y obligando una 
tensión en el pin en nivel alto (5 Volts).

Leer el estado de un pin.
-------------------------

Cuando requerimos leer el estado del pin, utilizamos el registro **PINx**, es un
registro de 8 bits, contiene el estado de cada unos de los pines del puerto,
Para censar al pin, debemos configurarlo como entrada en el registro DDRx.

Para
leer el estado del pin PC0, asumiendo que PC0 esta conectado a 5V y que PC0 
esta configurado como entrada en DDRC (con un cero),


```
//suponiendo que declaramos una variable adecuada llamada
//entrada
entrada = PINC & (1<<PC0);
//Es igual a
entrada = 0bxxxxxxx1 & 0b00000001;
//donde las x pueden ser 0 o 1
//aplicando and bit a bit resulta en
entrada = 0b00000001;
//entrada es exactamente igual a
entrada = 1;
//si PC0 estuvera conectado a tierra, 
//entrada resultaría en
entrada = 0bxxxxxxx0 & 0b00000001;
//aplicando and bit a bit resulta en
entrada =0b00000000;
//o
entrada = 0;
```

Operadores de asignación.
-------------------------

Para evitar verbosidad podemos usar operadores de asignación más complejos, 
por ahora presento dos adicionales, |= y &=, su utilidad es obvia

```
//en lugar de 
PORTx = PORTx | (1<<Pxn);
DDRx = DDRx & ~(1<<Pxn);
//podemos usar
PORTx |= 1<<Pxn;
DDRx &= ~(1<<Pxn);
//ambas expresiones producen resultados
//identicos
```
