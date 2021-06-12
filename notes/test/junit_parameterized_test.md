---
title: "JUnit. Параметризованные тесты"
tags:
  - test
  - external_lib
draft: false
---

**Параметризованные тесты** - это тесты, в которых можно изменять значения входных и ожидаемых данных, передавая различные значения с помощью аргументов тестового метода.

Параметризованные тесты долгое время были экспериментальной фичей и поэтому они не включены в основную библиотеку [JUnit](junit.md) и для их использования необходимо подключить к проекту дополнительную зависимость `junit-jupiter-params`.

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-params</artifactId>
    <version>${junit.version}</version>
    <scope>test</scope>
</dependency>
```

Для создания тестового метода необходимо поставить над тестовым методом аннотацию `@ParametrizedTest`.

Как будут сгенерированы различные аргументы метода можно задать с помощью различных аннотаций, одна из которых также должна стоять над тестовым методом:

- `@MethodSource` - значения передаются с помощью отдельного метода, в котором они подготавливаются.
- `@ValueSource` - позволяет передать в единственный аргумент тестового метода ряд различных значений
- `@NullSource` - позволяет передать null в качестве единственного аргумента
- `@EmptySource` - позволяет передать пустую строку или пустую коллекцию в качестве единственного аргумента
- `@EnumSource` - передает в единственный аргумент тестового метода все значения указанного перечисления
- `@CsvSource` -

### Документирование

Для того чтобы описать каждый из тест-кейсов тестового метода обычного `@DisplayName` недостаточно. Для этого может использоваться атрибут `name` аннотации `@ParameterizedTest`. В этот атрибут передается шаблонная строка, в которой могут быть использованы следующие плейсхолдеры:

- `{<argumentNumber>}` - порядковый номер аргумента, начиная с 0
- `{index}` - порядковый номер тест-кейса

```java
@ParameterizedTest(name = "[{index}] - check char count for string \"{0}\"")
@ValueSource(strings = { "book", "1001", "абвг", "    ", "\t\n\n" })
void strings(String fourLetterString) {
    Assertions.assertEquals( 4, fourLetterString.length() );
}
```

### Выведение типов

Значения парсятся в тип, соответствующий типу аргумента. При этом поддерживаются следующие типы, в которые могут быть преобразованы строки:

- UUID
- Временные типы из java.time
- Перечисления
- URL
- и др.

Строки также могут быть преобразованы в пользовательские типы данных, если в них есть статический метод генерации, принимающий строку.

---
## ValueSource

Позволяет передать в тестовый метод один аргумент одного из следующих типов:

- примитив
- строка
- класс

Список значений указывается с помощью одного из атрибутов аннотации:
```java
@ParameterizedTest
@ValueSource(strings = {"book", "1001", "абвг", "    ", "\t\n\n"})
void strings(String fourLetterString) {
    Assertions.assertEquals( 4, fourLetterString.length() );
}
```

К сожалению, `@ValueSource` не позволяет передать в качестве значения null.

---
## NullSource и EmptySource

Из-за ограничения на передачу null значений в качестве аргументов с помощью `@ValueSource` была введена специальная аннотация `@NullSource`. Также есть аннотация `EmptySource`, которая позволяет передать пустую строку или пустую коллекцию в метод.

Обе аннотации могут использоваться в дополнение к `@ValueSource`:
```java
@ParameterizedTest
@NullSource
@EmptySource
@ValueSource(strings = {"  ", "\t"})
void testIsBlank(String blankString) {
    Assertions.assertTrue( isBlank( blankString ) );
}

