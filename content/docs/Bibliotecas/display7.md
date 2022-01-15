---
title: 'Api Display Siete Segmentos'
date: Wed, 19 Jun 2019 04:37:56 +0000
draft: false
tags: ['gpio', '7segmentos', 'avr', 'Programación']
---
# Api para un display de siete segmentos

Escribamos una biblioteca/api sencilla que nos permita configurar un número en
un display de 7 segmentos, el circuito es el siguiente,

![Circuito](/eleckia/img/circuito7seg.png)

## Conexión Arduino-Display

Contamos con un display de cátodo común por lo tanto cada segmento se enciende 
con un voltaje alto en el pin correspondiente, es decir para encender el segmento
"A" debemos configurar el pin digital **D2** en alto. Evitamos la conexión en
los pines D0 y D1, debido a que estos están conectados al convertidor serial-usb,
y pueden provocar problemas para subir el código a la tarjeta.

Como primer paso debemos incluir la biblioteca "avr/io.h", para tener acceso a 
los puertos de GPIOx de entrada/salida, cuyos registros asociados son DDRx, 
PORTx, PINx.

```C
#include<avr/io.h>
```

Ahora declaramos la función principal, recordemos que la función principal es
llamada automáticamente cuando el programa es ejecutado,

```C
int main(void)
{
        return 0;
}
```

Dentro de las llaves del main configuramos los pines adecuados como salidas,
para hacerlo requerimos
saber a que pines del microcontrolador corresponden los pines del Arduino,

