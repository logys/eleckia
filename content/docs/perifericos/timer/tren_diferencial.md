---
title: "Tren de potencia diferencial"
date: 2021-12-31T14:29:18-06:00
draft: true
---

# Tren de potencia diferencial
Uno de los dispositivos más comunes en el mundo de los sistemas embebidos y
en el de la robótica es el tren potencia diferencial, aunque de nombre 
rimbombante no es más que un par de motores alineados:
![diferencial](/eleckia/img/timer/differential.jpeg)
el nombre deriva de la capacidad de movimiento dado la velocidad individual de
los motores, si los dos motores se mueven a la misma velocidad y en la misma
dirección entonces el robot se mueve en línea recta, una velocidad diferente
en cada motor implicaría una componente de rotación en el dispositivo.

Esta configuración tiene una capacidad fantástica de movimiento, además de ser
simple y barata. Nuestro objetivo es realizar una api, para indicar las 
velocidades lineal y angular del dispositivo.

## Requerimientos
Se necesita un módulo que nos permita indicar las velocidades del tren de 
potencia, la única forma que se tiene de hacer movimiento es a través de los
motores, también se requiere un encoder para cada rueda.

## Implementación
Comenzamos creando nuestro módulo y declarando la primera función:
```C
#ifndef DIFFERENTIAL_H
#define DIFFERENTIAL_H

void differential_setSpeeds(float lineal, float angular);

#endif// DIFFERENTIAL_H
```
Para la implementación requerimos dos motores y dos encoders:
```C
#include "differential.h"
#include "motor.h"
#include "encoder.h"

static Motor left_motor;
static Motor right_motor;
static Encoder left_encoder;
static Encoder right_encoder;

void differential_setSpeeds(float linear, float angular)
{
}
```
`Motor` y `Encoder` están definidas en sus respectivas interfaces.

Medir velocidad lineal y angular directamente es complicado, se requieren 
sensores como acelerometros y giroscopios, además de hardware adicional para
acondicionar las señales, afortunadamente no son fundamentas pues la 
geometría  del dispositivo nos da la información suficiente para obtener la 
velocidad de cada rueda en función de las velocidades lineal y angular.

![vels2wheelsVels](/eleckia/img/timer/vels2wheelsvels.svg)

Donde `d` es la distancia entre las dos ruedas.
Note que si la velocidad angular es cero entonces ambas ruedas giran a la 
misma velocidad, si la velocidad lineal es cero, entonces las ruedas 
giran a la misma velocidad pero en sentido opuesto, lo que provoca que el robot
rote en su lugar.
```C
#define WHEELS_D 0.20

static float vl_ref = 0;
static float vl_ref = 0;

void differential_setSpeeds(float linear, float angular)
{
	Vl = linear + WHEELS_D*angular/2;
	Vr = linear - WHEELS_D*angular/2;
}
```
Debemos indicar a los motores su velocidad, sin embargo los motores no conocen
ninguna forma de asegurar que girarán a la velocidad indicada, la forma de
solventar esta carencia es por medio de un controlador, a grandes rasgos un
controlador es un algoritmo que permite reducir un error a un valor mínimo, en
este contexto el error es la diferencia entre las velocidades establecidas y 
la velocidad actual de los motores, controladores hay varios, simples y 
complejos, intensivos y laxos, nos es el propósito de esta entrada entrar en
detalles sobre control, así que se implementara el controlador más sencillo un
controlador proporcional, que sirve para el propósito aunque definitivamente 
no tiene un gran rendimiento.

### Controlador proporcional
El algoritmo es super simple, 
- Leer la velocidad actual.
- Calcular el error.
- Multiplicar el error por una constante de ponderación.
- Acumular el resultado
- Enviar el acumulado al motor

Es interesante que el acumulado pueda expresarse de forma muy natural
como porcentaje, esto es fantástico, pues la forma de controlar a los motores
es precisamente por medio de el ciclo de trabajo de una onda PWM.

```C
#define KP 5
static float current_vl;
static float current_vr;
static float cv_vl = 0; //variable de control
static float cv_vr = 0;

void differential_do(void)
{
	current_vl = encoder_speed(left_encoder);
	current_vr = encoder_speed(right_encoder);
	error_vl = vl_ref - current_vl;
	error_vr = vr_ref - current_vr;
	cv_vl += KP*error_vl;
	cv_vr += KP*error_vr;
	motor_setWC(&left_motor, cv_vl);
	motor_setWC(&right_motor, cv_vr);
}
```
El valor de `KP` se obtiene por prueba y error, al proceso de encontrarlo se
le llama sintonizar, la línea importante a analizar es `cv_vl += KP*error_vl`,
al principio el error es grande pues la velocidad actual es `0`, esto provoca una
variable de control grande por lo tanto se manda un ciclo de trabajo grande y
el motor acelera mucho, si la función `differential_do`. Si se ejecuta de nuevo, la
velocidad actual habrá crecido, entonces el error se volverá más pequeño, el
acumulado entonces crecerá un poco enviando al motor un ciclo de trabajo aun
más grande que el anterior. Si se ejecuta de nuevo, tal vez la velocidad ahora
es mayor que la referencia, entonces el error se vuelve negativo, al sumarlo 
al acumulador resulta en un número más pequeño, enviándose al motor un
ciclo de trabajo menor al anterior. El punto es que la función debe 
ejecutarse periódicamente y ella irá ajustando el ciclo de trabajo necesario
para mantener la velocidad indicada.

