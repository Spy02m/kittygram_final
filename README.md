# Kittygram
## Описание
 - Kittygram — социальная сеть для обмена фотографиями любимых питомцев.
  - Проект состоит из бэкенд-приложения на Django и фронтенд-приложения на React.
   - Cмотреть информацию о котиках и управлять карточками собственных котиков могут только зарегистрированные и авторизованные пользователи.

[Ссылка на развернутый проект](https://kittygram.servequake.com)

## Технологии
 - Python
 - Docker
 - PostgreSQL
 - Django REST Framework
 - Nginx
 - React

## Отличия продакшн-версии от обычной
Продакшн-версия — это готовый продукт, оптимизированный для публичного использования и предназначен для развертывания на сервере.

Обычная версия — это тестовая версия, созданная для удобства разработчиков и используется для локального развертывания и отладки.

## Инструкция по локальному развертыванию
 - Клонировать репозиторий
```
git clone git@github.com:Spy02m/kittygram_final.git
```
 - Перейти в папку с проектом
```
cd kittygram_final
```
 - Создать файл .env с переменными окружения
```
POSTGRES_DB
POSTGRES_USER
POSTGRES_PASSWORD
DB_NAME
DB_HOST
DB_PORT
SECRET_KEY
DEBUG
ALLOWED_HOSTS
```
 - Развернуть проект
```
docker compose -f docker-compose.yml up
```
 - Сделать сбор статики и миграции
```
sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/static/
```
 - Проект будет доступен по локальному адресу: http://127.0.0.1:9000
## Инструкция по удаленному развертыванию
 - Cделать форк к себе в репозиторий.
 - Создать файл .env с переменными окружения на удаленном сервере в папке проекта
```
sudo nano .env
```
```
POSTGRES_DB
POSTGRES_USER
POSTGRES_PASSWORD
DB_NAME
DB_HOST
DB_PORT
SECRET_KEY
DEBUG
ALLOWED_HOSTS
```
 - Создать в репозитории секреты Github Actions:
```
DOCKER_PASSWORD
DOCKER_USERNAME
HOST
USER
SECRET_KEY_DJANGO
SSH_KEY
SSH_PASSPHRASE
TELEGRAM_TO
TELEGRAM_TOKEN
```
 - Изменить файл `.github/workflows/main.yml` под свои параметры
 - Отправить изменения в свой репозиторий
```
git push
```
Проект автоматически развернется на удаленном сервере.
 - Настроить сервер Nginx на порт 9000
```
sudo nano /etc/nginx/sites-enabled/default
```
```
server {
    server_name your-domen.com;

    location / {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:9000;
    }

    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/your-domen.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/your-domen.com/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
}
server {
    if ($host = your-domen.com) {
        return 301 https://$host$request_uri;
    }

    listen 80;
    server_name your-domen.com;
    return 404;
}
