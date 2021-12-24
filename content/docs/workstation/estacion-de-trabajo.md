---
title: 'Estación de trabajo'
date: Tue, 04 Jun 2019 22:52:26 +0000
draft: false
tags: ['Arduino', 'Fedora', 'Herramientas', 'Virtualbox']
---

# Estación de trabajo

_**No es obligatorio** instalar Fedora, pero todos los ejemplos se harán desde
este sistema operativo, eres libre de adaptar lo que aquí se mencione a tus circunstancias._

Para poder trabajar a gusto es necesario disponer de un conjunto de herramientas
adecuado, dejando de lado el tipo de computadora con la que se cuenta, lo
importante es el sistema operativo, utilizaremos software libre, nuestro sistema
operativo sera linux, concretamente Fedora, esta distribución cuenta con los 
paquetes necesarios para desarrollar software cómodamente, así que si utilizas
algo diferente comienza borrado tu disco duro e instala Fedora 
(obviamente esto es broma :D).

La manera más sencilla de comenzar en este mundo (y la que seguiremos aquí) es
con linux + arduino, hablemos rápida y brevemente de ellos.

Linux es una pieza de software que funciona como interfaz entre el hardware de
una computadora (memoria, procesador, periféricos, etc), en pocas palabras y de
manera muy general, linux se encarga de administrar el hardware de una PC, por
otro lado existe software (GNU y otros) que se comunica con linux para formar
un sistema operativo, de ahí surgen los sistemas GNU/Linux, a grandes rasgos el
software GNU se encarga de administrar el sistema, enviándole instrucciones a 
Linux (comúnmente llamado kernel o núcleo) y este último las ejecuta en el 
hardware. Para el que este interesado en entender un poco más sobre el origen y
filosofía de GNU/linux y el software libre, les recomiendo ver el documental 
"Código linux", disponible en youtube en español. Compañías aprovechan este 
conjunto de software para crear distribuciones, en nuestro caso trabajaremos 
con la distribución Fedora, patrocinada por Red Hat, disponible para su 
descarga gratuitamente.

Arduino es una plataforma de desarrollo, contiene tanto software como hardware,
sera común referirnos de manera indistinta al software o hardware con la palabra
Arduino, con respecto al hardware hay muchas versiones, siendo el "Arduino uno"
el más común y el que utilizaremos, este Arduino cuenta con un microcontrolador
atmega328p, es un microcontrolador de la empresa Microchip, anteriormente le 
pertenecía a Atmel, pero fue comprada por la anterior mencionada, el 
microcontrolador es de 8 bits y contiene numerosos periféricos. Arduino es 
software libre pero también hardware libre, esto implica que los clones 
funcionen de manera idéntica a el hardware original, aunque si puedes comprarlo
original siempre es bueno apoyar a los creadores. En la parte de software 
Arduino cuenta con su propio entorno de desarrollo (aunque a mi me parece 
horrible) y cuenta con miles de bibliotecas listas para ser usadas en nuestros 
proyectos.

Lo que requerimos:

\-Instalar Fedora  
\-Adquirir un Arduino  
\-Instalar Arduino dentro de Fedora

Comencemos instalando Fedora, como mencionamos Fedora es un sistema operativo,
por lo tanto requerimos instalarlo en nuestra PC, podemos instalar junto a 
nuestro sistema operativo actual, podemos reemplazar nuestro sistema operativo 
actual por Fedora o si ya utilizamos Fedora como sistema operativo principal no hay necesidad de reinstalarlo, la última opción es instalarlo en una maquina virtual, si eres usuario windows probablemente esta opción es la más adecuada para ti.

