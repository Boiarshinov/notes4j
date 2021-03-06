---
title: "MIME"
tags:
  - web
draft: false
---

**MIME** - Multipurpose Internet Mail Extensions - многоцелевые расширения интернет-почты.

MIME используется, для того чтобы обозначить тип передаваемого контента с помощью протоколов, которые первоначально были предназначены только для передачи текстовой информации, например SMTP и HTTP.

MIME первоначально предназначался для [электронной почты](../common/email.md), но впоследствии начал использоваться не только в ней, но и в [HTTP](../network/http.md) (может быть и еще где-то).

MIME позволяет устанавливать иерархию вложенности одних блоков контента в другие. При этом блоки контента могут быть смешанного содержимого.

Тип определяется двумя словами, записанными через прямой слеш. Первое слово - это общий тип, а второе - уточнение.
```
image/jpeg
```

### Общие типы:

- application - внутренний формат программы
- audio - аудио. Например mp3
- image - изображение. Например png
- message - сообщение
- model - 3D-модели
- multipart - множественное содержимое
- text - текст. Например html
- video - видео. Например mpeg

---
## К изучению

- [X] Вики. MIME-типы: https://ru.wikipedia.org/wiki/MIME