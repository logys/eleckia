---
title: 'Display Siete Segmentos V.'
date: Thu, 21 May 2020 06:09:36 +0000
draft: false
tags: ['7segmentos', 'avr', 'Programación']
---

Continuamos refactorizando el código para evitar repeticiones y ganar legibilidad, retomando.

```
//display7.c
#include"display7.h"
#include<avr/io.h>

#define led\_A (1<<PD2)
#define led\_B (1<<PD3)
#define led\_C (1<<PD4)
#define led\_D (1<<PD5)
#define led\_E (1<<PD6)
#define led\_F (1<<PD7)
#define led\_G (1<<PB2)

static void pinoutAsOutputs(void)
{
	//pinout como salidas
	DDRB |= led\_G;
	DDRD |= led\_A|led\_B|led\_C|
		led\_D|led\_E|led\_F;
}
static void pinoutLevelLow(void)
{
	//pinout nivel bajo
 	PORTB &= ~led\_G;
 	PORTD &= ~(led\_A|led\_B|led\_C|
		led\_D|led\_E|led\_F);
}
static void pinoutAsInputs(void)
{
	//pinout como entradas
	DDRB &= ~led\_G;
	DDRD &= ~(led\_A|led\_B|led\_C|
		led\_D|led\_E|led\_F);
}
void display7\_init(void)
{
	pinoutAsOutputs();
	pinoutLevelLow();
}
void display7\_destroy(void)
{
	pinoutAsInputs();
	pinoutLevelLow();
}
void display7\_setNumber(short number)
{
	if(number == 0){
		PORTB &= ~led\_G; //LED G apagado
		PORTD |= led\_A|led\_B|led\_C|
			led\_D|led\_E|led\_F;
	}else if(number == 1){
		PORTB &= ~led\_G;
 		PORTD &= ~(led\_A|
			led\_D|led\_E|led\_F);
		PORTD |= led\_B|led\_C;//LED B y C encendido
	}
	//... hasta number == 9
}
```

A ojo de buen cubero, me da la impresión que las tres funciones auxiliares se parecen mucho, en lo único que difieren es en el registro y en establecer o limpiar, por supuesto vamos a tratar de reducir esta repetición. Propongo el siguiente par de funciones auxiliares.

```
static void setBits(uint8_t *regi, uint8_t bits)
{
	*regi |= bits;
}
static void clearBits(uint8_t *regi, uint8_t bits)
{
	*regi &= ~bits;
}
```

toman como parámetro un apuntador hacia el registro y le asignan el valor del segundo parámetro, fácil o como decimos en español mexicano izi, esta refactorización nos ayuda en dos cosas legibilidad y claridad en la intensión del proceso

```
//display7.c
#include"display7.h"
#include<avr/io.h>
#include<stdint.h>

#define led\_A (1<<PD2)
#define led\_B (1<<PD3)
#define led\_C (1<<PD4)
#define led\_D (1<<PD5)
#define led\_E (1<<PD6)
#define led\_F (1<<PD7)
#define led\_G (1<<PB2)
static void setBits(uint8\_t \*regi, uint8\_t bits)
{
	\*regi |= bits;
}
static void clearBits(uint8\_t \*regi, uint8\_t bits)
{
	\*regi &= ~bits;
}
static void pinoutAsOutputs(void)
{
	setBits(&DDRD, led\_A|led\_B|led\_C|led\_D|led\_E|led\_F);
	setBits(&DDRB, led\_G);
}
static void pinoutLevelLow(void)
{
	clearBits(PORTD, led\_A|led\_B|led\_C|led\_D|led\_E|led\_F);
	clearBits(PORTB, led\_G);
}
static void pinoutAsInputs(void)
{
	clearBits(DDRD, led\_A|led\_B|led\_C|led\_D|led\_E|led\_F);
	clearBits(DDRB, led\_G);
}
void display7\_init(void)
{
	pinoutAsOutputs();
	pinoutLevelLow();
}
void display7\_destroy(void)
{
	pinoutAsInputs();
	pinoutLevelLow();
}
void display7\_setNumber(short number)
{
	if(number == 0){
		setBits(&PORTD, led\_A|led\_B|led\_C|led\_D|led\_E|led\_F);
		clearBits(&PORTB, led\_G);
	}else if(number == 1){
		setBits(&PORTD, led\_B|led\_C);
		clearBits(&PORTD, led\_A|led\_D|led\_E|led\_F);
		clearBits(&PORTB, led\_G);
	}
	//... hasta number == 9
}
```

vemos que aunque ganamos claridad, no eliminamos la repetición pero ahora se ve fácilmente, las llamadas a las funciones clearBits y setBits, son prácticamente iguales, se propone una refactorización basada en un apuntador de funciones

