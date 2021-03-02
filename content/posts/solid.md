---
title: 'SOLID'
date: Sat, 27 Jun 2020 23:19:57 +0000
draft: false
---

#### _Soy malo y eso es bueno, yo jamás seré bueno y eso no es malo, no hay nadie que quiera ser además de mi.  
\- Ralph el demoledor._

SOLID
-----

Dentro del mundo de la programación existen los famosos paradigmas de programación, entre ellos: la programación funcional, la programación orientada a eventos, la programación estructurada, la programación orientada a objetos y otros más. En esta fecha 2020, la programación orientada a objetos sin duda domina la forma en que se escribe el software en general, vale la pena aprender este paradigma pues no será raro encontrarnos con programas basados en él.

El éxito de la programación orientada a objetos, se basa en la abstracción de objetos, ¿qué es la abstracción de objetos?, personalmente la defino como una combinación entre imaginar, separar y simplificar, es decir tomamos un objeto real por ejemplo un motor, identificamos lo que nos interesa por ejemplo la velocidad y la dirección de giro y descartamos el resto de detalles, el tipo de motor, la potencia, la tensión de entrada, el par, marca, color, peso, etc, este motor que únicamente gira a la velocidad y dirección indicada, es una abstracción de un motor real, esto que parece tan trivial tiene la cualidad de simplificar la forma en que se resuelven los problema, pues los humanos estamos adaptados a pensar en objetos abstractos.

Dentro de la programación orientada a objetos han surgido diferentes corrientes de desarrollo, en los últimos años el desarrollo ágil gano una popularidad importante. En las catacumbas de un lugar de cuyo nombre no quiero acordarme, se reunió un grupo de programadores con gran experiencia en el desarrollo de software, conspirando en contra de la técnicas tradicionales escribieron el manifiesto ágil,

[https://agilemanifesto.org/iso/es/manifesto.html](https://agilemanifesto.org/iso/es/manifesto.html)

una vez popularizado el desarrollo ágil comenzaron a desarrollarse numerosas metodologías de trabajo, entre ellas: scrum, kaban, xp, tdd, etc, como mencione las personas funcionamos en base a abstracciones por lo que las metodologías ágiles pueden salir del mundo de la programación e implementarse en otros rubros de la humanidad.

Personalmente yo no me sentí seguro con mi código hasta que aprendí TDD (Test Driven Development), Desarrollo Guiado por Pruebas.

Aquí tratare de describir los cinco principios fundamentales del desarrollo ágil, acuñados por Robert C. Martin, Agile Software Development, Principles, Patterns, and Practices. Es un excelente referencia para cualquiera que pretenda aprender a programar.

SOLID es un acrónimo formador por los cinco principios del desarrollo ágil.

**S** _Single responsibility principle_ (Principio de responsabilidad única)  
**O** _Open/closed principle_ (Principio Abierto/Cerrado)  
**L** _Liskov substitution principle_ (Principio de sustitución de Liskov)  
**I** _Interface segregation principle_ (Principio de segregación de la interfaz)  
**D** _Dependency inversion principle_ (Principio de inversión de dependencia)

Si cumplimos con estos principios dentro de nuestro código, entonces podemos decir que nuestro código es ágil

* * *

### Sigue mi blog

Recibe nuevo contenido directamente en tu bandeja de entrada.

\[jetpack\_subscription\_form subscribe\_placeholder="Introduce tu dirección de correo electrónico" show\_subscribers\_total="false" button\_on\_newline="false" submit\_button\_text="Registrarse" custom\_background\_emailfield\_color="undefined" custom\_background\_button\_color="undefined" custom\_text\_button\_color="undefined" custom\_font\_size="16" custom\_border\_radius="0" custom\_border\_weight="1" custom\_border\_color="undefined" custom\_padding="15" custom\_spacing="10" submit\_button\_classes="" email\_field\_classes="" show\_only\_email\_and\_button="true" \]