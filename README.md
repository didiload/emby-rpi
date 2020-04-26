# emby-rpi

https://gitforwindows.org/
git clone

repositorio
https://github.com/disaned/emby-rpi.git

git status
gid add .

Los ficheros seleccionados se muestran en un apartado "Cambios almacenados provisionalmente". Escriba el mensaje descriptivo del commit en la caja superior y pulse Ctrl+Intro

HABILITAR SSH

Antes de introducir la tarjeta a la raspberry, desde el pc creamos un fichero vacio así:

ssh

# si no inicia lo realizaremos de la siguiente manera:

pi
raspberry
sudo systemctl enable ssh
sudo systemctl start ssh

# desconectar y conectar energia

# Metemos la microSD en la Pi y le conectamos el cable miniUSB para que se encienda. También es muy importante conectarla mediante cable de red (NO por Wifi), para poder aprovechar su red gigabit.

# Dejamos que termine de cargar y calculamos 1 minuto que tengamos la posibilidad de escribir, conectamos el teclado con números y pondremos el siguiente comando:

pi                                       y enter
raspberry                          y enter
sudo raspi-config            y enter   -la raya al medio con el símbolo ? del teclado en español
4 flechas abajo                y enter
1 flecha abajo                  y enter
1 flecha a la izquierda     y enter dos veces
tab y flecha a la derecha   y enter

si necesita reiniciar sudo reboot

buscaremos la IP en el router e ingresamos al terminal con el programa PuTTY

pondremos el siguiente comando:

sudo apt-get update

Y cuando termine haremos lo mismo con:

sudo apt-get upgrade

Si en alguno de estos comandos nos pregunta algo, pulsaremos la tecla Y (de Yes) y enter, para que siga descargándose las últimas actualizaciones e instalándolas. Una vez haya terminado con todo, pondremos el comando:

sudo raspi-config

Así nos meteremos en el configurador del sistema operativo. Pues bien, seleccionaremos la opción 4: localisation options y a continuación pulsaremos en Change locale:

Bajaremos hasta encontrar nuestra distribución de teclado, que en nuestro caso es la es_ES.UTF-8 UTF-8 la seleccionaremos pulsando la barra espaciadora y quitamos la que venía configurada, luego le daremos a la tecla tabulador y a Aceptar:

Nos preguntará que distribución queremos dejar, volveremos a seleccionar es_ES.UTF-8 UTF-8 y pulsaremos Aceptar y listo, ya lo tendremos en español:

Volveremos a donde estábamos y nos iremos a Change Timezone y con la tecla tabulador pulsaremos en Enter:

Y ahora seleccionaremos America y luego Bogota, para poner la zona horaria a la nuestra

Ahora vamos a asignarle una IP fija a nuestra Pi, para eso tendremos que saber si en nuestra red se usa el rango 192.168.1.1 o el 192.168.0.1, en cualquier PC con Windows, si le damos a propiedades de red nos lo dirá. En mi caso es el 192.168.1.1, así que le voy a asignar la IP 192.168.1.140 a la Pi. Para ello y ya fuera del configurador de la Pi, pondremos el comando:

sudo nano /etc/dhcpcd.conf

Quitamos los simbolos # o copiamos y pegamos esto:


interface eth0
static ip_address=192.168.0.16/24
static routers=192.168.0.1
static domain_name_servers=192.168.1.1

interface eth0
static ip_address=192.168.0.16/24
static routers=192.168.0.1
static domain_name_servers=190.157.8.100 8.8.8.8 fe80::98d:80d4:dab7:882%15

configurar de acuerdo a su ip y dns

Pulsaremos CTRL+X para guardar y si nos pregunta pulsaremos la tecla S (de Si) o Y (de Yes, si está aún en inglés) y enter. 

Y listo, ya tenemos una IP fija asignada a nuestra Raspberry, recordadla bien que nos hará falta.

Bien, vamos a poner ahora el kernel de 64 bits a la Pi, para poder instalar luego xteve (imprescindible para IPTV, guía EPG, etc) y luego ya terminaremos esta sección con la instalación de Emby Server y la de Xteve.

Para activar los 64 bits primero tendremos que actualizar el firmware de la Pi, se hace de forma muy sencilla, poniendo el comando:

sudo rpi-update

Seguramente nos dirá que hay una nueva versión disponible, pulsaremos Y (de Yes) para confirmar y empezará a bajarse la última versión y a instalarla. 

Es posible que se reinicie la Pi al finalizar:

Al terminar y ya de vuelta en la terminal, pondremos el comando:

