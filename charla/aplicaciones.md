# Aplicaciones hechas en python

Utilizaremos la libre pyGTK que permite crear e interacionar ventanas desde python

[Tutorial de la OSL](https://github.com/oslugr/curso-python-avanzado/blob/master/Interfaces_Graficas_con_PyGTK/contenido_PyGtk.md)

	#!/usr/bin/python
	# -*- coding: utf-8 -*-
	from gi.repository import Gtk

	class MyWindow(Gtk.Window):

	    def __init__(self):
	        Gtk.Window.__init__(self, title="Hola Mundo!")

	        self.button = Gtk.Button(label="Hazme click")
	        self.button.connect("clicked", self.on_button_clicked)
	        self.add(self.button)

	    def on_button_clicked(self, widget):
	        print("Hola Mundo!")

	def main():
	    win = MyWindow()
	    win.connect("delete-event", Gtk.main_quit)
	    win.show_all()
	    Gtk.main()

	main()

![gtk](https://github.com/oslugr/curso-python-avanzado/raw/master/img/InterfacesGtk_01_hola_mundo.png)

Utilizaremos Glade para diseñar el interface

![glade](https://github.com/oslugr/curso-python-avanzado/raw/master/img/InterfacesGtk_02_Glade_01.png)


# PyGame

Si lo que queremos hacer es un juego podemos usar pyGame

[Ejemplo sencillo](https://github.com/oslugr/curso-python-avanzado/blob/master/Videojuegos_con_PyGame/hola_pygame.md)

	#!/usr/bin/env python
	# -*- coding: utf-8 -*-

	# Importamos la librería
	import pygame

	# Iniciamos Pygame
	pygame.init()

	# Creamos una surface (la ventana de juego), asignándole un alto y un ancho
	Ventana = pygame.display.set_mode((600, 400))

	# Le ponemos un título a la ventana
	pygame.display.set_caption("Hola Mundo")

[Ejemplo de animaciones](https://github.com/oslugr/curso-python-avanzado/blob/master/Videojuegos_con_PyGame/animando_sprites.md)

![ejemplo](https://github.com/oslugr/curso-python-avanzado/raw/master/img/monigotillo.png)

[Tutorial de la OSL](https://github.com/oslugr/curso-python-avanzado/tree/master/Videojuegos_con_PyGame)