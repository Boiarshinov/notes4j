---
title: "Архивирование"
tags:
  - core
draft: false
---

Для архивирования существует специальный класс `ZipOutputStream`, для разархивирования - `ZipInputStream`

**Класс ZipOutputStream**

Для того чтобы несколько файлов, записанных в один архив, не слипались вместе, для каждого файла (папки) создается специальная сущность - `ZipEntry` (про него ниже).

Конструкторы:
- `ZipOutputStream(OutputStream)` - можно передать поток, в который будет осуществляться запись
- `ZipOutputStream(OutputStream, Charset)` - то же, что и выше, но еще указывается кодировка

```java
ZipOutputStream zos = new ZipOutputStream(Files.newOutputStream(fileName));
```

Методы:
- `void putNextEntry(ZipEntry)` - открывает контейнер для записи файлов.
- `void closeEntry()` - закрывает последний контейнер

---

**Класс ZipInputStream**

Конструкторы:
- `ZipInputStream(InputStream)` - можно передать поток, из которого будет осуществляться чтение
- `ZipInputStream(InputStream, Charset)` - то же, что и выше, но еще указывается кодировка

Методы:
- `ZipEntry getNextEntry()` - достает следующий по счету контейнер

---

**Класс ZipEntry**

`ZipEntry` - это контейнер, который предназначен для того чтобы несколько файлов, записанных в один архив, не слипались вместе.

Конструкторы:
- `ZipEntry(String)` - передается имя файла или директории
- `ZipEntry(ZipEntry)` -
- `ZipEntry()` -

Методы:
- `String getName()` - возвращает имя вложенного файла
- `long getSize()` - возвращает размер вложенного файла в байтах в несжатом виде. Возвращает -1, если заархивированный файл еще не прочитан из архива
- `long getCompressedSize()` - возвращает размер вложенного файла в байтах в сжатом виде. Возвращает -1, если заархивированный файл еще не прочитан из архива
- `int getMethod()` - возвращает порядковый номер метода сжатия
    - 0 - STORED - метод сжатия для несжатых файлов
    - 8 - DEFLATED - метод сжатия для сжатых файлов