sudo nano /boot/config.txt

Bajaremos al final del todo del documento y pondremos lo siguiente:

#64 Bit
arm_64bit=1

Y pulsaremos CTRL+X para guardar y si nos pregunta pulsaremos la tecla S (de Si) o Y (de Yes, si está aún en inglés) y enter. 

Para terminar ponemos el comando sudo reboot para finalizar la instalación con un reinicio. 

Cuando volvamos a tener operativa la terminal, pondremos el comando uname -a y veréis como os aparecerá una línea de información donde comprobaremos que aparece la palabra aarch64, con eso nos vale para confirmar que estamos trabajando con el kernel de 64 bits.

Asignar un password al usuario root:

sudo passwd root

Tras lo que se os pedirá que introduzcáis dos veces el password en este caso igual al de pi:
raspberry.


1. Instalar RClone
Rclone es un programa de línea de comandos para sincronizar archivos y directorios desde / hacia.

Para instalarlo, vamos a hacerlo de la forma más sencilla (no os colapséis por tener que usar la terminal XD).

Abriremos la terminal, donde tendremos que escribir lo siguiente:

curl https://rclone.org/install.sh | sudo bash              Y pulsamos INTRO.

Reiniciamos la Raspberry Pi con  y volveremos a abrir la terminal. Ahora pondremos esto:sudo reboot

sudo dpkg -i rclone*.deb              Y pulsamos INTRO.

Si no funciona este comando saltar al punto 2. Configurar RClone 

rm rclone*.deb                                       Y pulsamos INTRO.

Ya tenemos RClone instalado.


2. Configurar RClone

Con la terminal abierta, pondremos lo siguiente:

rclone config                                      Y pulsamos INTRO.

Y nos saldrá un menú como este:

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> n

Ahora le introduciremos n y le damos a INTRO.  Con esto haremos una nueva cuenta. Nos saldrá esto:
name> 

Aquí lo que nos pide es que le pongamos un nombre a la unidad. En mi caso puse drive. Así que introducís drive y le dais a INTRO.

Una vez puesto el nombre, RClone nos arrojará todos los servicios disponibles con los que podremos configurarlo:

Type of storage to configure.
Enter a string value. Press Enter for the default (“”).
Choose a number from below, or type in your own value
1 / A stackable unification remote, which can appear to merge the contents of several remotes
\ “union”
2 / Alias for a existing remote
\ “alias”
3 / Amazon Drive
\ “amazon cloud drive”
4 / Amazon S3 Compliant Storage Providers (AWS, Ceph, Dreamhost, IBM COS, Minio)
\ “s3”
5 / Backblaze B2
\ “b2”
6 / Box
\ “box”
7 / Cache a remote
\ “cache”
8 / Dropbox
\ “dropbox”
9 / Encrypt/Decrypt a remote
\ “crypt”
10 / FTP Connection
\ “ftp”
11 / Google Cloud Storage (this is not Google Drive)
\ “google cloud storage”
12 / Google Drive
\ “drive”
13 / Hubic
\ “hubic”
14 / JottaCloud
\ “jottacloud”
15 / Local Disk
\ “local”
16 / Mega
\ “mega”
17 / Microsoft Azure Blob Storage
\ “azureblob”
18 / Microsoft OneDrive
\ “onedrive”
19 / OpenDrive
\ “opendrive”
20 / Openstack Swift (Rackspace Cloud Files, Memset Memstore, OVH)
\ “swift”
21 / Pcloud
\ “pcloud”
22 / QingCloud Object Storage
\ “qingstor”
23 / SSH/SFTP Connection
\ “sftp”
24 / Webdav
\ “webdav”
25 / Yandex Disk
\ “yandex”
26 / http Connection
\ “http”

En mi caso es Google Drive. Así que que introduciré el número 12 y le daré a INTRO.
En nueva actualizacion es el número 13

Seguidamente, nos mandará un primer comando:

client_id     Pulsamos INTRO. 

un segundo comando:

client_secret    Pulsamos INTRO.

Ahora nos pedirá que le digamos que tipo de acceso queremos concederle. Le daremos acceso completo:

scope>
Introducimos 1  y pulsamos INTRO.

Seguidamente nos mandará dos comandos más los cuales tenemos que dejar en blanco pulsando INTRO en cada uno.

root_folder_id>
service_account_file>

