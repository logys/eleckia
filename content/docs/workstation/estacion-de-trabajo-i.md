---
title: 'Estación de trabajo I'
date: Tue, 04 Jun 2019 22:52:26 +0000
draft: false
tags: ['Arduino', 'Fedora', 'Herramientas', 'Virtualbox']
---

**No es obligatorio** instalar Fedora, pero todos los ejemplos se harán desde este sistema operativo, eres libre de adaptar lo que aquí se mencione a tus circunstancias.

Para poder trabajar a gusto es necesario disponer de un conjunto de herramientas adecuado, dejando de lado el tipo de computadora con la que se cuenta, lo importante es el sistema operativo, como se menciono en el post anterior utilizaremos software libre, por lo que nuestro sistema operativo sera linux, concretamente Fedora, esta distribución cuenta con los paquetes necesarios para nuestros propósitos, así que si utilizas algo diferente comienza borrado tu disco duro e instala Fedora (esto es broma obviamente).

La manera más sencilla de comenzar en este mundo (y la que seguiremos aquí) es con linux + arduino, hablemos rápida y brevemente de ellos.

Linux es una pieza de software que funciona como interfaz entre el hardware de una computadora (memoria, procesador, periféricos, etc), en pocas palabras y de manera muy general, linux se encarga de administrar el hardware de una PC, por otro lado existe software (GNU) que se comunica con linux para formar un sistema operativo, de ahí surgen los sistemas GNU/Linux, a grandes rasgos el software GNU se encarga de administrar el sistema, enviándole instrucciones a Linux (comúnmente llamado kernel o núcleo) y este último las ejecuta en el hardware. Para el que este interesado en entender un poco más sobre el origen y filosofía de GNU/linux y el software libre, les recomiendo ver el documental "Código linux", disponible en youtube en español. Compañías aprovechan este conjunto de software para crear distribuciones, en nuestro caso trabajaremos con la distribución Fedora, patrocinada por Red Hat, disponible para su descarga gratuitamente.

Arduino es una plataforma de desarrollo, contiene tanto software como hardware, sera común referirnos de manera indistinta al software o hardware con la palabra Arduino, con respecto al hardware hay muchas versiones, siendo el "Arduino uno" el más común y el que utilizaremos, este Arduino cuenta con un microcontrolador atmega328p, es un microcontrolador de la empresa Microchip, anteriormente le pertenecía a Atmel, pero fue comprada por la anterior mencionada, el microcontrolador es de 8 bits y contiene numerosos periféricos. Arduino es software libre pero también hardware libre, esto implica que los clones funcionen de manera idéntica a el hardware original, aunque si puedes comprarlo original siempre es bueno apoyar a los creadores. En la parte de software Arduino cuenta con su propio entorno de desarrollo (aunque a mi me parece horrible) y cuenta con miles de bibliotecas listas para ser usadas en nuestros proyectos.

Lo que requerimos:

\-**Instalar Fedora  
\-Adquirir un Arduino  
\-Instalar Arduino dentro de Fedora**

Comencemos instalando Fedora, como mencionamos Fedora es un sistema operativo, por lo tanto requerimos instalarlo en nuestra PC, podemos instalar junto a nuestro sistema operativo actual, podemos reemplazar nuestro sistema operativo actual por Fedora o si ya utilizamos Fedora como sistema operativo principal no hay necesidad de reinstalarlo, la última opción es instalarlo en una maquina virtual, si eres usuario windows probablemente esta opción es la más adecuada para ti.

Una maquina virtual es un entorno que permite ejecutar un sistema operativo adicional sobre uno ya en ejecución, el sistema adicional se conoce como "huesped"(guess) y el que ya esta en ejecución se le denomina "anfitrión"(host), de esta manera podemos instalar Fedora sin la necesidad de modificar nuestro sistema operativo actual.

Procedemos a instalar la maquina virtual, para ello visitamos el enlace [https://www.virtualbox.org/](https://www.virtualbox.org/)  
y descargamos la versión más reciente para nuestro sistema operativo anfitrion(probablemente windows), no entrare en detalles sobre como instalar virtual box puesto que cada sistema es diferente, pero confió en que sabes como instalar programas en tu sistema, para instalarlo en windows doble click sobre el ejecutable descargado, si eres usuario linux seguramente viene en los repositorios oficiales de tu distribución.

Ahora descargaremos Fedora, accedemos al enlace  
[https://getfedora.org/es/](https://getfedora.org/es/)  
existen varias ediciones, elegimos Workstation y la descargamos, la descarga es una imagen .iso de alrededor de 2GB, una vez descargado procedemos a instalar.

Abrimos Virtualbox y seleccionamos "nueva"

![](https://eleckia.files.wordpress.com/2019/06/vbox-1.png)

Se abrirá una nueva ventana escribimos "Fedora" en nombre, carpeta la dejamos por defecto, seleccionamos, tipo "Linux" y Versión "Fedora (64-bit)", click en siguiente.

Ahora nos pide configurar la memoria, aqui aparece un control de deslizamiento, selecciona por lo menos 2GB y da click en siguiente.

Ahora nos pide configurar un nuevo disco duro, seleccionamos "Crear un disco virtual Ahora" y damos click en siguiente.

Tipo de archivo de disco duro, VDI, siguiente.

Almacenamiento en unidad de disco duro física, Reservado dinámicamente, siguiente.

Ubicación del archivo y tamaño, dejamos la ubicación por defecto y seleccionamos un tamaño de 20GB, siguiente.

Hemos finalizado la creación de la maquina virtual, procedemos a instalar Fedora en el siguiente post.
