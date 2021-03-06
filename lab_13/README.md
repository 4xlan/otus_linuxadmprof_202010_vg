# Лабораторная работа 13.

## Цели работы

Работа с Ansible.

## Задачи

1. Подготовить стенд на Vagrant как минимум с одним сервером. На этом сервере используя Ansible необходимо развернуть nginx со следующими условиями:
    - Необходимо использовать модуль yum/apt
    - Конфигурационные файлы должны быть взяты из шаблона jinja2 с перемененными
    - После установки nginx должен быть в режиме enabled в systemd
    - Должен быть использован notify для старта nginx после установки
    - Сайт должен слушать на нестандартном порту - 8080, для этого использовать переменные в Ansible

---

Конфигурационные файлы:

[Общий каталог с файлами](./cfg)

[Vagrantfile](./cfg/Vagrantfile)

[Playbook](./cfg/nginx.yml)

[Nginx config](./cfg/templates/nginx.conf.j2)

---
