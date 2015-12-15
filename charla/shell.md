# Utilizando scripts

Los scripts son ficheros donde colocamos distintas órdenes que se irán realizando de forma consecutiva una tras otra

## Usando la cámara

![camara](./imagenes/camara.jpg)

La cámara tiene su propio conector, junto a las conectores GPIO.

Antes de poder utilizarla tenemos que activarla

	sudo raspi-config

![activaCamara](./imagenes/activaCamara.png)

Tenemos 2 aplicaciones para usar la cámara

	raspistill

Tomará imágenes fijas

	raspivid

Grabará un vídeo

## Ímagenes estáticas

Si queremos cambiar el retraso con el se captura, usamos la opción -t indicando el tiempo en milisegundos:

	raspistill -o myimage.jpg -t 3000

Este programa tiene muchas opciones que podemos ver usando:

	raspistill | less
	-?, --help
	: This help information
	-w, --width
	: Set image width <size>
	-h, --height : Set image height <size>
	-q, --quality : Set jpeg quality <0 to 100>
	-o, --output : Output filename <filename>
	-v, --verbose : Output verbose information during run
	-t, --timeout : Time (in ms) before taking picture
	(if not specified, set to 5s)
	-th, --thumb
	: Set thumbnail parameters (x:y:quality)
	-d, --demo
	: Run a demo mode
	-e, --encoding : Output format (jpg, bmp, gif, png)
	-tl, --timelapse : Timelapse mode. Takes a picture every <t>ms
	-p, --preview : Preview window settings <'x,y,w,h'>
	-f, --fullscreen : Fullscreen preview mode
	-n, --nopreview : Do not display a preview window
	-sh, --sharpness : Set image sharpness (-100 to 100)
	-co, --contrast : Set image contrast (-100 to 100)
	-br, --brightness : Set image brightness (0 to 100)
	-sa, --saturation : Set image saturation (-100 to 100)
	-ISO, --ISO
	: Set capture ISO
	-vs, --vstab
	: Turn on video stablisation
	-rot, --rotation : Set image rotation (90,180,270)
	-hf, --hflip
	: Set horizontal flip
	-vf, --vflip
	: Set vertical flip

Entre estas opciones podemos encontrar **-tl** que nos va a permitir tomar una imagen cada cierto tiempo. Con ello podemos generar una secuencia de imágenes con una sola línea de comando

	raspistill -o myimage_%d.jpg -tl 2000 -t 25000

Una imagen cada 2 segundos durante 25 segundos Cada foto tendrá un número de secuencia 

	myimage_1.jpg
	myimage_2.jpg
	myimage_3.jpg
	myimage_4.jpg
	...

 Si deseamos utilizar un formato de nombre más complejo, siempre podemos usar un script como el siguiente que además guardará las imágenes en una carpeta

		SAVEDIR=/var/tlcam/stills
		while [ true ]; do
		filename=$(date -u +"%d%m%Y_%H%M-%S").jpg
		/opt/vc/bin/raspistill -o $SAVEDIR/$filename
		sleep 4;
		done;

## Vídeo

raspivid  nos va a permitir grabar vídeos. Para capturar 5s de vídeo en formato h264 utilizaremos:

	raspivid -o video.h264

Si queremos capturar 10 segundos usaremos:

	raspivid -o video.h264 -t 10000

Para ver todas las opciones disponibles podemos hacer

	$raspivid | less

Para una documentación más detallada sobre las opciones del ejecutable se puede consultar el siguiente [enlace](https://github.com/raspberrypi/userland/blob/master/host_applications/linux/apps/raspicam/RaspiCamDocs.odt)

## Cámaras web

Podemos usar cámaras USB compatibles  como  la PS3 Eye.

Veremos si se ha detectado con:

	$ ls -l /dev/video*

Si se detecta

![imagen](./imagenes/webcamdetected.png)

Instalamos fswebcam

	 sudo apt-get install fswebcam

Que nos permitirá tomar una imagen con

	 fswebcam -d /dev/video0 -r 640x480 test.jpeg

Hagamos ahora un script para hacer un timelapse

	#!/bin/bash
	# Timelapse controller for USB webcam
	DIR=/home/pi/timelapse
	x=1
	while [ $x -le 1440 ]; do
		filename=$(date -u +"%d%m%Y_%H%M-%S").jpg
		fswebcam -d /dev/video0 -r 640x480 $DIR/$filename
		x=$(( $x + 1 ))
		sleep 10;
	done;

Podemos ver que se están realizando capturas de imágenes cada 10 segundos y como mucho se guardarán 1440 imágenes.


	./runtimelapse

## Control remoto de camaras

![camaraPrio](./imagenes/camaraPro.png)

También podemos controlar cámaras profesionales que suelen admitir conexión USB (como por ejemplo una Canon Rebel T4i / 650D)

Utilizaremos el software gphoto2 que  instalaremos con 

	 sudo apt-get install gphoto2

Podemos controlar casi todos los valores de exposición, ISO, etc de nuestra cámara remotamente, pero para no complicarnos vamos a suponer que la usamos en modo automático.

Podemos capturar una imagen, que se mantendrá en la cámara con:

	$ gphoto2 --capture-image

Para tomar una imagen y enviarla a la raspberry usaremos

	$ gphoto2 --capture-image-and-download

La librería gphoto2 por defecto guarda las imágenes en la memoria RAM de la Raspberry (no en la SD) con lo que es necesario que lo configuremos para evitar perderlas al cortar la alimentación.

	$ gphoto2 --get-config /main/settings/capturetarget

Para establecer nuestro almacenamiento usaremos:

	$ gphoto2 --set-config /main/settings/capturetarget=NuestroDirectorio

Veamos ahora como hacer un time-lapse, es decir capturar las imágenes cada
cierto tiempo. Usaremos el siguiente comando.

	$ gphoto2 --capture-image -F 1440 -I 30

Que almacenará en la cámara un máximo de 1440 imágenes tomadas cada 30
segundos

## Convertir fotos a vídeo

Una vez tengamos todas las imágenes podemos generar un vídeo con ellas. 

Instalamos un software llamado mencoder que será el que genere el vídeo.

	$ sudo apt-get install mencoder

 Ahora generamos un fichero que contenga todas las imágenes que queremos unir en el vídeo

	$ cd timelapse
	$ ls *.jpg > list.txt

Y ejecutamos memcoder con los parámetros adecuados (es una sóla línea)

	$ mencoder -nosound -ovc lavc -lavcopts vcodec=mpeg4:aspect=16/9:vbitrate=8000000 -vf scale=640:480 -o timelapse.avi -mf type=jpeg:fps=24 mf://@list.txt

Con esto generaremos un vídeo de 640x480 de resolución, con nombre timelapse.avi codificado en mpeg4, a 24 frame por segundo y con las imágenes cuyos nombres se incluyen en el fichero list.txt

Si queremos hacer un vídeo a partir de las imágenes tomadas con la cámara original de Raspberry usaremos el siguiente comando

	$ mencoder -nosound -ovc lavc -lavcopts vcodec=mpeg4:aspect=16/9:vbitrate=8000000 -vf scale=1920:1080 -o tlcam.avi -mf type=jpeg:fps=24 mf://list.txt

Hay que tener cuidado de no llenar el almacenamiento, puesto que este proceso consume mucho espacio