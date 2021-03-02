---
title: 'Display Siete Segmentos IV.'
date: Sat, 16 May 2020 22:06:49 +0000
draft: false
tags: ['7segmentos', 'avr', 'Programación']
---

Nuestro trabajo consiste en implementar las funciones del módulo contamos con un par de funciones, una para iniciar y otra para cerrar el módulo y una tercera para establecer el número, la interfaz es muy sencilla y para nuestro propósito es más que suficiente.

La mayor parte del trabajo recae en la tercer función y la dejamos para el final. Es muy común que los módulos contengan dos funciones, init y close o create y destroy, el trabajo de estas dos funciones es llevar al módulo a un estado conocido y liberar los recursos respectivamente. Para el módulo display7 ambas funciones son simples, en la función destroy, los recursos que se pueden liberar son los puertos y los pines, sin embargo en este caso es de poca utilidad, pues aunque liberemos los recursos nadie puede usarlos, esto no quiere decir que la función destroy siempre sera tan simple, en módulos más complejos tiene una importancia fundamental sobre todo la utilizar memoria dinámica.

La función init tiene la misión de establecer las condiciones adecuadas para que el resto del módulo pueda hacer su trabajo, modificara lo registros de los puertos para establecerlos como salidas y con nivel bajo, el código ya lo tenemos de entradas anteriores, así que lo movemos a la función display7\_init().

```
//display7.c
#include"display7.h"
#include<avr/io.h>

void display7\_init(void)
{
	//pinout como salidas
	DDRB |= (1<<PB0);
	DDRD |=  (1<<PD2)|(1<<PD3)|(1<<PD4)|
		(1<<PD5)|(1<<PD6)|(1<<PD7);
	//pinout nivel bajo
 	PORTB &= ~(1<<PB0);
 	PORTD &= ~((1<<PD2)|(1<<PD3)|(1<<PD4)|
		(1<<PD5)|(1<<PD6)|(1<<PD7));
}
void display7\_destroy(void)
{
}
void display7\_setNumber(short number)
{
}
```

se incluye avr/io.h para acceder a los registros, en el futuro modificaremos la función init a una versión mejorada, por ahora lo dejamos así. La función destroy regresa los puertos y pines a su estado original,

```
//display7.c
#include"display7.h"
#include<avr/io.h>
void display7\_init(void)
{
	//pinout como salidas
	DDRB |= (1<<PB0);
	DDRD |=  (1<<PD2)|(1<<PD3)|(1<<PD4)|
		(1<<PD5)|(1<<PD6)|(1<<PD7);
	//pinout nivel bajo
 	PORTB &= ~(1<<PB0);
 	PORTD &= ~((1<<PD2)|(1<<PD3)|(1<<PD4)|
		(1<<PD5)|(1<<PD6)|(1<<PD7));
}
void display7\_destroy(void)
{
	//pinout como entradas
	DDRB &= ~(1<<PB0);
	DDRD &= ~((1<<PD2)|(1<<PD3)|(1<<PD4)|
		(1<<PD5)|(1<<PD6)|(1<<PD7));
	//pinout nivel bajo
 	PORTB &= ~(1<<PB0);
 	PORTD &= ~((1<<PD2)|(1<<PD3)|(1<<PD4)|
		(1<<PD5)|(1<<PD6)|(1<<PD7));
}
void display7\_setNumber(short number)
{
}
```

Hay modificaciones necesarias para el código anterior, lo comentare al final. La tarea de la función setNumber, es modificar un número decimal entre 0 y 9 de tal forma que se vea reflejado en el display, la siguiente es la implementación más sencilla de la cual haré algunos comentarios.

```
void display7\_setNumber(short number)
{
	if(number == 0){
		PORTB &= ~(1<<PB0); //LED G apagado
		PORTD |= (1<<PD2)|(1<<PD3)|(1<<PD4)|
			(1<<PD5)|(1<<PD6)|(1<<PD7);
	}else if(number == 1){
		PORTB &= ~(1<<PB0);
 		PORTD &= ~((1<<PD2)|
			(1<<PD5)|(1<<PD6)|(1<<PD7));
		PORTD |= (1<<PD3)|(1<<PD4);//LED B y C encendido
	}
	//... hasta number == 9
}
```

