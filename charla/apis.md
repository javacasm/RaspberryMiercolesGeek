# APIS : conectando con el mundo exterior

Una de las ventajas de usar linux es que podremos integrarnos con muchos dispositivos para los que ya existen programas

## Integración con gtalk

Vamos a crear un bot que realiced las acciones que le enviemos desde cualquier dispositivo

	$ sudo apt-get install python-pip git-core python2.7-dev

Ahora actualizamos la lista de paquetes

	$ sudo easy_install -U distribute

Procedemos ahora a instalar los paquetes que vamos a necesitar

	$ sudo pip install Rpi.GPIO xmpppy pydns

Podemos [descargar el código](https://github.com/mitchtech/raspi_gtalk_robot) del enlace correspondiente.

En este código tendremos que cambiar los valores de las 3 variables a los correspondientes correos y contraseñas:

	BOT_GTALK_USER = 'bot_username@gmail.com'
	BOT_GTALK_PASS = 'password'
	BOT_ADMIN = 'admin_username@gmail.com'

Una vez configurado, lo ejecutamos (como root debido a que usa GPIO).

	$ sudo python ./raspiBot.py

y tendremos accesibles los siguientes comandos:

	[pinon|pon|on|high] [pin] : activa el GPIO pin
	[pinoff|poff|off|low] [pin] : apaga GPIO pin
	[write|w] [pin] [state] : escribe el estado en GPIO pin
	[read|r] [pin]: lee el estado de GPIO pin
	[shell|bash] [arg1] :ejecuta el comando que sigue a „shell‟ o „bash‟

## Reconocimiento de voz

Usaremos el API de Google, pero antes tenemos que digitalizar la voz para lo que instalaremos el paquete ffmpeg

	$ sudo apt-get install ffmpeg

Con este paquete crearemos un fichero que enviaremos a google via su API para recuperar el texto. Veamos todo esto en un script al que llamaremos speech2text.sh y al que daremos permiso de ejecución (chmod +x speech2text.sh)

	$ #!/bin/bash
	echo "Grabando.. Pulse Ctrl+C para parar."
	arecord -D "plughw:1,0" -q -f cd -t wav | ffmpeg -loglevel panic -y -i - -ar 16000 -acodec flac file.flac > /dev/null 2>&1
	echo "Procesando..."
	wget -q -U "Mozilla/5.0" --post-file file.flac --header "Content-Type:audio/x-flac; rate=16000" -O - "http://www.google.com/speech-api/v1/recognize?lang=en-us&client=chromium" | cut -d\" -f12 >stt.txt
	echo -n "Dijiste: "
	cat stt.txt
	rm file.flac > /dev/null 2>&1


## Preguntando a WolphranAlpha

Instalamos lo necesario

	$ apt-get install python-setuptools easy_install pip
	$ sudo python setup.py build
	$ sudo python setup.py

	#!/usr/bin/python
	import wolframalpha
	import sys
	# Obtenemos una clave en http://products.wolframalpha.com/api/
	# reemplaza la siguiente por la clave obtenida.
	app_id='HYO4TL-CLAVE'
	client = wolframalpha.Client(app_id)
	query = ' '.join(sys.argv[1:])
	res = client.query(query)
	if len(res.pods) > 0:
		texts = ""
		pod = res.pods[1]
		if pod.text:
			texts = pod.text
		else:
			texts = "No hay respuesta"
		# filtramos los caracteres
		texts = texts.encode('ascii', 'ignore')
		print texts
	else:
		print "No hay respuesta"

## Haciendo que nos hable nuestra raspberry

Instalamos el reproductor mplayer

	sudo apt-get install mplayer

Y con este script podemos hacer que nos lea lo que queramos

	#!/bin/bash
	say() { local IFS=+;/usr/bin/mplayer -ao alsa -really-quiet -noconsolecontrols "http://translate.google.com/translate_tts?tl=en&q=$*"; }
	say $*