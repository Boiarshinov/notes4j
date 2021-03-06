---
title: "Linux file system"
tags:
  - linux
draft: false
---

# Файловая система Linux

В Linux на верхнем уровне находится корневая директория, которая обозначается `/`.
Все вложенные каталоги отделяются друг от друга также слэшами `/`: `/etc/nginx.conf`.

Файловая система Linux реализована в соответствии со стандартом FHS - Filesystem Hierarchy Standard.

## Команды для передвижения по файловой системе

Можно выделить три основные команды:
- `cd` - Перемещает терминал в указанный каталог. Можно использовать как абсолютные пути, так и относительные.
- `ls` - Выводит список директорий и файлов, содержащихся в текущем каталоге.
- `pwd` - Выводит текущую директорию, в которой открыт терминал. Чаще всего эта команда используется в скриптах, чтобы как-то сослаться на текущую директорию.

Абсолютные пути всегда начинаются либо с корня - `/`, либо с домашней директории - `~`.
Относительные пути начинаются с названия конкретной директории, если нужно перейти на уровень выше, то используется обозначение `..`.

Ниже приведен пример перехода из директории `/bin/postman` в директорию `/etc/nginx` с помощью относительного и абсолютного пути.
```sh
arty/pc:/bin/postman $ cd ../../etc/nginx

arty/pc:/bin/postman $ cd /etc/nginx
```

Чтобы сослаться на текущую директорию, используется обозначение `.`.
Пример: запускаем с некоторыми аргументами программу `gradlew`, находящуюся в текущей директории:
```sh
./gradlew clean build
```

---
## Основные директории

В корне файловой системы в любой ОС на основе Linux будут находиться следующие директории:

#### `home`
Пользовательские данные.
Для каждого пользователя заводится отдельная субдиректория с названием, соответствующим имени пользователя.
Находясь в терминале под определенным пользователям можно сослаться на его директорию с помощью символа `~`.

#### `root`
Файлы с данными рутового пользователя.

#### `mnt`
Сокращение от `mount`.
В этой директории находятся пути до всех дополнительных жестких дисков и всех флешек, дисков, дискет и пр.

#### `boot`
Здесь находится ядро Linux, загрузчик и некоторые конфигурационные файлы.

#### `dev`
Сокращение от `devices` - устройства.


#### `etc`
В этой директории лежат конфигурационные файлы различных приложений.
Например, именно здесь будут лежать конфиги от [nginx](../devops/nginx.md) или [keepalived](../tools/keepalived.md).

#### `bin`
Директория с исполняемыми файлами различных подпрограмм

#### `lib`
Здесь лежат библиотеки, используемые совместно различными программами

#### `opt`
В эту директорию помещаются программы, установленные пользователем.

### `var`
В этом каталоге находятся файлы с изменяемыми данными.
Например хранимые данные баз данных, логи приложений, кэши и т.д.


---
## Типы файлов

В Linux - все файл.
Но файлы бывают различных видов.
Эти виды обозначаются с помощью символов:
- `-` - Обычный файл.
- `d` - Каталог - внезапно, в Linux каталог - это тоже файл.
- `l` - Символьная ссылка - аналог ярлыка в Windows - файл, который ссылается на другой файл.
- `b` - Блочные устройства - файл для общения с блочными внешними устройствами, например, с жестким диском.
- `c` - Символьные устройства - файл для общения с символьными внешними устройствами, например с терминалом.
- `s` - Сокет - файл, через который могут общаться различные программы, записывая и читая из него данные.
- `p` - Канал - то же, что и сокет, но взаимодействие происходит только в одну сторону.

---
## К изучению

- [X] Мобильное приложение "Linux & Bash. Курс"