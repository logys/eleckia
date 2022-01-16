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

## Main
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

## Optimizar ram
Observando el tamaño del binario obtengo la siguiente información:
```Bash
$ avr-size a.out --format=avr --mcu=atmega328p

AVR Memory Usage
----------------
Device: atmega328p

Program:     768 bytes (2.3% Full)
(.text + .data + .bootloader)

Data:         76 bytes (3.7% Full)
(.data + .bss + .noinit)
```
El módulo utiliza una cantidad importante de memoria ram, las tablas son las
responsables. 

En la arquitectura avr existen entre otras, dos espacios de memoria importantes,
la memoria de programa y la memoria ram, la memoria de programa se almacena en
la memoria flash del microcontrolador y las variables se almacenan en la memoria
ram (Data), con una capacidad de 32kB y 2kB respectivamente. 
La arquitectura dota de un bus diferente para cada una de estas 
memorias, pudiéndose acceder a una región de ambas al mismo tiempo, resultando
en un mejor rendimiento, sin embargo tenemos mucha flash y poca ram, ¿será 
posible almacenar variables en flash?, la respuesta corta es __si__, sin embargo
la ejecución normal del programa establece que la memoria flash es de solo 
lectura, volviéndose un espacio inviable para las variables, pero siendo posible
almacenar las __constantes__ en flash.

Para ello haremos uso de atributos, los atributos son modificadores que 
indicamos a las declaraciones, permitiéndoos modificar el comportamiento
de las mismas, los atributos son altamente dependientes del entorno, compilador,
arquitectura del microcontrolador, etc, por lo que añadirlos hace nula la 
portabilidad. Sin embargo estamos desarrollando un módulo de un periférico igualmente
no es portable por naturaleza. Los atributos se asignan con \_\_attribute\_\_
sin embargo la biblioteca `avr/pgmspace.h`, nos ofrece un macro para facilitarnos
el trabajo:
```C
#include <avr/pgmspace.h>

const int8_t gpio_table[][2] PROGMEM = {
```
Con esta simple modificación ahora el arreglo `gpio_table` se ha almacenado en
memoria flash, realizando la medición se obtiene:
```Bash
AVR Memory Usage
----------------
Device: atmega328p

Program:     768 bytes (2.3% Full)
(.text + .data + .bootloader)

Data:         18 bytes (0.9% Full)
(.data + .bss + .noinit)
```
La memoria ram se ha reducido considerablemente.

Sin embargo el programa ha dejado de funcionar, pues la ejecución normal espera
que el arreglo este en espacio de ram, hay que indicar que el arreglo está en
flash siempre que se requiere leer el arreglo. Para leer el arreglo basta con 
utilizar la función `pgm_read_byte` cuyo argumento es la dirección del arreglo
en ram:
```C
uint8_t gpio_port = pgm_read_byte(&gpio_table[pin_number][0]);
uint8_t gpio_pin = pgm_read_byte(&gpio_table[pin_number][1]);
```
Por supuesto lo mismo aplica para las otras tablas, sin embargo lo
que guardan no son números si no apuntadores, `avr/pgmspace.h` también tiene
una función para ellos, `pgm_read_ptr`
```C
volatile uint8_t * ddrx = pgm_read_ptr(&ddr_table[gpio_port]);
volatile uint8_t * portx = pgm_read_ptr(&port_table[gpio_port]);
volatile uint8_t * pinx = pgm_read_ptr(&pin_table[gpio_port]);
```
nuevamente midamos el tamaño del binario:
```Bash
AVR Memory Usage
----------------
Device: atmega328p

Program:     748 bytes (2.3% Full)
(.text + .data + .bootloader)

Data:          0 bytes (0.0% Full)
(.data + .bss + .noinit)
```
Lo cual es impresionante e indica que no hemos programado nada, tan solo 
hicimos wiring y si compilamos optimizando com `-Os`, obtenemos:
```Bash
AVR Memory Usage
----------------
Device: atmega328p

Program:     658 bytes (2.0% Full)
(.text + .data + .bootloader)

Data:          0 bytes (0.0% Full)
(.data + .bss + .noinit)
```
Me doy por satisfecho.

Por supuesto lo anterior tiene desventajas y es un pequeño incremento en la 
velocidad de acceso, lo que no es un problema pues las tablas solo se
usan una vez cuando se crea un pin, así hemos llegado a otro dogma de la 
programación:

_La optimización temprana es el origen de todos los males_

[gpio](https://github.com/logys/m328pDrivers/blob/main/src/gpio.c)

[avr/pgmspace.h](https://www.nongnu.org/avr-libc/user-manual/pgmspace_8h.html)
