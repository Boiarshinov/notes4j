---
title: "Монады"
tags:
  - functional_programming
draft: false
---

**Монада** - термин из функционального программирования, обозначающий контейнер, в который можно запаковать объект и совершать с ним действия, также возвращающие такие же контейнеры:

У монады обязательно должны быть определены два метода:

- unit - упаковка объекта в контейнер
- bind - связывание контейнера с функцией, возвращающий также контейнер.

![](../../../images/monad_fun_picture.jpg)

В Java примерами монад являются:

- `Optional<T>` (см. [Класс Optional](../../java/optional.md)) - монада MayBe
    - unit - `Optional.of()`, `Optional.ofNullable()`, `Optional.empty()`
    - bind - `flatMap()`
- `Stream<T>` (см. [Стримы](../../java/stream_and_lambda/stream.md))
    - unit - `Stream.of()`
    - bind - `flatMap()`

---
## К изучению

- [ ] Адитья Бхаргава о монадах в картинках:  http://adit.io/posts/2013-04-17-functors%2C_applicatives%2C_and_monads_in_pictures.html
- [X] Перевод предыдущей статьи:  https://habr.com/ru/post/183150/
- [ ] Попытка объяснить монады человеческим языком:  https://lex-kravetski.livejournal.com/540744.html
- [ ] Про монады и функторы на англ:  https://medium.com/beingprofessional/understanding-functor-and-monad-with-a-bag-of-peanuts-8fa702b3f69e