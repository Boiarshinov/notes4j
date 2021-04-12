---
title: "Spring MVC Form Validation"
tags:
  - spring
  - web
draft: false
---

Spring MVC Form Validation - это механизм, основанный на [Bean Validation](../java_ee/bean_validation.md), позволяющий валидировать поля, приходящие из формы с помощью [Spring MVC Form Tags](mvc_form_tags.md).

Для валидации полей модельного класса в контроллере перед модельным аргументом необходимо поставить аннотацию `@Valid`, что запустит механизм валидации. Ошибки, найденные при валидации, записываются в объект класса `BindingResult`. Объект этого класса необходимо добавить в аргументы метода контроллера сразу после модельного аргумента (иначе чудо не произойдет).

```java
    @RequestMapping("/processForm")
    public String processForm(@Valid @ModelAttribute("student") Student student,
                              BindingResult bindingResult) {
        if (bindingResult.hasErrors()) {
            return "student-form";
        } else {
            return "student-confirmation";
        }
    }
```

---
## Класс BindingResult

Объекты интерфейса `BindingResult` представляют собой результат присвоения полям модельного класса значений,в том числе они накапливают в себе ошибки валидации полей модельного объекта.

Реализации интерфейса:

- `BeanPropertyBindingResult` - стандартная реализация. Получает доступ к полям объектов с помощью геттеров и сеттеров.
- `MapBindingResult` - Реализация, основанная на [мапе](../java/collections/map.md).
- `DirectFieldBindingResult` - реализация, получающая доступ к полям объекта напрямую через рефлексию

---
## Преобработка приходящих значений

<mark>Не уверен, что это относится к form validation</mark>

Для того чтобы по каким-то правилам обрабатывать приходящие значения, необходимо в контроллере определить метод, помеченный аннотацией `@InitBinder`. Метод должен принимать объект класса `WebDataBinder`.

```java
@InitBinder
public void initBinder(WebDataBinder dataBinder) {
    final StringTrimmerEditor stringTrimmerEditor = new StringTrimmerEditor(true);
    dataBinder.registerCustomEditor(String.class, stringTrimmerEditor);
}
```

В переданном `WebDataBinder` можно зарегистрировать различные преобработчики приходящих значений. Все обработчики реализуют интерфейс `PropertyEditor` (входит в состав стандартной библиотеки Java).

Список этих обработчиков огромен, и все они разбросаны по различным библиотекам.

Спринговые обработчики находятся в пакете `org.springframework.beans.propertyeditors`, часть из них представлена ниже:

- `StringTrimmerEditor` - отсекает пустые символы с концов строк
- `FileEditor` - парсит приходящую строку в класс `File`.
- `URLEditor` - парсит приходящую строку в класс `URL`.

При необходимости можно создать собственную реализацию `PropertyEditor`.

---
## Возврат ошибок валидации

В результате валидации заполняемых полей при некорректных данных эти данные не будут приняты. При этом необходимо оповестить пользователя о том, по какой причине его данные не были приняты. Для этого существует тег `errors` в библиотеке тегов [Spring MVC Form Tags](mvc_form_tags.md). Для того чтобы вывести ошибку, необходимо записать этот тег с указанием переменной, при ошибке валидации которой будет выводиться сообщение.

```html
<form:errors path="postalCode" cssClass="error" />
```

- `path` - поле валидируемого объекта

Текст сообщения берется из валидирующей аннотации, которая стоит над полем:
```java
@Pattern(regexp = "^[a-zA-Z0-9]{5}]", message = "only 5 chars/digits")
private int postalCode;
```

### Сообщения об ошибках валидации

Сообщения об ошибках валидации можно настроить в валидирующих аннотациях с помощью атрибута `message`:

```java
@Min(value = 10, message = "should be greater than 9.")
```

Но иногда ошибка при попытке присвоения полю значения может быть не связана с валидирующими аннотациями. Например, при несовпадении типов.

В таком случае можно определить пользовательские сообщения об ошибках в [properties-файле](../java/properties.md). Названия сообщений должны подчинятся следующему паттерну:

```properties
<тип ошибки>.<валидируемый объект>.<поле> ... <внутреннее поле>
typeMismatch.myModelClass.myField = Invalid number
```

Сообщения будут доставаться из properties-файла автоматически после подключения к Spring-приложению бина `ResourceBundleMessageSource`.

Типы ошибок:

- `typeMismatch` - несовпадение типов
- ???

---
## К изучению

- [X] Курс Spring & Hibernate for Beginners на Udemy