Ahora nos mandará dos comandos más donde la respuesta será n e INTRO:
Edit advanced config? (y/n)
Use auto config? (y/n)
Ahora nos enviará al navegador para que iniciemos sesión con nuestra cuenta de Google Drive. Una vez iniciada, nos saldrá un código bastante largo el cual tendremos que copiar y pegar en la terminal, y damos a INTRO.

IMPORTANTE! En este paso nos pregunta si nuestra cuenta es una cuenta privada o una cuenta Team, tenéis que saber que tipo de cuenta habéis comprado para responder con n (si no es Team) o con y (si sí que lo es), y damos a INTRO.

 Y cerramos introduciendo q,       y damos a INTRO.


3. Montar Google Drive
Este paso es la clave de este tutorial, ya que lo que vamos a hacer es virtualizar nuestra Google Drive en la Raspberry.  ¿Que significa eso? Que haremos que aparezca un disco interno con todos los archivos de Google Drive, pero estos archivos no estarán en nuestra Raspberry, sino en GD.

Manos a la obra! Lo primero que tendremos que hacer es abrir la terminal y copiar esto:

sudo apt-get install fuse      y pulsamos INTRO

Ahora crearemos la carpeta en nuestro HOME donde irán los archivos de Google Drive, para ello intruciremos:

mkdir /home/pi/drive         y pulsamos INTRO

Ahora introduciremos el comando:

sudo nano /etc/fuse.conf        y pulsamos INTRO

Nos llevará a un menú donde nos saldrán diferentes líneas en color azul que empiezan con un #. Lo que tendremos que hacer es irnos al final y desmarcar la última línea. Las dos últimas líneas tendrán que quedar así:


# /etc/fuse.conf - Configuration file for Filesystem in Userspace (FUSE)

# Set the maximum number of FUSE mounts allowed to non-root users.
# The default is 1000.
#mount_max = 1000

# Allow non-root users to specify the allow_other or allow_root mount options.
#user_allow_other



#Allow non-root users to specify the allow_other or allow_root mount options.
#user–allow-others

Allow non-root users to specify the allow_other or allow_root mount options.
user_allow_other


# /etc/fuse.conf - Configuration file for Filesystem in Userspace (FUSE)

# Set the maximum number of FUSE mounts allowed to non-root users.
# The default is 1000.
#mount_max = 1000

Allow non-root users to specify the allow_other or allow_root mount options.
user_allow_other

Ahora damos a Ctrl + X para salir, y nos preguntará que si queremos guardar, le decimos que sí.

Seguidamente montaremos la unidad con:

rclone mount drive: /home/pi/drive --allow-other &       y pulsamos INTRO

Podremos observar como en el escritorio nos sale la unidad montada como si fuese un disco duro. Eso es porque todo ha salido bien jeje.



4. Configurar Crontab

Este paso es MUY IMPORTANTE, ya que nos permitirá que la unidad se monte cada vez que se inicie el sistema, por el contrario, tendríamos que volver a realizar este último proceso cada vez que se apague la Raspberry.

Para ello, introduciremos:

crontab -e             y pulsamos INTRO

Ahora nos aparecerá otro menú con un montón de líneas.
En este caso, tendremos que irnos hasta el final y poner una última línea con esto:

#
@reboot rclone mount drive: /home/pi/drive --allow-other &

Para terminar ponemos el comando sudo reboot para finalizar la instalación con un reinicio.

Ahora ya tenemos listo tanto Google Drive como las herramientas para virtualizar su contenido.

Podemos acceder ahora a verificar que la nube se ha montando accediendo a la carpeta 

cd /home/pi/drive

listando el contenido con un dir
regresar con cd ..


Ya lo tenemos todo listo.

¿Qué hacer si esto no funciona?

Hay usuarios que me han reportado que este sistema para automontar la nube no les ha funcionado y han tenido que recurrir a otras opciones que paso a detallar por si os encontráis con el citado problema.

Solución 1 (de Inés Guerrero): Crear un script con el mismo comando en:
etc/init.d

Solución 2 (de Jorge y Unai Rodri): Cambiar los guiones por guiones bajos con lo que nos quedaría lo siguiente:
@reboot rclone mount drive: /home/pi/drive __allow_other &

Solución 3 (de Antonio José Candel): editar crontab mediante la orden 
sudo crontab -u pi -e 
y posteriormente añadir la línea 
@reboot rclone mount drive: /home/pi/drive --allow-other &



Instalar Emby

