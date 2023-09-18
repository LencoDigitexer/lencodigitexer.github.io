---
title: "Установка анонимного поисковика SearXNG"
date: 2023-09-18
image: cover.png
description: Опрашивает десятки поисковых систем, чтобы предоставить вам частные и полные результаты поиска.
categories:
    - метапоиск
tags:
    - searxng
    - searx
    - metasearch
    - метапоиск
    - перевод
---

> [Статья на английском с сайта landchad.net](https://landchad.net/searxng/)

SearXNG - это бесплатный механизм метапоиска в Интернете, который объединяет результаты более чем 70 поисковых сервисов.
В этом руководстве настраивается рабочий экземпляр, доступ к которому можно получить с помощью домена по протоколу HTTPS.

Особенности включают в себя:

- Самостоятельный хостинг
- Нет отслеживания пользователей
- Отсутствие профилирования пользователей
- Около 70 поддерживаемых поисковых систем
- Простая интеграция с любой поисковой системой
- Файлы cookie по умолчанию не используются
- Безопасные, зашифрованные соединения (HTTPS / SSL)

> [SearXNG на GitHub](https://github.com/searxng/searxng)

## Установка​

Установите необходимые пакеты.

```sh
apt install git nginx nginx-extras -y
```

Откройте порты http и https.

```sh
iptables -I INPUT -m state --state NEW -p tcp --dport 80 -j ACCEPT
iptables -I INPUT -m state --state NEW -p tcp --dport 443 -j ACCEPT
netfilter-persistent save
ufw allow 80
ufw allow 443
```

 Сначала мы создадим пользователя для SearX.

```
useradd -mr -d "/usr/local/searxng" -c 'Privacy-respecting metasearch engine' -s /bin/bash searxng
```

Хотя в приведенном ниже сценарии автоматической установки мы создаем этого пользователя самостоятельно, мы можем пойти дальше и сделать так, чтобы предоставить клонированному репозиторию правильные разрешения.

Теперь мы клонируем репозиторий SearXNG в домашний каталог пользователя `searx`.

```sh
git clone https://github.com/searxng/searxng /usr/local/searxng/searxng-src
cd /usr/local/searxng/searxng-src
```

Установка SearXNG.

```sh
./utils/searxng.sh install all
```

## Настройка Nginx

Создайте новый файл `/etc/nginx/sites-available/searxng.conf` и добавьте в него
следующее:

```nginx
server {

    # Listens on http
    listen 80;
    listen [::]:80;

    # Your server name
    server_name searx.example.org ;

    # If you want to log user activity, comment these
    access_log /dev/null;
    error_log  /dev/null;

    # X-Frame-Options (XFO) header set to DENY
    add_header X-Frame-Options "DENY";

    # HTTP Strict Transport Security (HSTS) header
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";

    # Content Security Policy (CSP)
    add_header Content-Security-Policy "default-src 'self';";

    location / {
        uwsgi_pass unix:///usr/local/searxng/run/socket;

        include uwsgi_params;

        uwsgi_param    HTTP_HOST             $host;
        uwsgi_param    HTTP_CONNECTION       $http_connection;

        # see flaskfix.py
        uwsgi_param    HTTP_X_SCHEME         $scheme;
        uwsgi_param    HTTP_X_SCRIPT_NAME    /searxng;

        # see limiter.py
        uwsgi_param    HTTP_X_REAL_IP        $remote_addr;
        uwsgi_param    HTTP_X_FORWARDED_FOR  $proxy_add_x_forwarded_for;

    }
}
```

Теперь создайте символическую ссылку для включения этого сайта.

```sh
ln -s /etc/nginx/sites-available/searxng.conf /etc/nginx/sites-enabled/searxng.conf
```

Перезапустите Nginx и SearXNG.

```sh
systemctl restart nginx
service uwsgi restart searxng
```

## Настройка HTTPS с помощью Certbot

Установите certbot.

```sh
apt install python3-certbot-nginx
```

Установите SSL-сертификат Let's Encrypt в Nginx и, при необходимости, позвольте ему настроить HTTPS для вас. [Подробные инструкции и дополнительная информация](/basic/certbot).

```sh
certbot --nginx
```

Теперь SearXNG должен быть доступен из вашего домена.

## Конфигурация (редактирование файла)

Изменить настройки можно, отредактировав файл `/etc/searxng/settings.yml`.

## Вклады

Автор оригинальной статьи: goshawk22 -- [website](https://goshawk22.uk)

Сайт оригинальной статьи: landchad -- [website](https://landchad.net/searxng/)
