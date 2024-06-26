---
title: "System info"
tags:
  - linux
draft: false
---

# Информация о системе

На работе иногда приходится подключаться к незнакомым хостам с ОС Linux, при этом бывает нужно узнать данные о системе: какой диструбитив Linux используется, какие характеристики у железа (или выделенные лимиты у виртуального сервера) и другое.

Вот основные команды для этого:
- `uname -a` - выдает минимум информации о системе: какая версия ядра Linux, какой процессор, название хоста.
- `cat /etc/os-version` - тут можно какой дистрибутив Linux используется.
- `lscpu` - сведения о процессоре: количество ядер, тактовая частота, архитектура и прочее.
- `cat /proc/cpuinfo` - расширенная информация о каждом ядре.
- `free -h` - оперативная память: сколько всего, сколько используется в данный момент, размер файла подкачки.
- `cat /proc/meminfo` - дофига информации про оперативную память.
- `lsblk` - характеристики жестких дисков: общий объем, разбиение на разделы.
- `df -h` - использование диска.
- `du -sh /path/to/dir` - сколько места на диске занимает директория.
- `vmstat -a` - текущий расход ресурсов системы: процессор, оперативная память, ввод-вывод.

