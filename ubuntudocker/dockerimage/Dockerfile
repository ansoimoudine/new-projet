# va chercher l'os unbutu dans la version demander
FROM ubuntu:20.04

# lance la maj de apt
RUN apt update

# lance l'instalation nginx, php  fpm et supervisord
RUN apt install -y nginx php-fpm supervisor && \ 
rm -rf /var/lib/apt/lists/* && \
    apt clean

# definir une variable d'environement pour nginx sur vhost
ENV nginx_vhost /etc/nginx/sites-available/default

# definie une variable pour configurer php
ENV php_conf /etc/php/7.4/fpm/php.ini

# definie une variable pour configurer nginx
ENV nginx_conf /etc/nginx/nginx.conf

#definie une variable pour configurer supervisor
ENV supervisor_conf /etc/supervisor/supervisord.conf

# je copie la configuration de nginx que je trouve dans le fichier default sur nginx_vhost
COPY default ${nginx_vhost}

# lancer le remplacement de la configuration (cgi.fix_pathinfo=1) par la conf (cgi.fix_pathinfo=0)dans le (php.ini) qui passe par le lien (php_conf)
# puis ajoutez l'option démon désactivé à la variable par défaut nginx_conf
RUN sed -i -e 's/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/g' ${php_conf} && \
    echo "\ndaemon off;" >> ${nginx_conf}

# copier la configuration (supervisord.conf) dans la variable (supervisord_conf)
COPY supervisord.conf ${supervisor_conf}

#lancer la création d'un nouveau repertoire pour le fichier sock (php-fpm)
#puis changer la propriété du répertoire racine Web /var/www/html et du répertoire PHP-FPM /run/php en l'utilisateur par défaut www-data
RUN mkdir -p /run/php && \
    chown -R www-data:www-data /var/www/html && \
    chown -R www-data:www-data /run/php

#Définir le volume de l'image personnalisée pour pouvoir monter tous ces répertoires sur la machine hôte.
VOLUME ["/etc/nginx/sites-enabled", "/etc/nginx/certs", "/etc/nginx/conf.d", "/var/log/nginx", "/var/www/html"]

#j'ajoute le script (start.sh), et je défini la commande de conteneur par défaut(ou il yaura mon image) à l'aide de l'instruction CMD comme ci-dessous. 
COPY start.sh /start.sh
CMD ["./start.sh"]

# ouvre les port par default sur le conteneur
EXPOSE 80 443