Un dogma muy importante en el mundo de la programación especifica que, **la repetición de código es la mayor fuente de errores**, con forme vamos agregando casos a la función setNumber vamos aumentando las posibilidades de equivocarnos, vale la pena suspender el desarrollo en aras de refactorizar lo que ya tenemos. Refactorizar es un gran tema que podría entenderse como pulir el código, como primer paso vamos a reescribir esos horribles operandos OR, sabemos que PB0 corresponde al led G por el esquemático, es más eficiente que el propio código exprese a donde pertenece,

```
#define led_A (1<<PD2)
#define led_B (1<<PD3)
#define led_C (1<<PD4)
#define led_D (1<<PD5)
#define led_E (1<<PD6)
#define led_F (1<<PD7)
#define led_G (1<<PB0)
```

de esta forma el código luce más expresivo

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
void display7\_init(void)
{
	//pinout como salidas
	DDRB |= led\_G;
	DDRD |= led\_A|led\_B|led\_C|
		led\_D|led\_E|led\_F;
	//pinout nivel bajo
 	PORTB &= ~led\_G;
 	PORTD &= ~(led\_A|led\_B|led\_C|
		led\_D|led\_E|led\_F);
}
void display7\_destroy(void)
{
	//pinout como entradas
	DDRB &= ~led\_G;
	DDRD &= ~(led\_A|led\_B|led\_C|
		led\_D|led\_E|led\_F);
	//pinout nivel bajo
 	PORTB &= ~led\_G;
 	PORTD &= ~(led\_A|led\_B|led\_C|
		led\_D|led\_E|led\_F);
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

sin embargo se ve algo críptico y no muestra por si solo las intenciones de cada función. Haremos uso de funciones auxiliares, para ayudarnos a reducir el código repetido y ser más expresivo. De inmediato identificamos que la función init esta compuesta de dos partes, establecer los pines como salidas y establecer los niveles en bajo, podemos refactorizar utilizando dos funciones auxiliares de la forma

```
void display7_init(void)
{
	pinoutAsOutputs();
	pinoutLowLevel();
}
```

de esta forma es sencillo ver la intensión de la función init, para hacer valido el código colocamos ambas implementaciones antes de la función init

```
static void pinoutAsOutputs(void)
{
	//pinout como salidas
	DDRB |= led_G;
	DDRD |=  led_A|led_B|led_C|
		led_D|led_E|led_F;
}
static void pinoutLowLevel(void)
{
	//pinout nivel bajo
 	PORTB &= ~led_G;
 	PORTD &= ~(led_A|led_B|led_C|
		led_D|led_E|led_F);
}
void display7_init(void)
{
	pinoutAsOutputs();
	pinoutLowLevel();
}
```

La palabra static(cuidado static tiene otros usos en otros contextos) sirve para indicar que las funciones únicamente pueden ser llamadas desde el mismo archivo, en este caso display7.c, en el futuro haré una entrada especifica sobre el alcance(scope).

Logramos un poco de legibilidad, pero también importante es que evitamos la repetición de código, ¿en donde?, hay que mirar la función destroy, que puede ser refactorizada como

```
void display7_destroy(void)
{
	pinoutAsInputs();
	pinoutLowLevel();
}
```

pinoutLowLevel ya la implementamos, solo queda implementar pinoutAsInputs,

```
static void pinoutAsOutputs(void)
{
	//pinout como salidas
	DDRB |= led_G;
	DDRD |=  led_A|led_B|led_C|
		led_D|led_E|led_F;
}
static pinoutAsInputs(void)
{
	//pinout como entradas
	DDRB &= ~led_G;
	DDRD &= ~(led_A|led_B|led_C|
		led_D|led_E|led_F);
}
static void pinoutLowLevel(void)
{
	//pinout nivel bajo
 	PORTB &= ~led_G;
 	PORTD &= ~(led_A|led_B|led_C|
		led_D|led_E|led_F);
}
void display7_init(void)
{
	pinoutAsOutputs();
	pinoutLowLevel();
}
void display7_destroy(void)
{
	pinoutAsInputs();
	pinoutLowLevel();
}
```

aun le falta al código, pero ya comienza a verse la forma. Más refactorización en la siguiente entrada.