La frecuencia con la que debe ejecutarse la función depende de varios 
factores, principalmente de la dinámica del sistema (los elementos que 
almacenan energía), _Nise, Norman S : Control Systems Engineering_, para quien
quiera indagar en el tema.
### Inicialización
Paremos por referencia los motores y encoders necesarios:
```C
#ifndef DIFFERENTIAL_H
#define DIFFERENTIAL_H

#include "motor.h"
#include "encoder.h"

void differential_setSpeeds(float lineal, float angular);
void differential_init(Motor * left_motor, Motor *right_motor, 
		Encoder * lelft_encoder, Encoder * right_encoder);

#endif// DIFFERENTIAL_H
```
y la implementación:
```C
void differential_init(Motor * left_motor_, Motor *right_motor_,
		Encoder * lelft_encoder_, Encoder * right_encoder_)
{
	left_motor = left_motor_;
	right_motor = right_motor_;
	left_encoder = lelft_encoder_;
	right_encoder = right_encoder_;

	vl_ref = 0;
	vr_ref = 0;
	current_vr = 0;
	current_vl = 0;
	cv_vl = 0;
	cv_vr = 0;
}
```
y el módulo esta listo, vamos con `Motor` y `Encoder`.

## Motor
Esta es la excusa para usar un timer, específicamente para la generación de 
ondas pwm. Como siempre iniciamos declarando un nuevo módulo al que le iremos
agregando funcionalidad.
```C
#ifndef MOTOR_H
#define MOTOR_H

typedef struct Motor{
}Motor;

void motor_setWC(Motor &motor, float wc);

#endif// MOTOR_H
```
Establecer el ciclo de trabajo implica escribir directamente a los registros
del timer, no quiero pelearme con los registros en este nivel de abstracción,
dejare los registros para un nuevo módulo `pwm`. Para cada motor requerimos
dos ondas pwm, el atmega328p únicamente cuenta 3 timers y 2 canales para
generación de pwm, limitándonos a instanciar un máximo de 3 motores.
Probablemente los timers del atmega328p sean su peor periférico, es aquí donde
se ve que este microcontrolador está algo viejo y se notan sus carencias.

