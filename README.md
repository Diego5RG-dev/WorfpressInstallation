# -Instalación de WordPress-

Documentación para la instalación de WordPress en un Ubuntu Server

WordPress es el sistema de gestión de contenido (CMS) de código abierto más popular. Está construido sobre PHP y MySQL. Este tutorial cubre la instalación básica, la configuración de dependencias y la preparación del servidor web Apache2.

Requisitos

    Un equipo con Ubuntu Server 20.04 LTS (o similar).

    Conocimientos básicos de línea de comandos.

    Máquina virtual especificaciones:

    Ubuntu Server 24.04.3

    Memoria RAM: 8 GB

    Disco duro: 60 GB

# 1. Instalar Dependencias

Antes de instalar WordPress, es necesario instalar el servidor web Apache2, el servidor de bases de datos MySQL y los módulos necesarios de PHP.

Ejecuta el siguiente comando para actualizar la lista de paquetes e instalar todas las dependencias requeridas:

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

    sudo mkdir -p /srv/www
    sudo chown www-data: /srv/www

Descarga y descomprime WordPress:
Descarga el archivo y extráelo directamente en el directorio /srv/www/ usando el usuario www-data.

    curl [https://wordpress.org/latest.tar.gz](https://wordpress.org/latest.tar.gz) | sudo -u www-data tar zx -C /srv/www

# 3. Configurar Apache para WordPress

Debes crear un archivo de configuración de sitio virtual para indicarle a Apache cómo servir WordPress.

Crea el archivo de configuración en /etc/apache2/sites-available/wordpress.conf con la siguiente información:
La estructura de directorios de Apache ya estará creada.

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

![alt text](https://github.com/Diego5RG-dev/WorfpressInstallation/blob/main/imagenes/primeraConf.png)

# 4. Habilitar sitios de WordPress

Ahora lo que tenemos que hacer es habilitar los sitios de WordPress con los siguientes comandos:

**Habilita el nuevo sitio de WordPress**

    sudo a2ensite wordpress

**Habilita la reescritura de URL (para permalinks)**

    sudo a2enmod rewrite

**Deshabilita el sitio predeterminado "It Works"**

    sudo a2dissite 000-default

**Reinicia el servicio de Apache**

    sudo service apache2 reload

# 5. Creación de base de datos

Para crear la base de datos tendremos que ir siguiendo los siguientes comandos, con cuidado porque alguno puede no copiarse bien.

    sudo mysql -u root

Después de hacer eso, nos pasará a la pantalla como "usuarios" SQL.

    CREATE DATABASE wordpress;
    CREATE USER wordpress@localhost IDENTIFIED BY '<your-password>';

    GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
    -> ON wordpress.*
    -> TO wordpress@localhost;**

Como recomendación personal, de este comando copiar solo la parte superior y escribir a mano el ON y el TO para mayor comodidad, porque en mi caso lo copiaba mal.

    FLUSH PRIVILEGES;

Y finalmente saldremos con un:

    quit
    
Reiniciaremos el servicio con:

    sudo service mysql start

# 6. Conectaremos WordPress a la base de datos

Estos pasos pueden ser un poco confusos.

    sudo -u www-data cp /srv/www/wordpress/wp-config-sample.php /srv/www/wordpress/wp-config.php

Después, lo que haremos será esto:

    sudo -u www-data sed -i 's/database_name_here/wordpress/' /srv/www/wordpress/wp-config.php
    sudo -u www-data sed -i 's/username_here/wordpress/' /srv/www/wordpress/wp-config.php
    sudo -u www-data sed -i 's/password_here/<your-password>/' /srv/www/wordpress/wp-config.php

Aquí es importante que solo modifiquemos el apartado <your-password> con nuestra contraseña.

    sudo -u www-data nano /srv/www/wordpress/wp-config.php

En el siguiente archivo de configuración, lo necesario es encontrar los siguientes apartados:

    define( 'AUTH_KEY',         'put your unique phrase here' );
    define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
    define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
    define( 'NONCE_KEY',        'put your unique phrase here' );
    define( 'AUTH_SALT',        'put your unique phrase here' );
    define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
    define( 'LOGGED_IN_SALT',   'put your unique phrase here' );
    define( 'NONCE_SALT',       'put your unique phrase here' );

Y las cambiaremos por unas claves aleatorias, las cuales aparecen en el siguiente enlace:
https://api.wordpress.org/secret-key/1.1/salt/

![alt text](https://github.com/Diego5RG-dev/WorfpressInstallation/blob/main/imagenes/configuraciones.png)

# 7. WordPress

Pasos iniciales de WordPress

Ya dentro de WordPress, lo primero que hice, como pedía la tarea, fue escoger un tema. Una vez lo escogí, basándome en que ya lo había utilizado anteriormente, elegí Hestia, el cual nos permite bastante configuración y modificación de los diversos elementos de las páginas web y, a mí personalmente, me gusta mucho.

![alt text](https://github.com/Diego5RG-dev/WorfpressInstallation/blob/main/imagenes/temaWordpress.png)

En el apartado de los plugins, cogí un par que no he usado mucho, pero me parecieron bastante interesantes, empezando por:

**All-in-One WP Migration and Backup:** es un plugin que nos permite migrar nuestro WordPress y páginas de manera relativamente sencilla mediante copias de seguridad y, simplemente, recuperarlas en otro dispositivo.

**Essential Addons for Elementor:** este nos añade opciones de diseño y de cosas parecidas, simplemente insertándolas, así que está bastante bien.

**MC4WP: Mailchimp for WordPress:** este plugin no lo he utilizado nunca, pero he visto comentarios buenos de él y está en la sección de "populares" de WordPress. Básicamente, es un gestor de suscripciones y boletines para nuestra página.

**Akismet Anti-spam: Spam Protection:** Luego, Akismet lo trae por defecto WordPress y es un protector contra el spam en nuestro blog o plataforma.

![alt text](https://github.com/Diego5RG-dev/WorfpressInstallation/blob/main/imagenes/plugins.png)
