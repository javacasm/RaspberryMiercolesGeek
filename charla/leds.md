# GPIO

![1](./imagenes/GPIORasp.png)

* Son los pines que podemos usar como salidas o como entradas, pero siempre de tipo digital.
* Utilizan 3.3V
* Podemos configurar cada uno como entrada o como salida
* Algunos de ellos se pueden usar como comunicaciones especializadas: SPI, I2C, UART


# Precauciones

* Antes de realizar cualquier tipo de conexión en los conectores o pines debemos de tener siempre la precaución de tener desconectada la alimentación de la Raspberry Pi.
* Evitaremos derivaciones eléctricas o cortos .
* Conviene recordar que los pines de la CPU de la placa están conectados directamente a los diferentes conectores y pines, con lo que cualquier cosa que hagamos sobre los pines la estamos haciendo directamente sobre la CPU.
* También hay que tener en cuenta que los pines GPIO no soportan 5 V, sólo 3.3V y un máximo de 16 mA, por lo que hay que tomar precauciones en este sentido.

## Pines

Hay que tener cuidado con no equivocarse. Podemos usar una etiqueta

![1](./imagenes/etiquetas.png)

Las distinas versiones tienen algunos pines distintos

![1](./imagenes/GPIOV2.png)

Las versiones de 40 pines

![40](./imagenes/RP2_Pinout.png)

## Librerías

Hay ~~cuatro~~ 5 librerías GPIO

