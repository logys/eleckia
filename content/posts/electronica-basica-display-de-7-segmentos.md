---
title: 'Electrónica básica.- Display de 7 segmentos.'
date: Sat, 15 Jun 2019 19:19:11 +0000
draft: false
tags: ['7segmentos', 'eléctronica', 'led']
---

Un display de 7 segmentos es un arreglo de leds distribuidos de una manera determinada, 8 leds en total, el display de 7 segmentos tiene el propósito de mostrar caracteres, usualmente números pero también puede mostrar letras.

![](https://eleckia.files.wordpress.com/2019/06/7segdia.png)

Display 7 segmentos

el circuito equivalente es muy sencillo,

![](https://eleckia.files.wordpress.com/2019/06/7segcatodo-1.png)

Cátodo común

esta configuración es llamada cátodo común, puesto que el cátodo de todos los diodos está en corto circuito, es una buena característica pues tenemos un solo pin para todos los cátodos y podemos usar un solo cable para la conexión en lugar de ocho.

![](https://eleckia.files.wordpress.com/2019/06/7seganodo-1.png)

Ánodo común

La configuración de ánodo común es similar a la de cátodo común salvo que los ánodos son los que están conectados en corto.

El dispositivo cuenta con nueve terminales (aveces diez, dos dedicadas para el común) una para el común y el resto para los ocho leds, para el cátodo común; la terminal común se conecta a tierra y los leds se encienden con voltaje alto, por el contrario para el ánodo común; la terminal común se conecta a Vcc y los leds se encienden con voltaje bajo.

Cada terminal es simplemente un led, por lo tanto para usarlo debemos agregar una resistencia para limitar la corriente, para nuestro atmega328p; resistencias para cada led de 330 $latex \\Omega$ o un poco mayores son suficientes. Cada led sera alimentado por 10\[mA\], si todos los leds se encienden al mismo tiempo, en total tenemos 80\[mA\], por lo que nuestro microcontrolador puede manejarlos sin problema, recordemos que la corriente máxima provista por el microcontrolador es de 200\[mA\] por todos los pines y 40\[mA\] en cada pin.