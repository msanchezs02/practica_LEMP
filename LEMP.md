# Ejercicio de balanceo de carga sobre dos servidores

>En este ejercicio lo que se propone es crear cuatro máquinas virtuales, una de ellas actuará como balanceador de carga, la cual tendrá una red publica mediante la cual los usuarios se podrán conectar a nuestro balanceador, y una red privada que será la misma donde están conectados los dos servidores. Dos de las cuatro máquinas serán nuestros servidores web, que, en este caso, trabajamos con Nginx, estos servidores tendrán dos redes privadas, una para poder conectarse con el balanceador, y otra para poder conectarse con el servidor donde está alojado MySQL. En la última máquina, tendremos MySQL con la correspondiente base de datos de nuestra aplicación.
 
# Configuración de las máquinas

>Todas las configuraciones de las cuatro máquinas, salvo algunas configuraciones que deberemos estar dentro de la propia máquina, se harán mediante un script de aprovisionamiento para agilizar el proceso de configuración de las máquinas.

# Balanceador de carga

![](fotosnginx/balanceador/script.png)

>Primeramente, actualizamos repositorios con sudo apt update y upgrade. Después de actualizar repositorios, instalamos el servidor web que va a tener nuestro balanceador, que en este caso es Nginx.

![](fotosnginx/balanceador/file.png)

>En el Vagrant File configuramos nuetro balanceador de carga con una dirección IP publica, mediante la cual los usuarios van a poder conectarse a nuestro servidor donde tenemos la aplicación. También tiene una dirección IP privada que, esta dirección, sirve para tener conexión con los servidores donde tenemos nuestra aplicación. La máquina está provisionada mediante un script.

![](fotosnginx/balanceador/1.png)

>Comprobamos que, después de instalar Nginx en el balanceador, el servicio está corriendo perfectamente en nuestro balanceador.

![](fotosnginx/balanceador/2.png)

![](fotosnginx/balanceador/3.png)

>Comprobamos que tenemos conectividad con los servidores donde alojaremos nuestra aplicación. Estos servidores tienen que estar en la misma red que nuestro balanceador.

![](fotosnginx/balanceador/4.png)

>Para empezar a configurar nuestro balanceador de carga, primero tenemos que eliminar el fichero "default" del directorio /etc/nginx/sites-enabled.

![](fotosnginx/balanceador/5.png)

>Ahora, creamos el fichero donde alojaremos la configuración para que la maquina actue como balanceador de carga. La configuración sería la siguiente:

![](fotosnginx/balanceador/6.png)

>Comprobamos que hemos hecho bien la configuración del archivo con el siguiente comando:

![](fotosnginx/balanceador/7.png)

>Como podemos ver, no tenemos ningún error de sintaxis, con lo cual, la configuración del fichero se ha realizado correctamente.

>Por último, reiniciamos Nginx para aplicar las configuraciones realizadas.

![](fotosnginx/balanceador/8.png)



# Servidores Nginx

![](fotosnginx/servidornginx/script.png)

>Primeramente, actualizamos repositorios con sudo apt update y upgrade. Después, instalamos Nginx, php-fpm y php-mysql.

![](fotosnginx/servidornginx/file.png)

>En el vagrant file, configuramos nuestros servidores con dos direcciones IP privadas, una de ellas servirá para conectarse con el balanceador de carga, y la otra dirección IP servirá para conectarse con el servidor MySQL. Ambas máquinas son provisionadas a través de un script.

>La siguiente configuración es aplicable a ambas máquinas.

![](fotosnginx/servidornginx/1.png)

>Comprobamos tras la instalación que el servicio está corriendo perfectamente en nuestra máquina.

![](fotosnginx/servidornginx/2.png)

>Dentro del directorio /var/www tenemos que crear un directorio en el cual tendremos alojados todos los archivos referentes a nuestra aplicación.

![](fotosnginx/servidornginx/3.png)

>Tras crear el directorio, cambiamos el dueño y el grupo al que pertenece ese directorio.

![](fotosnginx/servidornginx/4.png)

>Dentro de ese directorio, clonamos el repositorio donde están todos los archivos de nuestra aplicación.

![](fotosnginx/servidornginx/5.png)

>Dentro de el repositorio clonado, se encuentra un directorio llamado "src", cuyo contenido son los archivos de la aplicacion. Todo el contenido de este directorio lo movemos al directorio que hemos creado al principio.

![](fotosnginx/servidornginx/6.png)

>Borramos el repositorio despues de mover los archivos.

![](fotosnginx/servidornginx/7.png)

>Dentro del directorio /etc/nginx/sites-available se encuentra un archivo llamado "default". Tenemos que hacer una copia de este archivo, en este caso la copia del archivo se llama sitionginx.

![](fotosnginx/servidornginx/8.png)

>Dentro de este archivo, en la linea donde pone root /var/www/html, tenemos que cambiar el directorio html por el directorio que hemos creado al principio donde tenemos los archivos de la aplicación.

![](fotosnginx/servidornginx/9.png)

>Después de configurar la copia del fichero "default", tenemos que hacer un enlace del fichero "sitionginx" para que se encuentre en el directorio sites-enbabled. Comprobamos que hemos creado correctamente el enlace

