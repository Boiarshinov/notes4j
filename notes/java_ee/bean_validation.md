---
title: "Bean Validation"
tags:
  - java_ee
draft: false
---

# Bean Validation

Bean Validation - это стандарт, входящий в состав [Java EE](../java_ee/java_ee.md) (начиная с версии 6.0)и позволяющий валидировать приходящие значения на удовлетворение каким-либо условиям.

Начиная с версии 2.0 для использования библиотеки в своем проекте необязательно подтягивать зависимость всей Java EE.

Bean Validation представляет собой спецификацию, реализовывать которую могут различные вендоры.

Реализации:
- Hibernate Validator - единственная успешная реализация.
- [Apache BVal](http://bval.apache.org/index.html) - еще одна реализация, которая почему-то не признана Bean Validation 

С помощью Bean Validation можно валидировать следующее:
- обязательность поля
- валидация значений чисел
- валидация длины строки / числа
- валидация по регулярному выражению
- кастомная валидация

Весь код Bean Validation находится в пакете `javax.validation`, который необходимо подключать к проекту отдельно.

---
## Использование

Bean Validation позволяет устанавливать ограничения на значения полей (а еще аргументов методов) объектов с помощью установки аннотаций, несущих в себе метаданные об ограничениях.
Аннотации могут ставиться на поля, на аргументы метода, на целый класс и даже на обобщенные типы в классах-контейнерах.
Вот к примеру обычный модельный класс, в котором с помощью аннотаций Bean Validation установлены ограничения на значения ряда полей:

```java
@ValidUser
public class User {
    @NotNull private String name;
    @Email private String email;
    @Min(18) private int age;
    @NotEmpty private List<@Skill Skill> skills;
    
    /* ... getters, setters, etc. */
}
```

Проверка соответствия объекта заданным ограничениям выполняется с помощью специального валидатора:
```java
ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
Validator validator = factory.getValidator();
Set<ConstraintViolation<User>> violations = validator.validate(user);
```

Класс `Validation` - это входная точка в библиотеку Bean Validation.
С его помощью можно выбрать конкретную реализацию стандарта Bean Validation и настроить фабрику валидаторов.

<mark>Дописать</mark>

---
## Валидирующие аннотации

### Общего назначения

- `@NotNull` - проверяет, что поле имеет значение, отличное от *null*. *Важно*: не работает для примитивных типов - необходимо использовать классы-обертки.
- `@Null` - проверяет, что поле имеет значение *null*.

### Числа

- `@Min` - проверяет, что число больше указанного минимального значения.
- `@Max` - проверяет, что число меньше указанного максимального значения.
- `@Negative` - число должно быть отрицательным.
- `@Positive` - число должно быть положительным.
- много других

```java
@Positive
@Max(100500)
private final Integer salary;
```

### Строки

- `@NotBlank` - строка не должна быть пустой.
- `@Size` - длина строки должна быть в указанных пределах
- `@Pattern` - проверяет, что строка удовлетворяет регулярному выражению.
- `@Email` - валидирует, что строка представляет собой валидный e-mail адрес.

### Логические переменные

- `@AssertTrue` - проверяет, что логическая переменная имеет значение true
- `@AssertFalse` - проверяет, что логическая переменная имеет значение false

### Даты / Время

- `@Future` - проверяет, что значение даты находится в будущем относительно текущего времени
- `@Past` - проверяет, что значение даты находится в прошлом относительно текущего времени

### Коллекции

- `@Size` - задает ограничение по количеству элементов в коллекции минимальным и максимальным значением.
- `@NotEmpty` - проверяет, что в коллекции не пустая

---
## Создание собственных правил валидации

Для создания собственных правил валидации необходимо сделать две вещи:
1. Создать аннотацию, которая будет проставляться над полями, требующими нестандартной валидации
2. Создать класс-валидатор, который будет производить необходимые проверки.

### Создание аннотации

Создание пользовательской валидирующей аннотации почти ничем не отличается от создания обычной [аннотации](annotations.md).

`@Retention` должно быть задано значением `RetentionPolicy.RUNTIME`, так как аннотация будет работать во время выполнения.

`@Target` должно быть задано значением `ElementType.FIELD`, если планируется ставить аннотацию над полями класса, или `ElementType.METHOD`, если аннотацию планируется ставить над сеттерами. Можно задать оба этих значения (и даже больше).

Нетипичным шагом является навешивание аннотации `@Constraint`, в которой необходимо указать класс-валидатор, который будет производить проверки.

Для этого в аннотации нужно присвоить значение атрибуту `validatedBy` значение типа `Class`.

Также в аннотации обязательно должны быть определены следующие параметры:

- `String message()` - сообщение об ошибке. Обычно в целях интернализации в дефолтное значение вписывают название ключа, по которому можно найти текст сообщения в properties-файлике нужной локали. По соглашению значение ключа должно соответствовать полному пути до класс-файла аннотации `dev.boiarshinov.project.validation.MyConstraint.message`.
- `Class<?>[] groups()` - для кастомизации групп. Каждая валидация может проходить в рамках какой-либо группы валидации. Если ни одной группы не передано, то используется группа `Default`.
- `Class<? extends Payload>[] payload()` - метаданные о конкретном использовании ограничения. `Payload` - это маркировочный интерфейс, нужный для обеспечения типобезопасности. Переданные метаданные можно вытащить в валидаторе и на их основе реализовать какую-либо логику.

Полный пример создания пользовательской аннотации приведен ниже:
```java
@Constraint(validatedBy = MyConstraintValidator.class)
@Target({ElementType.METHOD, ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface MyConstraint {
    public String value();

    public String message() default "{dev.boiarshinov.project.validation.MyConstraint.message}";
    public Class<?>[] groups() default {};
    public Class<? extends Payload>[] payload() default {};
}
```

#### Объединение ограничений

Иногда требуемое ограничение может быть композицией нескольких существующих ограничений. 
Например, телефонный номер можно описать двумя ограничениями: размер строки и регулярка на цифры.
Тогда удобно создать одну композитную аннотацию
```java
@Pattern("\\d*")
@Size(min = 10, max = 10)
@ReportAsSingleViolation
@@Constraint(validatedBy = PhoneValidator.class)
@Target({ElementType.METHOD, ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface Phone {

    public String message() default "{dev.boiarshinov.project.validation.Phone.message}";
    public Class<?>[] groups() default {};
    public Class<? extends Payload>[] payload() default {};
}
```
При нарушении значением сразу нескольких ограничений будет получено сообщение по каждому из них.
Если хочется, чтобы сообщение было только одно, то нужно повесить аннотацию `@ReportAsSingleViolation`.
В этом случае при нарушении любого из ограничений в отчет попадет сообщение только композитной аннотации.


### Создание класса-валидатора

Кастомный валидатор должен реализовывать интерфейс `ConstraintValidator`. Этот интерфейс является обобщенным и для него необходимо указать два класса: аннотацию, которая ставится над валидируемым полем, и класс валидируемого поля.

```java
public class MyConstraintValidator implements ConstraintValidator<MyConstraint, String> { /* ... */ }
```

Также необходимо реализовать два метода:

- `void initialize(A annotation)` - инициализирует валидатор в соответствии со значениями параметров, определенных в аннотации. (Метод дефолтный, его можно не переопределять, если у аннотации нет аргументов). Пример использования можно посмотреть в валидаторе аннотации `@INN` hibernate-validator.
- `boolean isValid(T value, ConstraintValidatorContext)` - true, если пришедшее значение удовлетворяет ограничению.

Считается хорошим тоном возвращать true, если в метод `isValid()` приходит null. 
Для того чтобы запретить аннотированному параметру принимать значение null, разработчик должен дополнительно навесить на него ограничение `@NotNull`.

```java
public class MyConstraintValidator implements ConstraintValidator<MyConstraint, String> {

    private String valueFromAnnotation;

    @Override
    public void initialize(final MyConstraint myConstraint) {
        this.valueFromAnnotation = myConstraint.value();
    }

    @Override
    public boolean isValid(final String valueFromUser, final ConstraintValidatorContext context) {
        final boolean isValid = /* function(valueFromAnnotation, valueFromUser) */;
        return isValid;
    }
}
```
<mark>Что такое ConstraintValidatorContext ?</mark>

<mark>Можно ли кидать бизнесовые исключения в методе `isValid()`?</mark>

---
## Интеграция со Spring

<mark>Вынести в отдельную заметку</mark>

С помощью Bean Validation можно валидировать данные, приходящие из REST-контроллеров в аргументы методов, помеченных `@RequestParam` и `@PathVariable`.

Для того чтобы валидация начала работать необходимо над классом контроллера поставить аннотацию `@Validated`.

```java
@RestController
@Validated
public class AgentController {

    @GetMapping("/agent/basic-info")
    public List<AgentBasicInfo> basicInfo(
        @RequestParam(name = "referral_hash") @NotEmpty final List<String> agentsHashes,
    ) {
        /* */
    }
}
```

---
## Подключение зависимости

Для подключения библиотеки необходимо добавить в `pom.xml` следующую зависимость
```xml
<dependency>
    <groupId>javax.validation</groupId>
    <artifactId>validation-api</artifactId>
    <version>${validation.version}</version>
</dependency>
```

Также необходимо добавить зависимость одной из реализаций. Выбор тут небольшой:
```xml
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>${hibernate-validator.version}</version>
</dependency>
```

В Spring Boot приложениях достаточно добавить одну зависимость: `spring-boot-starter-validation`.

---
## Идеи

Даже если правило валидации какого-либо поля очень простое, в интересах переиспользования лучше создать отдельную аннотацию.
Тогда при изменениях в правилах валидации нужно будет изменить код только в одном месте.
Если в проекте микросервисная архитектура, то возможно стоит вынести эти правила валидации в отдельную библиотеку (возможно прикопать их в ту же библиотеку, где объявлены переиспользуемые DTO).

Валидаторы на класс должны проверять соответствие только тем ограничениям, которые определяются совокупностью нескольких параметров класса. 
Не стоит в том же валидаторе проверять то, что можно проверить отдельно.

Стоит подумать над тем, чтобы добавить аннотацию `@Enum`, которая проверяет, что пришедшая строка (из json) соответствует хотя бы одному из значений перечисления.
Возможно это нужно добавлять не сюда, а куда-то на стык с Spring MVC и Jackson.

Аннотация `@Length` из hibernate-validator является менее мощным вариантом аннотации `@Size`. 
Так что стоит объявить ее deprecated.

Можно создать курс по Bean Validation. 
В нем последовательно знакомить слушателей с разными концепциями и особенностями
- Валидация небольшого DTO с использованием стандартных аннотаций
- Разные виды простановки валидаторов
  * На поле
  * На геттер
  * На класс
  * На типы в контейнерах (`Optional`, `List`, etc.)
  * Валидация типов в кастомных контейнерах с использованием экстракции данных (возможно нужно давать это чуть позже остального)
- Создание композитной аннотации
- Наследование ограничений. Простановка ограничений на интерфейсах
- Каскадная валидация (с помощью @Valid)
- Сообщения о провале валидации
  * Получение сообщения об ошибке из properties-файла
  * Локализация сообщений об ошибках
- Группы валидации
- Создание валидатора для PbC
- ...
- Интеграция с JPA
- Интеграция со Spring

На [сайте Bean Validation](https://beanvalidation.org/2.0/) указано, что версию 2.0 стандарта поддерживает только Hibernate Validator, хотя в [гитхабе проекта Apache BVal](https://github.com/apache/bval) написано, что они тоже поддержали JSR 380

---
## К изучению
- [ ] Официальная документация: https://beanvalidation.org/
- [X] Github страничка Hibernate Validator: https://github.com/hibernate/hibernate-validator/
- [X] Курс Spring & Hibernate for Beginners на Udemy
- [X] Интеграция Bean Validation и спринговых Rest-контроллеров: https://www.baeldung.com/spring-validate-requestparam-pathvariable
- [ ] Документация на спринговую валидацию: https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#validation
- [X] Устаревшая статья с множеством ошибок: https://habr.com/ru/post/424819/
- [X] Статья про Bean Validation и его интеграцию со Spring Boot: https://habr.com/ru/post/536612/