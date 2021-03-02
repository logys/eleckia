---
title: 'Electrónica Básica.- Voltaje, Corriente, resistencia y ley de Ohm.'
date: Tue, 11 Jun 2019 14:56:24 +0000
draft: false
tags: ['eléctronica']
---

Cuando la gente de computación escucha las palabras en el titulo de esta entrada, suele entrar en pánico y no es para menos, el análisis de circuitos eléctricos puede llegar a ser muy complejo; sin embargo la electrónica descansa sobre una ley simple, la ley de Ohm,

$latex V = iR$

la ley de Ohm relaciona el voltaje con la corriente y la resistencia; pero esto no nos dice nada si no entendemos estos términos, comencemos con el **voltaje**, también llamado tensión o potencial eléctrico, en la física,

E_l voltaje se define como la cantidad de trabajo necesaria para mover una carga desde el infinito hasta un punto determinado_.

Ahora que ya sabes y entendiste claramente el voltaje pasemos con lo siguiente (es broma obviamente), la definición no nos sirve pues se aleja demasiado de la parte práctica, una forma más laxa y útil de interpretarlo es;

_El voltaje es una fuente de energía eléctrica que impulsa a la corriente eléctrica a fluir a través de un circuito eléctrico._

Esta definición no rigurosa pero nos es de gran utilidad, por la definición y por la ley de Ohm, su unidad de medida es el Volt \[V\], si el voltaje es muy grande la corriente que puede proveer la fuente es de mayor intensidad, por el contrario si el voltaje es negativo, "absorbe" corriente eléctrica.

¿Pero qué es la **corriente** eléctrica?, pues no es más que un flujo de cargas eléctricas, flujo de electrones a través de un medio, usualmente un conductor, pero puede ser incluso a través del aire formándose un arco eléctrico cuando hay voltaje muy alto, su unidad de medida es el Ampere \[A\], se le representa con un símbolo "i" en el esquema a continuación, la dirección de la corriente va de voltaje alto a voltaje bajo; o si solo hay una fuente va de la terminal positiva a tierra (una forma común de referirse al voltaje o potencial 0\[V\]).

![](https://eleckia.files.wordpress.com/2019/06/circuitos.png)

Dirección de la corriente

Una resistencia es un elemento que se opone a el paso de la corriente, si colocamos en un circuito eléctrico un resistencia de 10 Ohms, la corriente que pasa por ella seria mucho mayor que si colocáramos una resistencia de 1000 Ohms.

Por ejemplo, tenemos una fuente de 5V, una resistencia de 330 Ohms conectada entre sus terminales, ¿Qué corriente pasa por la resistencia?

sabemos que $latex V = i R$, dividiendo ambos lados entre la resistencia

$latex \\frac{V}{R} = \\frac{iR}{R}$

por lo tanto, la corriente es

$latex i = \\frac{V}{R}$  
$latex = \\frac{5}{330}=0.015 \[A\] = 15 \[mA\]$

¿Por qué es importante conocer la corriente?, debido a que cada pin de nuestro microcontrolador tiene poca capacidad de entregar o recibir potencia, dicho de otra forma la corriente que sale de cada pin está limitada, para el atmega328p la corriente máxima de cada pin es de 40mA y la corriente entre todos los pines no debe ser superior a 200mA, sobrepasar este limite implica dañar el microcontrolador, para manejar corrientes grandes mayores a 10\[mA\] utilizaremos componentes externos.