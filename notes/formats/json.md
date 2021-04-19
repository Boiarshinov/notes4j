---
title: "JSON"
tags:
  - formats
draft: false
---

**JSON** - это стандарт обмена данными между сервером и web-страницей с JavaScript, в котором поля объектов представляются в формате ключ-значение:

```json
{
	"name": "ArtyB",
	"age": 27,
	"programmLanguage": "Java"
}
```

---
## Форматы JSON

| **Тип** | **Что это** | **Пример** |
| --- | --- | --- |
| String | Строка | `"Text"` |
| Number | Число | `5`, `5.5`, `-8` |
| Boolean | Булево значение | `true`, `false` |
| Null | null | `null` |
| Object | Объект | `{"param1": "value1", "param2": "value2"}` |
| Array | Массив | `[5, 5.8, -34]` |

Значения параметров объектов могут быть любого типа (даже массивом)

### Представление мапы (словаря, отображения)

Для мап в JSON не предусмотрено отдельного формата. Вместо этого мапы представляются в виде объекта, в котором каждый ключ - это отдельное поле со своим объектом

```json
"map": {
    "key1": {
        "value_field_1": "string",
        "value_field_2": 5
    },
    "key2": {
        "value_field_1": "string",
        "value_field_2": 123
    }
}
```

---
## JSON-схема

Схема JSON - это аналог XML-схем, которая позволяет валидировать JSON
<mark>//дописать</mark>

---
## К изучению

- [X] Вики про JSON:  https://ru.wikipedia.org/wiki/JSON
- [X] Англоязычный туториал по JSON:  https://restfulapi.net/introduction-to-json/
- [ ] Официальный сайт по JSON-схемам http://json-schema.org/
- [X] Введение в JSON-схемы (читать до середины):  https://habr.com/ru/post/276305/
- [X] JSON-схемы для генерации POJO:  https://javarush.ru/groups/posts/1995-json-skhema-zachem-i-komu-ona-nuzhna
- [ ] Видео про кодогенерацию:  https://www.youtube.com/watch?v=9mF0zFW7cDQ&index=8&list=PLsVTVVvrKX9t7a0_KpweUSfEt7XeRDzM-