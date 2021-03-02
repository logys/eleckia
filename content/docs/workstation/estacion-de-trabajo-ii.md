---
title: 'Estación de trabajo II'
date: Wed, 05 Jun 2019 02:24:29 +0000
draft: false
tags: ['Arduino', 'Fedora', 'Herramientas', 'Virtualbox']
---

En este post instalaremos Fedora en la maquina virtual.

Abrimos VirtualBox y seleccionamos la maquina virtual Fedora que creamos anteriormente, ahora damos click en **Configuración**, se nos abrirá una nueva ventana, seleccionamos **Almacenamiento** y en controlador IDE, seleccionamos **Vació**, a la derecha de unidad óptica damos click en el icono de un **disco azul** y seleccionamos el .iso de Fedora que descargamos anteriormente, finalmente damos click en aceptar.

![](https://eleckia.files.wordpress.com/2019/06/cd.png)

Si tienes anfitrión linux, ahí mismo en configuración seleccionamos **Pantalla** y cambiamos **Controlador Gráfico** a VBoxVGA y aumenta la memoria de video a por lo menos 64MB.

Nuevamente en **Configuración**, ahora seleccionamos **Sistema** y en la pestaña **Procesador**, seleccionamos el número de procesadores que tenemos, damos click en aceptar.

Nuestra maquina virtual, esta lista para arrancar y ya tiene el "disco" de instalación insertado, procedemos a iniciarla con el icono **Iniciar**.

Arrancamos y la primer pantalla de selección nos da tres opciones, seleccionamos la primera, **Start Fedora**, cuidado por defecto esta seleccionada la segunda opción.

Esperamos a que el sistema inicie y tenemos una versión live de Fedora funcionando,

![](https://eleckia.files.wordpress.com/2019/06/gnomefedora.png)

Gnome en Fedora

Esta versión de Fedora es totalmente funcional, podemos jugar con ella, revisar los programas y menus, cambiar la resolución, etc, sabedores de que los cambios que hagamos no se guardaran, cuando estemos listos damos click en Install to Hard Drive. Se abrirá el asistente de instalación Anaconda (puede que tarde unos minutos), aquí elegimos el idioma y la región y damos click en continuar,

![](https://eleckia.files.wordpress.com/2019/06/anaconda.png)

Anaconda

seleccionamos nuestra distribución de teclado(probablemente español latinoamericano) y la zona horaria, click en **Destino de la Instalación**, sin modificar nada damos click en **Hecho** en la esquina superior izquierda y comenzamos la instalación.

![](https://eleckia.files.wordpress.com/2019/06/empezarin.png)

Comenzar Instalación

Esperamos unos minutos a que termine la instalación y damos click en salir.

![](https://eleckia.files.wordpress.com/2019/06/finalizado.png)

Finalizado

Apagamos la maquina virtual y **removemos la imagen** .iso de Fedora.

Iniciamos nuevamente la maquina virtual esta vez iniciara Fedora instalado, todo lo que hagamos ahora si se guardara, nos dará la bienvenida un asistente de configuración donde nos pedirá algunos datos, de ellos lo importante es el usuario y la contraseña, los cuales debemos recordar o en su defecto anotar. Una vez ingresados nuestro sistema estará listo para poder instalar Arduino.

![](https://eleckia.files.wordpress.com/2019/06/fedora.png)

Fedora

Continuaremos con la instalación de Arduino en el siguiente post.