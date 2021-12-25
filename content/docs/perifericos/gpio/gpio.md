---
title: 'Puertos de entrada/salida de propósito general (GPIO)'
date: Thu, 13 Jun 2019 19:18:09 +0000
draft: false
tags: ['atmega328p', 'avr', 'GPIO']
---

# Puertos de entrada/salida de propósito general.

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
y acceder a los valores de cada uno de los pines del microcontrolador.

Los pines en el microcontrolador tienen un nombre del tipo Pxn, donde x es el 
puerto y n es él número de pin, el pin PB0 y el pin PD7, son el pin 0 y el pin 7
del puerto B y del puerto D respectivamente.

## Configurar un Pin como Salida.
La primera necesidad que se resuelve al programar un microcontrolador es
encender y apagar un dispositivo externo, todos los pines que corresponden a
alguno de los puertos gpio pueden establecerse como salidas, establecer un pin
como salida implica dotar a ese pin de la capacidad de entregar o recibir 
corriente eléctrica, cada unos de los pines del atmega328p puede entregar un 
máximo de 20[mA] y un total de 200[mA] entre todos, esto es bastante aunque
no lo parezca, la mayoría de las veces tomaremos alrededor de 1[mA] de cada
pin o menos, el objetivo de un microcontrolador es controlar, no dotar de
potencia a los dispositivos, cuando se requiere más potencia como para manejar
un motor grande, se recurre a electronica adicional dedicada a ese rubro.

Suponiendo que requerimos encender un led (un led requiere alrededor de 10[mA])
que está conectado al pin PB4,
Debemos indicarle al registro DDRB que requerimos el pin PB4 como salida:
```C
DDRB |= 1<<PB4;
```
[Para mayores detalles sobre como establecer un bit](../../../avr/registros)

### Establecer el estado cuando el pin es configurado como salida.
Cuando un pin es configurado como salida, puede entregar/recibir potencia, 
poquita, pero suficiente para mover al mundo, para ello se requiere establecer 
el estado del pin, asumiendo que tenemos conectado un led al pin PB4 y que dicho
pin esta configurado como salida en el registro DDRB, entonces podemos 
establecer el estado del pin en el registro **PORTB**, donde un cero es nivel 
bajo(0 V) y un uno es nivel alto (5 V).

```C
//Led encendido
PORTB |= 1<<PB4;
//Led apagado 
PORTB &= ~(1<<PB4);
```
### Toggle
Algo muy curioso es que sin un pin esta configurado como salida y se escribe 
al registro correspondiente de entrada, el pin hace un toggle o un xor,
suponiendo que el pin PD1 se encuentre configurado como salida:
```C
PINB |= 1<<PD1; //El pin se pone en estado alto si estaba en bajo
PINB |= 1<<PD1; //El pin se pone en estado bajo si estaba en alto
```
Este es un comportamiento típico y raro pero útil de los avr de 8 bits.

## Configurar un Pin como Entrada.

Para poder hacer acciones más complejas requerimos que nuestro microcontrolador 
pueda "sentir" al mundo, los pines de nuestro microcontrolador pueden 
configurarse para que se pueda interpretar el estado de un pin, establecido por 
un elemento externo (como un botón pulsador). Para ello también se utiliza el 
registro DDRx, pero en esta ocasión el pin a ser configurado se establece con
un cero.
```C
DDRC &= ~(1<<PC0); //Pin PC0 como entrada
```
Establecer un pin como entrada implica que el pin es incapaz de recibir y 
entregar corriente, esto es muy bueno pues el dispositivo que esta enviando
los pulsos requiere un gasto de energía mínimo, por otro lado el pin
se vuelve extremadamente sensible a cualquier impulso incluso a las ondas
electromagnéticas generadas por diferentes fuentes, como lamparas o televisores.
Se dice que el pin del microcontrolador esta configurado en modo de alta 
impedancia. La mayoría de los microcontroladores cuentan con una resistencia
de pull up que resuelve este problema.
 
### Resistencia de Pull up.
Para evitar que el pin configurado como entrada se vuelva loco con solo mirarlo,
basta con habilitar una resistencia de pull up en el pin, la resistencia se puede
poner de forma externa o habilitar la resistencia interna que el microcontrolador
trae para este propósito, la resistencia de pull up no es más que una resistencia
conectada entre el pin y Vcc(5[V] en condiciones normales), de tal forma que 
el pin se "ancla" al voltaje de Vcc. La resistencia de pull up tiene un valor
grande no se requiere de mucha potencia para "desanclar" el pin de Vcc por parte
del dispositivo que envía la señal. 

Para habilitar la resistencia interna de pull up, asumiendo que el pin PC0
se encuentra configurado como entrada:
```C
PORTC |= 1<<PC0; //PC0 con resistencia de pullUp
```
Desafortunadamente el atmega328p no cuenta con resistencia de pull down.

### Leer el estado de un pin.

Cuando requerimos leer el estado del pin, utilizamos el registro **PINx**,
contiene el estado de cada unos de los pines del puerto,
Para censar al pin, debemos configurarlo como entrada en el registro DDRx.

Para
leer el estado del pin PC3, asumiendo que PC3 esta conectado a 5V y que PC3
esta configurado como entrada en DDRC (con un cero),
```C
uint8_t entrada = PINC & (1<<PC3);
//Es igual a
entrada = 0bxxxx1xxx & 0b00001000;
//donde las x pueden ser 0 o 1
//aplicando and bit a bit resulta en
entrada = 0b00001000;
//entrada es exactamente igual a
entrada = 8;
//si PC0 estuvera conectado a tierra, 
//entrada resultaría en
entrada = 0bxxxx0xxx & 0b00001000;
//aplicando and bit a bit resulta en
entrada =0b00000000;
//o
entrada = 0;
```
