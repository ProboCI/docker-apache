FROM proboci/ubuntu

RUN apt-get update
RUN apt-get -y upgrade

# Install apache, PHP, and supplimentary programs. curl and lynx-cur are for debugging the container.
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install apache2 apache2-utils libapache2-mod-php5 php5-mysql php5-gd php-pear php-apc php5-curl php5-ldap curl lynx-cur

# Enable apache mods.
RUN php5enmod openssl
RUN a2enmod php5
RUN a2enmod rewrite

# Update the PHP.ini file, enable <? ?> tags and quieten logging.
RUN sed -i "s/short_open_tag = Off/short_open_tag = On/" /etc/php5/apache2/php.ini
RUN sed -i "s/error_reporting = .*$/error_reporting = E_ERROR | E_WARNING | E_PARSE/" /etc/php5/apache2/php.ini

# Manually set up the apache environment variables
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid

EXPOSE 80

# Update the default apache site with the configs we created.
ADD apache-default-host.conf /etc/apache2/sites-enabled/000-default.conf
ADD apache-default-varnish-host.conf /etc/apache2/sites-available/000-default-varnish.conf
ADD apache-default-dotnet-host.conf /etc/apache2/sites-available/000-default-dotnet.conf
ADD ssl_environment_variable.conf /etc/apache2/conf-available/ssl_environment_variable.conf
ADD listen_8080.conf /etc/apache2/conf-available/listen_8080.conf

# Enable and disable config options and Apache modules
RUN a2enconf ssl_environment_variable
RUN a2enmod actions \
  alias \
  deflate \
  dir \
  expires \
  headers \
  mime \
  negotiation \
  rewrite \
  setenvif \
  proxy \
  proxy_http

RUN a2dismod autoindex

RUN sed -i -e 's/ServerSignature On/ServerSignature Off/g' /etc/apache2/conf-available/security.conf

# Set /var/www/ permissions
RUN chown -R root:root /var/www && chmod -R 755 /var/www

# Remove default site.
RUN rm -Rf /var/www/html

