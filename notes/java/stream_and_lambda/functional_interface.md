---
title: "Функциональные интерфейсы"
tags:
  - functional_programming
draft: false
---

Те интерфейсы, в которых объявлен только один метод, называются функциональными. К таким интерфейсам относятся: `Comparator`, `Runnable`

Перед такими интерфейсами обычно ставят аннотацию `@FunctionalInterface`.

Функциональные интерфейсы используются при создании [лямбда-выражений](lambda_expressions.md).

Популярные функциональные интерфейсы (находятся в пакете `java.util.function`):
- *Потребители* - те, кто принимает значение, но ничего не возвращает:
    - `Consumer<T>` с методом `accept()`
    - `BiConsumer<T, V>` с методом `accept()`
- *Поставщики* - те, кто ничего не принимает, но что-то возвращает
    - `Supplier<T>` с методом `get()`
- *Предикаты* - проводят какую-то оценку и возвращают булево значение
    - `Predicate<T>` с методом `test()`
    - `BiPredicate<T, V>` с методом `test()`
- *Функции* - принимают аргумент и возвращают значение какого-либо типа
    - `Function<T, R>` с методом `apply()`
    - `BiFunction<T, U, V>` с методом `apply()`
- *Операторы* - функции, у которых на входе и на выходе значения одного и того же типа
    - `UnaryOperator<T>` с методом `apply()`
    - `BinaryOperator<T, V>` с методом `apply()`
- Другие
    - `Runnable` с методом `run()`
    - `Callable<V>` с методом `call()`
    - `Comparator<T>` с методом `compare()`

Запоминать названия функциональных интерфейсов и их методов не нужно, т.к. обычно, вместо прямой реализации этих интерфейсов с помощью анонимных классов, используются лямбда-выражения. Компилятор сам разберет какой из функциональных интерфейсов реализует лямбда.

---
## Создание собственных функциональных интерфейсов
Совершенно законно создавать собственных функциональные интерфейсы.
Функциональные интерфейсы могут иметь больше, чем один метод, если это:
- методы, включенные в класс Object.
- статические методы
- дефолтные методы

```java
@FunctionalInterface
public interface MyFunInterface<T, R> {
    R function(T t);

    static void doNothing() {}
    default void doNothingByDefault() {}
}
```

Теперь этот интерфейс можно реализовывать с помощью лямбда-выражения:
```java
MyFunInterface<Integer, String> fun = x -> "" + Math.abs(x);
```

Собственные функциональные интерфейсы могут использоваться для реализации некоторых паттернов проектирования: стратегия, фабрика, шаблонный метод и пр.

## Интерфейс `Function<T, R>`

Интерфейс `Function` помимо метода `apply()`, который необходимо реализовать, также имеет дефолтные методы:

- `Function<V, R> compose(Function<V, T> before)` - заворачивает переданную функцию в нашу функцию так, что при вызове нашей функции сначала выполнится обернутая функция, а ее результат будет подставлен в качестве входного значения в нашу функцию.
- `Function<T, V> andThen(Function<R, V> after)` - оборачивает первоначальную функцию (из которой вызывался метод) в переданную и возвращает новую функцию, в которой при передаче значения будет сначала выполняться первоначальная функция, а затем к ее результату будет применяться переданная функция.

```java
@Test
private void composeAndThen() {
    final Function<Integer, Integer> sum = x -> x + 10;
    final Function<Integer, Integer> multiply = x -> x * 5;

    final Integer multiplyAndSum = sum.compose(multiply).apply(5);
    final Integer sumAndMultiply = sum.andThen(multiply).apply(5);

    Assert.assertEquals(multiplyAndSum, Integer.valueOf(35));
    Assert.assertEquals(sumAndMultiply, Integer.valueOf(75));
}
```

Помимо дефолтных методов у интерфейса есть также один статический метод:

- `static Function<T, T> identity()` - возвращает функцию, которая всегда возвращает переданный ей объект. Метод используется для того чтобы не создавать лишних объектов функций.

Интерфейс Function дополнен более узкими функциональными интерфейсами для различных примитивных типов:

- `double`:
    - `DoubleFunction<R>` - принимает double, возвращает что захочешь
    - `DoubleToIntFunction` - принимает double, возвращает int
    - `DoubleToLongFunction` - принимает double, возвращает long
    - `ToDoubleFunction<T>` - принимает что захочешь, возвращает double
    - `ToDoubleBiFunction<T, U>` - принимает что захочешь, возвращает double ???????
- `int`:
    - `IntFunction<R>` - принимает int, возвращает что захочешь
    - `IntToDoubleFunction` - принимает int, возвращает double
    - `IntToLongFunction` - принимает int, возвращает long
    - `ToIntFunction<T>` - принимает что захочешь, возвращает int
    - `ToIntBiFunction<T, U>` - принимает что захочешь, возвращает int ???????
- `long`:
    - `LongFunction<R>` - принимает long, возвращает что захочешь
    - `LongToIntFunction` - принимает long, возвращает int
    - `LongToDoubleFunction` - принимает long, возвращает double
    - `ToLongFunction<T>` - принимает что захочешь, возвращает long
    - `ToLongBiFunction<T, U>` - принимает что захочешь, возвращает long ???????

Эти интерфейсы не расширяют интерфейс `Function`, потому что принимают / отдают примитивные типы, которые не могут использоваться в [обобщениях](../generics.md).

Также, в отличие от интерфейса Function, у этих интерфейсов нет методов `compose()` и `andThen()`.

---
## Интерфейс `Predicate<T>`

Основной метод предикатов `test()` принимает аргумент и выдает ответ: удовлетворяет ли данный аргумент заданному условию.

```java
Predicate<String> isEvenLetters = s -> s.length() % 2 == 0;
Assert.assertSame(isEvenLetters.test("bla"), false);
```

Также в интерфейсе `Predicate` объявлены следующие дефолтные методы:
- `Predicate<T> and(Predicate<T>)` - объединяет первоначальный предикат с переданным с помощью булевой операции AND.
- `Predicate<T> or(Predicate<T>)` - объединяет первоначальный предикат с переданным с помощью булевой операции OR.
- `Predicate<T> negate()` - возвращает инверсированный предикат.

Эти методы прекрасны тем, что обеспечивают ленивое выполнение кода внутри предиката

```java
@Test
public void testOfLaziness() {
    final StringBuilder testString = new StringBuilder();
    final Predicate<String> truePredicate = a -> {
        testString.append("lazy");
        return true;
    };
    final Predicate<String> falsePredicate = a -> {
        testString.append(" - not!");
        return false;
    };

    final boolean result = truePredicate.or(falsePredicate).test("blank");
    Assert.assertTrue(result);
    Assert.assertEquals(testString.toString(), "lazy");
}
```

Кроме того есть еще один статический метод:
- `Predicate<T> isEqual(Object)` - возвращает предикат, который будет сравнивать переданный объект на равенство с аргументом метода `test()`.

Интерфейс `Predicate` дополняется более узкими функциональными интерфейсами для различных примитивных типов:
- `DoublePredicate` - предикат, принимающий в качестве значений переменные типа double
- `IntPredicate` - предикат, принимающий в качестве значений переменные типа int
- `LongPredicate` - предикат, принимающий в качестве значений переменные типа long

---
## К изучению
- [X] Курс на Stepik по функциональному программированию (на англ.): https://stepik.org/course/1595/syllabus
- [X] Р. Уорбэртон - Лямбда-выражения в Java 8