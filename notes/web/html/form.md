---
title: "Формы"
tags:
  - html
draft: false
---

# Формы

**Формы** используются для ввода пользователем неких данных (имени, почты, логина, пароля и т.д.), которые будут отправлены на сервер.

Формы задаются с помощью тегов `<form>`
Внутри могут быть текстовые поля, кнопки, блоки и пр. Полный список:
- Текстовое поле
- Специализированное текстовое поле
- Кнопки отправки данных и сброса
- Переключатели и флажки
- Раскрывающиеся и прокручиваемые списки
- Элементы управления выбором и выгрузкой файла
- Элементы обозначения даты и времени
- Элементы числового ввода
- Элементы выбора цвета

Атрибуты:
- `action` - указывается ссылка на обработчик формы
- `method` - указывает метод отправки данных на сервер. Имеются в виду методы [HTTP](../../network/http.md). `GET` - метод по умолчанию

```html
<form method="post" action="handler.do">
    ...
</form>
```

В Java-программах значение атрибута action присваивается в соответствии со смыслом выполняемых действий. Соответствие значения атрибута action и конкретного сервлета устанавливается в файле web.xml.

---
## Текстовое поле

Служит для введения пользователем данных. Записывается с помощью одиночного тега `<input>` с атрибутами
```html
<input type="text">
```

Атрибуты:
- `id` - задает элементу уникальный идентификатор, используется при доступе к элементу из JavaScript или CSS
- `name` - задает имя переменной, которую будет обрабатывать сервер
- `value` - задает текст, который отображается в форме по умолчанию
- `maxlength` - ограничивает количество вводимых символов

---
## Многострочное текстовое поле

Также служит для введения текстовых данных, но позволяет вводить много строк. Записывается с помощью тега `<textarea>`.

Атрибуты:
- `rows` - задает размер поля по высоте в количестве строк
- `cols` - задает размер поля по ширине по количеству символов

Если текст не влезает в область, автоматические будет создана полоса прокрутки
```html
<textarea rows=4 cols=50>
Текст
</textarea>
```

---
## Специальные поля ввода текста

Такие поля ввода используются для ввода паролей, поисковых запросов, адресов электронной почты, телефонных номеров, URL-адресов и прочего.

Задается с помощью указания типа в обычном текстовом поле
```html
<input type="password">
```

- `password` - поле ввода пароля. Все вводимые символы будут заменяться звездочками
- `checkbox` - флажок
- `color` - виджет с выбором цвета
- `date` - поле для ввода календарной даты
- `datetime` - поле для ввода даты и времени
- `email` - поле для ввода электронной почты
- `file` - поле для ввода имени файла, который пересылается на сервер
- `hidden` - скрытое поле (зачем???)
- `image` - графическая кнопка для отправки данных на сервер
- `month` - поле для ввода месяца и года
- `number` - поле для ввода чисел
- `radio` - радио-кнопка
- `range` - ползунок для ввода чисел в указанном диапазоне
- `search` - поле для ввода строки поиска
- `tel` - поле для ввода номера телефона
- `text` - текстовое поле
- `time` - поле для ввода времени
- `url` - поле для ввода web-адреса
- `week` - поле для ввода номера недели и года

У всех специальных текстовых полей есть те же атрибуты, что и у обычного текстового поля, и есть дополнительные

---
## Списки для выбора

Список объявляется с помощью тега `<select>`, а варианты выбора в нем с помощью `<option>`:
```html
<p>Кто из ваших друзей лох?
    <select name="Лох">
        <option value=1 selected="selected">Вован</option>
        <option value=2>Вован</option>
        <option value=3>Вован</option>
    </select>
</p>
```

После выбора пользователем значение value будет отправлено на сервер.

---
## Кнопки отправки данных и сброса

Для объявления кнопки сброса данных в первоначальное состояние нужно объявить поле с типом `reset`
```html
<input type="reset">
```

Для объявления кнопки отправки данных на сервер нужно объявить поле с типом `submit`
```html
<input type="submit">
```

Это единственные элементы, которые не требуют ввода атрибута `name`
Надпись на кнопке будет подстраиваться под региональные настройки языка.