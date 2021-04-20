---
title: 'Programando Avr en C.- Hola mundo'
date: Thu, 06 Jun 2019 21:11:38 +0000
draft: false
tags: ['avr', 'c']
---
# Hola mundo

Antes de analizar el código Blink en C, explicaremos la estructura básica de un programa en C, el siguiente código muestra un programa mínimo

```C
#include<bibliotecas.h>

int main(void)
{
        tipo1 variable1;
        tipo_n variable_n;

        while(1){
                hacer_cosas(var1, var2, varn);
        }
        return 0;
}
```
Al inicio de este listado tenemos una directiva del preprocesador, las directivas
comienzan con un **#**, en este caso tenemos una directiva include, que le indica
al compilador que copie el contenido del fichero bibliotecas.h en nuestro código,
esto nos habilita a usar los objetos y funciones que hay dentro de bibliotecas.h,
existen diferentes directivas que nos ayudaran a tener un código más limpio, es 
importante no subestimar al preprocesador, pues nos ayudara a hacer cosas que no
pueden hacerse de otra forma.

Posteriormente tenemos la función **main**, la función main es un ente 
importante, cuando el programa se ejecuta, la función main es llamada 
automáticamente, es decir el código dentro de las llaves se ejecutara línea por 
línea, lo que esté fuera de dicha función deberá ser llamado explícitamente para
poder ejecutarse. Por definición la función main retorna un entero al finalizar
y en el caso de microcontroladores no suele recibir argumentos por eso la 
palabra void (más adelante analizaremos la estructura de las funciones), una vez
llamada la función main, se comienzan a ejecutar cada uno de las líneas dentro 
de ella en orden descendente, en este caso declaramos dos variables de cierto 
tipo, _variable_1_ y _variable_n_.

Posteriormente hay una parte importante una estructura de flujo **while**, mantiene en ejecución el código que contiene mientras la condición que esta entre paréntesis se mantenga verdadera, un 1 siempre es verdadero por lo tanto esto se considera un ciclo infinito, es decir el código dentro del while se ejecutara constantemente, cuando llegue a la última línea saltara a la primera dentro sus llaves.

Finalmente tenemos un retorno, como nuestro programa se queda "ciclado" dentro del while, nunca alcanzaremos esta línea, sin embargo es conveniente poner esta línea o el compilador nos dará un warning o advertencia.

Ya estamos un poco más familiarizados con la estructura del programa, ahora si analicemos el programa Blink,

```C
#include<avr/io.h>                                                                 
#include<util/delay.h>
 
int main(void)                                                                     
{                                                                                  
        DDRB = 1 << PB5;                                                           
        PORTB = 0;                                                                 
                                                                                    
        while(1){                                                               
                PORTB = 1 << PB5;                                              
                \_delay\_ms(1000);                                                
                PORTB = 0;                                             
                \_delay\_ms(1000);                                                
        } 
        return 0;                                                                      
}
```

Al inicio tenemos dos directivas include, la primera io.h, nos permite acceder a los registros de los puertos de nuestro microcontrolador, en este caso el puerto B, haré una entrada especifica para este tema, la segunda delay.h nos permite usar la función \_delay\_ms, que como deducirás nos permite crear un tiempo muerto.

Iniciamos con la función main y lo primero que tenemos es una asignación, te preguntaras que es DDRB, para entenderlo primero debo explicar que es un registro y lo explicare en una entrada dedicada, por ahora vamos a decir que DDRB es una variable a secas, a esta variable le asignamos el resultado de una operación 1<<PB5, el símbolo "<<" es un operado binario llamado left shift o corrimiento a la izquierda, indica que el número a la izquierda sera recorrido el número de veces que tiene a la derecha en binario, por ejemplo

```
3 en binario es 0b0011
3<<1 es en binario 0b0011<<1 y da como resultado 0b0 0110 en decimal 6
3<<2 es en binario 0b0011<<2 y da como resultado 0b0 1100 en decimal 12
3<<3 es en binario 0b0011<<3 y da como resultado 0b1 1000 en decimal 24
```

en nuestro caso casi siempre, solo usaremos un 1 a la izquierda del operador, por lo tanto

```
1 en binario es 0b1
1<<1 da como resultado     0b10
1<<2 da como resultado    0b100
1<<3 da como resultado   0b1000
1<<7 da como result. 0b10000000
//bit                  76543210
//Posición             87654321
```

realmente no nos interesa el valor en decimal, lo que nos interesa es donde queda posicionado el 1, al final de la operación con 1<<2, el 1 queda posicionado en la tercera de derecha a izquierda, con 1<<7, queda en la octava de derecha a izquierda.

¿Para que es esto?, DDRB es una "variable" que le indica al microcontrolador si el pin es de entrada o de salida, si el bit es cero el pin operara como entrada, de lo contrario opera como salida. DDRB contiene 8 bits y cada bit corresponde a un pin de nuestro microcontrolador, al inicio contiene el valor binario 0b0000 0000, cuando revisas la hoja de datos de tu microcontrolador te darás cuenta que los pines están rotulados con las leyendas PA0, PB0, PC0, PA1, etc, estos son los "nombres" de los pines, A,B,C, es el puerto al que pertenecen, por lo tanto el pin 5 del puerto B es PB5.

El pin PB5 esta conectado al led de nuestra tarjeta, hay que decirle a DDRB que ponga el pin 5 como salida, tan fácil como

```
DDRB = 1<<PB5;
//que es exactamente igual a
DDRB = 0b00100000;
//bit    76543210
```

Seguimos adelante y tenemos otra asignación, PORTB es otro registro, de igual forma asumiremos por ahora que es una variable, controla el estado del pin, el estado puede ser 0 para bajo o 1 para alto, en síntesis cuando el pin se configura en alto tendrá 5 volts y cuando se configura en bajo tendrá 0 volts, cuando necesitemos encender el led lo configuramos como alto con

```
PORTB = 1<<PB5;
//que es exactamente igual a
PORTB = 0b00100000;
```

¿y si queremos ponerlo en bajo?, por ahora solo esto,

```
PORTB = 0; //que es idéntico a
PORTB = 0b00000000;
```

esto tiene el pequeño inconveniente de poner el resto de pines en bajo y es muy probable que nuestro colega que este usándolos se enoje con nosotros, más adelante encontraremos una mejor manera.

Siguiendo con el programa, entramos al ciclo infinito en donde la parte interesante es la función \_delay\_ms(), la función se explica por si misma, cuando se llama toma el control de la ejecución del programa y espera la cantidad de tiempo indicada en milisegundos, en este caso 1000 que equivale a un segundo.

El programa se queda en este bucle encendiendo y apagando el led cada dos segundos. La última línea no se ejecuta pues el ciclo no se interrumpe.
