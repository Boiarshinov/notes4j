---
title: "Base32"
tags:
  - formats
draft: false
---

# Base 32

__Base32__ - это формат представления данных в виде строки, в которой каждый символ может принимать только одно из 32 возможных значений.
Каждый символ может закодировать 5 бит информации (2^5 = 32).

Если закодированная информация является числовой, то можно считать Base32 формой записи 32-ой системы счисления.

В Base32 все символы представлены в одном регистре, поэтому он снискал популярность для:
- именования файлов в case-insensitive операционных системах
- dns имен
- One Time Password, предоставляемых пользователю
- капча


---
## Алфавит
К сожалению, нет единого универсального алфавита для Base32.
Даже несмотря на то, что был выпущен стандарт [RFC-4648][RFC4648], в некоторых системах используются альтернативные алфавиты.


### RFC-4648
Стандарт [RFC-4648][RFC4648] устанавливает два вида алфавитов.
Основной алфавит __base32__ состоит из всех букв латинского алфавита и цифр от 2 до 7:
```
ABCDEFGHIJKLMNOPQRSTUVWXYZ234567
```
Цифра 1 не используется, т.к. она слишком похожа на `l`.

Второй алфавит, описанный в стандарте, __base32Hex__ состоит из всех цифр и 22 букв латинского алфавита, исключая `W`, `X`, `Y`, `Z`.
```
0123456789ABCDEFGHIJKLMNOPQRSTUV
```
Фишка этого алфавита - если сравнить два разных файла побитово и посимвольно, предварительно закодировав в base32hex, то результаты сравнения не будут отличаться.
Другие алфавиты таким свойством не обладают.

#### Паддинг
По стандарту Base32 кодирует массивы байт группами по 5 байт (40 бит).
Если размер входных данных не кратен 5 байтам, то остаток добивается символами `=`.
Пример конвертации (по base32):
```
binary: 00101111 01100010
regroup by 5: 00101 11101 10001 00000
indexes: 5 29 17 0
symbols: F 5 R A
base64: F5RA====
```
Здесь 4 символа `=` были добавлены, т.к. всего было сформировано 4 группы по пять бит, и до 8 групп не хватало еще 4.

В некоторых случаях паддинг не применяется, например при энкодинге названий файлов или при коротких передаваемых данных.

### Крокфорд
Дуглас Крокфорд - создатель формата [JSON](./json.md), пошел дальше в попытках предотвратить некорректное восприятие закодированных символов человеком.
Он не стал включать в свой алфавит символы `I`, `L`, `O`, `U`.
`I` и `L` слишком сильно похожи на `1`, `O` похоже на цифру `0`, а букву `U` содержат множество ругательных слов.

В итоге алфавит Крокфорда:
```
0123456789ABCDEFGHJKMNPQRSTVWXYZ
```

Также Крокфорд предлагает добавлять еще один символ в качестве [чексуммы](../algorithms/checksum.md).
Чексумма при этом вычисляется как остаток от деления исходных данных на 37 (ближайшее к 32 простое число).
Т.к. в результате могут получиться значения от 0 до 36, то для выражения значений 32-36 используются спецсимволы: `*`, `~`, `$`, `=`, `U`.

Крокфордовский алфавит используется для строкового представления идентификаторов [ULID](../architecture/unique_id.md).


### Другие
Существует еще множество альтернативных вариантов алфавитов base32, но они не так популярны, как описанные выше.


---
## Java
В стандартной библиотеке Java нет классов, позволяющих работать с Base32.
В Apache Commons Codec и в Guava есть вспомогательные классы.
Эти классы работают с Base32, описанным в стандарте RFC-4648: и обычным, и hex.
Если требуются другие варианты алфавитов, нужно искать библиотеки отдельно (или скопипастить из интернетов).


---
## К изучению
- [X] [RFC-4648][RFC4648] - стандарт, описывающий Base32, Base64
- [X] [Crockford's base32](https://crockford.com/base32)
- [X] [Конвертер Base32](https://cryptii.com/pipes/base32)

[RFC4648]: https://datatracker.ietf.org/doc/html/rfc4648