---
title: "Аннотации"
tags:
  - core
  - syntax
draft: false
---

Аннотации позволяют добавлять в исходный код дополнительную информацию в декларативном стиле.

Аннотации записываются перед классами, методами, полями и др.:
```java
@MyAnnotation(s = "Example") //В скобках присваиваются значения членам 
public static void print() { ... }
```

Аннотацию, которая не влияет на поведение программы, называют *маркерной аннотацией*.

В Java существуют *встроенные аннотации*:
- входят в пакет `java.lang`:
    - `@Deprecated` - (маркер) классовый метод (класс, поле) устарел и не рекомендуется к использованию
    - `@Override` - (маркер) ставится перед переопределенными методами
    - `@SuppressWarnings("unchecked")` - отключает ругань компилятора на подозрительные места в коде. В скобках указывается какие именно предупреждения отключить
    - `@SafeVarargs` - (маркер) показывает, что в методе и конструкторе не возникает ошибок при обработке переменного числа аргументов. Служит для того чтобы компилятор не выдавал warning'ов.
    - `@FunctionalInterface` - (маркер) ставится перед [функциональными интерфейсами](stream_and_lambda/functional_interface.md).
- входят в пакет `java.lang.annotation` (про другие аннотации пакета смотри ниже):
    - `@Native` - используется для аннотирования поля, к которому имеет доступ машинный код


---
## Создание собственных аннотаций

Аннотации обычно создаются разработчиками фреймворков (таких как Spring или TestNG). Но в редких случаях возникает необходимость написать собственную аннотацию.

Аннотации создаются с помощью механизма, основанного на интерфейсах

```java
@interface MyAnnotation { //Создаем аннотацию. Первым символом обязательно ставится @

    String s(); //член аннотации. Выглядит как метод, но скорее является полем
}
```

Для параметра аннотации можно указать значение по умолчанию с помощью ключевого слова `default`:

```java
String s() default "Hello world!";
```

Над интерфейсом любой пользовательской аннотации обычно определяются две стандартные аннотации:

- `@Retention` - определяет будет ли аннотация видна в исходном коде, скомпилированном файле и в процессе выполнения. Аннотации должно быть передано одно из следующих значений:
    - `RetentionPolicy.SOURCE` - аннотации будут игнорироваться компилятором. Такой тип часто используется в кодогенераторах, которые дополняют код до его компиляции. Например, большинство аннотаций [Lombok](../external_lib/lombok.md) имеют подобный тип
    - `RetentionPolicy.CLASS` - (значение по умолчанию) аннотация будет записана в .class-файл, но не будет считываться виртуальной машиной во время выполнения. Этот тип используется в некоторых стандартных аннотациях, например `@Override` и `@SuppressWarning`.
    - `RetentionPolicy.RUNTIME` - аннотация будет доступна JVM во время выполнения. Такой тип чаще всего используется в фреймворках вроде [Spring](../spring/spring.md), [JPA](../jpa/jpa.md), [Bean Validation](../java_ee/bean_validation.md) и т.д.
- `@Target` - определяет в каком контексте может использоваться аннотация. Аннотации должно быть передано множество из следующих значений (по умолчанию - все значения кроме TYPE):
    - `ElementType.TYPE` - над классом, интерфейсом или перечислением
    - `ElementType.FIELD` - над полем
    - `ElementType.METHOD` - над методом
    - `ElementType.PARAMETER` - над аргументом метода
    - `ElementType.CONSTRUCTOR` - над конструктором
    - `ElementType.LOCAL_VARIABLE` - над локальной переменной
    - `ElementType.ANNOTATION_TYPE` - над аннотациями
    - `ElementType.PACKAGE` - над пакетом
    - `ElementType.TYPE_PARAMETER` -
    - `ElementType.TYPE_USE` -

Если необходимо определить сразу несколько контекстов, то это делается через запятую:

