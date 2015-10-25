---
layout: post
title: Personalizando la encriptación de devise
summary: Usando nuestro propio método de encriptación
categories: rails
---

Como se pueden haber visto durante muchas de [mis retros semanales](http://www.danilat.com/weblog/category/general/retro-semanal/), estuve trabajando en la migración de la tienda online de **[Bichomanía](http://www.bichomania.es/)**, de una plataforma desarrollada a medida había que pasar a una basada en [Spree](https://spreecommerce.com/).

Pues bien, una de las cosas que había que traer eran los datos de clientes ya existentes y que no tuvieran que crearse un nuevo usuario. Vamos, que se mantuviera su contraseña, por lo que había que mantener el mismo algoritmo de encriptación.

Como puede ser de suponer, Spree utiliza por debajo la gema de [devise](https://github.com/plataformatec/devise) para estas cuestiones. De modo que suponía que alguna forma habría de adaptarlo, y así fue, [había documentación de ello](https://github.com/plataformatec/devise/wiki/How-To:-Create-a-custom-encryptor).

Así que creé mi propio encryptor, de este estilo:

{% highlight ruby %}
#config/initializers/custom_encryptor.rb
require 'digest'

module Devise
  module Encryptable
    module Encryptors
      class CustomEncryptor < Base
        def self.digest(password, stretches, salt, pepper)
          Digest::SHA256.hexdigest password
        end
      end
    end
  end
end
{% endhighlight %}

Vale, una vez hecho esto me encontré con que tenía que extender la clase *Spree::User*, ya que no tengo un modelo usuario en mi aplicación como ponía en la documentación de devise. En la documentación de spree explican [cómo utilizar un modelo usuario propio](https://guides.spreecommerce.com/developer/authentication.html#the-user-model), pero vi que era un escenario en el que podía ser más limpio hacer uso de las posibilidades de metaprogramación que nos ofrece ruby.

De modo que lo que haríamos en un modelo propio, lo añadimos vía el uso de *[class_eval](http://apidock.com/ruby/Module/class_eval)*. Y quedaría algo así:

{% highlight ruby %}
#app/models/user_decorator.rb
Spree::User.class_eval do
  
  #le decimos que use nuestro encryptor
  devise :encryptable, :encryptor => :custom_encryptor

  #sobreescribimos el método de validación del password
  def valid_password?(password)
    Devise.secure_compare(
      Devise::Encryptable::Encryptors::CustomEncryptor.digest(password, nil, nil, nil),
      self.encrypted_password
    )
  end

  #como no hacemos uso de salt para encriptar el password, machacamos el getter y setter
  def password_salt
  'no salt'
  end
  def password_salt=(new_salt)
  end
end
{% endhighlight %}

Profit.