private boolean isBlank(String blankString) {
    return blankString == null || blankString.isBlank();
}
```

---
## EnumSource

Аннотация `@EnumSource` позволяет передать в тестовый метод в качестве аргумента некоторые значения определенного перечисления. По умолчанию будут проверены все возможные значения перечисления. Отфильтровать какие-либо значения перечисления можно с помощью дополнительных атрибутов аннотации:

```java
@ParameterizedTest
@EnumSource(value = DayOfWeek.class, names = {"SATURDAY", "SUNDAY"}, mode = EnumSource.Mode.EXCLUDE)
void isWorkDay(DayOfWeek dayOfWeek) {
    final EnumSet<DayOfWeek> workDays = EnumSet.of(
        DayOfWeek.MONDAY,
        DayOfWeek.TUESDAY,
        DayOfWeek.WEDNESDAY,
        DayOfWeek.THURSDAY,
        DayOfWeek.FRIDAY );
    Assertions.assertTrue( workDays.contains( dayOfWeek ) );
}
```

---
## CsvSource

В тех случаях, когда нужно передать несколько простых аргументов, можно воспользоваться аннотацией `@CsvSource`, в которой в виде массива строк передаются аргументы, указанные через запятую.

Перед парсингом строковое значение триммируется. Чтобы избежать триммирования, нужно обернуть значение в одиночные кавычки:

```java
@ParameterizedTest
@CsvSource(value = {
    "lol,lol",
    "'kek ',kek",
    "' cheburek',cheburek",
    "'  ',''",
    "'',''"
})
void trim(String stringToTrim, String trimmedString) {
    Assertions.assertEquals( trimmedString, stringToTrim.trim() );
}
```

Также существует подобная аннотация `@CsvFileSource`, которая позволяет подгружать значения из csv-файла.

---
## MethodSource

MethodSource - самый гибкий вариант параметризованного теста, потому что он позволяет передать в тестовый метод сколько угодно аргументов.

В аннотацию `@MethodSource` передается название метода, который должен возвращать `Stream<Arguments>`. Данный метод должен быть статическим.

```java
@ParameterizedTest(name = "[{index}] - convert datetime {0} to date {1}")
@MethodSource("dateTimeProvider")
void dateFromDateTime(LocalDateTime dateTime, LocalDate expectedDate) {
    final LocalDate actualDate = dateTime.toLocalDate();
    Assertions.assertEquals( expectedDate, actualDate );
}

private static Stream<Arguments> dateTimeProvider() {
    return Stream.of(
        Arguments.of( LocalDateTime.parse("2021-03-08T15:00"), LocalDate.parse( "2021-03-08" ) ),
        Arguments.of( LocalDateTime.parse("1969-12-31T23:59:59"), LocalDate.parse( "1969-12-31" ) )
    );
}
```

Вложенные тестовые классы, помеченные аннотацией `@Nested` не могут быть статическими и соответственно не могут иметь статических методов. Для того чтобы написать параметризованный тест в таком случае, необходимо задать политику создания объектов тестового класса `@TestInstanse(PER_CLASS)`:

```java
@Nested
@TestInstance( TestInstance.Lifecycle.PER_CLASS )
class MethodSourceTest { /* */ }
```

---
## Мысли и идеи

Зачастую в формируемом с помощью `@ParameterizedTest(name = "...")` названии теста сложно передать намерения тесткейса.
Так случается, когда передаваемые аргументы имеют слишком длинный `toString()`.
Из-за этого приходится в `Arguments.of()` добавлять дополнительный аргумент - строчку, которая разъясняет тесткейс. Этот аргумент приходится принимать в тестовом методе, хотя он не требуется для прогонки теста.

Было бы классно добавить в интерфейс `Arguments` метод `withDescription(String)`, который бы позволял описать тесткейс без введения дополнительных аргументов.

```java
private static Stream<Arguments> testArgumentsProvider() {
    return Stream.of(
        Arguments.of(ComplicatedDto.createInvalid1()).withDescription("lol"),
        Arguments.of(ComplicatedDto.createInvalid2()).withDescription("kek")
    );
}

@ParameterizedTest(name = "[{index}] Invalid invariants with {descr}")
@MethodSource("testArgumentsProvider")
void test(ComplicatedDto dto) {
    /* ... */
}
```

---
## К изучению

- [X] Официальная документация: https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests
- [X] Статья про параметризованные тесты на Baeldung: https://www.baeldung.com/parameterized-tests-junit-5
- [X] MethodSource во вложенных тестовых классах: https://stackoverflow.com/questions/47933755/junit5-methodsource-in-nested-class