![](fotosnginx/servidornginx/10.png)

![](fotosnginx/servidornginx/11.png)

>La configuración del siguiente fichero es para que Nginx pueda interpretar código PHP. En este caso, para que Nginx pueda interpretar código PHP, haremos la configuración de un socket TCP/IP

![](fotosnginx/servidornginx/12.png)

>En el apartado "listen" del fichero tenemos que indicar que Nginx y PHP van a estar en la misma máquina, con lo cual, tenemos que poner la dirección localhost, es decir, la dirección 127.0.0.1, y el puerto donde va a escuchar, por ejemplo el 9000.

![](fotosnginx/servidornginx/13.png)

>Despues de configurar este archivo, reiniciamos el servicio php7.4-fpm.


![](fotosnginx/servidornginx/14.png)

>Volvemos a configurar la copia del archivo "default".

![](fotosnginx/servidornginx/15.png)

>En la linea donde están los archivos index con varias extensiones, tenemos que poner index.php para que Nginx pueda buscar archivos con extensión .php y poder leerlos. También hay que descomentar varias lineas del archivo, y en la linea donde pone "fastcgi_pass", tenemos que volver a poner la dirección localhost y el puerto (127.0.0.1:9000).

![](fotosnginx/servidornginx/16.png)

>Tras hacer dicha configuración, con el comando "nginx -t" comprobamos que no hay ningún error en la configuración que hicimos.

![](fotosnginx/servidornginx/17.png)

>Después de realizar las configuraciones, reiniciamos Nginx.

>Una vez realizados los pasos anteriores, tenemos que configurar nuestro servidor para que pueda conectarse con el servidor MySQL.

![](fotosnginx/servidornginx/18.png)

>Empezamos esta configuración editando el archivo "config.php".

![](fotosnginx/servidornginx/19.png)

>En el apartado 'DB_HOST', tenemos que indicarle la dirección IP del servidor MySQL, que es 192.168.6.10.

![](fotosnginx/servidornginx/20.png)

>Después de editar el archivo, reiniciamos Nginx.

![](fotosnginx/servidornginx/21.png)

>Instalamos el cliente de MySQL para poder conectarnos al servidor.

![](fotosnginx/servidornginx/22.png)

>Comprobamos que la configuración se ha realizado correctamente conectandonos al servidor con el comando "mysql -u (usuario que creamos en el servidor MySQL) -p -h (dirección IP del servidor MySQL)".


# Servidor MySQL

![](fotosnginx/servidormysql/script.png)

>Primero, actualizamos repositorios con sudo apt update y upgrade. Una vez hecho esto, instalamos MySQL y le asignamos una contraseña al root.

![](fotosnginx/servidormysql/file.png)

>En el Vagrant File, configuramos nuestro servidor de base de datos con una dirección IP privada, mediante la cual, los servidores podrán conectarse a él. La máquina se aprovisiona mediante un script.

![](fotosnginx/servidormysql/1.png)

>Vemos que, tras haber instalado MySQL, el servicio está corriendo perfectamente.

![](fotosnginx/servidormysql/2.png)

>Editamos el archivo "50-server-cnf" que se encuentra en el directorio /etc/mysql/mariadb.conf.d.

![](fotosnginx/servidormysql/3.png)

>En el campo bind-address tenemos que poner la dirección IP de la máquina donde está corriendo MySQL, que, en este caso, es 192.168.6.10.

![](fotosnginx/servidormysql/4.png)

>Reiniciamos MySQL tras haber modificado el archivo anterior.

![](fotosnginx/servidormysql/5.png)

>Nos metemos dentro de MySQL y creamos el usuario que tendrá nuestra aplicación para acceder a la base de datos y le asignamos todos los privilegios sobre la base de datos de la aplicación. Cuando ponemos 'nombre usuario'@'%', el simbolo % significa que el usuario se podrá conectar desde cualquier maquina al sevidor de base de datos, siempre y cuando las máquinas estén en la misma red que el servidor. Comprobamos que se ha creado el usuario correctamente.

![](fotosnginx/servidormysql/6.png)

![](fotosnginx/servidormysql/7.png)

>Volviendo a nuestra máquina, a simple vista creemos que todo funciona, pero para que funcione, necesitamos tener una base de datos en la cual almacenar diferentes tipos de datos, para ello, tenemos que volver a clonar el repositorio que clonamos anteriormente y, entrar dentro del directorio "db", en el cual se encuentra el archivo de creación de la base de datos llamado "databse.sql".

![](fotosnginx/servidormysql/8.png)

![](fotosnginx/servidormysql/9.png)

>En este fichero tenemos que eliminar las tres ultimas lineas, ya que la creación del ususario y la asignación de privilegios lo hemos hecho antes. Con lo cual el archivo queda así.

![](fotosnginx/servidormysql/10.png)

>A continuación, importamos el fichero de creación de la base de datos a nuestro servidor.

![](fotosnginx/servidormysql/11.png)

>Comprobamos que la base de datos ha sido creada correctamente.

![](fotosnginx/servidormysql/12.png)

>Después de hacer todo esto, reiniciamos MySQL.