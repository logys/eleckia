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

de esta forma podemos realizar una secuencia de números

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
será posible mostrar un uno, el problema habla y nos esta diciendo que 
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
Algo que me gustaría es que se pueda indicar los pines al inicializar la 
biblioteca:
```C
void display7_init(int8_t A, int8_t B, int8_t C, int8_t D,
                int8_t E, int8_t F, int8_t G, int8_t dot);
``` 
Se requiere una forma de guardar los pines ingresados por el usuario, un
arreglo será suficiente:
```C
#define TOTAL_PINS 8
uint8_t pins_table[TOTAL_PINS];

void display7_init(int8_t A, int8_t B, int8_t C, int8_t D, 
		int8_t E, int8_t F, int8_t G, int8_t dot)
{
	pins_table[0] = A;
	pins_table[1] = B;
	pins_table[2] = C;
	pins_table[3] = D;
	pins_table[4] = E;
	pins_table[5] = F;
	pins_table[6] = G;
	pins_table[7] = dot;
}
```
Desde el punto de vista del compilador no hay forma de saber a que gpio 
corresponde cada pin, debemos especificar la correspondencia, otra lookup
table es necesaria:
```C
enum {portB, portC, portD};
const int8_t gpio_table[][2] = {
	{-1, -1}, //pin 1
	{portD, PD0},
	{portD, PD1},
	{portD, PD2},
	{portD, PD3},
	{portD, PD4},
	{-1, -1}, //Vcc
	{-1, -1}, //Gnd
	{portB, PB6},
	{portB, PB7},
	{portD, PD5},
	{portD, PD6},
	{portD, PD7},
	{portB, PB0}, //pin 14
	{portB, PB1},
	{portB, PB2},
	{portB, PB3},
	{portB, PB4},
	{portB, PB5},
	{-1, -1}, //AVcc
	{-1, -1}, //ARef
	{-1, -1}, //Gnd
	{portC, PC0},
	{portC, PC1},
	{portC, PC2},
	{portC, PC3},
	{portC, PC4},
	{portC, PC5}, //pin 28
};
```
Ahora contamos con toda la información necesaria. Registrados los pines,
es necesario configurarlos como salidas, se propone:
```C
void display7_init(int8_t A, int8_t B, int8_t C, int8_t D, 
		int8_t E, int8_t F, int8_t G, int8_t dot)
{
	pins_table[0] = A;
	pins_table[1] = B;
	pins_table[2] = C;
	pins_table[3] = D;
	pins_table[4] = E;
	pins_table[5] = F;
	pins_table[6] = G;
	pins_table[7] = dot;
	pinsAsOutput();
}

static void pinsAsOutput(void)
{
	for(int i = 0; i<TOTAL_PINS; i++){
		if(gpio_table[pins_table[i]][0] == portB)
			DDRB |= gpio_table[pins_table[i]][1];
		else if(gpio_table[pins_table[i]][0] == portC)
			DDRC |= gpio_table[pins_table[i]][1];
		else if(gpio_table[pins_table[i]][0] == portD)
			DDRD |= gpio_table[pins_table[i]][1];
	}
}
```
Tan solo nos queda establecer y limpiar, se propone modificar los patrones
de los segmentos a la forma:
```C
0b76543210
  .GFEDCBA
``` 
es decir:
```C
const uint8_t segments_table[] = {
	//0b76543210
	//  .GFEDCBA
	0xFC, //0
	0x60, //1
	0xDA, //2
	0xF2, //3
	0x66, //4
	0xB6, //5
	0xBE, //6
	0xE0, //7
	0xFE, //8
	0xE6, //9
};
```
Para establecer cada segmento se recorren los pines registrados y si el
correspondiente bit está en alto, se establece el pin:
```C
static void setPins(uint8_t pins)
{
	for(int i = 0; i<TOTAL_PINS; i++){
		if((pins&(1<<i)) == 0)
			continue;
		if(gpio_table[pins_table[i]][0] == portB)
			PORTB |= 1<<gpio_table[pins_table[i]][1];
		else if(gpio_table[pins_table[i]][0] == portC)
			PORTC |= 1<<gpio_table[pins_table[i]][1];
		else if(gpio_table[pins_table[i]][0] == portD)
			PORTD |= 1<<gpio_table[pins_table[i]][1];
	}
}

static void clearPins(uint8_t pins)
{
	for(int i = 0; i<TOTAL_PINS; i++){
		if((pins&(1<<i)) == 0)
			continue;
		if(gpio_table[pins_table[i]][0] == portB)
			PORTB &= ~(1<<gpio_table[pins_table[i]][1]);
		else if(gpio_table[pins_table[i]][0] == portC)
			PORTC &= ~(1<<gpio_table[pins_table[i]][1]);
		else if(gpio_table[pins_table[i]][0] == portD)
			PORTD &= ~(1<<gpio_table[pins_table[i]][1]);
	}
}
```
Funciona pero es horriblemente feo, hagamos una lookup table para quitar esos
espantosos if's:
```C
volatile uint8_t * port_table[] = {
	&PORTB,
	&PORTC,
	&PORTD
};

static void setPins(uint8_t pins)
{
	volatile uint8_t * port_register;
	for(int i = 0; i<TOTAL_PINS; i++){
		if((pins&(1<<i)) == 0)
			continue;
		port_register = port_table[gpio_table[pins_table[i]][0]];
		*port_register |= 1<<gpio_table[pins_table[i]][1];
	}
}

static void clearPins(uint8_t pins)
{
	volatile uint8_t * port_register;
	for(int i = 0; i<TOTAL_PINS; i++){
		if((pins&(1<<i)) == 0)
			continue;
		port_register = port_table[gpio_table[pins_table[i]][0]];
		*port_register &= ~(1<<gpio_table[pins_table[i]][1]);
	}
}

volatile uint8_t * ddr_table[] = {
	&DDRB,
	&DDRC,
	&DDRD
};

static void pinsAsOutput(void)
{
	volatile uint8_t * dir_register;
	for(int i = 0; i<TOTAL_PINS; i++){
		dir_register = ddr_table[gpio_table[pins_table[i]][0]];
		*dir_register |= 1<<gpio_table[pins_table[i]][1];
	}
}
```
Aunque se ve un poco más simple, no deja de ser mala idea anidar indices de 
esa forma, algo un poco más decente es lo siguiente:
```C
static void setPins(uint8_t pins)
{
	volatile uint8_t * port_register;
	int8_t pin_number;
	int8_t port;
	int8_t pin;
	for(int i = 0; i<TOTAL_PINS; i++){
		if((pins&(1<<i)) == 0)
			continue;
		pin_number = pins_table[i];
		
		port = gpio_table[pin_number][0];
		pin = gpio_table[pin_number][1];
		port_register = port_table[port];
		*port_register |= 1<<pin;
	}
}
```
creo que se entiende mejor. Pero sigue sin satisfacerme, mañosamente he dejado
una línea para resaltar algo muy importante, esta función esta haciendo
dos tareas.

