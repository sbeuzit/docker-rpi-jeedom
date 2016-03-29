FROM armhf/debian

MAINTAINER serge.beuzit@gmail.com

RUN apt-get update
RUN apt-get -y dist-upgrade

# Installation des dépendances
RUN apt-get -y install dialog apt-utils libterm-readline-perl-perl supervisor \
 mysql-client \
 nginx-common nginx-full ntp ca-certificates unzip curl sudo \
 php5-cli php5-common php5-curl php5-fpm php5-json php5-mysql php5-gd \
 wget cron 
RUN apt-get autoremove 

# NGINX
RUN mkdir -p /var/www/html/log && \
 sed -i 's/max_execution_time = 30/max_execution_time = 600/g' /etc/php5/fpm/php.ini && \
 sed -i 's/upload_max_filesize = 2M/upload_max_filesize = 1G/g' /etc/php5/fpm/php.ini && \
 sed -i 's/post_max_size = 8M/post_max_size = 1G/g' /etc/php5/fpm/php.ini && \
 sed -i 's/expose_php = On/expose_php = Off/g' /etc/php5/fpm/php.ini && \
 sed -i 's/pm.max_children = 5/pm.max_children = 20/g' /etc/php5/fpm/pool.d/www.conf && \
 rm /etc/nginx/sites-available/default && \
 rm /etc/nginx/sites-enabled/default 

# Modified Jeedom nginx file (ssl + certificates) http://nginx.org/en/docs/http/configuring_https_servers.html 
COPY nginx_default /etc/nginx/sites-available/default 
RUN ln -s /etc/nginx/sites-available/default /etc/nginx/sites-enabled/default

# Creation du cron
# Moved to supervisord to avoid HARDLINKS issue
# RUN echo "* * * * * su --shell=/bin/bash - www-data -c '/usr/bin/php /var/www/html/core/php/jeeCron.php' >> /dev/null" | crontab -

# Droit root
RUN echo "www-data ALL=(ALL) NOPASSWD: ALL" | (EDITOR="tee -a" visudo)

# Ajout des fichiers Jeedom
RUN mkdir -p /var/www/html && \
 rm -rf /root/core-* && \
 wget https://github.com/jeedom/core/archive/stable.zip -O /tmp/jeedom.zip && \
 unzip -q /tmp/jeedom.zip -d /root/ && \
 cp -R /root/core-*/* /var/www/html/ && \
 cp -R /root/core-*/.htaccess /var/www/html/

# For Openzwave
ADD https://raw.githubusercontent.com/jeedom/plugin-openzwave/master/ressources/install.sh /root/openzwave_install.sh
RUN chmod +x /root/openzwave_install.sh
RUN /root/openzwave_install.sh
RUN rm /root/openzwave_install.sh

RUN mkdir -p /var/www/html/plugins/openzwave/data && \
  chown -R www-data.www-data /var/www/html/plugins/openzwave/data

RUN sed -i 's/,127.0.0.1/,127.0.0.1,172.17.*.*/' /var/www/html/core/config/default.config.ini


# SSL - https://www.digitalocean.com/community/tutorials/how-to-create-an-ssl-certificate-on-nginx-for-ubuntu-14-04
RUN mkdir /etc/nginx/ssl
RUN openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt -subj "/C=FR/ST=France/L=Nice/O=Home/OU=Home/CN=jeedom.com"

# Supervisor
RUN echo "daemon off;" >> /etc/nginx/nginx.conf

COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Add crontab  when container is launched (to avoid the HARDLINKS issue)
COPY ./add_crontab /etc/supervisord/
RUN chmod +x /etc/supervisord/add_crontab

# Create log dir in case it is launched using tmpfs
# CMD ["/usr/bin/supervisord"]
COPY ./start_supervisord /
RUN chmod +x /start_supervisord

CMD ["/start_supervisord"]

