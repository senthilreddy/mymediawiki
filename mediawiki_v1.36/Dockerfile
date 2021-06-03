FROM ubuntu:latest
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -v apache2 mysql-server php php-mysql libapache2-mod-php php-xml php-mbstring apache2-utils php-intl --no-install-recommends && ln -s /var/lib/mediawiki/ /var/www/html/mediawiki
ADD https://releases.wikimedia.org/mediawiki/1.36/mediawiki-1.36.0.tar.gz /var/lib/
WORKDIR /var/lib/
RUN tar -xzf mediawiki-1.36.0.tar.gz && mv mediawiki-1.36.0 mediawiki
#COPY LocalSettings.php /var/lib/mediawiki/
CMD ["apache2ctl", "-D", "FOREGROUND"]