1.- Revisa las coincidencias entre el segmento y el bit.

2.- Establece el bit en el puerto.

Para mayor claridad y mejor "mantenibilidad", propongo poner la segunda tarea 
en otra función.

```C
static void setPin(int8_t pin_number);
static void setPins(uint8_t pins)
{
	int8_t pin_number;
	for(int i = 0; i<TOTAL_PINS; i++){
		if((pins&(1<<i)) == 0)
			continue;
		pin_number = pins_table[i];
		setPin(pin_number);
	}
}

static void setPin(int8_t pin_number)
{
	int8_t port = gpio_table[pin_number][0];
	int8_t pin = gpio_table[pin_number][1];
	volatile uint8_t * port_register = port_table[port];
	*port_register |= 1<<pin;
}
```
Mucho mejor, ahora la función `setPins` no tiene nada que ver con los pines
del microcontrolador, propongo cambiar el nombre `setSegments` y la tabla de
`pins_table` a `segments_pins_table`. Por supuesto también ser requiere una función 
`clearPin`:
```C
static void setPin(int8_t pin_number)
{
	int8_t port = gpio_table[pin_number][0];
	int8_t pin = gpio_table[pin_number][1];
	volatile uint8_t * port_register = port_table[port];
	*port_register |= 1<<pin;
}

static void clearPin(int8_t pins)
{
	int8_t port = gpio_table[pin_number][0];
	int8_t pin = gpio_table[pin_number][1];
	volatile uint8_t * port_register = port_table[port];
	*port_register &= ~(1<<pin);
}
```
La repetición es evidente, se propone condensar las dos funciones en una sola y
renombrarla:
```C
typedef enum {LOW=0, HIGH} PIN_LEVEL;
static void setPinLevel(int8_t pin_number, PIN_LEVEL level)
{
	int8_t port = gpio_table[pin_number][0];
	int8_t pin = gpio_table[pin_number][1];
	volatile uint8_t * port_register = port_table[port];
	if(level == LOW)
		*port_register &= ~(1<<pin);
	else if(level == HIGH)
		*port_register |= 1<<pin;
}
```
Se ve bien. Lo mismo pasa con las renombradas `setSegments` y `clearSegments`,
también se propone condensar ambas funciones:
```C
static void setSegments(uint8_t pins)
{
	int8_t pin_number;
	for(int i = 0; i<TOTAL_PINS; i++){
		pin_number = segments_pins_table[i];
		if((pins&(1<<i)) == 0)
			setPinLevel(pin_number, LOW);
		else
			setPinLevel(pin_number, HIGH);
	}
}
```
Fantástico. Así como existe una función que indica el nivel de un pin, la 
función `pinsAsOutput` nos pide a gritos una función `setPinDirection`:
```C
static void setPinLevel(int8_t pin_number, PIN_LEVEL level)
{
	int8_t port = gpio_table[pin_number][0];
	int8_t pin = gpio_table[pin_number][1];
	volatile uint8_t * port_register = port_table[port];
	if(level == LOW)
		*port_register &= ~(1<<pin);
	else if(level == HIGH)
		*port_register |= 1<<pin;
}

typedef enum {INPUT=0, OUTPUT}PIN_DIR;
static void setPinDirection(int8_t pin_number, PIN_DIR direction)
{
	int8_t port = gpio_table[pin_number][0];
	int8_t pin = gpio_table[pin_number][1];
	volatile uint8_t * port_register = dir_table[port];
	if(direction == INPUT)
		*port_register &= ~(1<<pin);
	else if(direction == OUTPUT)
		*port_register |= 1<<pin;
}
```
Casi son idénticas, podría aplicarse el mismo razonamiento y condensarlas pero
una función con más de dos parámetros es muy desagradable, pero más importante
estas funciones se utilizarán en dos contextos totalmente diferentes,
prefiero dejarlas separadas.

