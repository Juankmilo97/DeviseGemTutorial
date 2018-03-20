# DeviseGemTutorial

Este repo explica como configurar una app con **Ruby on Rails** usango la gema de Devise y la autenticación que ésta soporta.

## Paso-A-Paso

Sigue las instrucciones a continuación para configurar una **App con Rails** usando la gema de **Devise** en *Cloud9 (AWS)*, en una máquina con **S.O. Linux** y desplegarla en **Heroku**, usando las notificaciones por correo electrónico.

 1. Vamos a utilizar el *Ruby Version Manager* como la herramienta más facil para actualizar y manejar el *lenguaje Ruby*. Ejecuta la siguiente linea de código:

```type rvm | head -1 ```
  
   **Resultado esperado:**
 
```rvm is a function```

 2. Verifiquemos la versión con el commando:
 
```rvm -v```

   **Resultado esperado:**
  
```rvm 1.29.2 (latest) by Michal Papis, Piotr Kuczynski, Wayne E. Seguin [https://rvm.io/]```

 3. Vamos actualizar el Ruby a la versión 2.3.:
 
```rvm install 2.3```

 4. Configurarlas para su uso por defecto en la terminal:
 
```rvm use 2.3```

```rvm --default use 2.3```

 5. Verificamos la versión de Ruby con:
 
```ruby -v```

   **Resultado esperado:**
    
```ruby 2.3.3p222 (2016-11-21 revision 56859) [x86_64-linux]```

 6. Procedemos a instalar la gema de *Rails* y a actualizar la versión de *Node.js* cuando vayamos a usar los archivos de Front-End:
 
```gem install rails```

```nvm install v8.9.4```

 7. Generamos una *llave SSH* que nos identifique de forma única con la aplicación y con la plataforma a la que posteriormente vamos a desplegar la app (**Heroku**):
 
```ssh-keygen -C example@dominio.com -t rsa```

   Realizamos los pasos que por defecto va solicitando la terminal. Se recomienda asignar un Passphrase distinto a vacío dado que si alguien obtiene esa llave puede autenticarse sobre nuestra aplicación y realizar cambios indeseados.
   
 8. Añadimos la nueva *llave SSH al agente de autenticación* por medio de los siguientes comandos:
    Iniciamos el agente de autenticación con:
    
```eval `ssh-agent` ```

   Añadimos la *llave SSH al agente de autenticación*:
   
```ssh-add ~/.ssh/id_rsa```

 9. Verificamos que tengamos instalado el *Heroku-CLI* para poder desplegarlo posteriormente:
 
```heroku version```

   **Resultado esperado:**
   
```heroku-cli/6.14.4-d011db2 (linux-x64) node-v8.4.0```

 10. Añadimos nuestra **llave SSH de autenticación a Heroku** con:
 
```heroku keys:add```

   Va a solicitar que se inicie sesión con la *cuenta de Heroku*, seguir los pasos solicitados en la terminal.
   
 11. Creamos una aplicación en *Rails*, y probamos que esté funcionando correctamente:
 
```rails new testapp```

```cd testapp```

```rails server -b 0.0.0.0```

   La URL para ver la aplicación corriendo es:
   
```http://nameworkspace-username.c9users.io/```

 12. Modificamos el *GemFile* para que en el despliegue de la App tanto en *Heroku* como en *Cloud9* su funcionamiento sea el esperado:
```
# GemFile

## Use sqlite3 as the database for Active Record
## gem 'sqlite3'
gem 'pg'
gem 'simple_token_authentication', '~> 1.0' # see semver.org

...

gem 'bcrypt', '~> 3.1.7' # Descomentar ésta linea de código.

...

group :development, :test do
  # Call 'byebug' anywhere in the code to stop execution and get a debugger console
  gem 'sqlite3'
  gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
  # Adds support for Capybara system testing and selenium driver
  gem 'capybara', '~> 2.13'
  gem 'selenium-webdriver'
end

group :production do
  # Call 'byebug' anywhere in the code to stop execution and get a debugger console
  gem 'pg'
  gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
  # Adds support for Capybara system testing and selenium driver
  gem 'capybara', '~> 2.13'
  gem 'selenium-webdriver'
end

...
```

 13. Actualizar la gema de Bundle, y correr el siguiente comando para instalar las gemas requeridas. Además instalar la gema de Devise para generar los modelos y controladores que se van a usar en las migraciones de las bases de datos para :
 
```gem install bundler```
```bundle install```

   Instalación de la Gema de Devise
   
```rails generate devise:install```

 14. Configurar los archivos de Ruby para desarrollo y producción (`testapp/config/environments/development.rb` y `*/production.rb`) de la aplicación para las notificaciones por medio del correo electrónico:
 
```
config.action_mailer.default_url_options = { host: 'http://nameworkspace-username.c9users.io/', port: 8080 }
config.action_mailer.delivery_method = :smtp 
config.action_mailer.smtp_settings = { address: "smtp.dominio.com", port: 587, authentication: "plain", enable_starttls_auto: true, user_name: "example@dominio.com", password: "password" }
```

 15. Configurar el archivo de Ruby para completar el email desde el que se van a enviar las notificaciones con *Devise Mailer* en `testapp/config/initializers/devise.rb`:
 
```
  # Descomentar config.secret_key
  config.secret_key = '9e7339d059c094f86bb5bf6a06aad9b7701382d6f48c4f0ddaa7781702d1434d05f7d40881b11cf1eecce6174867f3b40e009b9f7295de41ceffcc596012755b'
  
  ...
  
  config.mailer_sender = 'example@dominio.com'
  
  ...
```

 16. Crearemos un controlador de ejemplo para este tutorial para que visualicemos el funcionamiento de ésta autenticación
 
```rails generate controller Pages```

   Crearemos además un modelo Usuario utilizando la gema de **Devise** Devise.

```rails generate devise user```

 17. Añadimos al archivo __*/routes.db*__ la ruta que ejecutará la pagina de incio sobre el controlador *Pages*:
 
 ```
 root 'pages#home'
 ```
 
