# Настройка сервера 1С: Предприятия на CentOS 7

## Настройка системы
Установил виртуальную машину.  
Сначала не работает сеть. Запустим ее.
```
dhclient
yum install net-tools
```
Проверка сетевых адаптеров
```
ip a
```
Допустим, имя нашего адаптера `ens32`.
Смотрим как называется нужный нам и делаем по образцу
```
nano /etc/sysconfig/network-scripts/ifcfg-ens32
```
Приводим к следующему виду
```
DEVICE=ens32
BOOTPROTO=static
IPADDR=192.168.68.11
NETMASK=255.255.255.0
GATEWAY=192.168.68.1
DNS1=192.168.68.1
DNS2=192.168.68.1
ONBOOT=yes
```

Перезагрузим службу

```
systemctl restart network
```

Отключаем selinux
```
nano /etc/sysconfig/selinux
```
Находим строку `SELINUX=`, она должна быть 
```
SELINUX=disabled
```
После изменения файла выполнить команду (хотя у меня работает и без этого):
```
setenforce 0
systemctl restart network
```
Обновляем библиотеки
```
yum -y update
yum -y install epel-release
yum -y install nano mc wget net-tools rpm-build gcc make glibc-devel bison flex python-devel tcl-devel readline-devel zlib-devel openssl-devel krb5-devel e2fsprogs-devel gettext pam-devel openldap-devel icu libicu libicu-devel
```

## Устанавливаем сервер 1С.
```
mkdir tmp
cd tmp
```
Файл `rpm64_8_3_23_1912.tar.gz` предварительно передали на сервер. Извлекаем.
```
tar -xvf rpm64_8_3_23_1912.tar.gz
```
Устанавливаем извлеченные пакеты. Смотри чтоб лишних пакетов в папке не было.
```
yum localinstall ./*.rpm
```
Прописываем автозагрузку
```
chkconfig srv1cv83 on
```

Дополнительная магия для **CentOS7**. default - это имя кластера (он ведь у нас один):
```
ln /opt/1cv8/x86_64/8.3.23.1912/srv1cv8-8.3.23.1912@{,default}.service
systemctl link /opt/1cv8/x86_64/8.3.23.1912/srv1cv8-8.3.23.1912@default.service
```
Проверяем, что получилось. Служба должна быть, но неактивна.
```
systemctl status srv1cv8-8.3.23.1912@default.service
```
Включаем автоматический запуск
```
systemctl enable srv1cv8-8.3.23.1912@default.service
```

Это можно сделать для собственного спокойствия.
```
chown -R usr1cv8:grp1cv8 /opt/1cv8
```

Запуск
```
systemctl start srv1cv8-8.3.23.1912@default.service
```

Дальше идем на [ИТС](https://its.1c.ru/db/v8323doc#bookmark:cs:TI000000305), там описание настроек службы. Изменить настройки получается только с помощью:
```
nano /opt/1cv8/x86_64/8.3.23.1912/srv1cv8-8.3.23.1912@default.service
```

## Сервер хранилища конифигураций

[Источник](https://sysadminium.ru/1c_configuration_storage_server_on_linux/)

```
adduser csuser
su - csuser
mkdir repos
su root
```

Далее под пользователем root:
```
nano /etc/systemd/system/crserver.service
```
Вставить текст:
```
[Unit]
Description=1c repository server
[Service]
Type=oneshot
ExecStart=/opt/1cv8/x86_64/8.3.23.1912/crserver -d /home/csuser/repos -daemon
RemainAfterExit=yes
User=csuser
Group=csuser
[Install]
WantedBy=multi-user.target
```

