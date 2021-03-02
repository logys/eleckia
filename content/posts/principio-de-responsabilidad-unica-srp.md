---
title: 'Principio de responsabilidad única (SRP)'
date: Sun, 28 Jun 2020 02:28:46 +0000
draft: false
tags: ['Programación', 'solid', 'srp']
---

En el desarrollo ágil, el principio de responsabilidad única es uno de los cinco principios fundamentales, el principio define lo siguiente:

_Un módulo debe tener un solo motivo para cambiar._

Y eso es todo, ¿fácil no?, yo también lo creo, ¿qué es lo que creo?, no lo entiendo. Vamos por partes, las palabras son obvias, pero la frase es un poco ambigua, el meollo del asunto es claramente _un solo motivo para cambiar_, podemos apoyarnos en el nombre del principio, _responsabilidad única_, mmm parece ser que ambas frases se refieren a lo mismo, ¿a qué mismo?, mmm, lo que esta claro es que un módulo solo debe tener una sola responsabilidad, por ejemplo suponiendo que requerimos un sistema que controle el movimiento de un robot móvil, el robot puede ir hacia adelante, atrás y rotar las ruedas de dirección, una forma de visualizarlo es la siguiente,

![](https://eleckia.files.wordpress.com/2020/06/srp-robot.png?w=291)

Separación obvia.

Se sugiere una separación de responsabilidades, en lugar de que el módulo robot\_posición administre el avance, retroceso y giro, separamos las responsabilidades y las asignamos a nuevos módulos, esta separación es lo primero que se me vino a la mente, ¿pero esta separación es la más adecuada?, las repuesta no es evidente ni definitiva, es aquí donde recurrimos a la definición _un solo motivo para cambiar_, los módulos traslación y rotación cumplen con el principio srp, solo si al requerir una nueva característica, solo uno de los dos módulos necesita modificarse.

Suponiendo que los requerimientos cambian y que se requiere que la velocidad a la que se mueve el robot hacia adelante y atrás sea controlable, es evidente que el módulo traslación sera el único que cambie, por lo tanto la separación que se realizo es adecuada y cumple con el principio srp.

Ahora suponiendo que la especificación cambia, se desea que el robot tenga movimiento continuo, es decir que el robot avance hacia adelante o atrás mientras está rotando. Ahora nuestros módulos no cumplen con el principio, pues ambos deben ser modificados para alcanzar los nuevos requerimientos, este será un buen momento para replantear nuestra anterior separación en módulos, una refactorización nos da el siguiente modelo,

![](https://eleckia.files.wordpress.com/2020/06/srp_tren.png?w=329)

De esta manera velocidad solo se encarga de mover los motores avance y de retroceso, tren\_de\_giro se encarga de girar un motor de dirección o el ajuste necesario dependiendo de la forma del robot.

Con esto quiero decir que los módulos deben agruparse de tal forma que las razones por las que cambian estén juntas, con esto logramos que al agregar nuevos cambios, solo debamos modificar el mínimo de módulos.

¿Y cuales son esas razones de cambio?, la respuesta es el cliente, el usuario, si el cliente del módulo hace una nueva especificación que implique un cambio y para lograr la implementación se requieren modificar más de un módulo se debe replantear la forma y el tamaño de los módulos.

Repito, el objetivo del SRP es proveer una forma de "modularizar" el programa en base a peticiones de cambios, aunque exista la tentación de agrupar módulos por forma, color, sabor, etc, que pudieran parecer más naturales, agrupar por motivos de cambios, nos dará como resultado un software altamente ágil en el sentido de nuevas características.

En el ejemplo anterior, ¿que sentido tendría mezclar el código de control de posición con el código de control visión del robot?, si nosotros requerimos modificar el contraste o el brillo de la imagen, ¿sería necesario modificar la velocidad del robot?, las respuesta obvia es que no, sin embargo piensa que las cámaras aveces son muy lentas, quizá requerimos tomar una imagen cuando el robot se acerque a un objeto, entonces si la cámara es lenta necesariamente requerimos bajar la velocidad, ni modo, es triste, pero debemos refactorizar los módulos en harás de lograr el objetivo.

El SRP es el principio más sencillo de describir, pero el más difícil de asimilar, requiere práctica, esfuerzo, experiencia y mucha meditación.