![Correspondencia de pines](/eleckia/img/328pToArduino.png)
fuente:(http://www.chicoree.fr/w/Arduino_sur_ATmega328P)

En la anterior imagen observamos la correspondencia entre los pines del Arduino 
y los del microcontrolador, por ejemplo el pin digital D2 es en realidad el pin
PD2 del atmega328p, por ejemplo el pin digital D8 es en realidad el pin PB0 del
atmega328p. Configuramos el registro DDRB y DDRD como salidas,

```C
DDRB |= 1<<PB0;
DDRD |= 1<<PD2 | 1<<PD3 | 1<<PD4 | 1<<PD5 | 1<<PD6 | 1<<PD7;
```

Ahora solo encendemos los leds, poniendo en alto los pines requeridos, por 
ejemplo para el número cinco, encendemos los segmentos a,c,d,f,g.

![Cinco](/eleckia/img/7segdia5.png)

Para ello escribimos a los puertos PORTB y PORTD los pines indicados

```C
PORTB |= 1<<PB0;
PORTD |= 1<<PD2 | 1<<PD4 | 1<<PD5 | 1<<PD7;
```

El código completo luce como el siguiente,

```C
#include<avr/io.h>

int main(void)
{
       DDRB |=  1<<PB0;
       DDRD |=  1<<PD2 | 1<<PD3 | 1<<PD4 | 1<<PD5 | 1<<PD6 | 1<<PD7;
       PORTB |=  1<<PB0 ;
       PORTD |=  1<<PD2 | 1<<PD4 | 1<<PD5 | 1<<PD7;
       return 0;
}
```
Compilamos y subimos.

![Ejecutando](/eleckia/img/7seg5.png)

El "estilo" en que hemos escrito nuestro programa es coloquialmente llamado 
hardcoding, debido a que nosotros especificamos tanto los valores como el flujo,
usualmente lo que queremos es que el programa calcule los valores y elija el 
flujo, imaginemos que ahora requerimos una secuencia de números en serie,
comenzado con 1 y terminando en 9 y repetir esta secuencia 1000 veces, seria un
poco tedioso cambiar uno por uno los estados de los segmentos, para evitar este 
trabajo nos apoyaremos en funciones y sentencias de flujo.

## Biblioteca

Nuestro anterior programa sirve para dos cosas, para nada y para la basura, no hay
ninguna diferencia con respecto a conectar los pines del display directamente a
Vcc, requerimos funcionalidad, que el microcontrolador haga lo adecuado para mostrar
cualquier número indicado, con una interfaz del tipo


```C
display7_showNumber(3); //Muestra el número 3 en el display
```

De esta forma podemos realizar una secuencia de números

```C
int main(void)
{
  while(1){
    display7_showNumber(0);
    //delay
    display7_showNumber(1);
    //delay
    .
    .
    .
    display7_showNumber(9);
    //delay     
  }
}
```

El display mostrara los números del 0 al 9 en orden ascendente, nuestro objetivo
entonces es definir la función `display7_showNumber(short const number)`.

Aunque en este problema sencillo podemos programar la solución de forma 
monolítica(un solo fragmento de código), es de utilidad desarrollarlo en forma 
modular, como en la siguiente figura

![dependencia](/eleckia/img/7segdep.png)

De tal forma que podemos utilizarla dentro de nuestra aplicación con un simple 
include, pensar en forma modular tiene grandes ventajas y conforme crece el 
proyecto se vuelve la única forma sostenible de escribir código,

Para nuestro propósito un módulo es un ente de software que aglutina las 
características:
- Responsabilidad única, solo realiza una tarea.
- Débilmente acoplado, dos módulos están débilmente acoplados si ambos desconocen 
los detalles de implementación del otro.
- Altamente cohesivo, un módulo es altamente cohesivo si utiliza la totalidad
de sus miembros, para realizar sus tareas.

La definición de módulo es un poco volátil, cada autor lo llama diferente, 
algunos indican que una clase es equivalente a un módulo, algunos indican que
un conjunto de clases es un módulo, otros les llaman entidades, componentes
o paquetes. Para
este propósito cuando digo módulo me refiero al conjunto de un fichero .h con 
su o sus respectivos ficheros .c. 

Una bonita interfaz para nuestro modulo puede ser

```C
void display7_init(void);
void display7_showNumber(int8_t number);
```

Elegir buenos nombres es una parte fundamental para que nuestro código sea de 
alta calidad, estoy seguro que al leer las funciones anteriores de inmediato
formaste una idea de lo que hacen y a que pertenecen. 

Ni tardos ni perezosos comenzamos con la creación del módulo, declaramos la 
interfaz del módulo en un fichero display7.h:
```C
#ifndef DISPLAY7_H
#define DISPLAY7_H

#include <stdint.h>

void display7_init(void);
void display7_showNumber(int8_t number);

#endif// DISPLAY7_H
```
Comenzaré con la función _display7\_showNumber_, pues actualmente no tengo
idea de que requiero inicializar en la otra función, rápidamente defino la 
función dentro de un fichero display7.c:
```C
#include "display7.h"

void display7_showNumber(int8_t number)
{
}
```
Enfocándonos en la función requerimos que _number_ sea traducida a un formato
donde los pines puedan ser establecidos, supongamos que siempre _number = 1_,
bastaría con:
```C
#include<avr/io.h>

void display7_showNumber(int8_t number)
{
	PORTD |= 1<<PD3 | 1<<PD4;
}
```
Ahora consideremos el caso en que _number_ puede ser uno o siete,
```C
void display7_showNumber(int8_t number)
{
	if(number == 1)
		PORTD |= 1<<PD3 | 1<<PD4;
	else if(number == 7)
		PORTD |= 1<<PD3 | 1<<PD4 | 1<<PD2;
}
```
Parece la dirección correcta, sin embargo, si primero se muestra siete, ya no
será posible mostrar un uno, el problema habla y nos está diciendo que 
requerimos implementar la funcionalidad de establecer los pines y de borrar
los pines complementarios. Se proponen dos funciones adicionales:
```C
static void setPins(uint8_t pins);
static void clearPins(uint8_t pins);

void display7_showNumber(int8_t number)
{
	if(number == 1){
		setPins(1<<PD3 | 1<<PD4);
		clearPins(1<<PD3 | 1<<PD4);
	}
	else if(number == 7){
		setPins(1<<PD3 | 1<<PD4 | 1<<PD2);
		clearPins(1<<PD3 | 1<<PD4 | 1<<PD2);
	}
}

static void setPins(uint8_t pins)
{
	PORTD |= pins;
}

static void clearPins(uint8_t pins)
{
	PORTD &= ~pins;
}
```
El código tiene dos problemas graves, el primero es que comienza a verse
algo de repetición, este es un dogma de la programación:

_La repetición de ideas, es el origen de todos los males._

El segundo es que no todos los pines están en el puerto D, hay uno en el B.

Solucionemos rápidamente el primer problema: 
```C
void display7_showNumber(int8_t number)
{
	uint8_t pins;
	if(number == 1)
		pins = 1<<PD3 | 1<<PD4;
	else if(number == 7)
		pins = 1<<PD3 | 1<<PD4 | 1<<PD2;
	setPins(pins);
	clearPins(pins);
}
```
Lo anterior tiene un nombre, se llama refactoring, algunos le dicen refactorizar
pero evidentemente es una palabra que no existe, aunque tu deberías llamarle como
te de la gana, el refactoring tiene sus
reglas y directrices, es un tópico relativamente avanzado que tocaremos en otra
entrada, para quien desee adelantarse, tiene disponible 
_Martin Fowler_:_Refactoring_.

El segundo problema no es tan sencillo de atacar, no hay 
forma de diferencia PB0 de PD0, ambos son un cero, para esta 
configuración de pines tenemos suerte, solo hay PB0 y podemos trabajar 
con una condicional sobre el, pero deberíamos buscar una forma más generalizada:
```C
static void setPins(uint8_t pins)
{
	PORTD |= pins;
	if(pins & (1<<PB0))
		PORTB |= 1<<PB0;
}

static void clearPins(uint8_t pins)
{
	PORTD &= ~pins;
	if(pins & (1<<PB0))
		PORTB &= ~(1<<PB0);
}
```
No me da buenas sensaciones pero funciona, más adelante lo intentaremos de 
nuevo, por ahora agreguemos un caso más, cuando `number` puede ser también 
cuatro:
```C
void display7_showNumber(int8_t number)
{
	uint8_t pins;
	if(number == 1)
		pins = 1<<PD3 | 1<<PD4;
	else if(number == 7)
		pins = 1<<PD3 | 1<<PD4 | 1<<PD2;
	else if(number == 4)
		pins = 1<<PD3 | 1<<PD4 | 1<<PB0 | 1<<PD7;
	setPins(pins);
	clearPins(pins);
}
```
Esos if's se ven horribles y faltan más, ¿habrá alguna forma de quitarlos?, 
actualmente representamos los segmentos como sigue:
```C
0b76543210
  FEDCBA0G
```
Si sabemos que a cada valor de `number` le corresponde una y solo una 
representación de segmentos, entonces ¿no podríamos guardar los "patrones"
que le corresponde a cada valor de `number`?, por supuesto, a eso se le 
conoce como lookup table o tabla de consulta, hay muchas formas de
implementarla pero la más sencilla es por medio de un arreglo:
```C
const uint8_t segments_table[] ={
	1<<PD2 | 1<<PD3 | 1<<PD4 | 1<<PD5 | 1<<PD6 | 1<<PD7, //0
	1<<PD3 | 1<<PD4, //1
	1<<PD2 | 1<<PD3 | 1<<PD5 | 1<<PD6 | 1<<PB0, //2
	1<<PD2 | 1<<PD3 | 1<<PD4 | 1<<PD5 | 1<<PB0, //3
	.
	.
	.
};

void display7_showNumber(int8_t number)
{
	uint8_t pins = segments_table[number];
	setPins(pins);
	clearPins(pins);
}
```
Ahora si, se ve simple y claro. ¿Qué pasaría si el usuario introduce un
número más grande que el tamaño del arreglo?, claramente habría un error,
debemos protegernos de valores inadecuados:

```C
void display7_showNumber(int8_t number)
{
	if(number < 0 || number > 9)
		return ;
	uint8_t pins = segments_table[number];
	setPins(pins);
	clearPins(pins);
}
```
Si se introduce un número incorrecto, la función simplemente lo ignora.

Ya sabemos que lo único que requerimos inicializar, son los pines en la 
función `display7_init`
```C
void display7_init(void)
{
	DDRD |= 1<<PD2 | 1<<PD3 | 1<<PD4 | 1<<PD5 | 1<<PD6 | 1<<PD7;
	DDRB |= 1<<PB0;
}
```
Solo nos queda incluir la biblioteca dentro del main:
```C
#include "display7.h"
#define F_CPU 16000000UL
#include <util/delay.h>

int main(void)
{
	display7_init();
	int i = 0;
	while(1){
		display7_showNumber(i);
		_delay_ms(1000);
		i++;
		if(i > 9)
			i = 0;
	}
}
```
Compilando el programa con:
```Bash
$ avr-gcc -mmcu=atmega328p -Wall -Os display7.c main.c
```
Obtengo los siguientes resultados:
```bash
$ avr-size --mcu=atmega328p --format=avr a.out
AVR Memory Usage
----------------
Device: atmega328p

Program:     260 bytes (0.8% Full)
(.text + .data + .bootloader)
Data:          8 bytes (0.4% Full)
(.data + .bss + .noinit)
```
Lo que es fantástico, pues no hemos utilizado ni el 1% de la memoria flash
y una miserable cantidad de memoria ram. 

Desafortunadamente esa biblioteca es muy concreta, depende directamente de
la asignación de pines indicada en el esquemático, cualquier cambio en alguno
de los pines deriva en cambiar tres funciones y la tabla.

## Generalizando más
Las expectativas son altas, necesito que este código se ejecute con cualquier
combinación de pines y que sea altamente portable, la anterior implementación no
cubre los requerimientos, veamos otra posibilidad donde se usa la biblioteca
gpio, desarrollada en la entrada __perifericos->Api gpio__.

Asumiendo que cada segmento es representado por un pin, procedemos a implementar
la función `display7_showNumber`, es casi idéntica y también reutilizamos la 
tabla,
```C
const uint8_t segments_table[] = {
	//0b76543210
	//  .GFEDCBA
	0x3F, //0
	0x06, //1
	0x5B, //2
	0x4f, //3
	0x66, //4
	0x6d, //5
	0x7d, //6
	0x07, //7
	0x7F, //8
	0x6f //9
};

static void setSegments(uint8_t segments);

void display7_showNumber(int8_t number)
{
	if(number < 0 || number > 9)
		return;
	uint8_t segments = segments_table[number];
	setSegments(segments);
}
```
La novedad está en `setSegments`, pues ya no hay acceso a los registros,
en su lugar tenemos una lista de segmentos representados por pines, la
idea es iterar sobre todos los segmentos desde A hasta G e ir revisando su 
estado, si el estado es cero el pin se apaga.
```C
static void setSegments(uint8_t segments)
{
	for(int i = 0; i<TOTAL_PINS-1; i++){
		if((segments &(1<<i)) == 0)
			gpio_setPinLevel(segments_pins_table[i], LOW);
		else
			gpio_setPinLevel(segments_pins_table[i], HIGH);
	}
}
```
`segments_pins_table`, ya no contiene el número de pin correspondiente a 
cada segmento, ahora es una tabla que contiene apuntadores a instancias `Pin`,
por supuesto la función `display7_init` es la encargada de recibir los pines y
guardarlos en la tabla: 
```C
#define TOTAL_PINS 8

Pin * segments_pins_table[TOTAL_PINS];

void display7_init(Pin * A, Pin * B, Pin * C, Pin * D, 
		Pin * E, Pin * F, Pin * G, Pin * dot)
{
	segments_pins_table[0] = A;
	segments_pins_table[1] = B;
	segments_pins_table[2] = C;
	segments_pins_table[3] = D;
	segments_pins_table[4] = E;
	segments_pins_table[5] = F;
	segments_pins_table[6] = G;
	segments_pins_table[7] = dot;
}
```
Observe la simplicidad del código, esto es debido a la separación de responsabilidades
entre  el manejo de los segmentos y el manejo de los registros.

## Comentarios finales
Ahora contamos con dos módulos que deberían verse de la siguiente forma:
```Bash
.
├── display7
│   ├── include
│   │   └── display7.h
│   └── src
│       └── display7.c
├── gpio
│   ├── include
│   │   └── gpio.h
│   └── src
│       └── gpio.c
└── main.c

6 directories, 5 files
```
Comparando las dos versiones, es evidente que la segundo es mucho más limpia, pero
tomó mucho más tiempo y se cometieron más errores, además ocupa más recursos y
es más lenta. ¿Vale la pena realizar la sobrecarga de trabajo?, la respuesta no
es simple, pienso que vale la pena cuando no existe un módulo que ya lo haga, 
si ya existe no pierdas el tiempo y úsalo, si no existe y solo vas a realizar
un proyecto con ese dispositivo, olvídalo no vale la pena "harcodea", si no
existe y vas a utilizar de forma regular esa características, vale totalmente
la pena, la verdad es muy bonito que cuando requieres comenzar un proyecto ya
tengas tus bibliotecas de los periféricos listas y no tengas que andar
buscando registros y pines en el data sheet.

[módulo display7](https://github.com/logys/m328pUtils/tree/main/display7)
