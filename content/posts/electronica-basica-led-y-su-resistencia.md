---
title: 'Electrónica básica.- Led y su resistencia.'
date: Fri, 14 Jun 2019 18:54:50 +0000
draft: false
tags: ['diodo', 'eléctronica', 'led']
---

La ley de Ohm es una herramienta fundamental en la electrónica, en esta ocasión vamos a calcular la resistencia adecuada para que un diodo pueda operar de forma segura.

Antes que nada definamos que es un diodo, sin entrar en detalles, un diodo es un dispositivo que permite el flujo de corriente en una sola dirección, cuenta con dos terminales, el ánodo y el cátodo, la regla es que si se conecta un voltaje alto al ánodo y uno bajo al cátodo, entonces fluye corriente, esta operación del diodo se conoce como polarización directa, si se invierten las conexiones se dice que el diodo esta polarizado en inversa y no fluye corriente. Un circuito simple se muestra en la figura,

![](https://eleckia.files.wordpress.com/2019/06/diodo1dia-1.png)

Circuito con Diodo

Ahora reemplazamos el diodo por su circuito equivalente,

![](https://eleckia.files.wordpress.com/2019/06/diododirecta.png)

Diodo en Directa

La resistencia "interna" del diodo es muy pequeña por lo que podemos despreciarla, no debemos olvidar que este circuito es un modelo, el modelo funciona bien porque representa el comportamiento del diodo, no significa que dentro del diodo exista una resistencia y una fuente de voltaje, el valor de la fuente de voltaje varia de diodo a diodo, debemos verificar la hoja de datos para ver que voltaje tiene el diodo en cuestión, en algunos casos el voltaje es pequeño comparado con el resto de voltajes y simplemente se desprecia, modelandolo como un simple corto circuito.

En el caso de la polarización inversa el circuito es aun más simple

![](https://eleckia.files.wordpress.com/2019/06/diodoinversa.png)

Diodo en Inversa

es un circuito abierto y no hay circulación de corriente.

#### Led.

Un led es un diodo que tiene la capacidad de emitir luz, el voltaje del diodo led es mayor que el voltaje de otro tipo de diodos, siendo el rojo el mas pequeño alrededor de 1.8 \[V\] y el azul el más grande alrededor de 4.8 \[V\], esto para leds de 5mm, sin embargo esto valores cambian según el tamaño del led, lo mejor es revisar en la hoja de datos del diodo en cuestión.

Un led generalmente se acompaña de una resistencia conectada en serie, ¿para qué?, la respuesta es simple, para limitar su corriente, entendamos que es un corto circuito.

![](https://eleckia.files.wordpress.com/2019/06/corto-2.png)

Partiendo del circuito anterior, tenemos una fuente en serie con una resistencia, recordemos que la corriente es

$latex i = \\frac{V}{R}$

un corto se entiende como una conexión eléctrica de cero resistencia entre dos terminales. De esta forma los puntos A y B en el esquema están en corto, dicho de otra manera no hay resistencia entre ellos, esto implica que la corriente puede fluir totalmente entre estos dos puntos, Las terminales C y D no están en corto pues hay una resistencia entre ellas, ¿que pasaría si quitamos la resistencia?, pues tendríamos a las terminales C y D en corto y las consecuencias serian desastrosas. de la ley de Ohm tenemos $latex i = \\frac{V}{0} = \\infty$, las fuentes no pueden otorgar energía infinita, están diseñada para dar una corriente especifica, si se sobrepasa el limite se dañaran, es por eso que **nunca** debes juntar las terminales de una fuente.

Regresando a nuestro diodo led, ¿qué pasaría si no colocamos una resistencia entre el diodo y la fuente?,

![](https://eleckia.files.wordpress.com/2019/06/diododirectacorto.png)

Diodo en corto

como dijimos la resistencia del modelo del diodo es muy pequeña por lo tanto la fuente y el diodo están prácticamente en corto, es decir fluirá una gran cantidad de corriente desde la fuente hacia el diodo, si se tiene suerte el diodo se quemara y se abrirá el circuito, si tienes un mal día tu fuente se dañara también, los diodos están fabricados para trabajar a un cierta corriente, en el caso de los diodos pequeños de 5mm la corriente suele estar alrededor de los 10\[mA\], en el caso de los diodos led de iluminación es común encontrar corrientes de 300\[mA\]. Calculemos rápidamente la resistencia de un led de 5mm rojo de 1.8\[V\] y 10\[mA\], que se desea conectar a una fuente de 5\[V\]. El circuito es el siguiente.

![](https://eleckia.files.wordpress.com/2019/06/diodocalculo-1.png)

de la ley de Ohm

$latex V = Ri$

dividiendo ambos lados entre la corriente

$latex \\frac{V}{i} = R \\frac{i}{i} = R$

para la resistencia R

$latex R = \\frac{V\_R}{i}$

y la aplicamos a la resistencia R, recordemos que la corriente es un flujo, por lo tanto la corriente que pasa por la resistencia es la misma que pasa por el led, sabemos que el voltaje en la terminal positiva de la resistencia es 5V y el voltaje en la terminal negativa de la resistencia es 1.8V (estamos despreciando la resistencia interna del diodo), por lo tanto el voltaje que hay entre las terminales de la resistencia R es

$latex V\_R = (5 - 1.8) \[V\] = 3.2\[V\]$

sabemos que la corriente de operación del led es de 10\[mA\], por lo tanto la resistencia adecuada debe ser

$latex R = \\frac{3.2\[V\]}{10\[mA\]} = \\frac{3.2\[V\]}{0.010\[A\]} = 320 \\Omega$

la resistencia comercial más cercana es de 330 Ohms, usaremos esa, la corriente disminuirá una cantidad marginal.

¿Y si quiero añadir un led adicional a la misma fuente?, podemos añadir el led de forma paralela al led ya existen y recalcular la resistencia.

![](https://eleckia.files.wordpress.com/2019/06/diodocalculo-2.png)

Leds en paralelo

El cálculo es idéntico salvo que ahora la corriente es el doble, pues se requiere satisfacer la corriente de cada diodo $latex i = i\_{D1} + i\_{D2} = 20\[mA\]$, la resistencia necesaria es $latex R = 160 \\Omega$, el problema es que la resistencia disipara más potencia, que significa que se calentara más. Un método más recomendado es conectar una segunda rama (con diodo y resistencia incluidos), en paralelo a la fuente,

![](https://eleckia.files.wordpress.com/2019/06/diodoparaleloconr.png)

Diodos con resistencia en paralelo

de esta forma las dos resistencias son iguales a 330 Ohms. Podemos conectar tantas ramas en paralelo como deseemos, el limite es la capacidad de corriente de nuestra fuente, hay que recordar que el limite de un pin del atmega328p es de 40\[mA\].