```
typedef enum {DIRECTION, LEVEL}PORT\_TYPE;
static void actionOnPort(void (\*action)(uint8\_t \*, uint8\_t), PORT\_TYPE port\_type)
{
	if(port\_type == DIRECTION){
		action(&DDRD, led\_A|led\_B|led\_C|led\_D|led\_E|led\_F);
		action(&DDRB, led\_G);
	}else if(port\_type == LEVEL){
		action(&PORTD, led\_A|led\_B|led\_C|led\_D|led\_E|led\_F);
		action(&PORTB, led\_G);
	}
}
```

de esta forma el código refactorizado queda

```
//display7.c
#include"display7.h"
#include<avr/io.h>
#include<stdint.h>

#define led\_A (1<<PD2)
#define led\_B (1<<PD3)
#define led\_C (1<<PD4)
#define led\_D (1<<PD5)
#define led\_E (1<<PD6)
#define led\_F (1<<PD7)
#define led\_G (1<<PB2)

static void setBits(uint8\_t \*regi, uint8\_t bits)
{
	\*regi |= bits;
}
static void clearBits(uint8\_t \*regi, uint8\_t bits)
{
	\*regi &= ~bits;
}
typedef enum {DIRECTION, LEVEL}PORT\_TYPE;
static void actionOnPorts(void (\*action)(uint8\_t \*, uint8\_t), PORT\_TYPE port\_type)
{
	if(port\_type == DIRECTION){
		action(&DDRD, led\_A|led\_B|led\_C|led\_D|led\_E|led\_F);
		action(&DDRB, led\_G);
	}else if(port\_type == LEVEL){
		action(&PORTD, led\_A|led\_B|led\_C|led\_D|led\_E|led\_F);
		action(&PORTB, led\_G);
	}
}
static void pinoutAsOutputs(void)
{
	actionOnPorts(setBits, DIRECTION);
}
static void pinoutAsInputs(void)
{
	actionOnPorts(clearBits, DIRECTION);
}
static void pinoutLevelLow(void)
{
	actionOnPorts(clearBits, LEVEL);
}
void display7\_init(void)
{
	pinoutAsOutputs();
	pinoutLevelLow();
}
void display7\_destroy(void)
{
	pinoutAsInputs();
	pinoutLevelLow();
}
void display7\_setNumber(short number)
{
	if(number == 0){
		setBits(&PORTD, led\_A|led\_B|led\_C|led\_D|led\_E|led\_F);
		clearBits(&PORTB, led\_G);
	}else if(number == 1){
		setBits(&PORTD, led\_B|led\_C);
		clearBits(&PORTD, led\_A|led\_D|led\_E|led\_F);
		clearBits(&PORTB, led\_G);
	}
	//... hasta number == 9
}
```

claramente eliminamos la repetición, sin embargo no la totalidad, ahora tenemos repetición dentro de la función actionOnPorts, debemos eliminarla, la refactorización anterior también nos muestra algo que puede parecer redundante, en lugar de

```
void display7_init(void)
{
	pinoutAsOutputs();
	pinoutLevelLow();
}
```

podemos exponer directamente la función actionOnPorts de la siguiente forma

```
void display7_init(void)
{
	actionOnPorts(setBits, DIRECTION);
	actionOnPorts(clearBits, LEVEL);
}
```

y eliminar las funciones pinoutAsOutputs y pinoutLevelLow, sin embargo pienso que la función actionOnPorts no muestra claramente la intensión de la función init, personalmente prefiero no eliminar las funciones ya establecidas en init. Las llamadas a las funciones actions son identicas salvo por los puertos, se propone una función que determine los puertos adecuados

```
typedef enum {DIRECTION, LEVEL}PORT\_TYPE;
static void selectPorts(uint8\_t \*\* port1, uint8\_t \*\* port2, PORT\_TYPE port\_type)
{
	if(port\_type == DIRECTION){
		\*port1 = &DDRD;
		\*port2 = &DDRB;
	}else if(port\_type == LEVEL){
		\*port1 = &PORTD;
		\*port2 = &PORTB;
	}
}
```

de esta forma hemos eliminado las repeticiones

