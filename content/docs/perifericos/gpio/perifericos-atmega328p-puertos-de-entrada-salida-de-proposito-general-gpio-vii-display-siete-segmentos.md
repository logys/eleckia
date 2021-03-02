---
title: 'Display Siete Segmentos VI.'
date: Fri, 22 May 2020 07:49:50 +0000
draft: false
tags: ['7segmentos', 'avr', 'Programación']
---

La función setNumber puede ser mejorada, antes de ello quiero hacer una modificación de diseño, el módulo display7 hace varias cosas, configura los puertos, convierte de decimal a la colección adecuada de pines de salida y finalmente escribe las salidas a los puertos, este último par de acciones en nuestra implementación se realizan en la misma función setNumber, se recomienda que las funciones sean responsables de una sola acción bien definida.

Tomando en cuenta lo anterior sugiero lo siguiente,

```
void display7\_setNumber(short number)
{
	uint8\_t pines7 = decoder7(number);
	writeTo7(pines7);
}
```

de esta manera separamos las dos acciones y la función setNumber únicamente se encarga de controlar el flujo. Nos concentramos en implementar la función decoder7, recibe un número decimal y lo convierte en un código (codificación) 7 segmentos. Cuando pasamos el número 5 por ejemplo, decoder7 retorna el valor binario 0b0110 1101 que en decimal corresponde a 109, la correspondencia es la siguiente

```
 GFE DCBA
0b0110 1101 //Cada bit corresponde a un led

//para el número cero
   GFE DCBA
0b0010 1111 //Cada bit corresponde a un led 
```

la implementación es de la forma

```
static uint8\_t decoder7(short number)
{
	if(number == 0)
		return 0b0111111;
	else if(number == 5)
		return 0b01101101;
}
```

la notación 0b, es una notación no estándar, prefiero utilizar una notación hexadecimal,

```
static uint8\_t decoder7(short number)
{
	if(number == 0)
		//0b0011 1111
		return 0x3f;
	else if(number == 1)
		//0b0000 0110
		return 0x06;
	else if(number == 2)
		//0b0101 1011
		return 0x5A;
	else if(number == 3)
		//0b0100 1111
		return 0x4F;
	else if(number == 4)
		//0b0110 0110
		return 0x66;
	else if(number == 5)
		//0b0110 1101
		return 0x6D;
	else if(number == 6)
		//0b0111 1101
		return 0x7D;
	else if(number == 7)
		//0b0000 0111
		return 0x07;
	else if(number == 8)
		//0b0111 1111
		return 0x7F;
	else if(number == 9)
		//0b0110 1111
		return 0x6F;
}
```

La solución está bien, pero es horriblemente grande, efectivamente requerimos un nuevo look, la refactorización es muy simple, guardamos todos los valores en un arreglo y retornamos el valor al cual apunta el indice,

```
static uint8\_t decoder7(short number)
{
	const uint8\_t code7\[10\] = {
		 0x3f,	//0 0b0011 1111
		 0x06,	//1 0b0000 0110
		 0x5B,	//2 0b0101 1011
		 0x4F,	//3 0b0100 1111
		 0x66,	//4 0b0110 0110
		 0x6D,	//5 0b0110 1101
		 0x7D,	//6 0b0111 1101
		 0x07,	//7 0b0000 0111
		 0x7F,	//8 0b0111 1111
		 0x6F	//9 0b0110 1111
	};
	return code7\[number\];
}
```

mucho mejor. Ahora implementaremos writeTo7, debemos tomar los pines retornados por decoder7 y escribirlos en los puertos PORTD y PORTB, sabemos que

```
decorder7 retorna 8 bits de la forma
0b0GFE DCBA
y nuestros puertos, aceptan 8 bits de la forma
PORTD
0bFEDC BA00
PORTB
0b0000 000G
```

la solución consiste en corrimientos, se propone

```
static void writeTo7(uint8\_t pines\_to\_set)
{
	uint8\_t port\_D;
	port\_D = pines\_to\_set<<2;
	uint8\_t port\_B;
	port\_B = pines\_to\_set >> 6;
	pinoutLevelLow();
	setBits(&PORTD, port\_D);
	setBits(&PORTB, port\_B);
}
```