Ahora le toca el turno a Emby, que es más fácil. Para ello os recomendaría instalar una versión beta, que están más actualizadas. Nos vamos a la web de emby server beta, nos pondremos sobre la última versión disponible (la primera que sale) y le daremos a Assets. Ahora buscaremos el paquete que termine en armhf.deb, nos pondremos con el ratón encima y haremos click derecho –> copiar dirección de enlace. Ahora nos vamos a Putty, escribiremos wget (ojo que tiene espacio detrás del wget) y haremos click derecho con el ratón para que se pegue el enlace, quedando así:

sudo wget https://github.com/MediaBrowser/Emby.Releases/releases/download/4.5.0.6/emby-server-deb_4.5.0.6_armhf.deb

Pulsamos enter y se decargará. Para instalarlo, es tan sencillo como poner el comando sudo dpkg -i nombredelpaquete.deb (en mi caso emby-server-deb_4.5.0.5_armhf.deb) y pulsamos enter para que se instale:

Como ya no necesitamos más ese archivo lo podemos borrar con:

rm nombredelpaquete.deb

Para acabar con tanta instalación, nos queda como último paso instalar el paquete ffmpeg. Para ello nada más sencillo que poner el comando:

sudo apt-get install ffmpeg 

y listo, con eso se nos instalará (si pregunta en algún paso pulsaremos la tecla S (de Si) o Y (de Yes, si está aún en inglés) y enter).

Finalizamos con un reinicio poniendo el comando sudo reboot y pulsando enter ¡y listo! Ya tenemos nuestra Pi con el kernel de 64 bits, en español, con el teclado español, con una IP fija, con xteve y emby instalados y que autoarrancan al encender la Pi y con SSH activado para hacerlo todo cómodamente a través de un PC. Nos queda ahora configurar Emby y Xteve para terminar la guía.

Utilizar Emby por el puerto 80:

https://www.sysadminsdecuba.com/2018/06/emby-una-manera-diferente-de-ver-peliculas-y-serie/


PASANDO LA INSTALACIÓN A UN DISCO SSD, MUY RECOMENDABLE
Con todo ya funcionando y sin problema, os recomendaríamos un último paso: usar un SSD USB como memoria en lugar de una microsd, ya que es muchísimo más rápida y tiene una tasa de operaciones de I/O (entrada y salida) mucho más alta. De hecho, para IPTV es imprescindible si no queremos sufrir cortes y congelaciones. Su instalación es algo complicada, principalmente porque no hay nada de informacion en la red. Así que vamos con ello.
Lo primero será que abramos Putty y nos conectaremos a nuestra Pi (ya con la el SSD USB conectado -y la microSD también, ésta se tiene que dejar siempre puesta aunque el sistema luego cargue desde el SSD). Lo primero será identificar el SSD, para ello pondremos el comando sudo fdisk -l, ahora nos fijaremos en la unidad que tenga el tamaño de nuestro SSD, en mi caso es /dev/sda:

Ahora pondremos el comando sudo fdisk /dev/sda (cambiar por el vuestro si fuera diferente) , para iniciar el SSD. Nos preguntará el asistente que que hacemos a continuación, pulsaremos d y le daremos al enter, para que elimine cualquier partición que existiera previamente. Una vez hecho, pulsaremos la tecla n y enter (para que nos cree una nueva partición), luego nos preguntará si queremos que sea primaria o extendida, como queremos que sea primaria pulsaremos la tecla p y pulsaremos enter. A continuación nos preguntará varias cosas, podemos dejarlas en blanco solo pulsando enter hasta que vuelva a salirnos lo de «command (m for help)» donde pulsaremos w para finalizar y aplicar los cambios.
Una vez fuera del asistente de creación de particiones, pondremos el comando sudo mkfs.ext4 /dev/sda pulsaremos Y (de Yes) y enter y ya terminará. Ahora crearemos el punto de montaje con el comando sudo mkdir /media/external y montaremos el SSD con el comando sudo mount /dev/sda /media/external y terminamos sincronizando todo con sudo rsync -avx / /media/external con eso habremos copiado todo el contenido de la microSD al nuevo SSD. Nos queda el último paso: indicárselo al boot.
Para finalizar, pondremos el comando: sudo cp /boot/cmdline.txt /boot/cmdline.txt.bak para tener una copia de seguridad del boot. Ahora lo editamos con sudo nano /boot/cmdline.txt ahí buscaremos la palabra root= , quitaremos los números que encontraremos después del igual -ojo, solo los de esa palabra, no todos los que hay después- y los sustituiremos por /dev/sda, de forma que quede: root=/dev/sda pulsaremos CTRL+X para guardar y si nos pregunta pulsaremos la tecla S (de Si) o Y (de Yes, si está aún en inglés) y enter. Y listo, ya tenemos todo en nuestro flamante SSD. Apagamos y encendemos la Pi y veréis que todo va muchísimo más rápido que antes, síntoma de que lo tenemos todo funcionando en el SSD (la microSD no la quitéis, nos hace falta solo para que cargue el boot). Si tenéis dudas de este último proceso, podéis verlo en vídeo -en inglés- en el siguiente link, a partir del minuto 3.
ESO ES TODO AMIGOS
Bueno y si has llegado hasta aquí enhorabuena, es un trabajo bastante lioso y complicado, pero esperamos que con la guía que hemos elaborado te sea más sencillo. Ya lo tienes todo listo, solo te queda lo más fácil: bajarte la app para tu dispositivo y al abrirla, solo tendrás que conectarte con tu cuenta de Emby y nada más…¡a disfrutar!



