---
title: "Manejador de Tiempos"
weight: 1
draft: True
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# Manejador de tiempos
Cuando comenzamos a desarrollar software para sistemas embebidos nos encontramos
con la situación donde todo esta supeditado por el big loop
```C
while(1){
}
```
Entonces nos vemos
diseñando soluciones donde el código se ejecuta una y otra vez, aunque no haya 
ninguna razón para ejecutar reiteradamente esa solución. Supongamos que se 
requiere hacer que tres leds parpadeen a diferente ritmo, 100ms, 1s 2s, 
alguien muy creativo comenzara a declarar banderas y poner condicionales o 
diseñara un máquina de estados, nota como un problema simple requiere una
solución relativamente compleja, esto se deriva de la naturaleza del big loop; 
adecuado para una sola tarea.

Esta aproximación
tiende a salirse de control rápidamente, debido al aumento exponencial de la
complejidad, para liberarnos de la tiranía del big loop, desarrollaremos un
módulo que nos permite ejecutar código de forma cooperativa.

## Requerimientos y Análisis

- Requerimos encender 3 leds a diferente ritmo en forma quasi paralela.

La forma de hacerlo será insertando un módulo entre el big loop y las tareas,
de tal forma que sea el módulo quien controle los tiempos;
```C
while(1){
	time_scheduler();
	_delay_ms(10);
}
```
La idea es que `time_scheduler` añada un tiempo de 10ms a todos los temporizadores
(uno por cada led) y que revise si se ha alcanzado el intervalo de tiempo, de 
ser así el temporizador ejecuta una función asignada previamente y desactiva el
temporizador.