* Shell (línea de comandos)
* Rpi. GPIO
* wiringPi (Gordon Henderson wiringpi.com)
* BCM 2835
* [ **GPIO ZERO**](https://www.raspberrypi.org/blog/gpio-zero-a-friendly-python-api-for-physical-computing/) ([tutorial](https://www.raspberrypi.org/documentation/usage/gpio-plus-and-raspi2/))



Veamos como llaman a los distintos pines

![1](./imagenes/NombresGPIO.png)

## Wiring

Para instalarlo tenemos que tener instalado parte del entorno de desarrollo de python

	sudo apt-get install python-dev python-setuptools git-core

Descargamos el código (también podíamos haber descargado el fichero zip)

	git clone git://git.drogon.net/wiringPi

La compilamos

	./build

Y ya podemos udarla

	gpio readall


![readall](./imagenes/readall.png)

## Conectando un led

Este es el esquema para conectar un led

![led](./imagenes/led.png)

El montaje sería

![esquema](./imagenes/esquemaled.png)

Hagamos un programa que parpadea el led conectado

	import time
	# Importamos la librería wiringpi
	import wiringpi2
	#Configuramos la numeración de los pines con respecto al
	#estandar de la librería wiringpi (pin de entrada salida
	#	GPIO0)

	io = wiringpi2.GPIO(wiringpi2.GPIO.WPI_MODE_PINS)

	#Configuramos el pin 0 como salida
	io.pinMode(0,io.OUTPUT)

	# Ciclo for que ejecutamos 3 veces
	for x in range (0,3):
		io.digitalWrite(0,io.HIGH) #encendemos el led
		time.sleep(0.5) # esperamos medio segundo
		io.digitalWrite(0,io.LOW) # apagamos el led
		time.sleep(0.5) # esperamos medio segundo

Para ejecutar estos programas necesitamos permiso de administrador

	sudo python blink.py

# Conectado un pulsador

![pulsador](./imagenes/pulsador.png)

Usando el código

![codigo](./imagenes/codigopulsador.png)

## Usando GPIO

Instalamos la librería

	sudo apt-get install python-dev python-rpi.gpio

El programa que los usa

	import RPi.GPIO as GPIO
	# Usamos la numeración de los GPIO no el numero de los pines
	GPIO.setmode(GPIO.BCM)
	GPIO.setup(7, GPIO.IN) # establecemos el GPIO 7 como entrada
	GPIO.setup(8, GPIO.OUT) # establecemos el GPIO 8 como salida
	input_value = GPIO.input(7) # recuperamos el valor de entreda
	GPIO.output(8, True) # establecemos la salida en alto

O este ejemplo más complejo

	import RPi.GPIO as GPIO
	import time
	# Usamos la posición en el conector
	GPIO.setmode(GPIO.BOARD)
	# pin 11 (GPIO17) como output
	GPIO.setup(11, GPIO.OUT)
	var=1
	print "Empezamos el bucle infinito"
	while var==1 :
		print "Output False"
		GPIO.output(11, False)
		time.sleep(1) # esperamos un tiempo
		print "Output True"
		GPIO.output(11, True)
		time.sleep(1)


## Usando más potencia

En el caso bastante normal de que necesitemos más potencia de las que nos da un pin (16mA) Podemos utilizar un transistor. Veamos el montaje

![transistor](./imagenes/transistor.png)


A la salida de este transistor podemos conectar un relé para obtener aún más potencia

## Leyendo valores analógicos

Para leer valores analógicos usaremos electrónica externa, com pueden se [esta placa](http://www.abelectronics.co.uk/products/3/Raspberry-Pi/17/) o [esta otra](http://www.adafruit.com/products/1085), ambas de 16 bits. El [montaje es sencillo](http://learn.adafruit.com/reading-a-analog-in-and-controlling-audio-volume-with-the-raspberry-pi)

![adc](./imagenes/adc.png)



## Usos de los GPIOs

* Encender apagar LEDs (no podemos aspirar a encender nada de mayor potencia directamente). Estas son las salidas digitales, capaces de estar en estado alto o bajo.
* Algunos de estos pines pueden generar PWM (modulación por ancho de pulso) protocolo que usan los servos.
* Detectar pulsaciones de botones/interruptores. Estas son las entradas digitales.
• Acceso al puerto serie por los terminales TX/TX
• Acceso al bus I2C, bus de comunicaciones usado por muchos dispositivos
• Acceso al bus SPI, bus de comunicaciones similar al I2C pero con diferentes especificaciones

El bus I2C y SPI nos permiten conectar con dispositivos externos que nos
expanden su funcionalidad. Es como si conectáramos periféricos a nuestra
Raspberry.

![pines](./imagenes/pi2GPIO.jpg)

* También están disponibles las líneas de alimentación de 5v y 3.3v y por supuesto tierra.

* Todos los pines se pueden configurar tanto de entrada como de salida.

* Algunos de los pines tienen una segunda función como por ejemplo los etiquetados como SCL y SDA utilizados para I2C y los MOSI, MISO y SCKL utilizados para conectar con dispositivos SPI.
* Hay que tener muy claro que todos los pines usan niveles lógicos de 3.3V y no es seguro conectarlos directamente a 5V, porque las entradas han de ser menores de 3.3V. Igualmente no podemos esperar salidas superiores a 3.3V.
* En caso de querer conectar con lógica de 5v tendremos que usar una electrónica para adaptar niveles.
* Existen dispositivos convertidores de niveles (level shifters) con diferentes tecnologías. Los más antiguos están formados por unas resistencias y unos transistores.

![shifper](./imagenes/shifter.png)

Para identificar más fácilmente los pines podemos usar una etiqueta

![etiqueta](./imagenes/etiqueta.png)

## Placas GPIO

### Clobber

* Es bastante arriesgado y complicado trabajar directamente con los pines del conector GPIO de la RaspBerry.
* Existen en el mercado una gran variedad de placas que nos facilitan la vida.
* Algunas sólo nos facilitan la conexión.
* Otras nos proporcionan mayor funcionalidad.
* En cualquier caso ganamos en tranquilidad al usarlas.


![clobber](./imagenes/clobber.png)

Son simples adaptadores que nos facilitan la vida permitiendo conectar de manera sencilla
con las placas de prototipo

### PiPlate

![piplate](./imagenes/piplate.png)

Se trata de una placa de prototipo especialmente adaptada al tamaño de la Raspberry y que nos permite acceder de forma sencilla a los pines por nombre y funcionalidad.

### PiFace

![piface](./imagenes/piface.png)

* Tiene un fin claramente educativo,
* Incluye diferentes dispositivos
* Leds que se pueden activar independientemente,
* 2 relés para activar cargas de potencia y
* 4 pulsadores conectados a otras tantas entradas

![esquemapiface](./imagenes/esquemapiface.png)

### Slice of I/O

![slice](./imagenes/slice.png)

Se trata de una p laca sencilla que nos permite acceder a 8 entradas y otras tantas salidas con la seguridad de que existe una electrónica que protege a nuestra RaspBerry

### Gertboard

![gertboard](./imagenes/gertboard.png)

Es una placa de desarrollo con una enorme cantidad de complementos, como son controladores de motores, ADC, DAC, 12 leds, 3 pulsadores y hasta un microcontrolador ATMega (similar a Arduino)

### RaspiRobot

![raspirobot](./imagenes/raspirobot.png)

* El manejo de motores es mucho más complejo que el manejo de leds.
* La programación es exactamente la misma,
* La electrónica necesaria para controlarlos es totalmente diferente.
* Si bien podemos conectar directamente un led a un pin de GPIO, conectar un motor es totalmente desaconsejable, por varias razones:
	* La primera porque los motores requieren de mayor potencia para funcionar,
	* Necesitaremos una electrónica capaz de gestionar estas potencias
	* Serán controladas desde los pines de la RaspBerry.
	* En caso de forzar la electrónica de alimentación de nuestra Raspberry a dar una mayor potencia podríamos quemarla.
	* El funcionamiento de los motores hace que estos generen al acelerar unas corrientes de inducción de sentido opuesto a las que les aplicamos para funcionar y que de no ser suprimidas podrían dañar la electrónica a la que están conectados.

En la [web de raspbirobot](https://github.com/simonmonk/raspirobotboard/wiki) vemos instrucciones de montaje

* Controla 2 motores,
* 2 leds,
* 2 entradas de pulsador,
* 2 salidas de colector abierto, para poder usar mayores potencias
* Conector I2C y
* otro serie

Descargamos la librería

	wget https://github.com/simonmonk/raspirobotboard/archive/master.zip

y la instalamos

	sudo python setup.py install

Un programa podría ser

	from raspirobotboard import *
	rr = RaspiRobot() # creamos el objeto
	rr.set_led1(1) # activamos el led 1
	rr.set_led2(0) # desactivamos el led 2
	rr.set_oc1(1) # activamos la salida 1
	rr.forward() # movemos los dos motores hacia adelante
	rr.reverse() # movemos los dos motores hacia atrás
	rr.left() # motor izquierdo hacia adelante, derecho hacia atrás
	rr.right() # motor izquierdo hacia atrás, derecho hacia adelante
	rr.stop() # los dos motores hacia atrás
	rr.sw1_closed() # devuelver True o False según cerrado o abierto


### Steppers: motores paso a paso

![stepper](./imagenes/stepper.png)

Los motores paso a paso son motores que nos permiten una gran precisión de giro, pudiendo determinar su moviendo en grados.

Vamos a ver cómo usar el motor de la imagen, que tiene 4 bobinas. La placa de control es muy sencilla y necesita de 4 pines para controlarla (en realidad la placa sólo transforma la salida de los pines de raspberry en una señal de la potencia que necesita el motor)

Veamos como conectarla
	5V (P1-02)
	GND (P1-06)
	Inp1 (P1-18)
	Inp2 (P1-22)
	Inp3 (P1-24)
	Inp4 (P1-26)

Vamos a ver ahora la programación.

	import timeimport RPi.GPIO as GPIO
	GPIO.setmode(GPIO.BCM)
	StepPins = [24,25,8,7] # Pines que conectamos a la placa de control
	for pin in StepPins: # configuramos todos los pines como salida
		GPIO.setup(pin,GPIO.OUT)
		GPIO.output(pin, False)
		StepCounter = 0
		WaitTime = 0.5
		StepCount1 = 4
		Seq1 = []
		Seq1 = range(0, StepCount1) # Definimos la secuencia de giro
		Seq1[0] = [1,0,0,0]
		Seq1[1] = [0,1,0,0]
		Seq1[2] = [0,0,1,0]
		Seq1[3] = [0,0,0,1]
		while 1==1: # realizamos un bucle infinito enviando la secuencia
			for pin in range(0, 4): #iteramos sobre los pasos de la secuencia
				xpin = StepPins[pin]
				if Seq[StepCounter][pin]!=0:
					GPIO.output(xpin, True)
				else:
					GPIO.output(xpin, False)
				StepCounter += 1
				time.sleep(WaitTime)

Veamos un ejemplo de su precisión

![polarplot](./imagenes/polarplot.png)		

## Servos

Los servos son motores pensados para mantener una posición concreta, disponen de electrónica de control propia y a la se le indica la posición que deben mantener mediante un pulso que hay que enviar 50 veces por segundo.

El ancho de este pulso determina la posición a mantener, como podemos ver en la imagen adjunta.

![servocontrol](./imagenes/servocontrol.png)

La estabilidad de la posición depende de la precisión con la enviemos la señal de control.

Veamos un método para generar esta señal con python. Está pensada para controlar 2 servos:

	def mover_servo(grados,servo):
		if servo==1: GPIO_servo=22
		elif servo==2: GPIO_servo=21
		# creamos el pulso
		pos_servo=(0.0000122*grados)+0.0002
		GPIO.output(GPIO_servo, True) #activamos la salida
		time.sleep(pos_servo) # esperamos la duración del pulso
		GPIO.output(GPIO_servo, False) # desativamos la señal porque el pulso ha terminado
		#esperamos el tiempo necesario hasta enviar el siguiente pulso
		time.sleep(0.0025-pos_servo)


Si lo probamos veremos que el servo vibra debido a la mala calidad de la señal por su falta de estabilidad. Python es un lenguaje interpretado y temporización que hemos hecho dependerá de la carga que tenga nuestra Raspberry

Podemos mejorar la calidad de la señal utilizando un programa escrito en C que producirá una mejor temporización.


## Uniendolo todo

Vamos a utilizar un par de servos para hacer que una cámara [siga una cara](http://www.instructables.com/id/Pan-Tilt-face-tracking-with-the-raspberry-pi/?ALLSTEPS)

![opencvCamara](./imagenes/opencvCamara.png)

Estos son los pasos para instalar todo lo necesario

	sudo apt-get update
	sudo apt-get install git python-opencv python-all-dev libopencv-dev
	sudo modprobe servoblaster
	git clone https://github.com/mitchtech/py_servo_facetracker

Y para ejecutarlo

	cd py_servo_facetracker
	python ./facetracker_servo_gpio.py 0
