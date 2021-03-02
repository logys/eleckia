---
title: 'Display Siete Segmentos I.'
date: Wed, 19 Jun 2019 04:37:56 +0000
draft: false
tags: ['7segmentos', 'avr', 'Programación']
---

Escribamos un programa simple que nos muestre un número en un display de 7 segmentos, el circuito es el siguiente,

![](https://eleckia.files.wordpress.com/2019/06/circuito7seg.png)

Conexión Arduino-Display

Contamos con un display de cátodo común por lo tanto cada segmento se enciende con un voltaje alto, es decir para encender el segmento "A" debemos configurar el pin digital **D2** en alto. Evitamos la conexión en los pines D0 y D1, debido a que estos están conectados al convertidor serial-usb, y pueden provocar problemas para subir el código a la tarjeta.

Como primer paso debemos incluir la biblioteca "avr/io.h", para tener acceso a los registros de entrada/salida, DDRx, PORTx, PINx.

```
#include<avr/io.h>
```

Ahora declaramos la función principal, recordemos que la función principal es llamada automáticamente cuando el programa es ejecutado,

```
int main(void)                                                                     
{                                                                                  
        return 0;                                                                  
}
```

dentro de las llaves del main configuramos los pines adecuados como salidas,

![](https://eleckia.files.wordpress.com/2019/06/333e3-1gxvtkbhgpvqfxhn75j1-9a.png)

fuente: [https://medium.com/@jrejaud/arduino-to-avr-c-reference-guide-7d113b4309f7](https://medium.com/@jrejaud/arduino-to-avr-c-reference-guide-7d113b4309f7)

en la anterior imagen observamos la equivalencia entre los pines del Arduino y los del microcontrolador, por ejemplo el pin digital D2 es en realidad el pin PD2 del atmega328p, por ejemplo el pin digital D8 es en realidad el pin PB0 del atmega328p. Configuramos el registro DDRB y DDRD,

```
DDRB |= (1<<PB0);
DDRD |= (1<<PD2)|(1<<PD3)|(1<<PD4)|
        (1<<PD5)|(1<<PD6)|(1<<PD7);
```

Ahora solo encendemos los leds, poniendo en alto los pines requeridos, por ejemplo para el número cinco, encendemos los segmentos a,c,d,f,g.

![](https://eleckia.files.wordpress.com/2019/06/7segdia5.png)

Número Cinco

```
PORTB |= (1<<PB0);
PORTD |= (1<<PD2)|(1<<PD4)|(1<<PD5)|(1<<PD7);
```

el código completo luce como el siguiente,

```
#include<avr/io.h>                                                              
                                                                                   
int main(void)                                                                     
{                                                                                  
       DDRB |= (1<<PB0);                                                           
       DDRD |=  (1<<PD2)|(1<<PD3)|(1<<PD4)|                                        
                (1<<PD5)|(1<<PD6)|(1<<PD7);                                        
       PORTB |= (1<<PB0);                                                          
       PORTD |= (1<<PD2)|(1<<PD4)|(1<<PD5)|(1<<PD7);                               
       return 0;                                                                   
}
```

compilamos y subimos.

![](https://eleckia.files.wordpress.com/2019/06/7seg5.png)

El "estilo" en que hemos escrito nuestro programa es coloquialmente llamado hardcoding, debido a que nosotros especificamos tanto los valores como el flujo de nuestro programa, usualmente lo que queremos es que el programa calcule los valores y elija el flujo, imaginemos que ahora requerimos una secuencia de números en serie, comenzado con 1 y terminando en 9 y repetir esta secuencia 1000 veces, seria un poco tedioso cambiar uno por uno los estados de los segmentos, para evitar este trabajo nos apoyaremos en funciones y sentencias de flujo.