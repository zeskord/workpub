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

Отключение файрвола (до перезагрузки)
```
systemctl stop firewalld
```