Aprovechando la nueva función se hace refactoring en `pinsAsOutput`:
```C
static void pinsAsOutput(void)
{
	int8_t pin;
	for(int i = 0; i<TOTAL_PINS; i++){
		pin = segments_pins_table[i];
		setPinDirection(pin, OUTPUT);
	}
}
```
Vamos bien. Finalmente se modifica `display7_showNumber` para que utilice las
nuevas funciones:
```C
void display7_showNumber(int8_t number)
{
	if(number < 0 || number > 9)
		return;
	uint8_t pins = segments_table[number];
	setSegments(pins);
}
```
El trabajo esta hecho, pero hay algo más que debe hacerse.

## Ceder la responsabilidad
Actualmente las tablas y las funciones se ven como sigue:
```C
uint8_t segments_pins_table[TOTAL_PINS];
const uint8_t segments_table[];
void display7_showNumber(int8_t number);
static void setSegments(uint8_t pins);
void display7_init(int8_t A, int8_t B, int8_t C, int8_t D,
                int8_t E, int8_t F, int8_t G, int8_t dot);
static void pinsAsOutput(void);

const int8_t gpio_table[][2];
volatile uint8_t * port_table[];
volatile uint8_t * ddr_table[];
static void setPinLevel(int8_t pin_number, PIN_LEVEL level);
static void setPinDirection(int8_t pin_number, PIN_DIR direction);
```
Nuevamente de forma explicita dejé un espacio para resaltar que las funciones 
de arriba únicamente utilizan las tablas superiores, mientras que las dos
funciones de bajo solo utilizan las tres tablas de abajo. Dicho en forma
elegante las tablas y funciones de arriba son altamente cohesivas entre ellas,
y las funciones y las tablas de abajo son altamente cohesivas entre ellas, 
es absolutamente necesarios dividir el módulo en dos, por lo que se propone
trasladar las tablas y funciones de abajo a un nuevo par de ficheros, `gpio.h`
y `gpio.c`:
```C
#ifndef GPIO_H
#define GPIO_H

#include <stdint.h>

typedef enum {LOW=0, HIGH} PIN_LEVEL;
typedef enum {INPUT=0, OUTPUT}PIN_DIR;

void setPinLevel(int8_t pin_number, PIN_LEVEL level);
void setPinDirection(int8_t pin_number, PIN_DIR direction);

#endif// GPIO_H
```

