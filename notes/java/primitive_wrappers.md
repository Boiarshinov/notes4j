---
title: "Обертки над примитивными типами. Autoboxingundefined Unboxing"
tags:
  - core
draft: false
---

## Классы-обертки

Каждому примитивному типу соответствует класс с единственным полем того же примитивного типа

- `Boolean` - `boolean`
- `Character` - `char`
- `Number`
    - `Byte` - `byte`
    - `Short` - `short`
    - `Integer` - `int`
    - `Long` - `long`
    - `Float` - `float`
    - `Double` - `double`

Общие методы:
- `static Integer valueOf(int)` - запаковывает значение в обертку (boxing). Подобный метод есть у каждого типа
- `int intValue()` - распаковывает значение из обертки (unboxing)
- `String toString()` - преобразует в значение в строку

Конструкторы:
- `Integer(int)` - запаковывает число. Вместо такого конструктора лучше использовать valueOf, т.к. он работает быстрее
- `Integer(String)` - пытается запарсить строку, если не получается, то кидает NumberFormatException
- аналогично для остальных классов

Начиная с JDK 5 в Java реализована автоупаковка и автораспаковка. При этом автораспаковка работает даже в том случае, если необходимо привести к другому примитивному типу. Например `Integer` может автораспаковаться в `double`, если того требует сигнатура метода, в который передается объект.

Нюансы:
1. При применении различных операторов к классам-оберткам производится автоматическая распаковка и последующая упаковка, что приводит к уменьшению производительности. Поэтому различные вычисления следует проводить с помощью методов.

При запаковывании чисел с помощью `valueOf()` если число находится в интервале от -128 до 127, то значение будет присвоено из кэша. Другими словами:
```java
Integer.valueOf(127) == Integer.valueOf(127) //true
Integer.valueOf(200) == Integer.valueOf(200) //false
```

---

## Класс Number
Методы:
- `byte byteValue()`
- `short shortValue()`
- `int intValue()`
- `long longValue()`
- `float floatValue()`
- `double doubleValue()` - все вышеперечисленные методы наследуются каждым классом, расширяющим Number. Они возвращают значение переменной в указанном виде.

---
**Класс Character**
Класс Character помимо превращения примитива в объект предоставляет большое количество полезных методов.

Методы:
- `boolean isDefined(char)` - определяет является ли символ символом Unicode
- `boolean isDigit(char)` - определяет является ли символ цифрой (аналог `\\d` в регулярках)
- `boolean isISOControl(char)` - определяет является ли символ управляющим
- `boolean isLetter(char)` - определяет является ли символ буквой (аналог `\\w` в регулярках)
- `boolean isJavaLetterOrDigit(char)` - определяет является ли символ буквой или цифрой
- `boolean isLowerCase(char)` - true, если это буква в нижнем регистре
- `boolean isUpperCase(char)` - true, если это заглавная буква
- `boolean isSpaceChar(char)` - true, если это пробельный символ или его аналог (аналог `\\s` в регулярках)

---
## Класс BigDecimal
Для работы с действительно большими числами существует класс BigDecimal
<mark>//Законспектировать статью, приведенную ниже</mark>

---
## К изучению:
- [ ] https://javarush.ru/groups/posts/2274-kak-ispoljhzovatjh-bigdecimal-v-java