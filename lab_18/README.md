# Лабораторная работа 18.

## Цели работы

Развертывание сетевой лаборатории.

## Задачи

На базе [Vagrantfile](https://github.com/erlong15/otus-linux/tree/network) с начальным построением сети (`inetRouter`, `centralRouter`, `centralServer`) построить следующую архитектуру:

1. Сеть office1

    - 192.168.2.0/26 - dev
    - 192.168.2.64/26 - test servers
    - 192.168.2.128/26 - managers
    - 192.168.2.192/26 - office hardware

2. Сеть office2

    - 192.168.1.0/25 - dev
    - 192.168.1.128/26 - test servers
    - 192.168.1.192/26 - office hardware


3. Сеть central

    - 192.168.0.0/28 - directors
    - 192.168.0.32/28 - office hardware
    - 192.168.0.64/26 - wifi


![Рис. 1. Схема сети.](./addfiles/diag.png)

Итого должны получиться следующие сервера:

- inetRouter
- centralRouter
- office1Router
- office2Router
- centralServer
- office1Server
- office2Server

# Теоретическая часть

- Найти свободные подсети.
- Посчитать сколько узлов в каждой подсети, включая свободные.
- Указать broadcast адрес для каждой подсети.
- Проверить нет ли ошибок при разбиении.

# Практическая часть

- Соединить офисы в сеть согласно схеме и настроить роутинг.
- Все сервера и роутеры должны ходить в инет черз inetRouter.
- Все сервера должны видеть друг друга.
- У всех новых серверов отключить дефолт на нат (eth0), который вагрант поднимает для связи.
- При нехватке сетевых интерфейсов добавить по несколько адресов на интерфейс.

---

## Выполнение

### Теоретическая часть

#### Найти свободные подсети

Т.к. в ТЗ нет обозначения, в пределах какой сети необходимо найти свободные подсети, предположим, что используется весь диапазон 192.168.0.0/16. Следовательно, присутствуют следующие свободные подсети:

> Помним, что согласно исходному [Vagrantfile](https://github.com/erlong15/otus-linux/tree/network) между centralRouter и inetRouter используется подсеть 192.168.255.0/30.

- 192.168.0.16/28
- 192.168.0.48/28
- 192.168.0.128/25
- 192.168.4.0/22
- 192.168.8.0/21
- 192.168.16.0/20
- 192.168.32.0/19
- 192.168.64.0/18
- 192.168.128.0/18
- 192.168.192.0/19
- 192.168.224.0/20
- 192.168.240.0/21
- 192.168.248.0/22
- 192.168.252.0/23
- 192.168.254.0/24
- 192.168.255.4/30
- 192.168.255.8/29
- 192.168.255.16/28
- 192.168.255.32/27
- 192.168.255.64/26
- 192.168.255.128/25

#### Посчитать сколько узлов в каждой подсети, включая свободные + Указать broadcast адрес для каждой подсети.

| Подсеть | Количество узлов | Broadcast |
| :-- | :--: | :--: |
| 192.168.0.0/28 | 14 | 192.168.0.15 |
| 192.168.0.32/28 | 14 | 192.168.0.47 |
| 192.168.0.64/26 | 62 | 192.168.0.127 |
| 192.168.1.0/25 | 126 | 192.168.1.127 |
| 192.168.1.128/26 | 62 | 192.168.1.191 |
| 192.168.1.192/26 | 62 | 192.168.1.255 |
| 192.168.2.0/26 | 62 | 192.168.2.63 |
| 192.168.2.64/26 | 62 | 192.168.2.127 |
| 192.168.2.128/26 | 62 | 192.168.2.191 |
| 192.168.2.192/26 | 62 | 192.168.2.255 |
| 192.168.255.0/30 | 2 | 192.168.255.3 |

#### Проверить нет ли ошибок при разбиении

- Наличие `192.168.255.0/30` при остальных сетях, умещающихся в `192.168.0.0/22`: подсети лучше планировать более кучно, не делая больших разрывов.
- Использование `/30` маски: если есть уверенность в том, что соединение между сетевым оборудованием всегда будет p2p, то лучше использовать `/31` маску для соединения, чтобы не тратить два адреса на подсеть и широковещательный. 
- Две пустых подсети (`192.168.0.16/28`, `192.168.0.48/28`). Но без наличия более точного ТЗ нельзя однозначно сказать, является ли это ошибкой (т.к. не исключено что это могут быть зарезервированные подсети для расширения). 

### Практическая часть 

Выполнение практической части представлено в каталоге [cfg](./cfg).

> <!> Т.к. опытным путем было выяснено, что не всегда сразу после рестарта подтягивается новый дефолтный маршрут, в данной работе используется велосипед, который проверяет корректность обновления стандартного маршрута. Перед запуском необходимо внести изменения в files/network_crutch.sh, указав в переменной WRONG_ADDR адрес шлюза, используемого сетевой подсистемой гипервизора. (Или не указывать и тогда он просто отработает вхолостую.)

---