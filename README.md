# tz_devops
Creating LEMP server


Установка программ Nginx, MySQL 8.0, PHP 8.1 и Node.js (версии 16 и 20) 

1. Обновление системы

sudo apt update
sudo apt upgrade -y

2. Установка Nginx 1.18

sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx

3. Установка MySQL 8.0

wget https://dev.mysql.com/get/mysql-apt-config_0.8.32-1_all.deb
sudo dpkg -i mysql-apt-config_0.8.32-1_all.deb
sudo apt update
sudo apt install mysql-server -y
sudo systemctl start mysql
sudo systemctl enable mysql
sudo mysql_secure_installation


4. Установка PHP 8.1
Добавление рабочего репозитория (офф. репозиторий заблокирован) для PHP 8.1 и установка необходимых пакетов:

sudo apt install lsb-release apt-transport-https ca-certificates curl -y
sudo wget -qO /etc/apt/trusted.gpg.d/sury-php.pgp https://debian.octopuce.fr/sury-php/apt.gpg
echo "deb [ signed-by=/etc/apt/trusted.gpg.d/sury-php.pgp ] http://debian.octopuce.fr/sury-php/ bullseye main" | sudo tee /etc/apt/sources.list.d/php.list
sudo apt update
sudo apt install php8.1 php8.1-fpm php8.1-mysql php8.1-cli php8.1-curl php8.1-gd php8.1-mbstring php8.1-xml php8.1-zip -y 
sudo systemctl start php8.1-fpm 
sudo systemctl enable php8.1-fpm
systemctl status php8.1-fpm

6. Настройка Nginx для работы с PHP

Создание тестового файла конфигурации для сайта:
sudo nano /etc/nginx/sites-available/your_domain

Добавить конфигурацию:

server {
    listen 80;
    server_name your_domain www.your_domain;

    root /var/www/your_domain;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}

Активация конфигурации:

sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx

6. Создание пользователя без прав sudo

sudo adduser newuser
sudo usermod -aG www-data newuser

7. Установка Node.js (версии 16 и 20) для нового пользователя
Установка nvm (Node Version Manager) для управления версиями Node.js:

Сначала войти под новым пользователем:
su - newuser

Затем установите nvm:
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.4/install.sh | bash
source ~/.bashrc

Установка Node.js версии 16:
nvm install 16
nvm use 16
nvm alias default 16

Установка Node.js версии 20:
nvm install 20

8. Проверка установленных версий
Проверьте установленные версии Node.js:

node -v
npm -v
nvm ls

9. Снова залогиниться под пользователя с sudo и выполнить настройку прав доступа к веб-папке
sudo mkdir -p /var/www/your_domain
sudo chown -R www-data:www-data /var/www/your_domain
sudo chmod -R 755 /var/www/your_domain

10. Проверка PHP
Создание файла info.php для проверки PHP:

sudo nano /var/www/your_domain/info.php

Добавить следующий код:
<?php
phpinfo();
?>

11. Добавить в hosts строку с ip сервера LEMP и доменом 
12. Перейти в браузере по адресу http://your_domain/info.php для проверки информации о PHP.
