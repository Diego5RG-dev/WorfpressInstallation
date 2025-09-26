# -Instalacion de Wordpress-
Documentacion para la instalacion de Wordpress en un Ubuntu Server

WordPress es el sistema de gestión de contenido (CMS) de código abierto más popular. Está construido sobre PHP y MySQL. Este tutorial cubre la instalación básica, la configuración de dependencias y la preparación del servidor web Apache2.

Requisitos

    Un equipo con Ubuntu Server 20.04 LTS (o similar).

    Conocimientos básicos de línea de comandos.

# 1. 🛠️ Instalar Dependencias

Antes de instalar WordPress, es necesario instalar el servidor web Apache2, el servidor de bases de datos MySQL y los módulos necesarios de PHP.

Ejecuta el siguiente comando para actualizar la lista de paquetes e instalar todas las dependencias requeridas:
Bash

sudo apt update
sudo apt install apache2 \
    ghostscript \
    libapache2-mod-php \
    mysql-server \
    php \
    php-bcmath \
    php-curl \
    php-imagick \
    php-intl \
    php-json \
    php-mbstring \
    php-mysql \
    php-xml \
    php-zip

# 2. Instalar WordPress

Se recomienda usar la versión oficial de WordPress.org en lugar del paquete del archivo de Ubuntu.

    Crea el directorio de instalación y establece permisos:
    Bash

sudo mkdir -p /srv/www
sudo chown www-data: /srv/www

Descarga y descomprime WordPress:
Descarga el archivo y extráelo directamente en el directorio /srv/www/ usando el usuario www-data.
Bash

    curl https://wordpress.org/latest.tar.gz | sudo -u www-data tar zx -C /srv/www

# 3. ⚙️ Configurar Apache para WordPress

Debes crear un archivo de configuración de sitio virtual para indicarle a Apache cómo servir WordPress.

    Crea el archivo de configuración en /etc/apache2/sites-available/wordpress.conf con la siguiente información:
    Apache la estructura de directorios ya estara creada solo mediante 

<VirtualHost *:80>
    DocumentRoot /srv/www/wordpress
    <Directory /srv/www/wordpress>
        Options FollowSymLinks
        AllowOverride Limit Options FileInfo DirectoryIndex index.php
        Require all granted
    </Directory>
    <Directory /srv/www/wordpress/wp-content>
        Options FollowSymLinks
        Require all granted
    </Directory>
</VirtualHost>

# Habilitar sitios de wordpress

Ahora lo que tenemos que haces es habilitar los sitios de worpress con los siguientes comandos

**Habilita el nuevo sitio de WordPress**

sudo a2ensite wordpress

**Habilita la reescritura de URL (para permalinks)**

sudo a2enmod rewrite

**Deshabilita el sitio predeterminado "It Works"**

sudo a2dissite 000-default

**Reinicia el servicio de apache**

sudo service apache2 reload

# Creacion de base de datos

para crear la base de datos tendremos que ir siguiendo los siguientes comandos, con cuidado porque alguno puede no copiarse bien 

**sudo mysql -u root**

despues de hacer eso nos pasara a la pantalla como "usuarios" sql 

**CREATE DATABASE wordpress;**
**CREATE USER wordpress@localhost IDENTIFIED BY '<your-password>';**

**GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER**
    -> ON wordpress.*
    -> TO wordpress@localhost;**
Como recomendacion personal este comando solo copiar la parte superior y escrubur a mano el on y el to para maypr comodidad porque en mi caso lo copiaba mal

**FLUSH PRIVILEGES;**

Y finalmente saldremos con un 

**quit**

Reiniciaremos el servicio con 

**sudo service mysql start**


