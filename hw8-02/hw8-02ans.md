# Домашнее задание к занятию "`Система мониторинга Zabbix. Часть 1`" - `Трубицын Михаил`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1 

Установите Zabbix Server с веб-интерфейсом.

#### Процесс выполнения
1. Выполняя ДЗ, сверяйтесь с процессом отражённым в записи лекции.
2. Установите PostgreSQL. Для установки достаточна та версия, что есть в системном репозитороии Debian 11.
3. Пользуясь конфигуратором команд с официального сайта, составьте набор команд для установки последней версии Zabbix с поддержкой PostgreSQL и Apache.
4. Выполните все необходимые команды для установки Zabbix Server и Zabbix Web Server.

#### Требования к результаты 
1. Прикрепите в файл README.md скриншот авторизации в админке.
2. Приложите в файл README.md текст использованных команд в GitHub.

---

### Решение 1

1. Установлен Zabbix сервер на OC Debian 11 c IP адресом: 192.168.219.71

Скриншот авторизации в админке
![Скриншот авторизации в админке](https://github.com/mishatru/netology-hw/blob/main/hw8-02/img/adm.png)

2. Текст использованных команд

Установка PostgreSQL:
```
sudo apt install postgresql
```

Устанавливаем репозиторий Zabbix и обновляем кэш:
```
# wget https://repo.zabbix.com/zabbix/6.0/debian/pool/main/z/zabbix-release/zabbix-release_6.0-4+debian11_all.deb
# dpkg -i zabbix-release_6.0-4+debian11_all.deb
# apt update
```

Устанавливаем сервер:
```
apt install zabbix-server-pgsql zabbix-frontend-php php7.4-pgsql zabbix-apache-conf zabbix-sql-scripts zabbix-agent
```

Создаем пользователя DB и базу данных:
```
su - postgres -c 'psql --command "CREATE USER zabbix WITH PASSWORD '\'password\'';"'
su - postgres -c 'psql --command "CREATE DATABASE zabbix OWNER zabbix;"'
```

Импортируем начальную схему DB и данные:
```
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix
```

Настраиваем DB для zabbix сервера:
```
sed -i 's/# DBPassword=/DBPassword=cen19pass/g' /etc/zabbix/zabbix_server.conf
```

Запускаем процесс zabbix сервера и настраиваем автозапуск:
```
sudo systemctl restart zabbix-server zabbix-agent apache2
sudo systemctl enable zabbix-server zabbix-agent apache2
```

---
### Задание 2

Установите Zabbix Agent на два хоста.

#### Процесс выполнения
1. Выполняя ДЗ, сверяйтесь с процессом отражённым в записи лекции.
2. Установите Zabbix Agent на 2 вирт.машины, одной из них может быть ваш Zabbix Server.
3. Добавьте Zabbix Server в список разрешенных серверов ваших Zabbix Agentов.
4. Добавьте Zabbix Agentов в раздел Configuration > Hosts вашего Zabbix Servera.
5. Проверьте, что в разделе Latest Data начали появляться данные с добавленных агентов.

#### Требования к результаты 
1. Приложите в файл README.md скриншот раздела Configuration > Hosts, где видно, что агенты подключены к серверу
2. Приложите в файл README.md скриншот лога zabbix agent, где видно, что он работает с сервером
3. Приложите в файл README.md скриншот раздела Monitoring > Latest data для обоих хостов, где видны поступающие от агентов данные.
4. Приложите в файл README.md текст использованных команд в GitHub

---
### Решение 2

1. Добавлены 3 хоста с агентами:
    - Zabbix server
    - ubnt-test
    - WindowsPC

![Скриншот раздела Configuration > Hosts](https://github.com/mishatru/netology-hw/blob/main/hw8-02/img/hosts-agent.png)

2. Скриншот статуса zabbix-agent и логфайла
![Скриншот раздела логфайла](https://github.com/mishatru/netology-hw/blob/main/hw8-02/img/log.png)

3. Скриншот раздела Monitoring > Latest, где видны поступающие данные от 3-х агентов
![Скриншот раздела Monitoring > Latest](https://github.com/mishatru/netology-hw/blob/main/hw8-02/img/lat.png)

4. Выполняемые команды
Добавление репозитория для установки zabbix агента и обновления кэша:
```
wget https://repo.zabbix.com/zabbix/6.0/debian/pool/main/z/zabbix-release/
zabbix-release_6.0-4%2Bdebian11_all.deb
dpkg -i zabbix-release_6.0-4+debian11_all.deb
apt update
```

Установка zabbix агента:
```
sudo apt install zabbix-agent -y
```

Запуск zabbix агента и настройка атозапуска:
```
sudo systemctl restart zabbix-agent
sudo systemctl enable zabbix-agent
```

Редактирование конфигурационного файла zabbix агента:
```
nano /etc/zabbix/zabbix_agentd.conf
```

---

### Задание 3 со звёздочкой*
Установите Zabbix Agent на Windows (компьютер) и подключите его к серверу Zabbix.

#### Требования к результаты 
1. Приложите в файл README.md скриншот раздела Latest Data, где видно свободное место на диске C:

--- 

### Решение 3*

На компьютер с ОС Windows установлен zabbix агент
В хосты zabbix сервера добавлен хост WindowsPC
Создана шаблон Check-Disks-Windows с ч4 элементами данных:
- DiskFree (свободное место на диске C:)
- DiskPFree (свободное место на диске C: в процентах)
- DiskTotal (полный объем диска C:)
- DiskUsed (исполльзованное пространство на диске C:)
Созданы 5 графиков для каждого элемента данных в шаблоне Check-Disks-Windows и один общий, объединяющий все элементы данных

Скриншот раздела Latest Data для элемента данных свободного пространства на диске C хоста WindowsPC:
![Скриншот раздела Monitoring > Latest](https://github.com/mishatru/netology-hw/blob/main/hw8-02/img/data-windows.png)

Скриншот списка элементов данных шаблона Check-Disks-Windows:
![Скриншот раздела Monitoring > Latest](https://github.com/mishatru/netology-hw/blob/main/hw8-02/img/t-windows.png)

Скриншот графикоф с данными шаблона
![Скриншот раздела Monitoring > Latest](https://github.com/mishatru/netology-hw/blob/main/hw8-02/img/graf-windows.png)