### Circuito integrado
Para manejar la corriente de un motor se requiere de circuitería especial, pues
el microcontrolador entrega muy poca corriente, lo más adecuado es utilizar un
circuito integrado para este propósito como el l298 que es el más común (no el 
mejor), para nuestro propósito usaremos este integrado, 
![l298n](https://www.prometec.net/wp-content/uploads/2016/11/Conexiones-L298N.png)
[Fuente: https://www.prometec.net/wp-content/uploads/2016/11/Conexiones-L298N.png](https://www.prometec.net/wp-content/uploads/2016/11/Conexiones-L298N.png)
Las conexiones son evidentes, lo que nos interesa está en las entrada IN1 a IN4,
las primeras dos controlan al motor A y las últimas dos controlan al motor B,
El control obedece las reglas:

| IN1  	| 0  	| 1       	| 0         	| 1  	|
|------	|----	|---------	|-----------	|----	|
| IN2  	| 0  	| 0       	| 1         	| 1  	|
| Giro 	| No 	| Derecha 	| Izquierda 	| No 	|

La idea es: para controlar la intensidad de giro a la derecha se establece
IN2 en cero y se establece una onda pwm en IN1, la intensidad sera 
proporcional al ciclo de trabajo, para girar a la izquierda se establece
IN1 en cero y se establece la onda pwm en IN2, para detener IN1=IN2=0, lo
mismo aplica para el otro motor. Jugando con la posibilidades de los pines
es posible controlar un motor con una sola onda pwm, yo solo requiero dos
así que decido seguir este esquema.

### Implementación
Se propone lo siguiente
```C
#include "motor.h"
#include "pwm.h"

void motor_setWC(Motor * motor, float wc)
{
	if(wc > 0){
		pwm_setWC(motor->pwmIn1, wc);
		pwm_setWC(motor->pwmIn2, 0);
	}else if(wc<0){
		pwm_setWC(motor->pwmIn1, 0);
		pwm_setWC(motor->pwmIn2, -1*wc);
	}else{
		pwm_setWC(motor->pwmIn1, 0);
		pwm_setWC(motor->pwmIn2, 0);
	}
}
```
Nada del otro mundo, ahora definamos como regresar una instancia de motor. 
### Inicialización
Cada
motor requiere de dos instancias pwm, una función como la siguiente tiene,
todo el sentido:
```C
#ifndef MOTOR_H
#define MOTOR_H

#include "pwm.h"

typedef struct Motor{
	Pwm * pwmIn1;
	Pwm * pwmIn2;
}Motor;

Motor motor_create(Pwm * pwmIn1, Pwm * pwmIn2);
void motor_setWC(Motor * motor, float wc);

#endif// MOTOR_H
```
Las instancias pwm se crean en otro lugar y simplemente las pasamos como 
argumentos, de esta forma, el módulo motor no tiene ningún conocimiento 
sobre el módulo pwm más que su interfaz. 

La definición es igual de simple:
```C
Motor motor_create(Pwm * pwmIn1, Pwm * pwmIn2)
{
	Motor motor = {
		.pwmIn1 = pwmIn1,
		.pwmIn2 = pwmIn2
	};
	return motor;
}
```
Y el motor esta listo.

## Pwm
### Implementación
Ahora si no hay escapatoria y hay que lidiar con registros, aunque cada timer
puede generar dos ondas pwm, el timer 2 se suele utilizar para manejar tiempos,
personalmente evitaría su utilización siempre que fuera posible,
```C
fndef PWM_H
#define PWM_H

typedef struct Pwm{
}Pwm;

void pwm_setWC(Pwm * pwm, float wc);

#endif// PWM_H
```
El valor que se recibe va de 0 a 100, pero el registro OCM recibe valores de
0-255, así que se requiere escalar wc:

```C
```

### Inicialización
Cada motor requiere una instancia de una pwm, tenemos que lidiar con las
limitaciones de los timers y con la disponibilidad  de los mismos
```C
typedef enum {PWM_1, PWM_2, PWM_3,PWM_4, PWM_5, PWM_6}PWM_NUMBER;
typedef enum {F33KHZ, F33khz}PWM_FREQUENCY;

Pwm pwm_create(PWM_NUMBER pwm_number, PWM_FREQUENCY frequency);
```
## Encoder
Un encoder es un dispositivo que nos permite medir la velocidad  y dirección
de rotación, para ello genera una par de ondas cuadradas `phase A` y `phase B`,
cuando el encoder gira a la derecha la onda `phase A` adelanta a la `phase B`,
cuando gira a la izquierda el la onda `phase B` la que adelanta, midiendo
los niveles de cada fase se puede inferir la dirección de rotación. Sin embargo
no nos interesa medir la dirección tan solo la velocidad.

Aprovechando una de las ondas y sabiendo que la onda va cambiando entre 
0 y 1 cuando gira el encoder, podemos contar el número de cambios entre unos y
ceros correspondiente a una rotación de la rueda.

### Implementación
Al igual que en el caso del motor, prefiero no lidiar con registros en este 
nivel de abstracción:
```C
#ifndef ENCODER_H
#define ENCODER_H

typedef struct Encoder{
}Encoder;

float encoder_speed(Encoder encoder);

#endif// ENCODER_H
```
Para calcular la velocidad se tiene que tomar en cuenta tres aspectos;
- El número de ticks(cambios entre 0 y 1) de la onda desde el último periodo.
- El número de ticks correspondientes a una rotación completa
- El tiempo entre cada medición.
De las clases de primaria sabemos que la velocidad de rotación es igual a la
distancia angular entre el tiempo. La distancia angular no es más que la
proporción de ticks recorridos, por ejemplo si `1 rev = 100 ticks` y la última
lectura da 50 ticks, entonces se habrá recorrido media revolución o 180 grados o
pi[rad] o media vuelta:
```C
#include "encoder.h"
#include "tick_counter.h"

float encoder_speed(Encoder * encoder)
{
	return tick_counter(encoder->tick) / encoder->REV_TICKS / encoder->time;
}
```
### Inicialización
El encoder requiere un contador de ticks, esa tarea se la dejamos a un módulo
aparte:
```C
#ifndef ENCODER_H
#define ENCODER_H

#include "tick_counter.h"

typedef struct Encoder{
	TickCounter * tick;
	int8_t const REV_TICKS;
	float time;
}Encoder;

Encoder encoder_create(TickCounter * tick);
float encoder_speed(Encoder * encoder, int8_t rev_ticks, int8_t time);

#endif// ENCODER_H
```
La definición:
```C
Encoder encoder_create(TickCounter * tick, int8_t rev_ticks, int8_t time);
{
	return (Encoder){
		.tick = tick,
		.REV_TICKS = rev_ticks,
		.time = time
	};
}
```
## Tick counter
Finalmente el encoder require un contador de ticks:
```C
#ifndef TICK_COUNTER
#define TICK_COUNTER

#include <stdint.h>

typedef struct TickCounter{
}TickCounter;

uint8_t tick_counter(TickCounter * tick);

#endif// TICK_COUNTER
```
En el atmega328p esta tarea puede relazarse a través de interrupciones
externas, de las cinco posibles fuentes de interrupción externa solo dos
son viables para implementar el contador de ticks, INT0 e INT1, 

## Ejecución
