---
layout: post
title: Requests y Selenium sobre Tor
summary: Automatizando peticiones HTTP bajo capas de cebolla
categories: python
---

¿Conoces el [proyecto Tor](https://www.torproject.org/about/overview.html.en)? Me extrañaría que no lo hubieras visto alguna vez demonizado en medios de comunicación o alabado en sitios *geek*. 

![Sugerencias de google al buscar Tor](/assets/tor-google.png)

Básicamente es un proyecto que permite conectarse a internet con mayor seguridad y privacidad implementando **Onion Routing**, hay montones de información de este proyecto para quien quiera profundizar sobre ello, cosa que no es mi intención en esta anotación.

La cuestión es que he estado hace unos días experimentando con Tor y 2 herramientas que uso habitualmente para automatizar tareas de navegación repetitivas con Python: [Selenium](https://selenium-python.readthedocs.org/) y [Requests](http://docs.python-requests.org/en/latest/). 

###Instalación de tor

En mi caso, en Mac OS usé homebrew para instalar tor. Para otras opciones hay un puñado de [guías de instalación](https://www.torproject.org/docs/installguide.html.en). 

{% highlight bash %}
brew install tor
{% endhighlight %}

Ejecutando el comando **tor** ya tendremos el servicio corriendo.

{% highlight bash %}
tor
{% endhighlight %}


###Usándolo con Selenium

Bien, no hemos tocado ningún tipo de configuración. Vayamos a por Selenium con el driver de Firefox, modificando algunas preferencias del profile. Le diremos que usamos un proxy manual y que va por protocolo [SOCKS](http://es.wikipedia.org/wiki/SOCKS) a una IP y al puerto de tor. En este ejemplo la IP de loopback y al puerto por defecto, el 9050.

{% highlight python %}
from selenium import webdriver
profile = webdriver.FirefoxProfile()
profile.set_preference('network.proxy.type', 1)
profile.set_preference('network.proxy.socks', '127.0.0.1')
profile.set_preference('network.proxy.socks_port', 9050)
driver = webdriver.Firefox(profile)
driver.get('http://estoeslapollaconcebol.la')
{% endhighlight %}

###Requests no, Requesocks!

 Pero bueno, igual queremos usar algo de más bajo nivel que un navegador web. Siguiendo con python, estuve viendo si con requests podría llegar a utlizarlo, cosa que no es posible ya que al parecer no es posible trabajar con socks. Por suerte, al ser requests *open source*, existe un *fork* del proyecto llamado [Requesocks](https://github.com/dvska/requesocks).

 Lo bueno es que el API es igualita a Requests, no hay diferencia en la forma de usarlo (la único incompatibilidad que encontré fue el poder usar el parámetro *stream* en una petición, desconozco la razón); y como el paquete simplemente está renombrado no hay problema en usar uno u otro.

 En fin, al turrón. Una vez instalado requesocks iniciaremos una sesión a la que le pasaremos los proxies para las peticiones http y https indicando de nuevo IP de loopback y el puerto 9050.

{% highlight python %}
import requesocks
session = requesocks.session()
session.proxies = {'http': 'socks5://127.0.0.1:9050', 'https': 'socks5://127.0.0.1:9050'}
session.get('http://avecesllorocortandocebol.la')
{% endhighlight %}

A partir de este momento usaríamos tor como proxy de todas nuestras peticiones y acciones de navegación que ejecutáramos automáticamente. Para estas cuestiones quedaría pendiente ver qué tal se entiende [Scrapy](http://scrapy.org/) con tor, pero no es algo a lo que le haya dedicado ningún tiempo.