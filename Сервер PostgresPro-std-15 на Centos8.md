Страница с инструкцией на сайте (требует регистрации)
https://postgrespro.ru/products/download/postgrespro/15.5.1

```
localectl set-locale LANG=ru_RU.UTF-8
```
Перезагрузить компьютер.
Проверить локаль после перезагрузки.
```
locale
```
В выводе по всем пунктам должен быть русский язык.

## Если нет возможности подключить репозиторий
```
mkdir tmp
cd tmp
wget http://repo.postgrespro.ru/std-15/rhel/8Server/os/x86_64/rpms/postgrespro-std-15-server-15.5.1-1.el8.x86_64.rpm
wget http://repo.postgrespro.ru/std-15/rhel/8Server/os/x86_64/rpms/postgrespro-std-15-contrib-15.5.1-1.el8.x86_64.rpm
wget http://repo.postgrespro.ru/std-15/rhel/8Server/os/x86_64/rpms/postgrespro-std-15-libs-15.5.1-1.el8.x86_64.rpm
wget http://repo.postgrespro.ru/std-15/rhel/8Server/os/x86_64/rpms/postgrespro-std-15-client-15.5.1-1.el8.x86_64.rpm
yum localinstall *
```
Если при установке будет на хватать пакетов (они будут выделены зеленым), то установить их с помощью команды
```
yum install имяпакета
```
После этого повторить.

```
/opt/pgpro/std-15/bin/pg-setup initdb --tune=1c
/opt/pgpro/std-15/bin/pg-setup service enable
/opt/pgpro/std-15/bin/pg-setup service start
```
Временно подключаемся под postgres
```
sudo - postgres
/opt/pgpro/std-15/bin/psql
```
Появится приглашение командной строки postgresql.
```
alter user postgres with password 'MyPassw0rd';
```
Выходим из пользователя postgres
```
exit
```
Редактируем настройки postgresql (здесь без подробностей):

```
nano /var/lib/pgpro/std-15/data/pg_hba.conf
nano /var/lib/pgpro/std-15/data/postgresql.conf
```