---
title: 'Programando Avr en C.- Hola mundo'
date: Thu, 06 Jun 2019 21:11:38 +0000
draft: false
tags: ['avr', 'c']
---
# Comenzando con avr y C

Para iniciar en la programación de dispositivos avr, se requieren algunas
cosas:

- Un compilador de C y sus utilidades para avr:
	- Aunque existen varios compiladores disponibles, __avr_gcc__ es el que
se encuentra en la mayoría de los proyectos.
- Un editor de texto:
	- Nuevamente hay muchas opciones, en lo personal prefiero vim.
- __avrdude__, este programa nos ayuda a subir el código al microcontrolador.

## Escribiendo el programa

Comenzamos creando un fichero llamado blinky.c, dentro del cual agregamos:
```C
#include <avr/io.h>
```
La directiva _"include<>_ copia el contenido del fichero indicado, en nuestro
fichero blinky.c, en este caso copia el contenido del fichero _io.h_ que se 
encuentra dentro del directorio _avr_, no nos interesa la ubicación del
directorio avr, el compilador sabe donde se encuentra y lo incluye de manera
automática. Lo que si nos interesa saber es que incluir el fichero io.h nos
permite acceder a los registros que controlan los pines del microcontrolador.

Posteriormente tenemos la función **main**:
```C
int main(void)
{
	return 0;
}
```

La función main es un ente 
importante, cuando el programa se ejecuta, la función main es llamada 
automáticamente, el código dentro de las llaves se ejecutara línea por 
línea, lo que esté fuera de dicha función deberá ser llamado explícitamente para
poder ejecutarse. Por definición la función main retorna un entero al finalizar
y en el caso de microcontroladores no suele recibir argumentos por eso la 
palabra void (más adelante analizaremos la estructura de las funciones). 

Después viene el __superloop__,  
```C
int main(void)
{
	while(1){
		//tareas
	}
	return 0;
}
```
El superloop es un ciclo infinito que se ejecuta dentro del main, en general
las tareas deben definirse dentro del loop de tal forma que se repitan una
y otra vez.

Finalmente tenemos un retorno, como nuestro programa se queda "ciclado" dentro 
del while, nunca alcanzaremos esta línea, sin embargo es conveniente poner esta
línea o el compilador nos dará una advertencia.

Ya estamos un poco más familiarizados con la estructura del programa, 
ahora si analicemos el programa Blink,

```C
#include<avr/io.h>
#include<util/delay.h>

int main(void)
{
        DDRB = 1<<PB5;
        PORTB = 0;

        while(1){
                PORTB = 1<<PB5;
                _delay_ms(1000);
                PORTB = 0;
                _delay_ms(1000);
        }
        return 0;
}
```

Al inicio tenemos dos directivas include, la primera io.h, nos permite acceder 
a los registros de los puertos de nuestro microcontrolador, en este caso el 
puerto B, haré una entrada especifica para este tema, la segunda delay.h nos 
permite usar la función _\_delay\_ms_, que como deducirás nos permite crear un
tiempo muerto.

Iniciamos con la función main y lo primero que tenemos es una asignación, 
te preguntaras que es DDRB, para entenderlo primero debo explicar que es un 
registro y lo explicare en una entrada dedicada, por ahora vamos a decir que 
DDRB es una variable a secas, a esta variable le asignamos el resultado de una 
operación _1<<PB5_, el símbolo "<<" es un operado binario llamado left shift o 
corrimiento a la izquierda, indica que el número a la izquierda sera recorrido 
el número de veces que tiene a la derecha en binario, por ejemplo

```Bash
3 en binario es 0b0011
Aplicar un corrimiento a la izquierda en una unidad implica:
3<<1
es exactamente igual a:
0b0011<<1 
y da como resultado 
0b0110 
en decimal 6
```
En nuestro caso, casi siempre usaremos un 1 a la izquierda del operador, por lo tanto

