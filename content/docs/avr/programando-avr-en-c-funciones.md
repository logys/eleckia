---
title: 'Programando Avr en C.- Funciones'
date: Sun, 07 Jul 2019 20:09:49 +0000
draft: false
tags: ['avr', 'c', 'funciones']
---

Las funciones son características fundamentales de cualquier lenguaje de programación, inclusive existe un "estilo" de programación llamado programación funcional, veamos brevemente como funcionan las funciones en C. Una función tiene la siguiente sintaxis en C,

```
tipo_de_retorno nombre_de_funcion(tipo_dato argumento_n)
{
             ...
        hacer_tareas
             ...
        return un_objeto_del_tipo_de_retorno;
}
```

las funciones pueden ser usadas como una forma de realizar tareas de manera reiterada, pueden recibir "n" cantidad de argumentos y retornar un valor, un ejemplo sencillo, definimos la función suma,

```
int8_t suma(int8_t sumando1, int8_t sumando2) 
{                                             
        int8_t resultado = sumando1 + sumando2;
        return resultado;                    
}
```

esta función simplemente suma los dos argumentos pasados en ella, para usarla se usa la sintaxis:

```
int8_t numero = suma(3,2);
```

al ejecutarse, la variable _numero_ contendrá el valor de 5, las funciones pueden retornar cualquier tipo de dato, incluso alguno definido por nosotros como veremos en el futuro.

Ahora que sabemos definir funciones sencillas, escribamos un programa sencillo donde la usemos.

```
#include<avr/io.h>      //No se usará
#include<stdint.h>      //Para int8\_t 
int main(void)
{             
        int8\_t numero = suma(2,3);
        return 0;
}                
                 
int8\_t suma(int8\_t sumando1, int8\_t sumando2)
{                                            
        int8\_t resultado = sumando1 + sumando2;
        return resultado; 
}
```

Ahora compilamos y nos dará un error o probablemente no, si compilamos este programa con un compilador de C y sin el software de Arduino, nos mostrar un error donde señala que la función no fue declarada,

```
program.c: In function ‘int main()’:
program.c:5:25: error: ‘suma’ was not declared in this scope
         int8_t numero = suma(2,3);
```

por definición lo más recomendable es declarar cualquier objeto antes de usarlo, en nuestro código la función suma se **declara** y **define** al final del fichero, podríamos reescribir la función suma antes de la función main, esto esta bien para programas pequeños, sin embargo en programas grandes se vuelve una forma difícil de mantener, una forma más adecuada es usar un prototipo, un prototipo es una forma de declarar una función sin definirla, la sintaxis es similar a la definición de una función pero sin llaves y sin cuerpo,

```
tipo_de_retorno nombre_de_funcion(tipo_dato argumento_n);
```

en el caso de nuestra función suma su prototipo es

```
int8_t suma(int8_t sumando1, int8_t sumando2);
```

el prototipo debe tener el mismo nombre el mismo tipo de retorno y los mismos tipos de argumentos. Ahora agregando este prototipo antes de la función main, podemos estar seguros de que no habrá un error de compilación, el compilador se encargara de buscar la definición en el resto del código.

Arduino usa un compilador de C++ y probablemente no nos muestre ningún error, debido a que autogenera los prototipos, personalmente por cuestiones de legibilidad, prefiero definirlos yo mismo.

En las siguientes entradas entenderemos las ventajas que se obtiene de esta separación entre prototipo(**declaración**) y la **definición**. El código final es el siguiente:

```
#include<avr/io.h>      //No se usará
#include<stdint.h>      //Para int8\_t
                       
int8\_t suma(int8\_t sumando1, int8\_t sumando2);//Prototipo
                                              
int main(void)                                
{                                             
        int8\_t numero = suma(2,3);//Llamado a la función    
        return 0;                             
}                                             

//Definición de la función 
int8\_t suma(int8\_t sumando1, int8\_t sumando2)
{                                             
        int8\_t resultado = sumando1 + sumando2;
        return resultado;                     
}
```

al ejecutarse el programa el valor final de la variable **numero** sera 5.