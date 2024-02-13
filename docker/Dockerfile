FROM ubuntu:22.04

ENV TZ=Europe/Kiev
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Обновление репозиториев
RUN apt-get update && apt-get -y install gpg && echo -n 'deb http://ppa.launchpad.net/ondrej/php/ubuntu groovy main' > /etc/apt/sources.list.d/ondrej-ubuntu-php-groovy.list && \
        apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 14AA40EC0831756756D7F66C4F4EA0AAE5267A6C
        RUN apt-get update

RUN apt-get install -y software-properties-common && \
        add-apt-repository -y ppa:ondrej/php && \
        apt-get update

# Установка Apache, PHP и зависимостейзависимостей
RUN apt-get install -y curl nano apache2
# Установка PHP 7.4
RUN apt-get install -y php7.4 libapache2-mod-php7.4 php7.4-mysql php7.4-mbstring php7.4-curl php7.4-json php7.4-gd php7.4-xml php7.4-cli unzip
# Установка PHP 8
RUN apt-get install -y php8.1 libapache2-mod-php8.1 php8.1-mysql php8.1-mbstring php8.1-curl php8.1-cli php8.1-gd php8.1-xml unzip
# Установка PHP 8.3
RUN apt-get install -y php8.3 libapache2-mod-php8.3 php8.3-mysql php8.3-mbstring php8.3-curl php8.3-cli php8.3-gd php8.3-xml unzip

RUN a2enmod ssl

ARG PHP_VERSION
RUN update-alternatives --set php "/usr/bin/php${PHP_VERSION}" && \
    update-alternatives --set phar "/usr/bin/phar${PHP_VERSION}" && \
    update-alternatives --set phar.phar "/usr/bin/phar.phar${PHP_VERSION}"

COPY 000-default.conf /etc/apache2/sites-available/000-default.conf

RUN apt-get install -y vsftpd

# Создание пользователя для FTP
RUN useradd -m -d /home/ftpuser ftpuser
RUN echo "ftpuser:password" | chpasswd

# Настройка vsftpd
RUN sed -i 's/anonymous_enable=YES/anonymous_enable=NO/g' /etc/vsftpd.conf
RUN echo "local_enable=YES" >> /etc/vsftpd.conf
RUN echo "write_enable=YES" >> /etc/vsftpd.conf
RUN echo "local_umask=022" >> /etc/vsftpd.conf
RUN echo "dirmessage_enable=YES" >> /etc/vsftpd.conf
RUN echo "xferlog_enable=YES" >> /etc/vsftpd.conf
RUN echo "connect_from_port_20=YES" >> /etc/vsftpd.conf
RUN echo "chroot_local_user=NO" >> /etc/vsftpd.conf
RUN echo "allow_writeable_chroot=NO" >> /etc/vsftpd.conf
RUN echo "chroot_list_enable=YES" >> /etc/vsftpd.conf
RUN echo "chroot_list_file=/etc/vsftpd.chroot_list" >> /etc/vsftpd.conf

# Создание директории для FTP
RUN mkdir -p /var/www/wp49
RUN chown -R ftpuser:ftpuser /var/www/wp49
RUN chmod 777 -R /var/www/wp49

# Переход в директорию приложения
WORKDIR /var/www

# Назначение прав на запись для директории storage
RUN chown -R www-data:www-data /var/www/html

# Порт для работы приложения
EXPOSE 80

# Запуск Apache при старте контейнера
CMD service vsftpd start && /usr/sbin/apache2ctl -D FOREGROUND