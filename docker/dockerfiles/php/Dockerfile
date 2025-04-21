FROM php:8.2-fpm

############################################################################
# NOTE: All these settings were copied from a Pimcore project
# TODO: Review everything in this file, in the context of a Symfony project
############################################################################

############################################################################
# Update Package Index Files on the System
############################################################################

RUN apt-get update

############################################################################
# Intall PHP Xdebug
#
# [NOTES]
# install, but also enable the extension
############################################################################

RUN pecl install xdebug \
    && docker-php-ext-enable xdebug

############################################################################
# Install Composer
#
# [NOTES]
# including curl and unzip (pre-requisites)
############################################################################

RUN apt-get install curl unzip -y
RUN curl -sS https://getcomposer.org/installer -o composer-setup.php
RUN php composer-setup.php --install-dir=/usr/local/bin --filename=composer
RUN rm composer-setup.php

############################################################################
# Install Pimcore Requirements
#
# [NOTES]
# Installation order is important for some extensions
# Some requirements have been installed above (curl, unzip, composer)
############################################################################

RUN apt-get install zlib1g-dev libonig-dev libxml2-dev libpng-dev zip libzip-dev locales-all -y
RUN docker-php-ext-install mysqli pdo pdo_mysql intl opcache iconv dom simplexml gd exif fileinfo mbstring zip

############################################################################
# Install Symfony Client
#
# [NOTES]
# TODO: Fix <brew install symfony-cli/tap/symfony-cli> fails during the first build but works after in the container, so it is removed from this file for the time being
# Useful for checking whether the system is meets the Symfony requirements
############################################################################

### Run all commands with bash (needed to run <source> otherwise sh will not find <source>)
SHELL ["/bin/bash", "-c"]

### Install homebrew and dependencies, and create a symlink for the homebrew bin directory
RUN apt-get install git -y
RUN git clone https://github.com/Homebrew/brew ~/.linuxbrew/Homebrew \
&& mkdir ~/.linuxbrew/bin \
&& ln -s ../Homebrew/bin/brew ~/.linuxbrew/bin

### Update PATH with symlinkcreated above
RUN echo "export PATH=\"~/.linuxbrew/bin:$PATH\"" >> ~/.bashrc
RUN source ~/.bashrc
RUN cat ~/.bashrc

### Install Symfony Client & use <exit 0> to prevent docker-compose from exiting with code 1
#RUN brew install symfony-cli/tap/symfony-cli