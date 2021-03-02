---
title: 'Programando Avr en C.- El flujo del programa, if, else, if else.'
date: Sun, 28 Jul 2019 02:55:18 +0000
draft: false
tags: ['avr', 'c', 'else', 'if', 'Programación']
---

Definimos el flujo del programa como, la ruta que toma la ejecución del mismo, imaginemos que tenemos una lista de tareas que debemos ejecutar, digamos, tarea 1, tarea 2, tarea 3, ..., tarea n, ya vimos que podemos abstraer tareas como funciones, entonces simplemente podemos agregar cada una de las funciones en orden para ejecutarlas,

```
int main(void)
{
  tarea1();
  tarea2();
  tarea3();
  tarean();
  return 0;
}
```

pero que pasa si queremos ejecutar alguna de estas tareas solo si se cumple una condición, digamos, solo si se pulsa un botón determinado, o solo si el resultado de una operación satisface algún requerimiento.

Para ello contamos con la palabra **if**, si se cumple una condición, el código dentro de sus llaves se ejecutara, de lo contrario el programa continuara después de la llave de cierre,

```
int main(void)
{
  tarea1();
  if(condicion){ //si condición es verdadera
    tarea2();    //se ejecutaran ésta dos tareas
    tarea3();
  }
  tarea4();  //Aquí continua
  tarean();
  return 0; 
}
```

también es posible agregar la palabra **else**, para controlar el flujo cuando la condición es falsa

```
int main(void)
{
  tarea1();
  if(condicion){ //si condición es verdadera
    tarea2();    //se ejecutara ésta tarea
  }else{
    tarea3();   //si condición es falsa, ejecuta ésta tarea
  }
  tarea4();
  tarean();
  return 0; 
}
```

agregar más sentencias nos permite un control aun más preciso.

```
int main(void)
{
  tarea1();
  if(condicion == 0){ //si condición es igual a 0
    tarea2();    //se ejecutara ésta tarea
  }else if(condicion == 1){
    tarea3();   //si condición igual a 1, ejecuta ésta tarea
  }else{
    tarea4();  //si no se cumple ninguna de las anteriores
  }
  tarean();
  return 0; 
}
```

es importante señalar que la ejecución es en orden descendente, si tenemos varios if else y en más de uno se cumple la condición, solo se ejecutara el primero que la cumpla en orden descendente.

```
int main(void)
{
  tarea1();
  if(true){ 
    tarea2();    //se ejecutara ésta tarea
  }else if(true){
    tarea3();  //No se ejecuta
  }else{
    tarea4();  //No se ejecuta
  }
  tarean(); //una vez ejecutada tarea2, el programa continua aquí
  return 0; 
}
```