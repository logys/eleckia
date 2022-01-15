---
title: "Api gpio"
weight: 1
draft : false
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# Api gpio
Para agilizar el desarrollo y desacoplar los módulos de alto nivel de los 
detalles del hardware, es buena idea desarrollar una api/driver para cada 
periférico a utilizar, dándonos la oportunidad de reutilizar el código 
desarrollado en otros proyectos.

En esta entrada desarrollaremos un módulo que nos permita crear acceder a los
pines en el atmega328p de una manera simple.

## Requerimientos
Los gpio's son los periféricos más utilizados en cualquier desarrollo de software
embebido, es necesario desarrollar una api para los mismos.

## Implementación 

Cuando hablamos de gpios, lo que nos interesa es hacer una de las siguientes
operaciones:
- Establecer la dirección del pin (entrada/salida).
- Establecer el nivel del pin (alto/bajo).
- Obtener el estado del pin(alto/bajo)

Es natural iniciar especificando estas tres operaciones en forma de funciones:
```C
void gpio_setPinDirection(Pin * pin, PIN_DIR direction)
{
}
void gpio_setPinLevel(Pin * pin, PIN_LEVEL level)
{
}
PIN_LEVEL gpio_pinLevel(Pin * pin)
{
}
```
Comencemos por establecer la dirección, `Pin` es una estructura que contiene
toda la información relevante sobre el pin, (numero, ddrx, portx, pinx):
```C
void gpio_setPinDirection(Pin * pin, PIN_DIR direction)
{
	if(direction == INPUT)
		*pin->ddrx &= ~(1<<pin->number);
	else if(direction == OUTPUT)
		*pin->ddrx |=  1<<pin->number;
}
```
muy simple, tan solo revisamos la dirección y asignamos usando el operador
de indirección, el nivel se implementa de manera prácticamente idéntica.
```C
void gpio_setPinLevel(Pin * pin, PIN_LEVEL level)
{
	if(level == LOW)
		*pin->portx &= ~(1<<pin->number);
	else if(level == HIGH)
		*pin->portx |=  1<<pin->number;
}
```
Finalmente se requiere obtener el nivel del pin, el razonamiento es el mismo:
```C
PIN_LEVEL gpio_pinLevel(Pin * pin)
{
	if (*pin->pinx & (1<<pin->number))
		return HIGH;
	else 
		return LOW;
}
```
Todo muy simple, pero necesitamos una función que cree los pines y les asigne 
sus valores, la interfaz del módulo es la siguiente:
```C
#ifndef GPIO_H
#define GPIO_H

#include <stdint.h>

typedef enum {INPUT=0, OUTPUT} PIN_DIR;
typedef enum {LOW=0, HIGH} PIN_LEVEL;
typedef struct Pin{
	uint8_t number;
	volatile uint8_t *ddrx;
	volatile uint8_t *portx;
	volatile uint8_t *pinx;
}Pin;

Pin gpio_create(uint8_t pin_number, PIN_DIR direction, PIN_LEVEL level);
void gpio_setPinDirection(Pin * pin, PIN_DIR direction);
void gpio_setPinLevel(Pin * pin, PIN_LEVEL level);
PIN_LEVEL gpio_pinLevel(Pin * pin);

#endif// GPIO_H
```
La complejidad de este módulo se centra en la función `gpio_create`, el usuario
debe indicar el número de pin, el atmega328p en paquete DIP tiene un total de
28 pines, de los cuales la gran mayoría pertenecen a los puertos gpio, sin
embargo no hay ninguna forma de establecer una relación entre el número de pin
físico 
y el puerto al que pertenece, es decir si yo indico el pin __4__, no existe
una función registro o ente que me indique que tal pin físico pertenece al gpio
__PD2__ del puerto __D__. 

