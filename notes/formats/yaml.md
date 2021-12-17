---
title: "YAML"
tags:
  - formats
draft: false
---

# YAML

YAML - YAML Ain't Markup Language - текстовый [формат](formats.md) описания данных.
Чаще всего YAML используется для описания каких-либо конфигураций: настройки сервисов, описание образов в docker-compose, плэйбуки в Ansible и многое другое.
Особую любовь он приобрел в DevOps сообществе.

YAML для описания иерархии между объектами использует разрывы строк и отступы (идентацию).
Из-за этого YAML невозможно свернуть в одну строку, как это можно сделать с [JSON](json.md) или [XML](xml.md).
Именно поэтому он не завоевал популярности в качестве формата для передачи данных между сервисами.
Интересно, что при этом YAML является надмножеством над JSON: в YAML файле в любом месте могут быть вставки в формате JSON.

Файлы написанные с использованием YAML синтаксиса имеют формат `.yaml` или `.yml`.


## Синтаксис

Название параметра отделяется от значения с помощью двоеточия:
```yaml
key: value
```

Комментарии обозначаются решеткой. Все что после решетки до конца строки - комментарий
```yaml
# next line will have value and comment
nonComment: lol # comment after value
```

### Типы данных
- строки - можно писать просто, а можно заключать в кавычки, если строку можно перепутать с числом или чем-нибудь еще
- числа - целые и десятичные
- булевы значения - привычные `true`, `false`, но зачем-то добавили еще `yes` с `no`, и `on` с `off`
- null - null есть null
- многострочные строки - строки, которые позволяют писать текст с разрывами строк безо всяких `\n`

```yaml
nudeString: lol
quotedString: 'lol'
doubleQuotedString: "lol"
integer: 1
float: 1.5
boolean: true
dumbBoolean: yes # Тоже boolean, вот это да!
dumbBoolean2: off # Тоже boolean, вот это да! (2)
nullable: null
multilineString: | # Вся магия благодаря вертикальной палке
  WOW men
  This is multiline string
  Awesome!
singleline: > # Волшебный знак 'больше'
  And this is multiline string
  That would be interpret
  As a single string
```

### Структуры данных
- пары ключ-значение (мапа)
- объекты
- списки

Все, что находится в YAML на одном уровне - это пары ключ-значение.
```yaml
property: value
key: value
# here is an object
user:
  name: Arty
  age: 30
  # with list:
  adresses:
    - 'Moscow, Pervomayskaya street, 80'
    - 'Tolghiatti, Revolution street, 3a'
  # and list of object
  childs:
    - name: Varvara
      sex: female
    - name: Zahar
      sex: male
```


## Схема

Для yaml не существует схем, но благодаря тому, что YAML является надмножеством над JSON, для описания его структуры может использоваться JSON-схема.


## Инструменты
В Java для работы с yaml существуют библиотеки:
- `snakeyaml` для YAML спецификации до 1.1 и `snakeyaml-engine` для YAML 1.2


---
## К изучению

- [ ] [Спецификация](https://yaml.org/spec/1.2.2/) на YAML
- [X] [Видео](https://www.youtube.com/watch?v=1uFVr15xDGg&ab_channel=TechWorldwithNana) на английском