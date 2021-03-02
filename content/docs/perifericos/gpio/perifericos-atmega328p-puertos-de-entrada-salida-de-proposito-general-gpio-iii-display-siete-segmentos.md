---
title: 'Display Siete Segmentos II.'
date: Sun, 10 May 2020 07:57:36 +0000
draft: false
tags: ['7segmentos', 'avr', 'Programación']
---

Nuestro anterior programa sirve para dos cosas, para nada y para la basura, no hay ninguna diferencia con respecto a conectar los pines del display directamente a Vcc, requerimos funcionalidad, que el microcontrolador haga lo adecuado para mostrar el número que nosotros indiquemos y que nosotros hagamos el mínimo trabajo para indicarlo, algo similar a

```
setNumber(3); //Muestra el número 3 en el display
```

de esta forma podemos realizar una secuencia de números

```
int main(void)
{
  while(1){
    setNumber(0);
    delay
    setNumber(1);
    delay
    .
    .
    .
    setNumber(9);
    delay     
  }
}
```

el display mostrara los números del 0 al 9 en orden ascendente, nuestro objetivo entonces es definir la función **setNumber**.

Aunque en este problema sencillo podemos programar la solución de forma monolítica(un solo fragmento de código), es de utilidad desarrollarlo en forma modular, como en la siguiente figura

![](https://eleckia.files.wordpress.com/2020/05/7segdep.png?w=235)

display 7 segmentos como módulo

pensar en forma modular tiene grandes ventajas y conforme crece el proyecto se vuelve la única forma sostenible de escribir código, la primer gran ventaja es la posibilidad de reutilizar código, seria absurdo escribir un controlador de 7 segmentos en cada proyecto nuevo, la segunda ventaja es la capacidad de extender el código ya escrito, la tercer gran ventaja es que el código es más "mantenible", entre muchas otras que se irán comentando.

Para nuestro propósito un módulo es un ente de software que está aislado, autocontenido y es altamente cohesivo, dicho de otra forma, la función main no tiene conocimiento de las variables y funciones del módulo, salvo por un conjunto de funciones comúnmente llamadas interfaz. Una bonita interfaz para nuestro modulo puede ser

```
void display7_init(void);
void display7_setNumber(short number);
void display7_destroy(void);
```

elegir buenos nombres es una parte fundamental para que nuestro código sea de alta calidad, estoy seguro que al leer las funciones anteriores de inmediato formaste una idea de lo que hacen y a que pertenecen. Sin pensarlo hemos llegado a uno de los grandes dogmas de la programación y de la resolución de problemas en general, dividimos el problema en problemas más pequeños, ni tardos ni perezosos modificamos nuestro programa y agregamos el módulo,

```
#include<avr/io.h>

void display7\_init(void);
void display7\_setNumber(short number);
void display7\_destroy(void);

int main(void)
{
       DDRB |= (1<<PB0);
       DDRD |=  (1<<PD2)|(1<<PD3)|(1<<PD4)|
                (1<<PD5)|(1<<PD6)|(1<<PD7);
       PORTB |= (1<<PB0);
       PORTD |= (1<<PD2)|(1<<PD4)|(1<<PD5)|(1<<PD7);
       return 0;
}

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

este código compila sin problemas pero no hace el trabajo requerido, al principio agregamos los prototipos del módulo, pero aun no los llamamos en la función main, tampoco hemos implementado dichas funciones. Antes de definir la interfaz, anteriormente indique que la función main no tiene conocimiento de las funciones y variables de nuestro módulo, si escribimos el código en el mismo fichero, el usuario de la función main tendrá a la vista todas las funciones y variables y probablemente tenga la tentación de usarlas, una buena idea para evitar que esto suceda es utilizar un par de archivos extra exclusivos para nuestro módulo, esto lo veremos en la siguiente entrada.