refactorizamos las primeras 4 lineas en una sola función, esto para evitar tener más de una acción dentro de la función write7

```
static getLedsToPorts(uint8\_t \* port\_D, uint8\_t \* port\_B, uint8\_t pines\_to\_set)
{
	\*port\_D = pines\_to\_set<<2;
	\*port\_B = pines\_to\_set >> 6;
}
static void writeTo7(uint8\_t pines\_to\_set)
{
	uint8\_t port\_D;
	uint8\_t port\_B;
	getLedsToPorts(&port\_D, &port\_B, pines\_to\_set);
	pinoutLevelLow();
	setBits(&PORTD, port\_D);
	setBits(&PORTB, port\_B);
}
```

nuestro programa esta casi listo, como última modificación debemos agregar un guardia a la función setNumber, ¿que pasa si convertimos el número 10 o el número negativo -1?, setNumber(10), el comportamiento no esta definido por nosotros, para evitar comportamientos no definidos agregamos una condición que nos maneje dichos casos

```
void display7\_setNumber(short number)
{
	if(number > 9 || number < 0){
		return ;
	}
	uint8\_t pines7 = decoder7(number);
	writeTo7(pines7);
}
```

en mi caso, tan solo le indico que quiero ignorar tales casos. Hemos terminado nuestra implementación, el programa funciona perfectamente bien, cumple con las expectativas (las especificaciones), podemos modificar la función main y cambiar el orden de los números decimales sin el más mínimo miedo de pensar que el módulo se romperá, literalmente aislamos al display de el programa principal.

Pero, ¿es necesario todo este trabajo para un simple display de 7 segmentos?, pues eso depende de tu proyecto y un juicio de valores personal, menciono lo que logramos

*   Aislamos el módulo.
*   El módulo se puede utilizar en otros módulos.
*   Los usuarios del módulo tan solo utilizaran 3 funciones.
*   Minimizamos la posibilidad de errores.

Todo esto en 292 bytes, que corresponden a menos del 1% del espacio disponible. Pienso que no esta nada mal, anexo la implementación completa

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
#define led\_G (1<<PB0)

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
static void pinoutLevelHigh(void)
{
	actionOnPorts(setBits, LEVEL);
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
static uint8\_t decoder7(short number)
{
	const uint8\_t code7\[10\] = {
		 0x3f,	//0 0b0011 1111
		 0x06,	//1 0b0000 0110
		 0x5B,	//2 0b0101 1011
		 0x4F,	//3 0b0100 1111
		 0x66,	//4 0b0110 0110
		 0x6D,	//5 0b0110 1101
		 0x7D,	//6 0b0111 1101
		 0x07,	//7 0b0000 0111
		 0x7F,	//8 0b0111 1111
		 0x6F	//9 0b0110 1111
	};
	return code7\[number\];
}
static getLedsToPorts(uint8\_t \* port\_D, uint8\_t \* port\_B, uint8\_t pines\_to\_set)
{
	\*port\_D = pines\_to\_set<<2;
	\*port\_B = pines\_to\_set >> 6;
}
static void writeTo7(uint8\_t pines\_to\_set)
{
	uint8\_t port\_D;
	uint8\_t port\_B;
	getLedsToPorts(&port\_D, &port\_B, pines\_to\_set);
	pinoutLevelLow();
	setBits(&PORTD, port\_D);
	setBits(&PORTB, port\_B);
}
void display7\_setNumber(short number)
{
	if(number > 9 || number < 0){
		return ;
	}
	uint8\_t pines7 = decoder7(number);
	writeTo7(pines7);
}
```

105 líneas de trabajo esfuerzo e ilusiones, por supuesto es mejorable, pero la única forma de mejorar es haciendo muchos programas, de eso no se puede escapar.

Todo el trabajo fue basado en un especificación bien definida, pero rara vez tenemos tal especificación, la mayoría de las veces no sabemos que rumbo debe tomar el diseño, en la siguiente entrada cambiaremos la especificación, en esta ocasión el pines no estarán definidos, es decir el usuario del módulo deberá especificarlos antes de usar setNumber.