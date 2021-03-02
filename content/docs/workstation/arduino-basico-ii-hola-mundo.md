---
title: 'Arduino Básico II.- Hola Mundo'
date: Wed, 05 Jun 2019 19:58:34 +0000
draft: false
tags: ['Arduino', 'Ardunio']
---

Prometo que ahora si comenzamos. :D

Conectamos nuestro Arduino a nuestra PC por medio del cable USB, si estas utilizando Virtual Box, es necesario que le des permiso al "invitado" para que use el puerto USB, esto se hace en el menú de Virtual Box, **devices->USB-> dispositivo correspondiente a Arduino(probablemente FTDI)**. Para verificar que nuestro Arduino esta conectado y lo reconoce el sistema, abrimos una terminal y tecleamos

```
[eleckia@tuxin-lp ~]$ dmesg
```

si el dispositivo esta conectado obtendremos una salida similar a esta

```
[26227.988155] usb 2-1: new full-speed USB device number 3 using ohci-pci
[26228.164271] usb 2-1: New USB device found, idVendor=2341, idProduct=0043, bcdDevice= 0.01
[26228.164279] usb 2-1: New USB device strings: Mfr=1, Product=2, SerialNumber=220
[26228.164284] usb 2-1: Manufacturer: Arduino (www.arduino.cc)
[26228.164287] usb 2-1: SerialNumber: 7553334343635121A191
[26228.282966] cdc_acm 2-1:1.0: ttyACM0: USB ACM device
[26228.287986] usbcore: registered new interface driver cdc_acm
[26228.287989] cdc_acm: USB Abstract Control Model driver for USB modems and ISDN adapters
```

Esto implica que nuestro Arduino esta conectado y la PC lo reconoce como un dispositivo ACM, ahora abrimos el IDE de Arduino, la primera vez que lo abrimos nos aparecerá algo como esto. (Si no aparece la salida anterior, probablemente no seleccionaste el dispositivo correcto en virtual box, prueba con otro.)

*   ![](https://eleckia.files.wordpress.com/2019/06/arduino.png)
    

hacemos click en **herramientas->Puerto** y seleccionamos el puerto al que este conectado el Arduino, probablemente solo haya uno disponible, en mi caso **/dev/ttyACM0**, en el caso de que no apareciera ningún puerto disponible, lo más probable es que te falte agregar tu usuario a los grupos lock y dialout simplemente,

```
[eleckia@tuxin-lp ~]$ sudo gpasswd -a $USER lock
[eleckia@tuxin-lp ~]$ sudo gpasswd -a $USER dialout
```

debes salir de la sesión o en su defecto reiniciar tu sistema.

Continuamos, el "Hola Mundo" es un programa tradicional en el mundo de la programación, consiste en mostrar la frase "Hola mundo" en la pantalla, traducida a los sistemas embebidos consiste en hacer parpadear un led. El entorno de desarrollo trae el código ya escrito, vamos a **Archivo->Ejemplos->Basic->Blink** nos abrirá una ventana con el siguiente código

```
// the setup function runs once when you press reset or power the board
void setup() {
  // initialize digital pin LED\_BUILTIN as an output.
  pinMode(LED\_BUILTIN, OUTPUT);
}

// the loop function runs over and over again forever
void loop() {
  digitalWrite(LED\_BUILTIN, HIGH);   // turn the LED on (HIGH is the voltage level)
  delay(1000);                       // wait for a second
  digitalWrite(LED\_BUILTIN, LOW);    // turn the LED off by making the voltage LOW
  delay(1000);                       // wait for a second
}
```

Sin modificar nada, vamos a subir este programa a la tarjeta, tan solo hacemos click en el icono de la palomita para compilar y después en el icono de la flecha para subir, o con las teclas ctrl+R y ctrl+U respectivamente. Si todo esta correcto veremos que el led de nuestro Arduino comienza a parpadear cada segundo aproximadamente.