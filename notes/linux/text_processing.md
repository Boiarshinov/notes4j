---
title: "Text processing"
tags:
  - linux
  - text
draft: false
---

# Работа с текстом

## Разрывы строк
В Linux и в Windows используются различные спецсимволы для обозначения разрыва строк
- `\n` - в Linux, также обозначается как __LF__
- `\r\n` - в Windows, также обозначается как __CRLF__

Во многих текстовых редакторах в интерфейсе указывается вид разрывов строк.

## Команды для работы с текстом

- `cat` - выводит сразу все содержимое файла в терминал. Подходит для маленьких файлов
- `less` - выводит в терминал только ту часть файла, которая влезает в окно терминала. Позволяет навигироваться по файлу. Хорошо подходит для больших файлов
- `tail` - выводит в терминал последние строки файла, при этом постоянно обновляет вывод, если в файл идет запись. Часто используется для просмотра логов работающего приложения
- `vim` - мощнейший редактор текста, ставший притчей во языцах
- `nano` - легковесный редактор
- `grep` - позволяет искать вхождения строк в текстовых файлах


### `cat`
Команда `cat` выводит сразу все содержимое файла в терминал.
```bash
cat /path/to/file
```

Можно вывести сразу несколько файлов:
```bash
cat file1 file2
```
Вывод будет последовательным: сначала весь первый файл, затем весь второй.

`cat` часто используют, чтобы склеивать несколько файлов в один с помощью [перенаправления потоков](./io_redirection.md):
```bash
cat file1 file2 file3 > gigafile
```


### `less`
Команда `less` выводит в терминал только ту часть файла, которая влезает в окно терминала.
```bash
less /etc/passwd
```
Можно навигироваться по файлу с помощью стрелок.
Чтобы выйти из просмотра файла, нужно нажать `q`.

В `less` можно искать вхождения строк.
Для этого нужно нажать `/` и ввести искомую строку, после чего нажать `enter`.
По найденным вхождениям строки можно навигироваться с помощью кнопки `n` (next).


### `tail`
Команда `tail` выводит в терминал последние строки файла.
Можно указать количество выводимых строк:
```bash
tail -n 20 /var/log/mysql/mysql.log
```
По умолчанию выводится 10 строк.

Команду можно запустить в режиме, когда она будет показывать изменения, происходящие в файле на лету.
Для этого ее нужно запустить с ключом `-f`:
```bash
tail -f /var/log/kafka/kafka-server.log
```