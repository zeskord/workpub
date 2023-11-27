# Установка сервера Postgresql 15 для 1С на CentOS 7

Сначала необходимо выполнить подготовку: настройку системы и установку пакетов.

## Установка Postgresql 15

```
mkdir tmp
cd tmp
```
Файлы `postgresql_15.4_1.1C_x86_64_addon_rpm.tar.bz2` и `postgresql_15.4_1.1C_x86_64_rpm.tar.bz2` предварительно передали на сервер. Извлекаем.
```
tar -xvf postgresql_15.4_1.1C_x86_64_rpm.tar.bz2
tar -xvf postgresql_15.4_1.1C_x86_64_addon_rpm.tar.bz2
```
Устанавливаем извлеченные пакеты. Смотри чтоб лишних пакетов в папке не было.
```
cd ./postgresql-15.4-1.1C_x86_64_rpm/
yum localinstall ./*.rpm
cd ..
```
Перед установкой доп модулей необходимо доустановить кое-что.
```
yum install centos-release-scl-rh -y
yum install devtoolset-7 llvm-toolset-7 -y
cd ./postgresql-15.4-1.1C_x86_64_addon_rpm/
yum localinstall ./*.rpm
```



```
su - postgres
```
```
/usr/pgsql-15/bin/initdb -D ~/15/data --locale=ru_RU.UTF-8
```
Запускаем сервер.
```
/usr/pgsql-15/bin/pg_ctl -D ~/15/data -l logfile start

```
Сервер запущен.
```
/usr/pgsql-15/bin/psql
```
Появится приглашение командной строки `postgres=#`
```
alter user postgres with password '123';
```
Появится вывод `ALTER ROLE`.
Выходим из командной строки postgresql
```
\q
```
Проверим, что что-то работает:
```
psql -l
```
Выведется таблица.

```
nano /var/lib/pgsql/15/data/pg_hba.conf
```
В открывшемся файле задаем в нем доступ к базам по паролю, а также передачу данных аутентификации в формате md5 хеш суммы.
```
# IPv4 local connections:
host    all             all             0.0.0.0/0               md5
```
Перезагружаем сервер.

После перезагрузки запускаем сервер и добавляем в автозагрузку.
```
service postgresql-15 start
chkconfig postgresql-15 on
```
Проверяем результат
```
service postgresql-15 status
```

Настройка сервера
```
nano /var/lib/pgsql/15/data/postgresql.conf
```
Привести строку к виду
```
listen_addresses = '*'          # what IP address(es) to listen on;
```
Доустанавливаем шрифты, кодировки, библиотеки для работы с картинками.
```
yum -y install xorg-x11-fonts-Type1 xorg-x11-fonts-truetype
yum install -y curl cabextract xorg-x11-font-utils fontconfig
rpm -i https://downloads.sourceforge.net/project/mscorefonts2/rpms/msttcore-fonts-installer-2.6-1.noarch.rpm
yum install ImageMagick.i686 ImageMagick libgsf.i686 unixODBC.i686 glib2.i686
```

