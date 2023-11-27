# Настройка сервера 1С:Предприятие на CentOS 7

Сначала необходимо выполнить подготовку: настройку системы и установку пакетов.

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

После записи файла выполнить
```
systemctl daemon-reload
systemctl start crserver.service
```