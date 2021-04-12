---
title: "Bean Validation"
tags:
  - java_ee
draft: false
---

Bean Validation - это библиотека, входящая в состав [Java EE](../java_ee/java_ee.md) (начиная с версии 6.0), позволяющая валидировать приходящие значения на удовлетворение каким-либо условиям.

Начиная с версии 2.0 для использования библиотеки в своем проекте необязательно подтягивать зависимость всей Java EE.

Bean Validation представляет собой спецификацию, реализовывать которую могут различные вендоры.

Реализации:

- Hibernate Validator - единственная успешная реализация.

С помощью Bean Validation можно валидировать следующее:

- обязательность поля
- валидация значений чисел
- валидация длины строки / числа
- валидация по регулярному выражению
- кастомная валидация

Весь код Bean Validation находится в пакете `javax.validation`, который необходимо подключать к проекту отдельно.

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

@Positive
@Max(100500)
private final Integer salary;

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

Нетипичным шагом является навешивание аннотации `@Constraint` в которой необходимо указать класс-валидатор, который будет производить проверки.

Для этого в аннотации нужно присвоить значение атрибуту `validatedBy` значение типа `Class`.

Также в аннотации обязательно должны быть определены следующие параметры:

- String message() - сообщение об ошибке, которое должно начинаться с полного пути до класса `dev.boiarshinov.project.validation.MyRule.<message>`.
- `Class<?>[] groups()` - для кастомизации групп ???
- `Class<? extends Payload>[] payload()` - для расширения ???

Полный пример создания пользовательской аннотации приведен ниже:
```java
@Constraint(validatedBy = MyRuleConstraintValidator.class)
@Target({ElementType.METHOD, ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface MyRule {
    public String value();

    public String message() default "dev.boiarshinov.project.validation.MyRule.Vovan - loh!";

    public Class<?>[] groups() default {};
    public Class<? extends Payload>[] payload() default {};
}
```

### Создание класса-валидатора

Кастомный валидатор должен реализовывать интерфейс `ConstraintValidator`. Этот интерфейс является обобщенным и для него необходимо указать два  класса: аннотацию, которая ставится над валидируемым полем, и класс валидируемого поля.

Также необходимо реализовать два метода:

- `void initialize(A annotation)` - принимает данные из аннотации. (Метод дефолтный, его можно не переопределять, если у аннотации нет аргументов).
- `boolean isValid(T value, ConstraintValidatorContext)` - true, если пришедшее значение удовлетворяет ограничению.

```java
public class MyRuleConstraintValidator implements ConstraintValidator<MyRule, String> {

    private String valueFromAnnotation;

    @Override
    public void initialize(final MyRule myRule) {
        this.valueFromAnnotation = myRule.value();
    }

    @Override

    public boolean isValid(final String valueFromUser, final ConstraintValidatorContext context) {

        final boolean isValid = /* function(valueFromAnnotation, valueFromUser) */;

        return isValid;
    }
}
```
<mark>Что такое ConstraintValidatorContext ?</mark>

---
## Интеграция со Spring

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

Также необходимо добавить зависимость одной из реализаций. Допустим это будет Hibernate Validator:

```xml
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>${hibernate-validator.version}</version>
</dependency>
```

---
## К изучению
- [ ] Официальная документация: https://beanvalidation.org/
- [X] Github страничка Hibernate Validator: https://github.com/hibernate/hibernate-validator/
- [X] Курс Spring & Hibernate for Beginners на Udemy
- [X] Интеграция Bean Validation и спринговых Rest-контроллеров: https://www.baeldung.com/spring-validate-requestparam-pathvariable
- [ ] Документация на спринговую валидацию: https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#validation