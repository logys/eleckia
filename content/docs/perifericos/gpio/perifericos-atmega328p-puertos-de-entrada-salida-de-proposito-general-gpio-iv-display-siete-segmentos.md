---
title: 'Display Siete Segmentos III.'
date: Mon, 11 May 2020 04:53:21 +0000
draft: false
tags: ['7segmentos', 'avr', 'Programación']
---

No se si ya lo había mencionado pero odio el entorno de desarrollo de arduino, en esta ocasión por la forma tan pobre de manejar ficheros. Continuando la entrada anterior, vamos a mover nuestro módulo en un par de archivos, creamos dos ficheros cuyo nombre deje en claro que hay escrito en él, un buen nombre es display7,

```
$touch display7.c
$touch display7.h
```

ahora en nuestro entorno de desarrollo incluimos los dos ficheros, para ello vamos al menú **Programa->Añadir fichero** y añadimos ambos ficheros, ambos aparecerán en nuestro entorno de desarrollo y podrán ser editados desde el mismo, movemos el módulo a los nuevos ficheros, los prototipos van en él .h y las definiciones al .c, de esta manera tenemos los tres ficheros siguientes

```
//proyecto.ino
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
``````
//display7.h
void display7\_init(void);
void display7\_setNumber(short number);
void display7\_destroy(void);
``````
//display7.c
void display7\_init(void)
{
}
void display7\_setNumber(short number)
{
}
void display7\_destroy(void)
{
}
```

nuevamente compilamos el programa y no nos dará ningún error, salvo que no hace nada. Para poder acceder a la interfaz del módulo, es necesario incluirla en nuestro fichero principal, basta con agregar la directiva #include"display7.h" antes de la función main, de esta forma el módulo puede ser desarrollado de forma independiente y paralela al programa principal, modifiquemos nuestro .ino para usar la interfaz

```
#include<avr/io.h>
#include"display7.h"

int main(void)
{
	display7\_init();
	while(1){
		display7\_setNumber(0);
		\_delay\_ms(1000);
		display7\_setNumber(1);
		\_delay\_ms(1000);
	}
}
```

pienso que el código se ve definitivamente más limpio y muestra claramente que se está haciendo, sin embargo si tratamos de compilar nos dará un error, similar a este

```
undefined reference to `display7_init()'
```

en pocas palabras el compilador(en realidad el linker) no encuentra las definiciones del módulo, las definiciones están en el fichero .c, como mencionamos en entradas anteriores, el entorno arduino en realidad compila ficheros c++, pero como somos tercos y queremos programar en c debemos indicarle al compilador que nuestro ficheros no son c++, esto es muy sencillo, basta con añadir

```
extern "C" {
```

en el fichero de la interfaz y agregar después del ultimo prototipo un

```
}
```

el archivo .h queda

```
extern "C" {
void display7\_init(void);
void display7\_setNumber(short number);
void display7\_destroy(void);
}
```

Compilamos y esta vez no obtendremos ningún error. De esta forma el compilador hará lo adecuado para poder usar el módulo, esta solución sirve para nuestros propósitos, sin embargo en el futuro, desarrollaremos nuestros programas con un compilador de "c", el compilador no entenderá la línea extern "C" y nos dará un error, ¿cómo hacemos que el módulo funcione con el compilador de c y también con el de c++?, nuevamente la solución es sencilla, cuando usamos un compilador de c++, este pasa una definición, explicitamente

```
#define __cplusplus
```

esto no sucede cuando compilamos con un compilador de c, aprovechando esta definición podemos usar la directiva

```
#ifdef ALGO
  //si ALGO fue definido, coloca estas lineas
#endif
```

modificamos el fichero .h y obtenemos

```
//display7.h
#ifdef \_\_cplusplus
extern "C" {
#endif

void display7\_init(void);
void display7\_setNumber(short number);
void display7\_destroy(void);

#ifdef \_\_cplusplus
}
#endif
```

este fichero está casi listo, funciona con ambos compiladores, sin embargo tiene un gran problema, ¿qué pasa si por error incluimos más de 1 vez nuestras interfaz?, la respuesta es que nos indicara un error de doble definición, nuevamente nos apoyamos en la directiva #define, de la forma

```
#ifndef MODULO
#define MODULO

//Aquí las definiciones

#endif
```

claramente significa, si no esta definido, define y coloca la interfaz, de lo contrario si ya esta definido no hagas nada, nuestra implementación queda así

```
//display7.h
#ifndef DISPLAY7\_H
#define DISPLAY7\_H

#ifdef \_\_cplusplus
extern "C" {
#endif

void display7\_init(void);
void display7\_setNumber(short number);
void display7\_destroy(void);

#ifdef \_\_cplusplus
}
#endif
#endif//DISPLAY7\_H
```

el fichero principal queda así

```
//proyecto.ino
#include<avr/io.h>
//Necesario para \_delay\_ms(), frecuencia de nuestro arduino
#define F\_CPU 16000000UL 
#include<util/delay.h> //Nos permite usar delay\_ms()
#include"display7.h"

int main(void)
{
	display7\_init();
	while(1){
		display7\_setNumber(0);
		\_delay\_ms(1000);
		display7\_setNumber(1);
		\_delay\_ms(1000);
	}
}
```

anteriormente olvide las lineas 4 y 5, son necesarias para usar la función \_delay\_ms. Las definiciones están en el fichero .c, debemos agregar la interfaz a este fichero también con la directiva #include

```
//display7.c
#include"display7.h"
void display7\_init(void)
{
}
void display7\_setNumber(short number)
{
}
void display7\_destroy(void)
{
}
```

En un entorno de desarrollo decente el fichero .c nos hubiera dado problemas pues no esta permitido compilar una implementación sin indicar a que interfaz pertenece, sin embargo el entorno de arduino nos auto corrige el error, esto es otra cosa por lo que no me gusta arduino, me oculta mis errores. Nuestro código ahora luce más modular en un diagrama luce así

![](https://eleckia.files.wordpress.com/2020/05/7segmod.png?w=366)

Diseño en módulo

Como se ve en la imagen ahora proyecto.ino (función main) depende de la interfaz, mientras que display7.c implementa el modulo, dicho de otra forma, proyecto.ino no sabe que hay en display7.c, el solo conoce lo que hay en display7.h, de esta manera podemos reutilizar el módulo en otros módulos, sin miedo a romper el código fuera de este y muy importante sin tener que reescribir código extra.

Establecido lo anterior, ahora si nos toca implementar el módulo, por supuesto en la siguiente entrada.