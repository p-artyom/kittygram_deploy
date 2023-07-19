# Kittygram

## Описание

Kittygram — социальная сеть для обмена фотографиями любимых питомцев. Это
полностью рабочий проект, который состоит из бэкенд-приложения на Django и
фронтенд-приложения на React. Проект готов к деплою на удалённый сервер.

## Технологии

- Python 3.10.6;
- Django 3.2.3;
- Django REST framework 3.12.4;
- Gunicorn 20.1.0;
- Nginx 1.18.0;
- Node.js 18.16.1.

## Деплой проекта на удалённый сервер

Инструкция написана для сервера с установленной ОС Ubuntu 22.04.1 LTS.

- Подключитесь к удалённому серверу;

- Подключите сервер к аккаунту на GitHub;

- Клонируйте проект на сервер;

- Далее установите на сервер пакетный менеджер и утилиту для создания
виртуального окружения:

```text
sudo apt install python3-pip python3-venv -y
```

- Перейдите в директорию с бэкенд-приложением проекта:

```text
cd infra_sprint1/backend/
```

- Создайте и активируйте виртуальное окружение, установите пакеты из файла
requirements.txt:

```text
python3 -m venv venv
```

```text
source venv/bin/activate
```

```text
pip install -r requirements.txt
```

- Выполните миграции и создайте суперюзера:

```text
python manage.py migrate
```

```text
python manage.py createsuperuser
```

- Перейдите в директорию с файлом settings.py и откройте его в редакторе Nano:

```text
nano settings.py
```

- Отредактируйте этот файл - в список ALLOWED_HOSTS добавьте внешний IP-адрес
вашего сервера для доступа к приложению по внешнему интерфейсу, адреса
127.0.0.1 и localhost для доступа к приложению по внутреннему интерфейсу:

```text
ALLOWED_HOSTS = ['xxx.xxx.xxx.xxx', '127.0.0.1', 'localhost']
```

Фронтенд-приложение проекта написано на React. Чтобы его запустить, нужно
установить на сервер пакетный менеджер npm и установить зависимости для
фронтенд-приложения.

- Находясь на сервере, из любой директории выполните команду:

```text
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - &&\
sudo apt-get install -y nodejs
```

- Теперь можно установить зависимости для фронтенд-приложения. Перейдите в
директорию infra_sprint1/frontend/ и выполните команду:

```text
npm i
```

- На удалённом сервере при активированном виртуальном окружении проекта
установите пакет gunicorn:

```text
pip install gunicorn==20.1.0
```

- Выполните команду создания и открытия в Nano конфигурационного файла
для демона WSGI-сервера gunicorn:

```text
sudo nano /etc/systemd/system/gunicorn_kittygram.service
```

- Подставьте в код из листинга свои данные, добавьте этот код в файл
gunicorn_kittygram.service и сохраните изменения:

```text
[Unit]
Description=gunicorn_kittygram daemon

After=network.target

[Service]
User=<имя-пользователя-в-системе>

WorkingDirectory=/home/<имя-пользователя-в-системе>/<директория-с-проектом>/<директория-с-файлом-manage.py>/

ExecStart=/home/<имя-пользователя-в-системе>/<директория-с-проектом>/<путь-до-gunicorn-в-виртуальном-окружении> --bind 0.0.0.0:8080 kittygram_backend.wsgi

[Install]
WantedBy=multi-user.target
```

- Запустите процесс gunicorn_kittygram.service:

```text
sudo systemctl start gunicorn
```

- Дополнительной командой добавьте процесс Gunicorn в список автозапуска
операционной системы на удалённом сервере:

```text
sudo systemctl enable gunicorn
```

- Находясь на удалённом сервере, из любой директории выполните команду:

```text
sudo apt install nginx -y
```

- А потом запустите Nginx командой:

```text
sudo systemctl start nginx
```

- Перейдите в директорию infra_sprint1/frontend и выполните команду:

```text
npm run build
```

- Чтобы Nginx раздавал статику, он должен знать, где она лежит. У веб-сервера
есть системная директория, которую он использует по умолчанию для доступа к
статическим файлам, — /var/www/. Скопируйте в эту директорию содержимое папки
.../frontend/build/:

```text
sudo cp -r /home/<имя-пользователя-в-системе>/infra_sprint1/frontend/build/. /var/www/kittygram/
```

- Через редактор Nano откройте файл конфигурации веб-сервера:

```text
sudo nano /etc/nginx/sites-enabled/default
```

- Удалите все настройки из файла, запишите и сохраните новые:

```text
server {

    listen 80;
    server_name <внешний-IP-адрес-вашего-сервера>;

    location /api/ {
        proxy_pass http://127.0.0.1:8080;
        client_max_body_size 20M;
    }

    location /admin/ {
        proxy_pass http://127.0.0.1:8080;
        client_max_body_size 20M;
    }

    location /media/ {
        alias /var/www/kittygram/media/;
    }

    location / {
        root /var/www/kittygram;
        index index.html index.htm;
        try_files $uri /index.html;
    }

}
```

- Для проверки файла конфигурации на ошибки выполните команду:

```text
sudo nginx -t
```

- Далее перезагрузите конфигурацию Nginx:

```text
sudo systemctl reload nginx
```

- На удалённом сервере при активированном виртуальном окружении перейдите в
директорию с файлом manage.py и выполните команду:

```text
python manage.py collectstatic
```

- Скопируйте файлы статики бэкенда в системную директорию /var/www/kittygram/:

```text
sudo cp -r /home/<имя-пользователя-в-системе>/infra_sprint1/backend/static_backend/ /var/www/kittygram/
```

- Чтобы изменения в файле settings.py вступили в силу, перезапустите Gunicorn:

```text
sudo systemctl restart gunicorn
```

## Автор

Пилипенко Артем