https://www.avpasion.com/configura-emby-server-raspberry-smart-tv-rival-kodi-plex/







Instalar Jellyfin

 
 
mkdir path
cd path
mkdir to
 
 
mkdir /path/to/config
mkdir /path/to/cache
mkdir /path/to/media
 
version: "3"
services:
  jellyfin:
    image: jellyfin/jellyfin
    user: 1000:1000
    network_mode: "host"
    volumes:
      - /path/to/config:/config
      - /path/to/cache:/cache
      - /path/to/media:/media
 
 





Port Range Forwarding
External Port(s)
Internal Host/IP
Internal Port(s)
Protocol
Enabled
Actions


8096 - 8096
192.168.0.16
8096 - 8096
BOTH
Yes




32400 - 32400
192.168.0.12
32400 - 32400
BOTH
Yes







##
# You should look at the following URL's in order to grasp a solid understanding
# of Nginx configuration files in order to fully unleash the power of Nginx.
# https://www.nginx.com/resources/wiki/start/
# https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/
# https://wiki.debian.org/Nginx/DirectoryStructure
#
# In most cases, administrators will remove this file from sites-enabled/ and
# leave it as reference inside of sites-available where it will continue to be
# updated by the nginx packaging team.
#
# This file will automatically load configuration files provided by other
# applications, such as Drupal or Wordpress. These applications will be made
# available underneath a path with that package name, such as /drupal8.
#
# Please see /usr/share/doc/nginx-doc/examples/ for more detailed examples.
##

# Default server configuration
#
server {
        listen 80 default_server;
        listen [::]:80 default_server;

        # SSL configuration
        #
        # listen 443 ssl default_server;
        # listen [::]:443 ssl default_server;
        #
        # Note: You should disable gzip for SSL traffic.
        # See: https://bugs.debian.org/773332
        #
        # Read up on ssl_ciphers to ensure a secure configuration.
        # See: https://bugs.debian.org/765782
        #
        # Self signed certs generated by the ssl-cert package
        # Don't use them in a production server!
        #
#
server {
        listen 80 default_server;
        listen [::]:80 default_server;

        # SSL configuration
        #
        # listen 443 ssl default_server;
        # listen [::]:443 ssl default_server;
        #
        # Note: You should disable gzip for SSL traffic.
        # See: https://bugs.debian.org/773332
        #
        # Read up on ssl_ciphers to ensure a secure configuration.
        # See: https://bugs.debian.org/765782
        #
        # Self signed certs generated by the ssl-cert package
        # Don't use them in a production server!
        #
        # include snippets/snakeoil.conf;

        root /var/www/html;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name _;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }

        # pass PHP scripts to FastCGI server
        #
        #location ~ \.php$ {
        #       include snippets/fastcgi-php.conf;


        root /var/www/html;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name _;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }

        # pass PHP scripts to FastCGI server
        #
        #location ~ \.php$ {
        #       include snippets/fastcgi-php.conf;
        #
        #       # With php-fpm (or other unix sockets):
        #       fastcgi_pass unix:/run/php/php7.3-fpm.sock;
        #       # With php-cgi (or other tcp sockets):
        #       fastcgi_pass 127.0.0.1:9000;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #       deny all;
        #}
}


# Virtual Host configuration for example.com
#
# You can move that to a different file under sites-available/ and symlink that
# to sites-enabled/ to enable it.
#
#server {
#       listen 80;
#       listen [::]:80;
#
#       server_name example.com;
#
#       root /var/www/example.com;
#       index index.html;
#
#       location / {
#               try_files $uri $uri/ =404;
#       }
#}
#
#
#
#