Para establecer una relación se propone crear una tabla (lookup table), con un
arreglo.
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
De esta forma es posible consultar que puerto y que pin gpio corresponde a 
cada pin físico del microcontrolador:
```C
//por ejemplo
uint8_t gpio_port = gpio_table[pin][0];
uint8_t gpio_pin = gpio_table[pin][1];
```
Regresando a la función `gpio_create`, antes de establecer la dirección y 
nivel del pin, se requiere rellenar la estructura, se inicia con:
```C
Pin gpio_create(uint8_t pin_number, PIN_DIR direction, PIN_LEVEL level)
{
	uint8_t gpio_port = gpio_table[pin_number][0];
	uint8_t gpio_pin = gpio_table[pin_number][1];
	Pin pin = {.number = gpio_pin
	};
}
```
Sin embargo aunque sabemos el puerto, no sabemos los registros, nuevamente se
hacer uso de lookup tables,
```C
volatile uint8_t * const port_table[] = {
	&PORTB,
	&PORTC,
	&PORTD
};

volatile uint8_t * const ddr_table[] = {
	&DDRB,
	&DDRC,
	&DDRD
};

volatile uint8_t * const pin_table[] = {
	&PINB,
	&PINC,
	&PIND
};
```
Entonces ahora si podemos registrar la información.
```C
Pin gpio_create(uint8_t pin_number, PIN_DIR direction, PIN_LEVEL level)
{
	uint8_t gpio_port = gpio_table[pin_number][0];
	volatile uint8_t * ddrx = ddr_table[gpio_port];
	volatile uint8_t * portx = port_table[gpio_port];
	volatile uint8_t * pinx = pin_table[gpio_port];
	uint8_t gpio_pin = gpio_table[pin_number][1];
	Pin pin = {.number = gpio_pin,
		.ddrx = ddrx,
		.portx = portx,
		.pinx = pinx
	};
}
```
Finalmente establecemos la dirección, su nivel y retornamos el pin creado:
```C
Pin gpio_create(uint8_t pin_number, PIN_DIR direction, PIN_LEVEL level)
{
	uint8_t gpio_port = gpio_table[pin_number][0];
	volatile uint8_t * ddrx = ddr_table[gpio_port];
	volatile uint8_t * portx = port_table[gpio_port];
	volatile uint8_t * pinx = pin_table[gpio_port];
	uint8_t gpio_pin = gpio_table[pin_number][1];
	Pin pin = {.number = gpio_pin,
		.ddrx = ddrx,
		.portx = portx,
		.pinx = pinx
	};
	gpio_setPinDirection(&pin, direction);
	gpio_setPinLevel(&pin, level);
	return pin;
}
```
## Protección
La interfaz es pública, un usuario puede tener la mala fortuna de crear un pin
con un número incorrecto, para evitar esto existen varias aproximaciones, desde
detener el microcontrolador hasta programar defensivamente cada uno de los
posible casos de error, particularmente yo prefiero detener el programa cuando
de funciones _create_ o _init_ se trata y revisar cada caso particular en las
otras funciones.

Para detener el programa si el usuario ha introducido un pin incorrecto se
sugiere utilizar la biblioteca `assert.h`, el funcionamiento es simple:
```C
assert(true); //continua la ejecución
assert(false); //detiene la ejecución
```
Gracias a la lookup table de pines es fácil identificar si un pin es incorrecto,
por ejemplo el pin 7 pertenece a la alimentación y no puede ser usado como
gpio, al obtener el puerto de la tabla, obtenemos un `-1`, por lo tanto si 
hacemos un assert del puerto podemos verificar la viabilidad del pin.
```C
Pin gpio_create(uint8_t pin_number, PIN_DIR direction, PIN_LEVEL level)
{
	uint8_t gpio_port = gpio_table[pin_number][0];
	assert(gpio_port != -1); //¿pin valido?
	volatile uint8_t * ddrx = ddr_table[gpio_port];
	volatile uint8_t * portx = port_table[gpio_port];
	volatile uint8_t * pinx = pin_table[gpio_port];
	uint8_t gpio_pin = gpio_table[pin_number][1];
	Pin pin = {.number = gpio_pin,
		.ddrx = ddrx,
		.portx = portx,
		.pinx = pinx
	};
	gpio_setPinDirection(&pin, direction);
	gpio_setPinLevel(&pin, level);
	return pin;
}
```
En otra entrada mejoraremos el assert para que sea más ilustrativo, por ahora 
esto funciona, el resto de funciones están protegidas por los if's y no 
requieren mayor atención. 

Para probar nuestra flamante biblioteca, se propone el programa:
```C
#include "gpio.h"
#define F_CPU 16000000UL
#include <util/delay.h>

int main(void)
{
	Pin led = gpio_create(19, OUTPUT, LOW);
	while(1){
		gpio_setPinLevel(&led, HIGH);
		_delay_ms(1000);
		gpio_setPinLevel(&led, LOW);
		_delay_ms(1000);
	}
}
```
Compilado y subido el led comienza a parpadear con un periodo de 2 segundos.
