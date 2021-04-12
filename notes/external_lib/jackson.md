---
title: "Jackson"
tags:
  - external_lib
draft: false
---

**Jackson** - библиотека для сериализации и десериализации Java объектов в [JSON](evernote:///view/170585988/s440/c4882009-d9ad-4a56-8a06-bcc97c91d05c/c4882009-d9ad-4a56-8a06-bcc97c91d05c/).

<mark>//Не только для JSON, но и для XML. Написать про это отдельную заметку.</mark>

В Spring REST контроллерах Jackson работают автоматически. Все приходящие json мапятся в объекты, а все исходящие объекты конвертируются в json.

Условия для сериализации / десериализации:

- поля, которые мы хотим сериализовывать должны быть публичными или должны иметь геттеры и сеттеры
- Для десериализации из строки у класса должен быть пустой конструктор, либо над имеющимся конструктором должна быть проставлена аннотация `@JsonConstruct`.
- Класс должен быть помечен аннотацией @JsonAutoDetect (проверить. Возможно устаревшая инфа)

Классы, которые должны сериализоваться в JSON, и поля в них помечаются следующими **аннотациями**:

- Перед объявлением классов:
    - `@JsonAutoDetect` - ставится перед классом. Класс может быть сериализован в JSON
    - `@JsonPropertyOrder` - Ставится перед классом. Устанавливает порядок полей при сериализации в JSON
- Перед полями
    - `@JsonIgnore` - Ставится перед полем. Поле игнорируется при сериализации в JSON
    - `@JsonProperty(String)` - Ставится перед полем, сеттером или геттером. Позволяет задать полю имя, которое будет использовано в JSON.
```java
@JsonAutoDetect
class Cat {
    @JsonProperty("kittyName")
    public String name;
    public int age;
    @JsonIgnore
    public int weight;

    Cat() { } //Обязательно должен быть пустой конструктор
}
```

- для коллекций:
    - `@JsonDeserialize(as = Class)` - ставится перед полем, помогает мапперу понять, какой тип имеет данный объект

```java
@JsonDeserialize(as = LinkedList.class)
private List<MyObjects> myObjects;
```
-
    - `@JsonDeserialize(contentAs = Class)` - показывает, какого типа будет дженерик в списках
    - `@JsonDeserialize(keyAs = Class)` - показывает, какого типа будет дженерик ключей в словарях.

### Сериализация перечислений

По умолчанию enum'ы сериализуются по имени конкретного перечисления.

Но иногда требуется десериализовать ответ от какого-либо сервиса в перечисление и тогда необходимо сконвертировать названия перечислений из ответа в перечисление, используемое в приложении.

Для этого в перечислении объявляется поле, над которым навешивается аннотация `@JsonValue`. При десериализации Jackson будет автоматически пытаться перевести пришедшую строку в соответствующее ей перечисление.

```java
@Getter
@RequiredArgsConstructor
public enum ErrorCode {
    MISSING_INPUT_SECRET("missing-input-secret"),
    INVALID_INPUT_SECRET("invalid-input-secret"),
    MISSING_INPUT_RESPONSE("missing-input-response"),
    INVALID_INPUT_RESPONSE("invalid-input-response"),
    BAD_REQUEST("bad-request"),
    TIMEOUT_OR_DUPLICATE("timeout-or-duplicate");

    @JsonValue
    private final String value;
}
```

### Сериализация классов Date Time API

Для сериализации и десериализации объектов классов нового [Date Time API](../java/time/datetime_api.md) в Jackson необходимо подключить отдельный модуль - `JavaTimeModule`:
```java
//Где-то в конфигурационном классе
objectMapper.registerModule(new JavaTimeModule());
```

Тот формат, в который сериализуются классы времени, может не подходить для приложения. Для того чтобы установить формат по умолчанию нужно сконфигурировать упомянутый выше модуль. Для этого нужно передать в модуль свой форматтер для каждого из используемых классов времени:
```java
final JavaTimeModule timeModule = new JavaTimeModule();

timeModule.addSerializer(LocalDate.class, new LocalDateSerializer(DateTimeFormatter.ofPattern("dd.MM.yyyy")));

timeModule.addSerializer(LocalDateTime.class, new LocalDateTimeSerializer(DateTimeFormatter.ofPattern("dd.MM.yyyy HH:mm:ss")));

mapper.registerModule(timeModule);
```

Над теми полями, которые необходимо сериализовать каким-то отдельным способом можно настроить отдельный форматтер с помощью аннотации `@JsonFormat`. Шаблон сериализации указывается с помощью параметра `pattern`
```java
@JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "dd.MM.yyyy")
private final LocalDate creationDate;
```

---

## Сериализация полиморфных типов

- `@JsonTypeInfo` - содержит информацию о том, поля какого типа включаются в сериализацию
- `@JsonSubTypes` - определяет субклассы аннотированного класса
- `@JsonTypeName` - определяет дополнительное имя класса, которое используется

### @JsonTypeInfo
Параметры:

- `Id`
    - `NONE`
    - `CLASS`
    - `MINIMAL_CLASS`
    - `NAME`
    - `CUSTOM`
- `As`
    - `PROPERTY`
    - `WRAPPER_OBJECT`
    - `WRAPPER_ARRAY`
    - `EXTERNAL_PROPERTY`
    - `EXISTING_PROPERTY`

---
## Класс ObjectMapper

Данный класс позволяет сериализовать объект в строку, записанную в формате JSON.

В Spring приложениях `ObjectMapper` обычно объявляют один на все приложение и создают в виде бина в конфигурационном компоненте.

Конструктор:

- `ObjectMapper()` - пустой

Методы:

- `void writeValue(Writer, Object)` - сериализует переданный объект в символьный поток вывода
```java
StringWriter writer = new StringWriter();
ObjectMapper mapper = new ObjectMapper();
mapper.writeValue(writer, someObject); //Записали объект в формате JSON в поток
System.out.println(writer.toString());
```

- `V readValue(Reader, V.class)` - возвращает сериализованный объект, прочитанный из ридера
```java
MyObject myObject = mapper.readValue(reader, MyObject.class);
```

---
## К изучению

- [ ] Книга Baeldung (есть в папке Книги)
- [X] Учебник по JSON (перевод книги Baeldung, половина не переведена): https://nsergey.com/jackson-annotations/
- [ ] Официальная документация по Jackson: https://github.com/FasterXML/jackson-docs
- [X] Сериализация перечислений: https://www.baeldung.com/jackson-serialize-enums
- [X] Модуль Jackson для работы с Date Time API: https://github.com/FasterXML/jackson-modules-java8