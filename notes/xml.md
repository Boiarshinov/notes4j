---
title: "XML"
tags:
draft: false
---

**XML** - eXtensible Markup Language - расширяемый язык разметки.

Для описания структуры XML документов существуют специальные средства: DTD и XML Schema. Про них ниже.

---
## Основная структура

В начале XML-документа всегда записывается *декларация* (`prolog`). Она нужна для того, чтобы идентифицировать документ как xml и задать некоторые свойства.

```xml
<?xml version="1.0" encoding="UTF-8"?>
```

XML представляет собой дерево. У дерева может быть только один корневой элемент. Корневой элемент объявляется сразу после декларации.

*Элемент* - сущность, которую можно представить в виде объекта. Элементы записываются с помощью собственных тегов.

```xml
<computer>
    <motherboard>MSI</motherboard>
    <videocard>GeForce</videocard>
</computer>
```

*Атрибут* - свойство объекта, записывается в открывающем теге элемента.
```xml
<computer size="5" speedIndex="10">My computer</computer>
```

Если у элемента нет других элементов, а есть только атрибуты, то он может быть описан всего одним тегом
```xml
<computer motherboard="MSI" videocard="GeForce" />
```

---
## Комментарии
Комментарии записываются так же, как и в html:
```xml
<!-- комментарий -->
```

---
## Пространства имен

Для того чтобы разрешить коллизию имен элементов в xml было введено такое понятие как namespace - пространство имен. В тексте xml пространство имен задается с помощью ключевого слова xmlns, после которого ставится двоеточие и как обычный атрибут объявляется пространство имен и уникальный индекс.

```xml
<computers:cooler xmlns:computers="Unique ID 1">
    ...
</computers:cooler>
<watertools:cooler xmlns:watertools="Unique ID 2">
    ...
</watertool:cooler>
```

Достаточно часто в качестве уникального индекса используется URL.

Для корневого элемента также может быть определено стандартное пространство имен без указания префикса

```xml
<root xmlns="www.standart-namespace.com/">
    ...
</root>
```

---
## DTD - document type definition.

Основные конструкции:

- `ELEMENT` - объявление элементов. В скобках записываются элементы, которые входят в данных элемент. Количество элементов можно указывать с помощью квантификаторов:
    - `+` - 1+
    - `*` - 0+
    - `?` - 0 или 1
    - без квантификатора - обязательно должен быть один элемент
- `ATTLIST` - объявление аттрибутов, свойственных элементу. Указывается название аттрибута и его обязательность:
    - `#REQUIRED` - обязателен
    - `#IMPLIED` - необязателен
- `ENTITY` - определение алиасов (сокращений)

```dtd
<!ELEMENT computer (motherboard, videocard)>
<!ELEMENT motherboard (RAMs)>
<!ELEMENT RAMs (RAM+)>
<!ELEMENT RAM EMPTY>
<!ELEMENT videocard PCDATA>

<!ATTLIST computer
    size CDATA #REQUIRED
    speedIndex CDATA #IMPLIED
>

<!ENTITY GF "GeForce">
```

DTD может применяться к XML-файлу двумя способами:

- Вставка в сам XML-файл в корневой элемент (обязательно нужно прописать DOCTYPE)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE computer [
    <ELEMENT ...
]>
<computer>
    ...
</computer>
```

- ссылка на документ с DTD

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE computer SYSTEM "dtd_example1.dtd">
<computer>
    ...
</computer>
```

DTD не поддерживает пространства имен.

В настоящее время DTD используется редко. От него отказываются в пользу XML Schema.

---
## XML Schema

XML Schema описывается в отдельном файлике, который имеет расширение .xsd - xml scheme definition.

Схемы записываются с помощью того же синтаксиса xml, поэтому их достаточно привычно читать.

### Основные элементы 

- `element` - объект
- `comlexType` - указывает, что элемент является сложным и включает в себя другие элементы
- `sequence` - обозначает, что комплексный элемент может иметь список последующих элементов
- `attribute` - объявление атрибутов, свойственных элементу

Объявление элементов:

Указывается имя элемента, тип (если он не комплексный), и если элемент входит в состав другого комплексного элемента в виде списка, то указывается минимальное и максимальное количество вхождений

```xsd
<element name="address" type="string" maxOccurs="unbounded" minOccurs="0" />
```

Объявление комплексных типов и списков:
Если элемент комплексный, то он объявляется не в одиночном, а в двойном теге
```xsd
<element ... >
    <comlexType>
        <sequence>
            <element ... />
        </sequence>
    </comlexType>
</element>
```

Объявление аттрибутов:
указывается имя атрибута, его тип и обязательность:
```xsd
<attribute name="name" type="string" use="required"/>
```

### Подключение схемы к XML
В корневом элементе прописываются следующие строчки:
```xml
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemeLocation="http://www.artybor.org/ example_schema1.xsd"
```

В аргументе `schemeLocation` указывается список значений в формате:
```
    пространство_имен    путь_к_схеме
```

Также можно подключить схему без использования пространств имен. В таком случае в корневом элементе прописываются следующие строки:

```xml
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:noNamespaceSchemaLocation="example_schema1.xsd"
```

---
## Чтение XML в Java
Существует несколько способов:

- SAX
- DOM - при парсинге загружает весь xml в память приложения, из-за чего не подходит для обработки больших файлов
- JAXB

<mark>Про каждый из них нужно писать отдельную заметку</mark>

---
## Дескрипторы развертывания

xml-файлы широко применяются в качестве [дескрипторов развертывания](servlets/deployment_descriptor.md) в [Java EE](java_ee/java_ee.md).

Таблица соответствия дескрипторов развертывания и спецификация Java EE

| **Файл** | **Спецификация** | **Местоположение** |
| --- | --- | --- |
| `application.xml` | Java EE | META-INF |
| `application-client.xml` | Java EE | META-INF |
| `beans.xml` | [CDI](java_ee/cdi.md) | META-INF или WEB-INF |
| `ra.xml` | JCA | META-INF |
| `ejb-jar.xml` | EJB | META-INF или WEB-INF |
| `faces-config.xml` | JSF | WEB-INF |
| `persistence.xml` | JPA | META-INF |
| `validation.xml` | Валидация компонентов | META-INF или WEB-INF |
| `web.xml` | [Сервлеты](servlets/servlet.md) | WEB-INF |
| `web-fragment.xml` | [Сервлеты](servlets/servlet.md) | WEB-INF |
| `webservices.xml` | Веб службы SOAP | META-INF или WEB-INF |

---
## К изучению

- [X] Цикл из 3 статей про XML (написано корявенько): https://javarush.ru/groups/posts/620-konkurs-osnovih-xml-dlja-java-programmista---chastjh-1-iz-3
- [X] Самое введение: https://javarush.ru/groups/posts/2287-chto-takoe-xml
- [ ] Большой англоязычный сайт с туториалами по XML: https://www.w3schools.com/xml/default.asp
- [ ] Русскоязычный учебник для начинающих: https://msiter.ru/tutorials/uchebnik-xml-dlya-nachinayushchih