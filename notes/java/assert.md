---
title: "Ассерты"
tags:
  - core
  - syntax
draft: false
---

# Ассерты

Ассерт - это стандартная фича языка, позволяющая установить какое-либо ограничение в произвольном месте кода.
По сути ассерт является утверждением, о том что в данном месте кода должно выполняться заданное условие. 
Например, что при выходе из метода должны сохраняться инварианты класса.

Важнейшим свойством ассертов является то, что по умолчанию они не проверяются в рантайме. 
Для того чтобы включить проверку, нужно стартовать приложение особым образом, описанным ниже.

Ассерты описываются с помощью следующего синтаксиса:
```java
assert <predicate>; //при провале проверки приложение выкинет ошибку
assert <predicate> : <value>; //при провале проверки будет выведено значение
```
Где `assert` - это ключевое слово языка, `<predicate>` - это проверяемое условие, возвращающее `boolean` значение, а `<value>` - это значение, которое будет выведено в сообщении об ошибке, если утверждение оказалось ложным.

Если предикат оказывается ложным, то выбрасывается ошибка `AssertionError`.

Важно не выполнять в ассертах кода, который имеет побочные эффекты, т.к. это может привести к трудноуловимым ошибкам.


## Зачем нужны ассерты

Ассерты нужны для того, чтобы при написании кода отлавливать какие-то внутренние баги.
Например, можно включить ассерт в приватный метод, в который по логике класса не может прийти отрицательное значение.
Или написать ассерт, который проверяет, что соблюдаются инварианты класса при изменении объекта.

Ассерты не стоит использовать для валидации входных данных, потому что:
- Проваленный ассерт кидает не исключение, а ошибку.
- Проверка ассертов выключена по умолчанию.

В связи с этим валидацию лучше выполнять отдельно обычным Java кодом с выбрасыванием исключений типа `IllegalArgumentException` или пользоваться специальными библиотеками.

Пример использования ассертов на немного кривом классе, инкапсулирующем календарный день:
```java
    public void plusMonth(int monthCount) {
        if (monthCount < 0) {
            throw new IllegalArgumentException("Month count should not be negative");
        }

        final int plusYearCount = monthCount / 12;
        year += plusYearCount;

        int plusMonthCount = monthCount % 12;
        month += plusMonthCount;

        assert isValid() : toString();
    }

    private boolean isValid() {
        return month > 0
            && month <= 12
            && day > 0
            && day < 31;
    }
```

Здесь валидация входных данных проводится с помощью обычной проверки с выбрасыванием исключения, а в конце выполнения метода проверяется, что класс сохранил свой инвариант.
Сохранение инвариантов должно обеспечиваться кодом метода, а подобный ассерт нужен только для того чтобы отдебажить методы класса. 
При этом такие ассерты не будут сказываться на производительности приложения в рантайме.


## Включение проверки ассертов

По умолчанию ассерты не проверяются в рантайме, для того чтобы включить их проверку, нужно при запуске приложения передать ключ `-enableassertions` или его сокращенный вариант `-ea`.

Можно включить проверку ассертов для одних пакетов и исключить для других.
Например, можно включить проверку для пакетов со своим кодом и исключить для подключенных библиотек.
Отключение проверок устанавливается с помощью ключа `-disableassertions` или `-da`. Пакеты прописываются через двоеточие.
```sh
java -ea:dev.boiarshinov... -da:com.springframework... MyApp
```


## Интеграция с тестовыми средами

В [JUnit](../test/junit.md) при запуске тестов ассерты будут проверяться всегда.

Такой тест будет провален:
```java
@Test
void failedAssert() {
    assert false;
}
```

Выбрасываемая ошибка `AssertionFailedError` в утилитных методах класса `Assertions` является наследником ошибки `AssertionError`.


## К изучению
- [X] Статья от Oracle: https://docs.oracle.com/javase/8/docs/technotes/guides/language/assert.html
- [X] Перевод статьи выше: https://habr.com/ru/company/golovachcourses/blog/222679/