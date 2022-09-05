# Проектаная работа. 

Тема прокетной работы: "Развертывание инфраструктуры веб портала с помощью ansible, организация системы мониторинга на базе стеков ELK, Prometheus+Grafana".

## Схема проекта 

![projectscheme](https://user-images.githubusercontent.com/98832702/188460220-9289b77b-831d-44f1-8f03-da3575b5a7dc.jpg)

В качестве веб сервера используется демонстрационный сайт, написанный на Python/Django. Источник - https://github.com/jkariukidev/my-demo-website

В качестве firewall используется iptables. 

На всех хостах установлен Prometheus Node Exporter, цени добавлены в Prometheus на сервере monitoring. 

## Установка

Стенд разворачивается командой ```vagrant up``` .

В отдельные плейбуки вынесены отдельные роли, при необходимости можно заменить конфигурацию серверов командами:

web:
```
ansible-playbook -i ansible/inventory/hosts.ini ansible/web.yaml
```
backup:
```
ansible-playbook -i ansible/inventory/hosts.ini ansible/backup.yaml
```
monitoring:
```
ansible-playbook -i ansible/inventory/hosts.ini ansible/mon.yaml
```
nginx:
```
ansible-playbook -i ansible/inventory/hosts.ini ansible/backup.yaml
```
Начальная подготовка серверов (роль prepare):
```
ansible-playbook -i ansible/inventory/hosts.ini ansible/pre.yaml
```