```
//display7.c
#include"display7.h"
#include<avr/io.h>
#include<stdint.h>

#define led\_A (1<<PD2)
#define led\_B (1<<PD3)
#define led\_C (1<<PD4)
#define led\_D (1<<PD5)
#define led\_E (1<<PD6)
#define led\_F (1<<PD7)
#define led\_G (1<<PB2)

static void setBits(uint8\_t \*regi, uint8\_t bits)
{
	\*regi |= bits;
}
static void clearBits(uint8\_t \*regi, uint8\_t bits)
{
	\*regi &= ~bits;
}
typedef enum {DIRECTION, LEVEL}PORT\_TYPE;
static void selectPorts(uint8\_t \*\* port1, uint8\_t \*\* port2, PORT\_TYPE port\_type)
{
	if(port\_type == DIRECTION){
		\*port1 = &DDRD;
		\*port2 = &DDRB;
	}else if(port\_type == LEVEL){
		\*port1 = &PORTD;
		\*port2 = &PORTB;
	}
}
static void actionOnPorts(void (\*action)(uint8\_t \*, uint8\_t), PORT\_TYPE port\_type)
{
	uint8\_t \* port1;
	uint8\_t \* port2;
	selectPorts(&port1, &port2, port\_type);

	action(port1, led\_A|led\_B|led\_C|led\_D|led\_E|led\_F);
	action(port2, led\_G);
}
static void pinoutAsOutputs(void)
{
	actionOnPorts(setBits, DIRECTION);
}
static void pinoutAsInputs(void)
{
	actionOnPorts(clearBits, DIRECTION);
}
static void pinoutLevelLow(void)
{
	actionOnPorts(clearBits, LEVEL);
}
void display7\_init(void)
{
	pinoutAsOutputs();
	pinoutLevelLow();
}
void display7\_destroy(void)
{
	pinoutAsInputs();
	pinoutLevelLow();
}
void display7\_setNumber(short number)
{
	if(number == 0){
		setBits(&PORTD, led\_A|led\_B|led\_C|led\_D|led\_E|led\_F);
		clearBits(&PORTB, led\_G);
	}else if(number == 1){
		setBits(&PORTD, led\_B|led\_C);
		clearBits(&PORTD, led\_A|led\_D|led\_E|led\_F);
		clearBits(&PORTB, led\_G);
	}
	//... hasta number == 9
}
```

nos toca mejorar de alguna manera la función display7\_setNumber, podemos seguir llenando los casos, pero ya comienza a verse un desorden importante, otra aproximación para establecer los números puede ser, apagar todos y encender los necesarios o encender todos y apagar los necesarios, por ejemplo el cero, encendemos todos y apagamos G, en el caso de el uno, apagamos todos y posteriormente encendemos B y C,

```
static void pinoutLevelHigh(void)
{
	actionOnPorts(setBits, LEVEL);
}

void display7\_setNumber(short number)
{
	if(number == 0){
		pinoutLevelHigh();
		clearBits(&PORTB, led\_G);
	}else if(number == 1){
		pinoutLevelLow();
		setBits(&PORTD, led\_B|led\_C);
	}else if(number == 2){
		pinoutLevelHigh();
		clearBits(&PORTD, led\_F|led\_C);
	}else if(number == 3){
		pinoutLevelHigh();
		clearBits(&PORTD, led\_F|led\_E);
	}else if(number == 4){
		pinoutLevelHigh();
		clearBits(&PORTD, led\_A|led\_D|led\_E);
	}else if(number == 5){
		pinoutLevelHigh();
		clearBits(&PORTD, led\_B|led\_E);
	}else if(number == 6){
		pinoutLevelHigh();
		clearBits(&PORTD, led\_B);
	}else if(number == 7){
		pinoutLevelLow();
		setBits(&PORTD, led\_A|led\_B|led\_C);
	}else if(number == 8){
		pinoutLevelHigh();
	}else if(number == 9){
		pinoutLevelHigh();
		clearBits(&PORTD, led\_E);
	}
}
```

Compilando el programa y verificando su funcionamiento me doy cuenta de que el led G, no funciona, nuestras refactorizaciones nos han ayudado a minimizar la posibilidad de error, restringiendo el acceso a los puertos a un par de funciones, sin embargo el error no es probable que se encuentre ahí, pues el resto de leds usan dichas funciones y tiene un comportamiento correcto, el error debe estar en un lugar aislado, efectivamente en la directiva #define led\_G (1<<PB2) se encuentra el error, pues el pin correcto es PB0.

Definitivamente la función setNumber es mejorable, hay repetición de las funciones pinout y no me parece adecuado exponer el puerto PORTD en este nivel. Antes de arreglarlo, modifiquemos nuestro proyecto ino para realizar la cuenta.

```
//proyecto.ino
#define F\_CPU 16000000UL
#include<util/delay.h>
#include"display7.h"

int main(void)
{
	display7\_init();
	while(1){
		for(int i = 0; i<10; i++){
			display7\_setNumber(i);
			\_delay\_ms(1000);
		}
	}
}
```

Compilamos y subimos,

![](https://eleckia.files.wordpress.com/2020/05/7seg.gif?w=480)

En la siguiente entrada mejoraremos la función setNumber.