---
layout: post
title: Pon jekyll en tu vida
summary: ¿Cómo montar jekyll?
categories: jekyll
---

En [SenpaiDevs](http://senpaidevs.com) hemos propuesto a los kōhais que se crearan su propio blog para que vayan compartiendo sus avances, y para aprovechar para empezar a practicar con git y github decidimos tirar por [Jekyll](http://jekyllrb.com/), un CMS basado en ficheros de texto.

Antes de instalarlo hay que tener ruby ya instalado en el sistema, ya que jekyll se instala como una gema, también hay que tener nodejs (al parecer también soporta otros runtime JS).

Luego simplemente instalamos la gema.

{% highlight bash %}
gem install jekyll
{% endhighlight %}

Una vez instalado, en el directorio donde se quiera trabajar se ejecuta 

{% highlight bash %}
jekyll new blog
cd blog
jekyll serve
{% endhighlight %}

Y en cuanto veamos

{% highlight bash %}
  Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
{% endhighlight %}

Es que ya está corriendo y podremos ver el blog, dentro del directorio **_posts** escribiremos los posts en formato *[markdown](http://daringfireball. net/projects/markdown/)*. El nombre de los ficheros de los posts deben empezar con el formato de fecha *yyyy-MM-dd*.

Como hemos dicho que lo íbamos a hospedar en github, hay que inicializar el repositorio y hacer los commits necesarios. Tras ello hay que crearse un repositorio con el formato *tuusername.github.io* en github y añadirlo como remoto. A partir de ese momento al hacer *push* se publicará el blog en esa *tuusername.github.io*.

###Bonus track

Como se puede ver, en este blog tengo mi propio dominio. Para hacerlo es bastante simple: 

* Creamos un fichero llamado *CNAME* en el directorio raíz del blog y ahí ponemos el dominio o subdominio que queramos
* En nuestro proveedor de DNS añadimos un registro de tipo CNAME que apunte a *tuusername.github.io*.

###Y listo!

Ya podemos empezar a publicar posts escritos en ficheros de texto con markdown y publicarlos con git en github, un rollete bastante geek ¿que no?