Una maquina virtual es un entorno que permite ejecutar un sistema operativo adicional sobre uno ya en ejecución, el sistema adicional se conoce como "huesped"(guess) y el que ya esta en ejecución se le denomina "anfitrión"(host), de esta manera podemos instalar Fedora sin la necesidad de modificar nuestro sistema operativo actual.
### Instalando VirtualBox
Procedemos a instalar la maquina virtual, para ello visitamos el enlace [https://www.virtualbox.org/](https://www.virtualbox.org/)  
y descargamos la versión más reciente para nuestro sistema operativo anfitrion(probablemente windows), no entrare en detalles sobre como instalar virtual box puesto que cada sistema es diferente, pero confió en que sabes como instalar programas en tu sistema, para instalarlo en windows doble click sobre el ejecutable descargado, si eres usuario linux seguramente viene en los repositorios oficiales de tu distribución.

### Creando la maquina virtual
Ahora descargaremos Fedora, accedemos al enlace  
[https://getfedora.org/es/](https://getfedora.org/es/)  
existen varias ediciones, elegimos Workstation y la descargamos, la descarga es una imagen .iso de alrededor de 2GB, una vez descargado procedemos a instalar.

Abrimos Virtualbox y seleccionamos "nueva"

![VirtualBox](/eleckia/img/vbox-1.png)

Se abrirá una nueva ventana escribimos "Fedora" en nombre, carpeta la dejamos por defecto, seleccionamos, tipo "Linux" y Versión "Fedora (64-bit)", click en siguiente.

Ahora nos pide configurar la memoria, aqui aparece un control de deslizamiento, selecciona por lo menos 2GB y da click en siguiente.

Ahora nos pide configurar un nuevo disco duro, seleccionamos "Crear un disco virtual Ahora" y damos click en siguiente.

Tipo de archivo de disco duro, VDI, siguiente.

Almacenamiento en unidad de disco duro física, Reservado dinámicamente, siguiente.

Ubicación del archivo y tamaño, dejamos la ubicación por defecto y seleccionamos un tamaño de 20GB, siguiente.

Hemos finalizado la creación de la maquina virtual, procedemos a instalar Fedora en el siguiente post.

### Instalando Fedora dentro de VirtualBox

Abrimos VirtualBox y seleccionamos la maquina virtual Fedora que creamos
anteriormente, ahora damos click en Configuración, se nos abrirá una nueva 
ventana, seleccionamos **Almacenamiento** y en controlador IDE, seleccionamos
**Vació**, a la derecha de unidad óptica damos click en el icono de un **disco azul** y seleccionamos el .iso de Fedora que descargamos anteriormente, finalmente damos click en aceptar.

![cd](/eleckia/img/cd.png)

Si tienes anfitrión linux, ahí mismo en configuración seleccionamos **Pantalla** y cambiamos **Controlador Gráfico** a VBoxVGA y aumenta la memoria de video a por lo menos 64MB.

Nuevamente en **Configuración**, ahora seleccionamos **Sistema** y en la pestaña **Procesador**, seleccionamos el número de procesadores que tenemos, damos click en aceptar.

Nuestra maquina virtual, esta lista para arrancar y ya tiene el "disco" de instalación insertado, procedemos a iniciarla con el icono **Iniciar**.

Arrancamos y la primer pantalla de selección nos da tres opciones, seleccionamos la primera, **Start Fedora**, cuidado por defecto esta seleccionada la segunda opción.

Esperamos a que el sistema inicie y tenemos una versión live de Fedora funcionando,

![gnome](/eleckia/img/gnomefedora.png)


Esta versión de Fedora es totalmente funcional, podemos jugar con ella, revisar 
los programas y menus, cambiar la resolución, etc, sabedores de que los cambios 
que hagamos no se guardaran, cuando estemos listos damos click en Install to Hard Drive. Se abrirá el asistente de instalación Anaconda (puede que tarde unos minutos), aquí elegimos el idioma y la región y damos click en continuar,

![Anaconda](/eleckia/img/anaconda.png)

seleccionamos nuestra distribución de teclado(probablemente español 
latinoamericano) y la zona horaria, click en **Destino de la Instalación**, sin
modificar nada damos click en **Hecho** en la esquina superior izquierda y 
comenzamos la instalación.

![Comenzar](/eleckia/img/empezarin.png)

Esperamos unos minutos a que termine la instalación y damos click en salir.

![Finalizado](/eleckia/img/finalizado.png)

Apagamos la maquina virtual y **removemos la imagen** .iso de Fedora.

Iniciamos nuevamente la maquina virtual esta vez iniciara Fedora instalado, 
todo lo que hagamos ahora si se guardara, nos dará la bienvenida un asistente 
de configuración donde nos pedirá algunos datos, de ellos lo importante es el 
usuario y la contraseña, los cuales debemos recordar o en su defecto anotar. 
Una vez ingresados nuestro sistema estará listo para poder instalar Arduino.

![Fedora](/eleckia/img/fedora.png)

### Instalando Arduino
Ya casi estamos listos, damos click en **Actividades** y abrimos una terminal, 
la terminal puede ser intimidante, pero creeme una vez te acostumbres a ella la amaras.

En los sistemas linux tenemos diferentes tipos de usuarios cada uno de ellos con diferentes privilegios, el usuario con más privilegios es el usuario root, puede hacer y deshacer el sistema, nuestro usuario por defecto tiene pocos privilegios para evitar que el usuario pueda romper el sistema por accidente. Al abrir la terminal tendremos algo similar a esto

```
[eleckia@tuxin-lp ~]$
```

donde eleckia es el nombre de usuario que se eligió, y tuxin-lp es el nombre que se le dio a la pc, delante del signo $, podemos escribir.

DNF es el manejador de paquetes de Fedora, es una herramienta que nos permite actualizar, instalar y remover los programas y/o bibliotecas instaladas en nuestro sistema, para utilizarlo simplemente tecleamos

```
[eleckia@tuxin-lp ~]$ dnf
```

nos desplegara una enorme lista de las opciones disponibles para este comando, el que nos interesa por ahora es **update**, esta opción nos actualiza todos los paquetes a la última versión disponible, tecleamos

```
[eleckia@tuxin-lp ~]$ dnf update
Error: Este comando debe ejecutarse como usuario root.
```

el error nos indica que requerimos privilegios altos, la forma de escapar de la 
tiranía el usuario root, es anteponer la palabra **sudo**,

```
[eleckia@tuxin-lp ~]$ sudo dnf update
```

nos pedirá introducir la contraseña que creamos al iniciar Fedora por primera vez, la introducimos y nos desplegará la lista de actualizaciones disponibles, nos pedirá confirmación a lo que responderemos **s**. La primera vez que actualicemos, habrá muchas actualizaciones, por lo que se tardara un poco, al terminar de actualizar, reiniciaremos el sistema y abriremos nuevamente una terminal, una vez hecho esto instalaremos Arduino por fin. Para instalarlo tan fácil como escribir lo siguiente:

```
[eleckia@tuxin-lp ~]$ sudo dnf install arduino
```

terminada la descarga e instalación, ya tenemos disponible el software Arduino, hacemos click en actividades y lo ejecutamos, nos indicara que debemos agregar nuestro usuario a los grupos dialout y lock, le decimos que si e introducimos la contraseña del sistema, tenemos finalmente todo listo para comenzar a programar, tan solo nos falta conectar nuestro Arduino a un puerto usb.

Anteriormente mencione que trabajaremos con Arduino uno, sin embargo debo confesar que cualquier Arduino de 8 bits es valido, de hecho ni siquiera es necesario instalar Fedora, puedes instalar Arduino en casi cualquier sistema operativo, la razón de instalar Fedora es que nos ofrece todas las herramientas de manera simple e inmediata, al estar dentro de una máquina virtual no debemos preocuparnos de dañar el sistema accidentalmente, yo haré todo desde Fedora, por que es mi sistema instalado, pero sientete libre de utilizar lo que más te acomode, eso si, si tienes algún problema con tu sistema no podre ayudarte puesto que yo no lo uso (windows). Lo mismo va para la tarjeta Arduino que utilices, funciona cualquiera, pero los registros de cada microcontrolador varian mucho, sobre todo de una arquitectura a otra, los microcontroladores de 8 bits no tiene nada que ver con los micros de 32 o 16 bits.

No es de mi interés centrarme en Arduino, pero es la opción de aprendizaje más adecuada y sería tonto no empezar con ella.

Los lugares más comunes para adquirir la tarjeta es ebay, amazon y mercadolibre, es tu elección, también requerimos algunos dispositivos como leds, resistencias, transistores, capacitores, etc, en el siguiente post daremos algunos detalles adicionales.
### Fin
Finalizamos esta serie de post iniciales con una lista de materiales recomendada, 
en ebay existen una gran variedad de paquetes donde se incluyen Arduinos, 
resistencias, capacitores, transistores y algunos sensores, basta con ir a ebay 
o mercadolibre y buscar **kit Arduino**, adquirir uno de ellos es un buen comienzo.

Igualmente puedes adquirir kits de resistencias, capacitores y leds, además de jumpers, cables y un par de atmega328p DIP adicionales.

Con esto podemos comenzar nuestro estudio, más adelante requeriremos más componentes, pero se mencionarán con forme se requieran.
