# Kittygram

## Описание

Kittygram — социальная сеть для обмена фотографиями любимых питомцев. Это
полностью рабочий проект, который состоит из бэкенд-приложения на Django и
фронтенд-приложения на React. Проект готов к деплою на удалённый сервер.

## Технологии

- Python 3.10.6
- Django 3.2.3
- Django REST framework 3.12.4
- Gunicorn 20.1.0
- Nginx 1.18.0
- Node.js 18.16.1

## Деплой проекта на удалённый сервер

Инструкция написана для сервера с установленной ОС Ubuntu 22.04.1 LTS.

- Подключитесь к удалённому серверу

- Подключите сервер к аккаунту на GitHub

- Клонируйте проект на сервер

- Далее установите на сервер пакетный менеджер и утилиту для создания
виртуального окружения

```text
sudo apt install python3-pip python3-venv -y
```

- Перейдите в директорию с бэкенд-приложением проекта

```text
cd infra_sprint1/backend/
```

- Создайте и активируйте виртуальное окружение, установите пакеты из файла
requirements.txt

```text
python3 -m venv venv
```

```text
source venv/bin/activate
```

```text
pip install -r requirements.txt
```

- Выполните миграции и создайте суперюзера

```text
python manage.py migrate
```

```text
python manage.py createsuperuser
```

- Перейдите в директорию с файлом settings.py и откройте его в редакторе Nano

```text
nano settings.py
```

- Отредактируйте этот файл - в список ALLOWED_HOSTS добавьте внешний IP-адрес
вашего сервера для доступа к приложению по внешнему интерфейсу и адреса
127.0.0.1 и localhost для доступа к приложению по внутреннему интерфейсу

```text
ALLOWED_HOSTS = ['xxx.xxx.xxx.xxx', '127.0.0.1', 'localhost']
```

Фронтенд-приложение проекта написано на React. Чтобы его запустить, нужно
установить на сервер пакетный менеджер npm и установить зависимости для
фронтенд-приложения.

- Находясь на сервере, из любой директории выполните команду

```text
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - &&\
sudo apt-get install -y nodejs
```

- Теперь можно установить зависимости для фронтенд-приложения. Перейдите в
директорию infra_sprint1/frontend/ и выполните команду

```text
npm i
```

## Автор

Пилипенко Артем
