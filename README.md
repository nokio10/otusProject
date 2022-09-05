# Проектаная работа. 

Тема прокетной работы: "Развертывание инфраструктуры веб портала с помощью ansible, организация системы мониторинга на базе стеков ELK, Prometheus+Grafana".

# Схема проекта 

![projectscheme](https://user-images.githubusercontent.com/98832702/188460220-9289b77b-831d-44f1-8f03-da3575b5a7dc.jpg)

- В качестве веб сервера используется демонстрационный сайт, написанный на Python/Django. Источник - https://github.com/jkariukidev/my-demo-website

- В качестве firewall используется iptables. 

- На всех хостах установлен Prometheus Node Exporter, цени добавлены в Prometheus на сервере monitoring. 

# Установка

Перед развертыванием необходимо установить используемое на локальной машине ПО.

## Используемое ПО

- На локальной машине:
  - [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) - v2.25.1
  - [Vagrant](https://www.vagrantup.com/downloads) - v2.2.19
  - [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/index.html) - v2.9.6 
  - [Virtalbox](https://www.virtualbox.org/wiki/Downloads) - v6.1.38
- Устанавливаются на виртуальные машины:
  - [Docker](https://docs.docker.com/get-docker/) 
  - [Postgres](https://www.postgresql.org/download/linux/redhat/)
  - [Python](https://www.python.org/downloads/release/python-3810/)
  - [Django](https://docs.djangoproject.com/en/4.0/topics/install/) 
  - [Prometheus](https://prometheus.io/download/)
  - [Grafana](https://grafana.com/grafana/download)
  - [ELK](https://www.elastic.co/downloads/)
  - [Barman](https://pgbarman.org/downloads/)
  - [Nginx](https://nginx.org/ru/download.html)
  - [Iptables](https://www.netfilter.org/projects/iptables/downloads.html)
 
 ## Процесс развертывания проекта
 - Установить необходимое ПО.
 - Скопировать содержимое репозития командой ```git clone https://github.com/nokio10/otusProject```
 - Развернуть стенд командой ```vagrant up``` из директории otusProject.

 ## Примечание
  В отдельные плейбуки вынесены роли, при необходимости можно заменить конфигурацию серверов командами:

  - web:
  ```
  ansible-playbook -i ansible/inventory/hosts.ini ansible/web.yaml
  ```
  - backup:
  ```
  ansible-playbook -i ansible/inventory/hosts.ini ansible/backup.yaml
  ```
  - monitoring:
  ```
  ansible-playbook -i ansible/inventory/hosts.ini ansible/mon.yaml
  ```
  - nginx:
  ```
  ansible-playbook -i ansible/inventory/hosts.ini ansible/backup.yaml
  ```
  - Начальная подготовка серверов (роль prepare):
  ```
  ansible-playbook -i ansible/inventory/hosts.ini ansible/pre.yaml
  ```
  
  
  В конфиругационном файле ansible/inventory/group_vars/all.yaml доступно редактирование значений sysctl (применяется на серверах ролью "prepare") и паролей postgres пользователей. 
  
  # Краткие инструкции по работе с сервисами
  
  1. Веб сайт.
  
  - Главная страница доступна по адресу ```192.168.11.153:443```
  - Для проверки работы необходимо зарегистрироваться. ```Account > Sing Up```. После регистрации проверить таблицу "account_emailaddress" в базе данных "website", проверить авторизацию ```Account > Log out , Account > Log in```
  ```
  website=# select * from account_emailaddress;
 id |      email      | verified | primary | user_id
----+-----------------+----------+---------+---------
  1 | a.bakay@mail.ru | f        | t       |       1
(1 row)
  ```
  2. Grafana
  - Графана доступна по адерсу ```192.168.11.154:3000```, сдандартные авторизационные данные ```admin/admin```
  - Предустановлен дашборд "Node exporter", Prometheus установлен в качестве источника данных по умолчанию. 
  - Видно, что текущих 1024мб оперативной памяти для работы стека ELK+barman на сервере backup недостаточно, нужно увеличить в файле Vagrantfile
  ![image](https://user-images.githubusercontent.com/98832702/188472124-74df3f85-eea5-4a98-835d-ffbec6b289d3.png)
  
  3. ELK
  - Инструмент визуализации "Kibana" доступен по адресу ```192.168.11.155:5601```.
  - При развертывании с помощью ansible импортируется index pattern "myweb" в спейс "default". 
  - Для поиска логов по отдельным машинам использовать фильтр ```host.name```
  ![image](https://user-images.githubusercontent.com/98832702/188472974-ff8befbf-8117-4ed2-b9fd-9feedeaa36fe.png)

  4. Barman
  - Посмотреть список бекапов
  ```
  [root@backup ~]# barman list-backup pg
  pg 20220905T173002 - Mon Sep  5 14:30:03 2022 - Size: 34.3 MiB - WAL Size: 0 B
  pg 20220905T170002 - Mon Sep  5 14:00:04 2022 - Size: 34.2 MiB - WAL Size: 40.8 KiB
  ```
  - Восстановить мастер из бекапа
  ```
  barman recover pg 20220905T173002 /var/lib/pgsql/14/data/ --remote-ssh-comman "ssh postgres@192.168.11.150"
  ```
  
