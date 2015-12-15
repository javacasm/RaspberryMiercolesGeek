# Vigilancia

Podemos usar su cámara (la original o una USB)

Usaremos un software standard de Linux: motion

	sudo apt-get install motion

Editamos la configuracion

	sudo nano /etc/motion/motion.conf

![motion](./imagenes/motion.jpg)

Lo arrancamos

	sudo montion -n


Podremos acceder a la imagen en vivo de la cámara con

	 http://rasperry_ip:8001