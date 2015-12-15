# Acceso directo

Vamos a configurar nuestra raspberry y un portátil con Ubuntu para facilitar al máximo la conexión y así no tener que utilizar muchos componentes. De esta manera podremos trastear con un kit mínimo, evitando tener que usar un teclado, ratón y sobre todo un monitor.

![4](http://blog.elcacharreo.com/wp-content/uploads/2013/05/20130501_003523-150x150.jpg)

En concreto usaremos símplemente un cable de red (ethernet) y un cable micro-usb para alimentar la raspberry.

Con esta configuración no podemos consumir en total más de los 500mA que proporciona el USB.

Tendremos que modificar ficheros de configuración en el PC y en la raspberry.

Asumiremos que tenemos conexión a internet via Wifi y utilizaremos el cable ethernet para dar conectividad a la raspberry. Crearemos una red entre el portátil y la raspberry creando una subred distinta y haremos que el portátil actúe como gateway de esa red enrutando los paquetes hacia la raspberry y dándole acceso a internet.

Comencemos editando la configuración del pc, para lo que ejecutaremos en el pc:

	sudo vi /etc/network/interfaces

y dejamos el contenido del fichero (la red que se usa normalmente es las 192.168.1.x de ahí que el gateway sea 192.168.1.1 que es el real)

![3](http://blog.elcacharreo.com/wp-content/uploads/2013/05/paso1.png)

Ahora vamos a editar la configuración de la raspberry. La forma más sencilla es editando los ficheros de configuración desde el pc, para lo que insertamos la tarjeta sd de la raspberry (obviamente con esta apagada) en el pc y ejecutamos en este:

	sudo vi /media/10b4c001-2137-4418-b29e-57b7d15a6cbc/etc/network/interfaces

Quedando el mismo:

![2](http://blog.elcacharreo.com/wp-content/uploads/2013/05/paso2.png)

Ahora, colocamos la tarjeta sd en la raspberry y volvemos a encenderla

 
Conectamos el cable ethernet entre los dos

En el PC hacemos comprobamos que la tarjeta eth0 está ok y con la ip correspondiente, haciendo

	ifconfig /all

Veremos que aparece el interface eth0 con ip 192.168.0.80

Ahora vamos a hacer que el portátil actúe como router. Para ello ejecutamos los siguientes comandos

	sudo su -
	root@ubuntu-asus:~# echo 1 > /proc/sys/net/ipv4/ip_forward
	root@ubuntu-asus:~# /sbin/iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADEç

Por último editamos el fichero de configuración de DNS con

	sudo vi /etc/resolv.conf

y lo dejamos así

![1](http://blog.elcacharreo.com/wp-content/uploads/2013/05/paso3.png)


Ahora solo falta probar que tenemos conectividad, haciendo un ping

	ping 192.168.0.90


Si todo es correcto ya podremos acceder