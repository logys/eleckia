---
title: 'Funciones'
date: Sun, 07 Jul 2019 20:09:49 +0000
draft: false
tags: ['avr', 'c', 'funciones']
---
# Funciones en C
Las funciones son características fundamentales de cualquier lenguaje de 
programación, inclusive existe un paradigma de programación llamado programación
funcional, veamos brevemente como funcionan las funciones en C. Una función 
tiene la siguiente sintaxis en C,

```C
tipo_de_retorno nombre_de_funcion(tipo_dato argumento_n)
{
             ...
        hacer_tareas
             ...
        return un_objeto_del_tipo_de_retorno;
}
```
Las funciones son usadas como una forma de separar o estratificar tareas, 
pueden recibir "n" cantidad de argumentos y pueden retornar o no un valor,
un ejemplo sencillo, definimos la función suma,
```C
int8_t suma(int8_t sumando1, int8_t sumando2) 
{                                             
        int8_t resultado = sumando1 + sumando2;
        return resultado;                    
}
```
Es importante aclarar que el valor de retorno debe ser del mismo tipo que el
tipo de retorno declarada en la función, en este caso un entero de 8 bits.

Esta función simplemente suma los dos argumentos pasados en ella, definida la
función es posible llamarla desde main o cualquier otra función:
```C
int main(void)
{
	int8_t numero = suma(3,2);
	//ahora numero contiene 5
}
```
Al ejecutarse, la variable _numero_ contendrá el valor de 5.

## Declaración vs Definición

Ahora que sabemos 
definir funciones sencillas, escribamos un programa donde se define la función:
```C
#include<stdint.h>      //Para int8_t 

int main(void)
{             
        int8_t numero = suma(2,3);
        return 0;
}                
                 
int8_t suma(int8_t sumando1, int8_t sumando2)
{                                            
        int8_t resultado = sumando1 + sumando2;
        return resultado; 
}
```
Compilamos y nos dará un error:
```Bash
program.c: In function ‘int main()’:
program.c:5:25: error: ‘suma’ was not declared in this scope
         int8_t numero = suma(2,3);
```
El error indica que la función main no puede encontrar a la función _sum_,
En C hay dos conceptos fundamentales que indican la existencia de un símbolo:
- __Declaración__, Indica al compilador la existencia de un símbolo, de tal
forma que el compilador pueda seguir sin la necesidad de conocer los detalles
del símbolo. 
```C
int8_t funcion1(void); //Declaración de una función
char letra; //Declaración de una variable char
```
A las funciones declaradas se les suele llamar __prototipos__, la declaración de
variables no recibe un nombre en particular,
- __Definición__, Se indica al compilador en que espacio se encuentra y que 
tamaño tiene.
```C
letra = 'b';		//Definición de una variable prefiamente declarada
char vocal = 'o'; 	//Declaración y definición de una variable
int8_t suma(void) 	//Definición y Declaración de una función,
{			//Si la función fue previamente declarada solo se define
	return 1 + 2;
}
```
En nuestro código la función suma se **declara** y **define** al final del 
fichero, ¿por qué nos indica un error de declaración?, El compilador analiza el
código linea por linea de arriba hacia abajo, cuando llega a la función main,
aun no ha encontrado ningún símbolo con el nombre _suma_, la solución es tan
simple como escribir el prototipo una o varias líneas antes de la función main.
```C
int8_t suma(int8_t sumando1, int8_t sumando2);
int main(void)
{
```
Con esto el compilador estará feliz de hacer su trabajo, en otra etapa de 
compilación encontrara la definición de la función y hará el trabajo necesario
para asignar su dirección de memoria y su tamaño.

En las siguientes entradas entenderemos las ventajas que se obtiene de esta 
separación entre prototipo(**declaración**) y la **definición**. 

## Parámetros y retorno
### Parámetros
Los parámetros sirven para enviar datos a las funciones, aunque una 
interpretación más útil es que las funciones operan sobre los datos, una 
función puede recibir cualquier cantidad de parámetros, el limite es el
tamaño de memoria, sin embargo lo deseable es que las funciones reciban pocos
o ningún parámetros, algunos ejemplos:
```C
int8_t suma(float sumando1, float sumando2);
int8_t factorial(int8_t numero);
int8_t contador(char * palabra); //apuntador tipo char
int8_t simbolo(char letra);
int8_t mesActual(void); //el tipo void(vacio) indica que no hay parámetros
```
### Retorno
Las funciones pueden retornar uno o ningún parámetro por medio de la 
palabra return, al igual que los parámetros de entrada puede ser de
cualquier tipo:
```C
int8_t suma();
char * palabras();
void encender(); //Sin retorno
char ultimaLetra();
int * lista();
```

## Llamada a funciones
Las funciones pueden ser llamadas dentro de cualquier otra función,
incluso existe una técnica llamada recursividad donde una función se llama 
a si misma entrando en una especie de ciclo, aunque es una técnica maravillosa
es poco utilizada en sistemas embebidos, principalmente por:
- La razón más importante: es una técnica altamente elegante que esta reservada
 para gente altamente sofisticada y de buen gusto.
- Otra razón menos importante es que los microcontroladores carecen de la 
memoria suficiente para almacenar todas las variables de llamadas sucesivas
a una función.

Para llamar a una función basta con indicar su nombre, pasar los argumentos y
guardar el retorno si es necesario.
```C
void funcion1(void)
{
	int numero = suma(3, 5);
	//Hacer tareas con numero
}
```
