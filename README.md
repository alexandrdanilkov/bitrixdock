## Автоматическая установка
```
curl -fsSL https://raw.githubusercontent.com/bitrixdock/bitrixdock/master/install.sh -o install.sh && chmod +x install.sh && sh install.sh
```

<details><summary>Ручная установка</summary>
<p>

## Ручная установка
#### Зависимости
- Git
```
apt-get install -y git
```
- Docker & Docker-Compose
```
cd /usr/local/src && wget -qO- https://get.docker.com/ | sh && \
curl -L "https://github.com/docker/compose/releases/download/1.18.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose && \
chmod +x /usr/local/bin/docker-compose && \
echo "alias dc='docker-compose'" >> ~/.bash_aliases && \
source ~/.bashrc
```

### Папки и файл Битрикс
```
mkdir -p /var/www/bitrix && \
cd /var/www/bitrix && \
wget http://www.1c-bitrix.ru/download/scripts/bitrixsetup.php && \
cd /var/www/ && \
git clone https://github.com/bitrixdock/bitrixdock.git && \
cd /var/ && chmod -R 775 www/ && chown -R root:www-data www/ && \
cd /var/www/bitrixdock
```

### Выполните настройку окружения

Скопируйте файл `.env_template` в `.env`

```
cp -f .env_template .env
```
⚠ Если у вас мак, удалите строчку `/etc/localtime:/etc/localtime/:ro` из docker-compose

По умолчанию используется nginx, php7, mysql. Настройки можно изменить в файле ```.env```. Также можно задать путь к каталогу с сайтом и параметры базы данных MySQL.


```
PHP_VERSION=php74          # Версия php
WEB_SERVER_TYPE=nginx      # Веб-сервер nginx/apache
DB_SERVER_TYPE=mysql       # Сервер базы данных mysql/percona
MYSQL_DATABASE=bitrix      # Имя базы данных
MYSQL_USER=bitrix          # Пользователь базы данных
MYSQL_PASSWORD=123         # Пароль для доступа к базе данных
MYSQL_ROOT_PASSWORD=123    # Пароль для пользователя root от базы данных
INTERFACE=0.0.0.0          # На данный интерфейс будут проксироваться порты
SITE_PATH=/var/www/bitrix  # Путь к директории Вашего сайта

```

### Запустите bitrixdock
```
docker-compose up -d
```
Чтобы проверить, что все сервисы запустились посмотрите список процессов ```docker ps```.
Посмотрите все прослушиваемые порты, должны быть 80, 11211, 9000 ```netstat -plnt```.
Откройте IP машины в браузере.
</p>
</details>

## Как заполнять подключение к БД
![db](https://raw.githubusercontent.com/bitrixdock/bitrixdock/master/db.png)

## Примечание
- По умолчанию стоит папка ```/var/www/bitrix/```
- В настройках подключения требуется указывать имя сервиса, например для подключения к базе нужно указывать "db", а не "localhost". Пример [конфига](configs/.settings.php) с подключением к mysql и memcached.
- Для загрузки резервной копии в контейнер используйте команду: ```cat /var/www/bitrix/backup.sql | docker exec -i mysql /usr/bin/mysql -u root -p123 bitrix```
- При использовании php74 в production удалите строку с `php7.4-xdebug` из файла `php74/Dockerfile`, сам факт его установки снижает производительность Битрикса и он должен использоваться только для разработки
