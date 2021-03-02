---
title: 'Arduino Básico III'
date: Thu, 06 Jun 2019 00:43:58 +0000
draft: false
tags: ['Arduino']
---

El siguiente paso obvio es analizar el programa Blink, sin embargo aquí no usaremos las bibliotecas de Arduino, usaremos código c para dispositivos AVR, entonces ¿para que tanto problema instalando la maquina virtual y Arduino?, simplemente porque utilizaremos la infraestructura ya hecha para nuestro propósito, abrimos el ejemplo Blink y reemplazamos el código por:

```
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

compilamos y subimos el código, el comportamiento es exactamente el mismo, pero el código es totalmente C y no C++, también evitamos el uso de las bibliotecas de Arduino en favor de las avr. Esto se nota de inmediato en el tamaño del programa, mientras que nuestro código usa 108 Bytes, el código de Arduino usa 924 Bytes, esto realmente no es determinante, en un proyecto complejo y grande usar una biblioteca u otra no tendrá gran impacto en el tamaño final. En realidad las Arduino usan como base las bibliotecas avr. Este pequeño hola mundo, da para comentar muchas cosas, no te preocupes por no entender el código, iremos desglosándolo en las siguientes entradas, también mostrare como liberarnos del IDE y por si fuera poco como liberarnos de la tarjeta.

¿Por qué no usar unicamente Arduino para todo?, la verdad es que la única razón verdadera y valida es que no me gusta, otras razones menos validas son:

*   Te limita a un puñado de microcontroladores.- Dentro de la familia de los microcontroladores avr apenas unos cuantos son utilizados para las tarjetas Arduino, existen ports a otras arquitecturas y micros, pero suelen ser limitadas, quizá lo que requieres realizar sea un programa muy sencillo, quizá no necesites toda la potencia que te ofrece un atmega328p, tal vez lo que necesites sea un attiny13(hiper barato y pequeño), por el contrario tal vez requieres algo muy rápido y potente como un amr cortex m7, o un risc V, no siempre tenemos a disponibilidad las bibliotecas de Arduino, pienso que es importante saber hacer tus propias bibliotecas y adaptarlas a un hardware cambiante.
*   Te obliga a usar C++, aunque es posible enlazar bibliotecas de C++ en C, es un proceso un poco traumático, el problema de C++ es que es difícil de leer, el ejemplo clásico es el siguiente

```
struct unaEstructura primero = nuevoNumero();
struct unaEstructura segundo = nuevoNumero();
// C
a = primero.num + segundo.num; //En C esto tiene un significado obvio
// C++
a = primero + segundo; //A priori no sabemos que esta pasando aquí
```

pudiera parecer una trivialidad, pero de igual forma sucede con varias otras situaciones, realmente es una cuestión de simple gusto, C++ es un lenguaje muy potente, si te sientes cómodo con él úsalo y explótalo al máximo.

*   El entorno en general es precario. No se puede tener todo, si es fácil tiene que haber un costo, el costo es que no tenemos herramientas de debugging, el editor es horrible, no se adapta muy bien a los proyectos colaborativos, no hay control de versiones.

Sin embargo hay muchas cosas buenas que no se encuentran en ningún otro ambiente, por lo tanto debemos equilibrar nuestras preferencias en aras de obtener el mejor rendimiento y calidad.