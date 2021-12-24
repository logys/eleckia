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
DDRB |= (1<<PB0);
DDRD |= (1<<PD2)|(1<<PD3)|(1<<PD4)|
        (1<<PD5)|(1<<PD6)|(1<<PD7);
```

Ahora solo encendemos los leds, poniendo en alto los pines requeridos, por 
ejemplo para el número cinco, encendemos los segmentos a,c,d,f,g.

![Cinco](/eleckia/img/7segdia5.png)

Para ello escribimos a los puertos PORTB y PORTD los pines indicados

```C
PORTB |= (1<<PB0);
PORTD |= (1<<PD2)|(1<<PD4)|(1<<PD5)|(1<<PD7);
```

El código completo luce como el siguiente,

```C
#include<avr/io.h>

int main(void)
{
       DDRB |= (1<<PB0);
       DDRD |= (1<<PD2)|(1<<PD3)|(1<<PD4)|
               (1<<PD5)|(1<<PD6)|(1<<PD7);
       PORTB |= (1<<PB0);
       PORTD |= (1<<PD2)|(1<<PD4)|(1<<PD5)|(1<<PD7);
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

![](/eleckia/img/7segdep.png)

De tal forma que podemos utilizarla dentro de nuestra aplicación con un simple 
include, pensar en forma modular tiene grandes ventajas y conforme crece el 
proyecto se vuelve la única forma sostenible de escribir código, la primer gran
ventaja es la posibilidad de reutilizar código, seria absurdo escribir un 
controlador de 7 segmentos en cada proyecto nuevo, la segunda ventaja es la
capacidad de extender el código ya escrito, la tercer gran ventaja es que el 
código es más "mantenible", entre muchas otras que se irán comentando.

Para nuestro propósito un módulo es un ente de software que está débilmente acoplado, 
autocontenido y es altamente cohesivo, dicho de otra forma, la función main no
tiene conocimiento de las variables y funciones del módulo, salvo por un 
conjunto de funciones comúnmente llamadas interfaz. Una bonita interfaz para 
nuestro modulo puede ser

```C
void display7_init(void);
void display7_showNumber(short const number);
void display7_destroy(void);
```

Elegir buenos nombres es una parte fundamental para que nuestro código sea de 
alta calidad, estoy seguro que al leer las funciones anteriores de inmediato
formaste una idea de lo que hacen y a que pertenecen. Sin pensarlo hemos llegado
a uno de los grandes dogmas de la programación y de la resolución de problemas 
en general, 

_Divide el problema en problemas más pequeños_

Ni tardos ni perezosos modificamos nuestro programa y agregamos el módulo,

```C
#include<avr/io.h>

void display7_init(void);
void display7_showNumber(short number);
void display7_destroy(void);

int main(void)
{
       DDRB |= (1<<PB0);
       DDRD |=  (1<<PD2)|(1<<PD3)|(1<<PD4)|
                (1<<PD5)|(1<<PD6)|(1<<PD7);
       PORTB |= (1<<PB0);
       PORTD |= (1<<PD2)|(1<<PD4)|(1<<PD5)|(1<<PD7);
       return 0;
}

void display7_init(void)
{
}
void display7_showNumber(short number)
{
}
void display7_destroy(void)
{
}
```

Este código compila sin problemas pero no hace el trabajo requerido, al 
principio agregamos los prototipos del módulo, pero aun no los llamamos en la 
función main, tampoco hemos implementado dichas funciones. Antes de definir la 
interfaz, anteriormente indique que la función main no tiene conocimiento de las 
funciones y variables de nuestro módulo, si escribimos el código en el mismo 
fichero, el usuario de la función main tendrá a la vista todas las funciones y 
variables y probablemente tenga la tentación de usarlas, una buena idea para 
evitar que esto suceda es utilizar un par de archivos extra exclusivos para 
nuestro módulo.

## Crear la biblioteca

No se si ya lo había mencionado pero odio el entorno de desarrollo de Arduino, 
en esta ocasión por la forma tan pobre de manejar ficheros. Continuando, vamos 
a mover nuestro módulo en un par de archivos, creamos dos ficheros cuyo nombre 
deje en claro que hay escrito en él, un buen nombre es display7,

```Bash
$touch display7.c
$touch display7.h
```

Ahora en nuestro entorno de desarrollo incluimos los dos ficheros, para ello 
vamos al menú **Programa->Añadir fichero** y añadimos ambos ficheros, ambos 
aparecerán en nuestro entorno de desarrollo y podrán ser editados desde el mismo,
movemos desde el fichero principal el módulo hacia los nuevos ficheros, 
los prototipos van en él .h y las definiciones al .c, de esta manera tenemos 
los tres ficheros siguientes

```C++
//proyecto.ino
#include<avr/io.h>
int main(void)
{
       DDRB |= (1<<PB0);
       DDRD |= (1<<PD2)|(1<<PD3)|(1<<PD4)|
               (1<<PD5)|(1<<PD6)|(1<<PD7);
       PORTB |= (1<<PB0);
       PORTD |= (1<<PD2)|(1<<PD4)|(1<<PD5)|(1<<PD7);
       return 0;
}
```
```C
//display7.h
void display7_init(void);
void display7_showNumber(short number);
void display7_destroy(void);
```
```C
//display7.c
void display7_init(void)
{
}
void display7_showNumber(short number)
{
}
void display7_destroy(void)
{
}
```

Nuevamente compilamos el programa y no nos dará ningún error, salvo el pequeño 
detalle de que no hace nada. Para poder acceder a la interfaz del módulo, es 
necesario incluirla en nuestro fichero principal, basta con agregar la directiva
`#include"display7.h"` antes de la función main, de esta forma el módulo puede ser
desarrollado de forma independiente y paralela al programa principal, 
modifiquemos nuestro .ino para usar la interfaz

```C++
#include<utils/delay.h>
#include"display7.h"

int main(void)
{
	display7_init();
	while(1){
		display7_showNumber(0);
		_delay_ms(1000);
		display7_showNumber(1);
		_delay_ms(1000);
	}
}
```

Pienso que el código se ve definitivamente más limpio y muestra claramente que 
se está haciendo, sin embargo si tratamos de compilar nos dará un error, 
similar a este

```Bash
undefined reference to `display7_init()'
```

En pocas palabras el compilador (en realidad el linker) no encuentra las 
definiciones del módulo, las definiciones están en el fichero .c, como 
mencionamos en entradas anteriores el entorno Arduino en realidad compila 
ficheros C++, pero como somos tercos y queremos programar en C debemos indicarle
al compilador que nuestros ficheros no son C++, esto es muy sencillo, basta con
añadir

```C
extern "C" {
```

En el fichero .h donde están los prototipos y cerrar después del ultimo 
prototipo con un

```C
}
```

El archivo .h queda

```C
extern "C" {
void display7_init(void);
void display7_showNumber(short const number);
void display7_destroy(void);
}
```

Compilamos y esta vez no obtendremos ningún error. De esta forma el compilador 
hará lo adecuado para poder usar el módulo, la solución sirve para nuestros 
propósitos, sin embargo en el futuro, desarrollaremos nuestros programas con un
compilador de "C", el compilador no entenderá la línea extern "C" y nos dará un
error, ¿cómo hacemos que el módulo funcione con el compilador de C y también con
el de C++?, nuevamente la solución es sencilla, cuando usamos un compilador de 
C++, este pasa una definición, explicitamente

```C
#define __cplusplus
```

lo anterior no sucede cuando compilamos con un compilador de C, aprovechando 
esta definición podemos usar la directiva

```C
#ifdef ALGO
  //si ALGO fue definido, coloca estas lineas
#endif
```

Modificamos el fichero .h y escribimos

```C
//display7.h
#ifdef __cplusplus
extern "C" {
#endif

void display7_init(void);
void display7_showNumber(short const number);
void display7_destroy(void);

#ifdef __cplusplus
}
#endif
```

El fichero está casi listo, funciona con ambos compiladores, sin embargo tiene 
un gran problema, ¿qué pasa si por error incluimos más de una vez nuestra 
interfaz?, la respuesta es que nos indicara un error de doble declaración, 
nuevamente nos apoyamos en la directiva #define, de la forma

```C
#ifndef MODULO
#define MODULO

//Aquí las definiciones

#endif
```

Significa, si no esta definido, define y coloca la interfaz, de lo contrario si
ya esta definido no hagas nada, nuestra implementación queda así

```C
//display7.h
#ifndef DISPLAY7_H
#define DISPLAY7_H

#ifdef __cplusplus
extern "C" {
#endif

void display7_init(void);
void display7_showNumber(short number);
void display7_destroy(void);

#ifdef __cplusplus
}
#endif
#endif//DISPLAY7_H
```

El fichero principal queda así

```C++
//proyecto.ino
//Necesario para _delay_ms(), frecuencia de nuestro arduino
#define F_CPU 16000000UL 
#include<util/delay.h> //Nos permite usar delay_ms()
#include"display7.h"

int main(void)
{
	display7_init();
	while(1){
		display7_showNumber(0);
		_delay_ms(1000);
		display7_showNumber(1);
		_delay_ms(1000);
	}
}
```

Las definiciones están en el fichero .c, debemos agregar la interfaz a este 
fichero también con la directiva #include

```C
//display7.c
#include"display7.h"
void display7_init(void)
{
}
void display7_showNumber(short const number)
{
}
void display7_destroy(void)
{
}
```

En un entorno de desarrollo decente el fichero .c nos hubiera dado problemas 
pues no esta permitido compilar una implementación sin indicar a que interfaz 
pertenece, sin embargo el entorno de Arduino nos auto corrige el error, esto es
otra cosa por lo que no me gusta Arduino, me oculta mis errores. Nuestro código 
ahora luce más modular en un diagrama luce así

![Diseño en modulo](/eleckia/img/7segmod.png)


Como se ve en la imagen ahora proyecto.ino (función main) depende de la interfaz,
mientras que display7.c la implementa, dicho de otra forma, proyecto.ino no sabe
que hay en display7.c, el solo conoce lo que hay en display7.h, de esta manera 
podemos reutilizar el módulo en otros módulos, sin miedo a romper el código 
fuera de éste y muy importante sin tener que reescribir código extra.

Establecido lo anterior, ahora si nos toca implementar el módulo.


## Implementando la biblioteca
Nuestro trabajo consiste en implementar las funciones del módulo contamos con un
par de funciones, una para iniciar y otra para cerrar el módulo y una tercera 
para mostrar el número, la interfaz es muy sencilla y para nuestro propósito es 
más que suficiente. Algo que olvide mencionar y que es importante, la interfaz 
del módulo evoluciona, pues el desarrollo mismo provee realimentación importante.

Es muy común que los módulos contengan dos funciones, init y close o create y 
destroy, el trabajo de estas dos funciones es llevar al módulo a un estado 
conocido y liberar los recursos respectivamente. Para el módulo display7 ambas 
funciones son simples.

En la función destroy, los recursos que se pueden liberar
son los puertos y los pines, sin embargo en este caso es de poca utilidad, pues
aunque liberemos los recursos nadie puede usarlos, esto no quiere decir que la 
función destroy siempre sera tan simple, en módulos más complejos tiene una 
importancia fundamental sobre todo la utilizar memoria dinámica.

La función init tiene la misión de establecer las condiciones adecuadas para que
el resto del módulo pueda hacer su trabajo, modificara los registros de los 
puertos para establecerlos como salidas y con nivel bajo. Por ahora sabemos la
correspondencia de los pines, pero ¿qué pasa si los encargados del hardware 
deciden cambiar alguno o todos los pines?, más adelante veremos este escenario.

Iniciemos tratando de configurar los dos primeros pines, PD2 y PD3, como
salida y en nivel bajo,

```C
//display7.c
#include "display7.h"
#include <avr/io.h>

void display7_init(void)
{
	short pin = PD2;
	DDRD |= (1<<pin);
	PORTD &= ~(1<<pin);

	pin = PD3;
	DDRD |= (1<<pin);
	PORTD &= ~(1<<pin);
}
void display7_destroy(void)
{
}
void display7_showNumber(short number)
{
}
```
Excelente funciona para los dos, pero las dos asignaciones son prácticamente 
idénticas, en la programación la duplicación de ideas debe evitarse pues es
una fuente de errores. Se propone crear una función que nos ayude a evitar
la repetición.

```C
//display7.c
#include "display7.h"
#include <avr/io.h>

static void setPinOutputAndLow(short const pin);

void display7_init(void)
{

	setPinOutputAndLow(PD2);
	setPinOutputAndLow(PD3);
}

static void setPinOutputAndLow(short const pin)
{
	DDRD |= (1<<pin);
	PORTD &= ~(1<<pin);
}

void display7_destroy(void)
{
}

void display7_showNumber(short const number)
{
}
```

Mucho mejor, ¿pero que pasa con el pin que pertenece al puerto B?,
no podemos pasar PB0 al registro DDRD y PORTD, mi propuesta es
"rotular" los pines del microcontrolador en una tabla y asignar su
nemotécnico, 

```C
//display7.c
#include "display7.h"
#include <avr/io.h>

short const pin_table[28] = {
	PC6,  /* pin 1*/
	PD0, 
	PD1, 
	PD2, 
	PD3, 
	PD4, 
	-1,
	-1,
	PB6, 
	PB7, 
	PD5, 
	PD6, 
	PD7, 
	PB0,  /*pin 14*/
	PB1,  
	PB2, 
	PB3, 
	PB4, 
	PB5, 
	-1,
	-1,
	-1,
	PC0, 
	PC1, 
	PC2, 
	PC3, 
	PC4, 
	PC5,  /*pin 28*/
};

static void setPinOutputAndLow(short const pin);

void display7_init(void)
{
	setPinOutputAndLow(4); //PD2
	setPinOutputAndLow(5); //PD3
	setPinOutputAndLow(14); //PB0
}

static void setPinOutputAndLow(short const pin)
{
	DDRD |= (1<<pin);
	PORTD &= ~(1<<pin);
}

void display7_destroy(void)
{
}

void display7_showNumber(short const number)
{
}
```
Ahora ajustaremos _setPinOutputAndLow_. Tenemos una tabla con cada uno de los 
pines, sacamos ventaja y discriminamos a los puertos por su agrupamiento.


```C
static void setPinOutputAndLow(short const pin)
{
	if((pin >= 2 && pin <= 6) || pin >= 11 && pin <= 13){
		//puertoD
	}else if(pin == 8 || pin == 9 || (pin >= 14 && pin <= 19)){
		//puertoB
	}else if(pin == 1 || (pin >= 23 && pin <= 28))
		//puertoC
}
```
Fácil, ahora nos interesa modificar los registros correspondientes al puerto y el
bit correspondiente al pin, para ello basta con acceder a la tabla con _pin-1_.

```C
#include "display7.h"
#include <avr/io.h>

short const pin_table[28] = {
	PC6,  /* pin 1*/
	PD0, 
	PD1, 
	PD2, 
	PD3, 
	PD4, 
	-1,
	-1,
	PB6, 
	PB7, 
	PD5, 
	PD6, 
	PD7, 
	PB0,  /*pin 14*/
	PB1,  
	PB2, 
	PB3, 
	PB4, 
	PB5, 
	-1,
	-1,
	-1,
	PC0, 
	PC1, 
	PC2, 
	PC3, 
	PC4, 
	PC5,  /*pin 28*/
};

static void setPinOutputAndLow(short const pin);

void display7_init(void)
{
i	setPinOutputAndLow(4); //PD2
	setPinOutputAndLow(5); //PD3
	setPinOutputAndLow(6); //PD4
	setPinOutputAndLow(11); //PD5
	setPinOutputAndLow(12); //PD6
	setPinOutputAndLow(13); //PD7
	setPinOutputAndLow(14); //PB0
}

static void setPinOutputAndLow(short const pin)
{
	if((pin >= 2 && pin <= 6) || pin >= 11 && pin <= 13){
		DDRD |= (1<<pin_table[pin-1]);
		PORTD &= ~(1<<pin_table[pin-1]);
	}else if(pin == 8 || pin == 9 || (pin >= 14 && pin <= 19)){
		DDRB |= (1<<pin_table[pin-1]);
		PORTB &= ~(1<<pin_table[pin-1]);
	}else if(pin == 1 || (pin >= 23 && pin <= 28)){
		DDRC |= (1<<pin_table[pin-1]);
		PORTC &= ~(1<<pin_table[pin-1]);
	}
}

void display7_destroy(void)
{
}

void display7_showNumber(short const number)
{
}
```
## Ceder la responsabilidad

Funciona, hemos inicializado nuestros pines, ahora requerimos implementar la 
función `display7_showNumber`, pero me gustaría pasar todo el trabajo anterior a
un nuevo módulo, requiero que `display7` se encargue solo de mostrar los 
números, no de malabarear los registros, la propuesta es la siguiente.

![gpio](/eleckia/img/gpio.png)

Dentro de gpio me interesan dos funciones,

```C
typedef enum {INPUT, OUTPUT} DIRECTION;
typedef enum {LOW, HIGH} LEVEL;

void gpio_setDirection(short const pin, DIRECTION);
void gpio_setLevel(short const pin, LEVEL);
```
al más puro estilo Arduino, así que trasladamos todo el trabajo de pines y 
registros, quedando los módulos como sigue:

```C
//display7.c
#include "display7.h"
#include "gpio.h"

static void setPinOutputAndLow(short const pin);

void display7_init(void)
{
	setPinOutputAndLow(4); //PD2
	setPinOutputAndLow(5); //PD3
	setPinOutputAndLow(6); //PD4
	setPinOutputAndLow(11); //PD5
	setPinOutputAndLow(12); //PD6
	setPinOutputAndLow(13); //PD7
	setPinOutputAndLow(14); //PB0
}

static void setPinOutputAndLow(short const pin)
{
	gpio_setDirection(pin, OUTPUT);
	gpio_setLevel(pin, LOW);
}

void display7_destroy(void)
{
}

void display7_showNumber(short const number)
{
}
```
```C
//gpio.h
#ifndef GPIO_H
#define GPIO_H
typedef enum {INPUT, OUTPUT} DIRECTION;
typedef enum {LOW, HIGH} LEVEL;

void gpio_setDirection(short const pin, DIRECTION);
void gpio_setLevel(short const pin, LEVEL);

#endif // GPIO_H
```
```C
//gpio.c
#include "gpio.h"
#include <avr/io.h>

short const pin_table[28] = {
	PC6,  /* pin 1*/
	PD0, 
	PD1, 
	PD2, 
	PD3, 
	PD4, 
	-1,
	-1,
	PB6, 
	PB7, 
	PD5, 
	PD6, 
	PD7, 
	PB0,  /*pin 14*/
	PB1,  
	PB2, 
	PB3, 
	PB4, 
	PB5, 
	-1,
	-1,
	-1,
	PC0, 
	PC1, 
	PC2, 
	PC3, 
	PC4, 
	PC5  /*pin 28*/
};


void gpio_setDirection(short const pin, DIRECTION direction)
{
	if(direction == OUTPUT){
		if((pin >= 2 && pin <= 6) || pin >= 11 && pin <= 13){
			DDRD |= (1<<pin_table[pin-1]);
		}else if(pin == 8 || pin == 9 || (pin >= 14 && pin <= 19)){
			DDRB |= (1<<pin_table[pin-1]);
		}else if(pin == 1 || (pin >= 23 && pin <= 28)){
			DDRC |= (1<<pin_table[pin-1]);
		}
	}else if(direction == INPUT){
		if((pin >= 2 && pin <= 6) || pin >= 11 && pin <= 13){
			DDRD &= ~(1<<pin_table[pin-1]);
		}else if(pin == 8 || pin == 9 || (pin >= 14 && pin <= 19)){
			DDRB &= ~(1<<pin_table[pin-1]);
		}else if(pin == 1 || (pin >= 23 && pin <= 28)){
			DDRC &= ~(1<<pin_table[pin-1]);
		}
	}
}

void gpio_setLevel(short const pin, LEVEL level)
{
	if(level == HIGH){
		if((pin >= 2 && pin <= 6) || pin >= 11 && pin <= 13){
			PORTD |= (1<<pin_table[pin-1]);
		}else if(pin == 8 || pin == 9 || (pin >= 14 && pin <= 19)){
			PORTB |= (1<<pin_table[pin-1]);
		}else if(pin == 1 || (pin >= 23 && pin <= 28)){
			PORTC |= (1<<pin_table[pin-1]);
		}
	}else if(level == LOW){
		if((pin >= 2 && pin <= 6) || pin >= 11 && pin <= 13){
			PORTD &= ~(1<<pin_table[pin-1]);
		}else if(pin == 8 || pin == 9 || (pin >= 14 && pin <= 19)){
			PORTB &= ~(1<<pin_table[pin-1]);
		}else if(pin == 1 || (pin >= 23 && pin <= 28)){
			PORTC &= ~(1<<pin_table[pin-1]);
		}
	}
}

```
la responsabilidad fue movida con éxito, pero el modulo _gpio_  dista de estar
terminado, la duplicación es obvia, esos _if_ y _else if_ se ven reducibles, 
se repiten 4 veces y son prácticamente idénticos.

Me apoyare de dos funciones locales, anteponiendo la palabra _static_ en la 
declaración le indicamos al compilador que el alcance de estas
funciones únicamente abarca el fichero, también indicamos a la tabla
de pines como local.
```C
typedef enum {DIRECTION_TYPE, LEVEL_TYPE} REGISTER_TYPE;

static char portOfPin(short const pin);
static volatile uint8_t *registerOfPort(char const port, REGISTER_TYPE);
```
la primer función _portOfPin_ indica en forma de carácter el puerto al que
pertenece el pin:
```C
static char portOfPin(short const pin)
{
	if((pin >= 2 && pin <= 6) || pin >= 11 && pin <= 13){
		return 'd';
	}else if(pin == 8 || pin == 9 || (pin >= 14 && pin <= 19)){
		return 'b';
	}else if(pin == 1 || (pin >= 23 && pin <= 28)){
		return 'c';
	}
}
``` 
bastante simple. La segunda función es más compleja, los registros son direcciones
de memoria en el microcontrolador, una clara diferencia de estos con respecto a 
otras localidades es que agentes externos puede cambiar el estado de los registros,
port ejemplo el registro _PIND_ puede ser cambiado por un pulsador en el pin _PD1_,
para asegurar la consistencia de la ejecución del programa utilizamos la 
palabra _volatile_ en aquellas variables que apunten a un registro que puede ser
cambiado externamente, entonces la función _registerOfPort_ retorna un apuntador
a un registro y lo calificamos como volatile, la implementación deja claro su
funcionamiento,
```C
static volatile uint8_t * registerOfPort(char const port, REGISTER_TYPE type)
{
	if(type == DIRECTION_TYPE){
		if(port == 'd')
			return &DDRD;
		else if(port == 'b')
			return &DDRB;
		else if(port == 'c')
			return &DDRC;
	}else if(type == LEVEL_TYPE){
		if(port == 'd')
			return &PORTD;
		else if(port == 'b')
			return &PORTB;
		else if(port == 'c')
			return &PORTC;
	}
}
```
vamos bien, ahora escribimos a los puertos, en el caso de establecer el pin 
como salida o entrada, obtenemos el puerto desde el pin, obtenemos el registro
desde el puerto y finalmente actualizamos el registro.
```C
void gpio_setDirection(short const pin, DIRECTION direction)
{
	char port = portOfPin(pin);
	volatile uint8_t *register_ = registerOfPort(port, DIRECTION_TYPE);

	if(direction == OUTPUT){
		*register_ |= 1<<pin_table[pin-1];
	}else if(direction == INPUT){
		*register_ &=~(1<<pin_table[pin-1]);
	}
}
```
Quizá no sea la mejor implementación, pero cumple bien su cometido y pienso que 
es clara y concisa, la implementación completa es la siguiente:
```C
//gpio.c
#include "gpio.h"
#include <avr/io.h>

static short const pin_table[28] = {
	PC6, PD0, PD1, PD2, PD3, PD4, -1, -1,
	PB6, PB7, PD5, PD6, PD7, PB0, PB1, PB2, 
	PB3, PB4, PB5, -1, -1, -1, PC0, PC1, 
	PC2, PC3, PC4, PC5  
};

typedef enum {DIRECTION_TYPE, LEVEL_TYPE} REGISTER_TYPE;

static char portOfPin(short const pin);
static volatile uint8_t *registerOfPort(char const port, REGISTER_TYPE);

void gpio_setDirection(short const pin, DIRECTION direction)
{
	char port = portOfPin(pin);
	volatile uint8_t *register_ = registerOfPort(port, DIRECTION_TYPE);

	if(direction == OUTPUT){
		*register_ |= 1<<pin_table[pin-1];
	}else if(direction == INPUT){
		*register_ &=~(1<<pin_table[pin-1]);
	}
}

static char portOfPin(short const pin)
{
	if((pin >= 2 && pin <= 6) || pin >= 11 && pin <= 13){
		return 'd';
	}else if(pin == 8 || pin == 9 || (pin >= 14 && pin <= 19)){
		return 'b';
	}else if(pin == 1 || (pin >= 23 && pin <= 28)){
		return 'c';
	}
}

static volatile uint8_t * registerOfPort(char const port, REGISTER_TYPE type)
{
	if(type == DIRECTION_TYPE){
		if(port == 'd')
			return &DDRD;
		else if(port == 'b')
			return &DDRB;
		else if(port == 'c')
			return &DDRC;
	}else if(type == LEVEL_TYPE){
		if(port == 'd')
			return &PORTD;
		else if(port == 'b')
			return &PORTB;
		else if(port == 'c')
			return &PORTC;
	}
}

void gpio_setLevel(short const pin, LEVEL level)
{
	char port = portOfPin(pin);
	volatile uint8_t *register_ = registerOfPort(port, LEVEL_TYPE);

	if(level == HIGH){
		*register_ |= 1<<pin_table[pin-1];
	}else if(level == LOW){
		*register_ &=~(1<<pin_table[pin-1]);
	}
}
```
## Decimal a 7 segmentos
Resuelto el problema de inicializar los pines, concentrémonos en encontrar
las secuencias de pines para mostrar el número deseado. El problema implica
convertir un número decimal entre 0 y 9 a siete llamadas a la función _gpio_setLevel_,
lo primero que se me ocurre es esto
```C
void display7_showNumber(short const number)
{
	if( number == 0){
		gpio_setLevel(4, HIGH);
		gpio_setLevel(5, HIGH);
		gpio_setLevel(6, HIGH);
		gpio_setLevel(11, HIGH);
		gpio_setLevel(12, HIGH);
		gpio_setLevel(13, HIGH);
		gpio_setLevel(14, LOW);
	}else if( number == 1){
		gpio_setLevel(4, LOW);
		gpio_setLevel(5, HIGH);
		gpio_setLevel(6, HIGH);
		gpio_setLevel(11, LOW);
		gpio_setLevel(12, LOW);
		gpio_setLevel(13, LOW);
		gpio_setLevel(14, LOW);
	}
	.
	.
	.
}
```
ya te la sabes, hay que eliminar la duplicación, una nueva función que
ejecute los pasos anteriores estaría bien, claramente hay dos listas, una
de pines y otra de niveles, serán los argumentos para la nueva función:

```C
static void writeToDisplay(short const pins[], LEVEL const levels[])
{
	for(int i = 0; i < 7; i++){
		gpio_setLevel(pins[i], levels[i]);
	}
}
```
ahora _showNumber_ queda así,
```C
void display7_showNumber(short const number)
{
	if( number == 0){
		short pins[] = {4, 5, 6, 11, 12, 13, 14};
		LEVEL levels[] = {HIGH, HIGH, HIGH, HIGH, HIGH, HIGH, LOW};
		writeToDisplay(pins, levels);
	}else if( number == 1){
		short pins[] = {4, 5, 6, 11, 12, 13, 14};
		LEVEL levels[] = {LOW, HIGH, HIGH, LOW, LOW, LOW, LOW};
		writeToDisplay(pins, levels);
	}
	.
	.
	.
}
```
podemos sacar _short pins_ de los if's, pero me prefiero sacarlo de la función 
_showNumber_ y dejarla con alcance de archivo, con la intención de utilizarla en
_display7_init_.
```C
void display7_init(void)
{
	for(int i = 0; i < 7; i++){
		setPinOutputAndLow(pins[i]);
	}
}

void display7_showNumber(short const number)
{
	if( number == 0){
		LEVEL levels[] = {HIGH, HIGH, HIGH, HIGH, HIGH, HIGH, LOW};
		writeToDisplay(pins, levels);
	}else if( number == 1){
		LEVEL levels[] = {LOW, HIGH, HIGH, LOW, LOW, LOW, LOW};
		writeToDisplay(pins, levels);
	}
	.
	.
	.
}
```
Pienso que el problema esta resuelto, quizá el arreglo levels no es tan eficiente,
pero la verdad no quiero invertirle más tiempo, pues cumple bien con el objetivo,
si lo requiero, en el futuro vengo y lo modifico. La condición de los _for_ no me
convence, la cambio por
```C
static short const pins_length = sizeof(pins)/sizeof(pins[0]);

void display7_init(void)
{
	for(int i = 0; i < pins_length; i++){
		setPinOutputAndLow(pins[i]);
	}
}
```
compilamos y vemos que el programa ocupa algo más del 10% de memoria ram, eso es
algo inaceptable, fui muy ingenuo al pensar que los arreglos no serian un problema,
antes de resolver ese problema, quiero hacer algo que me esta haciendo ruido desde
hace algunas lineas.

## Establecer los pines al inicio
Actualmente el código luce como sigue:
```C
//display7.c
#include "display7.h"
#include "gpio.h"

static short const pins[] = {4, 5, 6, 11, 12, 13, 14};
static short const pins_length = sizeof(pins)/sizeof(pins[0]);

static void setPinOutputAndLow(short const pin);
static void writeToDisplay(short const pins[], LEVEL const levels[]);

void display7_init(void)
{
	for(int i = 0; i < pins_length; i++){
		setPinOutputAndLow(pins[i]);
	}
}

static void setPinOutputAndLow(short const pin)
{
	gpio_setDirection(pin, OUTPUT);
	gpio_setLevel(pin, LOW);
}

void display7_showNumber(short const number)
{
	/*				A     B	    C     D     E     F    G */
	if( number == 0){
		LEVEL const levels[] = {HIGH, HIGH, HIGH, HIGH, HIGH, HIGH, LOW};
		writeToDisplay(pins, levels);
	}else if( number == 1){
		LEVEL const levels[] = {LOW, HIGH, HIGH, LOW, LOW, LOW, LOW};
		writeToDisplay(pins, levels);
	}else if( number == 2){
		LEVEL const levels[] = {HIGH, HIGH, LOW, HIGH, HIGH, LOW, HIGH};
		writeToDisplay(pins, levels);
	}else if( number == 3){
		LEVEL const levels[] = {HIGH, HIGH, HIGH, HIGH, LOW, LOW, HIGH};
		writeToDisplay(pins, levels);
	}else if( number == 4){
		LEVEL const levels[] = {LOW, HIGH, HIGH, LOW, LOW, HIGH, HIGH};
		writeToDisplay(pins, levels);
	}else if( number == 5){
		LEVEL const levels[] = {HIGH, LOW, HIGH, HIGH, LOW, HIGH, HIGH};
		writeToDisplay(pins, levels);
	}else if( number == 6){
		LEVEL const levels[] = {HIGH, LOW, HIGH, HIGH, HIGH, HIGH, HIGH};
		writeToDisplay(pins, levels);
	}else if( number == 7){
		LEVEL const levels[] = {HIGH, HIGH, HIGH, LOW, LOW, LOW, LOW};
		writeToDisplay(pins, levels);
	}else if( number == 8){
		LEVEL const levels[] = {HIGH, HIGH, HIGH, HIGH, HIGH, HIGH, HIGH};
		writeToDisplay(pins, levels);
	}else if( number == 9){
		LEVEL const levels[] = {HIGH, HIGH, HIGH, LOW, LOW, HIGH, HIGH};
		writeToDisplay(pins, levels);
	}else{
		LEVEL const levels[] = {LOW, LOW, LOW, LOW, LOW, LOW, HIGH};
		writeToDisplay(pins, levels);
	}
}

static void writeToDisplay(short const pins[], LEVEL const levels[])
{
	for(int i = 0; i < pins_length; i++){
		gpio_setLevel(pins[i], levels[i]);
	}
}
```
Mi problema con este código es con elegir los pines del display, por ahora utilizamos
el arreglo _pins_ para indicar los pines, me gustaría darle la posibilidad al 
usuarios de la biblioteca de poder elegir los pines que el necesite, propongo cambiar
la interfaz de la función _display7_init_, para permitir indicar los pines, 
posteriormente rellenar el arreglo _pins_:
```C
//display7.c
#define NUMBERDISPLAYPINS 8

static short pins[NUMBERDISPLAYPINS];

void display7_init(short const pin_a, short const pin_b, short const pin_c,
	       	short const pin_d, short const pin_e, short const pin_f,
		short const pin_g, short const pin_dot)
{
	pins[0] = pin_a;
	pins[1] = pin_b;
	pins[2] = pin_c;
	pins[3] = pin_d;
	pins[4] = pin_e;
	pins[5] = pin_f;
	pins[6] = pin_g;
	pins[7] = pin_dot;

	for(int i = 0; i < NUMBERDISPLAYPINS; i++){
		setPinOutputAndLow(pins[i]);
	}
}
```
y para llamar
```C
//proyecto.ino
#define F_CPU 16000000UL
#include<util/delay.h>
#include"display7.h"

int main(void)
{
	display7_init(4, 5, 6, 11, 12, 13, 14, 15);
	while(1){
		for(int i = 0; i<10; i++){
			display7_showNumber(i);
			_delay_ms(1000);
		}
	}
}
```
Agrego también el pin del punto nada más por puro remordimiento. Ahora si 
vamos a optimizar _showNumber_.

## Otra vez de nuevo
Ni modo, reimplementaremos la función tomando en cuenta las sensaciones anteriores,
```C
void display7_showNumber(short const number)
{
	/*				A     B	    C     D     E     F    G */
	if( number == 0){
		LEVEL const levels[] = {HIGH, HIGH, HIGH, HIGH, HIGH, HIGH, LOW};
		writeToDisplay(pins, levels);
	}else if( number == 1){
		LEVEL const levels[] = {LOW, HIGH, HIGH, LOW, LOW, LOW, LOW};
		writeToDisplay(pins, levels);
	}else if( number == 2){
		LEVEL const levels[] = {HIGH, HIGH, LOW, HIGH, HIGH, LOW, HIGH};
		writeToDisplay(pins, levels);
	.
	.
	.
}
```
¿cómo reimplement ese mazacote?, LEVELS debe irse, en lugar de indicar los pines 
en un array los indicamos en una variable de 8 bits, el pin __a__ corresponde al
bit 7, el pin __b__ corresponde al bit 6, etc.
```
  abcdefg.
0b76543210
```
en una nueva tabla guardamos los bits adecuados para cada número.
```C
uint8_t bits_table[] = {
		0b11111100, //0
		0b01100000, //1
		0b11011010, //2
		0b11110010, //3
		0b01100110, //4
		0b10110110, //5
		0b10111110, //6
		0b11100000, //7
		0b11111110, //8
		0b11100110, //9
		0b00000010  //-
	};
```
con el compilador que usa Arduino la asignación anterior es legar, sin embargo 
otros compiladores no aceptan la declaración de binarios de esta forma, prefiero
declarar los valores en forma hexadecimal,
```C
void display7_showNumber(short const number)
{
	uint8_t bits_table[] = {
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
		0x02  //-
	};
	if( number == 0){
		writeToDisplay(pins, bits_table[0]);
	}else if( number == 1){
		writeToDisplay(pins, bits_table[1]);
	}else if( number == 2){
		writeToDisplay(pins, bits_table[2]);
	}else if( number == 3){
		writeToDisplay(pins, bits_table[3]);
	}else if( number == 4){
		writeToDisplay(pins, bits_table[4]);
	}else if( number == 5){
		writeToDisplay(pins, bits_table[5]);
	}else if( number == 6){
		writeToDisplay(pins, bits_table[6]);
	}else if( number == 7){
		writeToDisplay(pins, bits_table[7]);
	}else if( number == 8){
		writeToDisplay(pins, bits_table[8]);
	}else if( number == 9){
		writeToDisplay(pins, bits_table[9]);
	}else{
		writeToDisplay(pins, bits_table[10]);
	}
}
```
ahora nos interesa modificar _writeToDisplay_ para que acepte en su segundo 
argumento un entero de 8 bits en lugar de un arreglo de niveles, se
propone lo siguiente:
```C
static void writeToDisplay(short const pins[], uint8_t const levels)
{
	for(int i = 0; i < NUMBERDISPLAYPINS; i++){
		LEVEL level;
		if(levels & (1<<(7-i)))
			level = HIGH;
		else
			level = LOW;
		gpio_setLevel(pins[i], level);
	}
}
```
recorremos cada uno de los bits de _levels_ y determinamos si es alto o bajo con
_if(levels & (1<<(7-i)))_, después solo escribimos a gpio. Podemos reducir más
_showNumber_ extrayendo esa horrible tabla de _else if_.

```C
void display7_showNumber(short const number)
{
	uint8_t bits_table[] = {
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
		0x02  //-
	};
	if(number>= 0 && number <=9)
		writeToDisplay(pins, bits_table[number]);
	else
		writeToDisplay(pins, bits_table[10]);
}
```
Mucho mejor, me gustan las funciones pequeñas más que las grandes, saco la tabla
de bits y la declaro estática para mayor comodidad. 
```C
//display7.c
#include "display7.h"
#include "gpio.h"
#include <stdint.h>

#define NUMBERDISPLAYPINS 8

static short pins[NUMBERDISPLAYPINS];
static uint8_t const bits_table[] = {
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
		0x02  //-
	};

static void setPinOutputAndLow(short const pin);
static void writeToDisplay(short const pins[], uint8_t const levels);

void display7_init(short const pin_a, short const pin_b, short const pin_c,
	       	short const pin_d, short const pin_e, short const pin_f,
		short const pin_g, short const pin_dot)
{
	pins[0] = pin_a;
	pins[1] = pin_b;
	pins[2] = pin_c;
	pins[3] = pin_d;
	pins[4] = pin_e;
	pins[5] = pin_f;
	pins[6] = pin_g;
	pins[7] = pin_dot;

	for(int i = 0; i < NUMBERDISPLAYPINS; i++){
		setPinOutputAndLow(pins[i]);
	}
}

static void setPinOutputAndLow(short const pin)
{
	gpio_setDirection(pin, OUTPUT);
	gpio_setLevel(pin, LOW);
}

void display7_showNumber(short const number)
{
	if(number>= 0 && number <=9)
		writeToDisplay(pins, bits_table[number]);
	else
		writeToDisplay(pins, bits_table[10]);
}

static void writeToDisplay(short const pins[], uint8_t const levels)
{
	for(int i = 0; i < NUMBERDISPLAYPINS; i++){
		LEVEL level;
		if( levels & (1<<(7-i)))
			level = HIGH;
		else
			level = LOW;
		gpio_setLevel(pins[i], level);
	}
}
```
Me siento satisfecho, se redujo la memoria ram ocupada de 10% a solo 4%, veamos 
como reducirla aun más, pero antes hay algo que me hace ruido otra vez.
## Guardianes
¿Qué pasa si el usuario introduce un pin invalido?, por ejemplo el pin 7 
pertenece a Vcc, ¿el programa debería detenerse? o ¿solo ignorar ese pin?,
yo prefiero solo ignorarlo, veamos la implementación de gpio

```C
//gpio.c
#include "gpio.h"
#include <avr/io.h>

static short const pin_table[28] = {
	PC6, PD0, PD1, PD2, PD3, PD4, -1, -1,
	PB6, PB7, PD5, PD6, PD7, PB0, PB1, PB2, 
	PB3, PB4, PB5, -1, -1, -1, PC0, PC1, 
	PC2, PC3, PC4, 	PC5  
};

typedef enum {DIRECTION_TYPE, LEVEL_TYPE} REGISTER_TYPE;

static char portOfPin(short const pin);
static volatile uint8_t *registerOfPort(char const port, REGISTER_TYPE);

void gpio_setDirection(short const pin, DIRECTION direction)
{
	char port = portOfPin(pin);
	volatile uint8_t *register_ = registerOfPort(port, DIRECTION_TYPE);

	if(direction == OUTPUT){
		*register_ |= 1<<pin_table[pin-1];
	}else if(direction == INPUT){
		*register_ &=~(1<<pin_table[pin-1]);
	}
}

static char portOfPin(short const pin)
{
	if((pin >= 2 && pin <= 6) || pin >= 11 && pin <= 13){
		return 'd';
	}else if(pin == 8 || pin == 9 || (pin >= 14 && pin <= 19)){
		return 'b';
	}else if(pin == 1 || (pin >= 23 && pin <= 28)){
		return 'c';
	}
}

static volatile uint8_t * registerOfPort(char const port, REGISTER_TYPE type)
{
	if(type == DIRECTION_TYPE){
		if(port == 'd')
			return &DDRD;
		else if(port == 'b')
			return &DDRB;
		else if(port == 'c')
			return &DDRC;
	}else if(type == LEVEL_TYPE){
		if(port == 'd')
			return &PORTD;
		else if(port == 'b')
			return &PORTB;
		else if(port == 'c')
			return &PORTC;
	}
}

void gpio_setLevel(short const pin, LEVEL level)
{
	char port = portOfPin(pin);
	volatile uint8_t *register_ = registerOfPort(port, LEVEL_TYPE);

	if(level == HIGH){
		*register_ |= 1<<pin_table[pin-1];
	}else if(level == LOW){
		*register_ &=~(1<<pin_table[pin-1]);
	}
}
```
Aprovechamos la función _portOfPin_, hace un análisis de todos los pines y 
retorna un carácter para identificar el puerto, basta con agregar un _else_ al 
final y retornar un carácter que identifique un puerto invalido derivado de un
pin invalido.  
```C
static char portOfPin(short const pin)
{
	if((pin >= 2 && pin <= 6) || pin >= 11 && pin <= 13){
		return 'd';
	}else if(pin == 8 || pin == 9 || (pin >= 14 && pin <= 19)){
		return 'b';
	}else if(pin == 1 || (pin >= 23 && pin <= 28)){
		return 'c';
	}else
		return '0'; //Cero, puerto invalido
}
```
finalmente agregamos la condición de retorno en las funciones _setLevel_ y 
_setDirection_.
```C
	char port = portOfPin(pin);
	if(port == '0')
		return;
	volatile uint8_t *register_ = registerOfPort(port, DIRECTION_TYPE);
```
## Fin
La biblioteca funciona muy bien, es posible que este omitiendo algo, la única 
forma de saberlo es creando una serie de pruebas exhaustivas o mejor aun usando 
la biblioteca exhaustivamente e ir modificando y adaptándola, ni modo. Este
es el código final. 
```C
//proyecto.ino
#define F_CPU 16000000UL
#include<util/delay.h>
#include"display7.h"

int main(void)
{
	display7_init(4, 5, 6, 11, 12, 13, 14, 15);
	while(1){
		for(int i = 0; i<11; i++){
			display7_showNumber(i);
			_delay_ms(1000);
		}
	}
}
```
```C
#ifndef DISPLAY7_H
#define DISPLAY7_H

#ifdef __cplusplus
extern "C" {
#endif

void display7_init(	short const pin_a,
			short const pin_b,
			short const pin_c,
			short const pin_d,
			short const pin_e,
			short const pin_f,
			short const pin_g,
			short const pin_dot
		);
void display7_showNumber(short const number);

#ifdef __cplusplus
}
#endif
#endif//DISPLAY7_H

```
```C
//display7.c
#include "display7.h"
#include "gpio.h"
#include <stdint.h>

#define NUMBERDISPLAYPINS 8

static short pins[NUMBERDISPLAYPINS];
static uint8_t const bits_table[] = {
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
		0x02  //-
	};

static void setPinOutputAndLow(short const pin);
static void writeToDisplay(short const pins[], uint8_t const levels);

void display7_init(short const pin_a, short const pin_b, short const pin_c,
	       	short const pin_d, short const pin_e, short const pin_f,
		short const pin_g, short const pin_dot)
{
	pins[0] = pin_a;
	pins[1] = pin_b;
	pins[2] = pin_c;
	pins[3] = pin_d;
	pins[4] = pin_e;
	pins[5] = pin_f;
	pins[6] = pin_g;
	pins[7] = pin_dot;

	for(int i = 0; i < NUMBERDISPLAYPINS; i++){
		setPinOutputAndLow(pins[i]);
	}
}

static void setPinOutputAndLow(short const pin)
{
	gpio_setDirection(pin, OUTPUT);
	gpio_setLevel(pin, LOW);
}

void display7_showNumber(short const number)
{
	if(number>= 0 && number <=9)
		writeToDisplay(pins, bits_table[number]);
	else
		writeToDisplay(pins, bits_table[10]);
}

static void writeToDisplay(short const pins[], uint8_t const levels)
{
	for(int i = 0; i < NUMBERDISPLAYPINS; i++){
		LEVEL level;
		if( levels & (1<<(7-i)))
			level = HIGH;
		else
			level = LOW;
		gpio_setLevel(pins[i], level);
	}
}
```
```C
//gpio.h
#ifndef GPIO_H
#define GPIO_H

#ifdef __cplusplus
extern "C" {
#endif

typedef enum {INPUT, OUTPUT} DIRECTION;
typedef enum {LOW, HIGH} LEVEL;

void gpio_setDirection(short const pin, DIRECTION);
void gpio_setLevel(short const pin, LEVEL);

#ifdef __cplusplus
}
#endif

#endif // GPIO_H
```
```C
//gpio.c
#include "gpio.h"
#include <avr/io.h>

static short const pin_table[28] = {
	PC6, PD0, PD1, PD2, PD3, PD4, -1, -1,
	PB6, PB7, PD5, PD6, PD7, PB0, PB1, PB2, 
	PB3, PB4, PB5, -1, -1, -1, PC0, PC1, 
	PC2, PC3, PC4, 	PC5  
};

typedef enum {DIRECTION_TYPE, LEVEL_TYPE} REGISTER_TYPE;

static char portOfPin(short const pin);
static volatile uint8_t *registerOfPort(char const port, REGISTER_TYPE);

void gpio_setDirection(short const pin, DIRECTION direction)
{
	char port = portOfPin(pin);
	if(port == '0')
		return;
	volatile uint8_t *register_ = registerOfPort(port, DIRECTION_TYPE);

	if(direction == OUTPUT){
		*register_ |= 1<<pin_table[pin-1];
	}else if(direction == INPUT){
		*register_ &=~(1<<pin_table[pin-1]);
	}
}

static char portOfPin(short const pin)
{
	if((pin >= 2 && pin <= 6) || pin >= 11 && pin <= 13){
		return 'd';
	}else if(pin == 8 || pin == 9 || (pin >= 14 && pin <= 19)){
		return 'b';
	}else if(pin == 1 || (pin >= 23 && pin <= 28)){
		return 'c';
	}else
		return '0';
}

static volatile uint8_t * registerOfPort(char const port, REGISTER_TYPE type)
{
	if(type == DIRECTION_TYPE){
		if(port == 'd')
			return &DDRD;
		else if(port == 'b')
			return &DDRB;
		else if(port == 'c')
			return &DDRC;
	}else if(type == LEVEL_TYPE){
		if(port == 'd')
			return &PORTD;
		else if(port == 'b')
			return &PORTB;
		else if(port == 'c')
			return &PORTC;
	}
}

void gpio_setLevel(short const pin, LEVEL level)
{
	char port = portOfPin(pin);
	if(port == '0')
		return;
	volatile uint8_t *register_ = registerOfPort(port, LEVEL_TYPE);

	if(level == HIGH){
		*register_ |= 1<<pin_table[pin-1];
	}else if(level == LOW){
		*register_ &=~(1<<pin_table[pin-1]);
	}
}
```
