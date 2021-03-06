---
title: "Base64"
tags:
  - formats
draft: false
---

# Base64

Существуют текстовые и бинарные [форматы представления данных](formats.md).
Но иногда необходимо передать бинарные данные в текстовом виде.
Для этого нужно перегнать массив байт в текст по какому-то определенному правилу.
При этом желательно использовать символы, которые будут иметь одинаковый порядковый номер в различных кодировках.
Поэтому используются только символы, входящие в первые 128 элементов ASCII таблицы.
Но среди этих символов есть нечеловекочитаемые символы, пробел, перевод строки и проч., которые бы разрывали представление бинарных данных.
Поэтому количество символов сокращено до следующей степени двойки - 2^6.

Base64 называется так, потому что использует для кодирования 64 символа и по сути является системой счисления с основанием 64.
Используемые символы: `A-Za-z0-9+/`.
В полном представлении: `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/`.
Букве `A` имеет индекс `0`, символ `\` - индекс `63`.
Также используется специальный символ `=`, о котором написано ниже.

Кодирование обычно происходит с помощью 24 битного буфера.
24 - как наименьшее число, кратное 6 (бит в одном символе Base64) и 8 (количество бит в байте). 
На каждые 3 байта информации будет приходиться 4 символа.
Таким образом последовательность двух байт данных `00101111 01100010` для кодирования в Base64 будет разбита на 3 фрагмента величиной не более 6 бит: `001011`, `110110` и `0010`. 
Последний сектет будет дополнен до полного нулями: `001000`.
Переводя из двоичной кодировки в десятичную получим три индекса: `11, 54, 8`.
Чтобы обозначить, что третьего байта не было в буфере, к результирующей строчке нужно добавить специальный символ `=`.
В итоге в результате преобразования по алгоритму Base64 получим:
```
binary: 00101111 01100010
sextet: 001011 110110 001000
indexes: 11 54 8
symbols: L 2 I
base64: L2I=
```

## Использование

Base64 используется для передачи в тексте бинарных данных: изображений, музыки, видео и пр.
Например Base64 используется в формате книжек FB2 для вставки изображений.
Еще он используется для вставки картинок и других медиа файлов в электронные письма.

Также Base64 используется для кодирования какой-либо информации для передачи ее в виде параметров URL. Т.к. символы `+`, `/` и `=` имеют значение в URL, то `+` заменяется на `-`, `/` на `_`, а `=` не проставляется.


## Java

В Java, начиная с 8 версии, для преобразования данных по правилам Base64 существует специальный класс `java.util.Base64`.
Этот класс предоставляет 3 вида декодеров и 3 вида энкодеров:
- стандартный - использует символы `+`, `/` и `=`
- URL and filename - использует символы `-`, `_` и `=`. Если требуется опускать символ `=`, то используется метод `withoutPadding()`.
- MIME - разделяет закодированные данные на строчки длиной не более 76 символов. Для разделения используется символ перевода строки.
  
Пример использования:
```java
class Base64Util {

    private Base64Util() { }

    private static final Base64.Encoder encoder = Base64.getEncoder();
    private static final Base64.Decoder decoder = Base64.getDecoder();

    public static String encode(byte[] input) {
        return encoder.encodeToString(input);
    }
    
    public static byte[] decode(String input) {
        return decoder.decode(input);
    }
}
```

Все реализации `Encoder` потокобезопасны и могут быть неоднократно переиспользованы.

---
## К изучению

- [X] [Wiki](https://ru.wikipedia.org/wiki/Base64)
- [x] `java.util.Base64` javadoc
- [x] [Base64URL в Java](https://stackoverflow.com/questions/5641303/base64url-in-java)