```C
#include "gpio.h"
#include <avr/io.h>

enum {portB, portC, portD};

const int8_t gpio_table[][2] = {
	{-1, -1}, //pin 1
	{portD, PD0},
	{portD, PD1},
	{portD, PD2},
	{portD, PD3},
	{portD, PD4},
	{-1, -1}, //Vcc
	{-1, -1}, //Gnd
	{portB, PB6},
	{portB, PB7},
	{portD, PD5},
	{portD, PD6},
	{portD, PD7},
	{portB, PB0}, //pin 14
	{portB, PB1},
	{portB, PB2},
	{portB, PB3},
	{portB, PB4},
	{portB, PB5},
	{-1, -1}, //AVcc
	{-1, -1}, //ARef
	{-1, -1}, //Gnd
	{portC, PC0},
	{portC, PC1},
	{portC, PC2},
	{portC, PC3},
	{portC, PC4},
	{portC, PC5}, //pin 28
};

volatile uint8_t * port_table[] = {
	&PORTB,
	&PORTC,
	&PORTD
};


volatile uint8_t * ddr_table[] = {
	&DDRB,
	&DDRC,
	&DDRD
};

void setPinLevel(int8_t pin_number, PIN_LEVEL level)
{
	int8_t port = gpio_table[pin_number][0];
	int8_t pin = gpio_table[pin_number][1];
	volatile uint8_t * port_register = port_table[port];
	if(level == LOW)
		*port_register &= ~(1<<pin);
	else if(level == HIGH)
		*port_register |= 1<<pin;
}

void setPinDirection(int8_t pin_number, PIN_DIR direction)
{
	int8_t port = gpio_table[pin_number][0];
	int8_t pin = gpio_table[pin_number][1];
	volatile uint8_t * port_register = port_table[port];
	if(direction == INPUT)
		*port_register &= ~(1<<pin);
	else if(direction == OUTPUT)
		*port_register |= 1<<pin;
}
```
Compilando y midiendo el tamaño obtengo:
```Bash
$ avr-size --format=avr --mcu=atmega328p a.out
AVR Memory Usage
----------------
Device: atmega328p

Program:     612 bytes (1.9% Full)
(.text + .data + .bootloader)

Data:         88 bytes (4.3% Full)
(.data + .bss + .noinit)
```
No está mal, sacrificamos un poco de memoria en favor de la portabilidad.

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
De esta manera `display7` puede usar el módulo `gpio` simplemente con
incluirlo, pero no solo eso, un módulo `led`, `buzzer` o cualquiera que requiera
utilizar los puertos gpio, queda habilitado para poder usarlos, sin tener que 
preocuparse por los registros, mejor aun, `gpio` puede ser adaptado rápidamente
a cualquier otro avr cambiando únicamente las tablas de pines. Por supuesto 
`display7` también puede ser incluido dentro de cualquier otro módulo que 
lo requiera. 

Comparando las dos versiones, es evidente que la segundo es mucho más compleja,
tomo mucho más tiempo y se cometieron más errores, además ocupa más recursos y
es más lenta. ¿Vale la pena realizar la sobrecarga de trabajo?, la respuesta no
es simple, pienso que vale la pena cuando no existe un módulo que ya lo haga, 
si ya existe no pierdas el tiempo y úsalo, si no existe y solo vas a realizar
un proyecto con ese dispositivo, olvídalo no vale la pena "harcodea", si no
existe y vas a utilizar de forma regular esa características, vale totalmente
la pena, la verdad es muy bonito que cuando requieres comenzar un proyecto ya
tengas tus bibliotecas de los periféricos listas y no tengas que andar
buscando registros y pines en el data sheet.

[módulo gpio](https://github.com/logys/m328pUtils/tree/main/gpio)

[módulo display7](https://github.com/logys/m328pUtils/tree/main/display7)