```java
@Target(ElementType.FIELD, ElementType.METHOD)
@interface MyAnnotation { /* ... */ }
```

Чуть реже определяются другие аннотации:

- `@Documented` - Используется для пометки других аннотаций. Если аннотация помечена `@Documented`, то любое ее применение является публичным контрактом. Например, если над классом (или чем-то еще) стоит аннотация, помеченная `@Documented`, то эта аннотация является метаинформацией о классе и будет учитываться при генерации javadoc'ов. Почти все стандартные аннотации являются `@Documented`.
- `@Repeatable` - таким образом помечаются аннотации, которые могут быть неоднократно применены к одному и тому же элементу. Для того чтобы использовать эту аннотацию необходимо определить аннотацию-контейнер, у которой будет значение в виде массива с типом повторяющихся аннотаций.  Аннотации должно быть передано значение - класс аннотации-контейнера.

```java
@Repeatable(Animals.class)
@interface Animal { }

@interface Animals {
    Animal[] value();
}
```

- `@Inherited` - (маркер) указывает на то, что данная аннотация при объявлении над классом (с интерфейсами не работает) будет применена также ко всем наследникам этого класса.

Все вышеперечисленные стандартные аннотации входят в пакет `java.lang.annotation`.


---
## Использование собственных аннотаций

Аннотации являются всего лишь метками и для того, чтобы определить какую-либо логику в зависимости от наличия аннотаций над элементами кода необходимо сначала эти аннотации отыскать.

Для этого можно либо написать свой код, сканирующий все классы, либо можно воспользоваться библиотекой **Reflections**.

Указав имя пакета с классами, которые требуется просканировать, можно воспользоваться методом `getTypesAnnotatedWith(Class)`, чтобы получить множество всех классов, помеченных заданной аннотацией:

```java
private static Set<Class<?>> getAnnotatedClasses(
    final Class<? extends Annotation> annotationClass,
    final String packageName)
{
    final Reflections reflections = new Reflections(packageName);

    final Set<Class<?>> annotatedClasses = reflections.getTypesAnnotatedWith(annotationClass);

    return annotatedClasses;
}
```

Если необходимо узнать значение, помещенное в аннотацию, нужно вызвать из объекта аннотации метод с таким же названием, как у значения аннотации.

Для того чтобы вытащить объект аннотации из класса можно воспользоваться методом `Class.getAnnotation(Class<? extends Annotation>)`:

```java
private static String getAnnotationValue(Class<?> annotatedClass)
{
    MyAnnotation annotation = annotatedClass.getAnnotation(MyAnnotation.class);
    return annotation.value();
}
```

Так как операции рефлексии достаточно дорогие, обычно в annotation processing инструментах результаты вызовы reflection методов кэшируются.
Ниже представлен пример кэширования из фреймворка [Bean Validation](../java_ee/bean_validation.md):
```java
private final ConcurrentReferenceHashMap<Class<?>, BeanMetaData<?>> beanMetaDataCache;

public <T> BeanMetaData<T> getBeanMetaData(Class<T> beanClass) {
    /* ... */
	BeanMetaData<? super T> beanMetaData = beanMetaDataCache.get( normalizedBeanClass );

	if ( beanMetaData != null ) {
		return (BeanMetaData<T>) beanMetaData;
	}

	beanMetaData = createBeanMetaData( normalizedBeanClass );
	beanMetaDataCache.putIfAbsent( normalizedBeanClass, beanMetaData );
    /* ... */
}
```

---

## К изучению:

- [X] Javadoc на стандартные аннотации Java
- [X] [Пример использования](https://vertex-academy.com/tutorials/ru/java-8-annotacii/) аннотации Repeatable
- [ ] GitHub библиотеки Reflections: https://github.com/ronmamo/reflections
- [X] [Для чего используются различные типы RetentionPolicy](https://stackoverflow.com/questions/8048941/annotation-source-retention-policy)