```Bash
1 en binario es 0b1
1<<1 da como resultado     0b10
1<<2 da como resultado    0b100
1<<3 da como resultado   0b1000
1<<7 da como result. 0b10000000
```

Realmente no nos interesa el valor en decimal, lo que nos interesa es donde queda
posicionado el 1, al final de la operación con 1<<2, el 1 queda posicionado en la
tercera de derecha a izquierda, con 1<<7, queda en la octava de derecha a izquierda.

¿Para que es esto?, DDRB es una "variable" que le indica al microcontrolador si
el pin es de entrada o de salida, si el bit es cero, el pin operara como entrada,
de lo contrario opera como salida. DDRB contiene 8 bits y cada bit corresponde 
a un pin de nuestro microcontrolador, al inicio contiene el valor binario 
0b0000 0000, si revisas la hoja de datos de tu microcontrolador te darás 
cuenta que los pines están rotulados con las leyendas PA0, PB0, PC0, PA1, etc, 
estos son los "nombres" de los pines y A,B,C, es el puerto al que pertenecen, 
por lo tanto el pin 5 del puerto B se llama PB5.

El pin PB5 esta conectado al led de nuestra tarjeta, hay que decirle a DDRB 
que ponga el pin 5 como salida, tan fácil como:
```C
DDRB = 1<<PB5;
//que es exactamente igual a
DDRB = 0b00100000;
//bit    76543210
```
Lo anterior tiene la desventaja de poner al resto de pines como entrada, por 
ahora no es un problema, pero no es la forma correcta, en otra entrada se 
dará la forma correcta.

Seguimos adelante y tenemos otra asignación, PORTB es otro registro, de igual 
forma asumiremos por ahora que es una variable, controla el estado del pin, 
el estado puede ser 0 para bajo o 1 para alto, en síntesis cuando el pin se 
configura en alto tendrá 5 volts y cuando se configura en bajo tendrá 0 volts, 
cuando necesitemos encender el led lo configuramos como alto con

```C
PORTB = 1<<PB5;
//que es exactamente igual a
PORTB = 0b00100000;
```

¿y si queremos ponerlo en bajo?, por ahora solo esto,

```C
PORTB = 0;
//que es exactamente igual a
PORTB = 0b00000000;
```
Siguiendo con el programa, entramos al ciclo infinito en donde la parte 
interesante es la función _\_delay\_ms()_, la función se explica por si misma, 
cuando se llama toma el control de la ejecución del programa y espera la 
cantidad de tiempo indicada en milisegundos, 1000 para este ejemplo, pasado este
tiempo la función termina y sigue con la ejecución del programa.

El programa se queda en este bucle encendiendo y apagando el led cada dos 
segundos.

## Compilando
Si no tenemos instalado avr\_gcc, en una terminal:
```Bash
$ sudo dnf install avr-gcc
```

El proceso de compilación es simple, abrimos una terminal, nos dirigimos al 
directorio donde 
se encuentra nuestro fichero y escribimos:
```Bash
$ avr-gcc -mmcu=atmega328p blinky.c
```
El compilador generará un archivo ejecutable en formato elf: a.out,
sin embargo se requiere convertir este fichero a otro formato antes de 
poder grabarlo en el microcontrolador. El archivo a.out contiene información
adicional a las instrucciones del programa, para "filtrar" la información
relevante:
```Bash
$ avr-objcopy -O ihex a.out blinky.hex
```
La herramienta _avr-objcopy_ extrae las instrucciones y las coloca en el
fichero blinky.hex.

## Grabando
Finalmente utilizamos la herramienta _avrdude_ para grabar el programa
```Bash
$ avrdude -c arduino -P /dev/ttyACM0 -p m328p -U flash:w:blinky.hex
```
Un par de segundos después el led del arduino comenzara a parpadear cada dos
segundos. 

El parámetro -P /dev/ttyACM0 indica el puerto, desafortunadamente no siempre
es el mismo, también puede ser /dev/ttyUSB0 o algún otro, pero por lo general
es alguno de esos dos.
