---
title: "Deep link"
tags:
  - web
  - network
draft: false
---

# Deep link

В области мобильных приложений часто используют [URI](./uri.md) с кастомной схемой, которая ассоциируется с тем или иным приложением. 
Такие ссылки называются __deep link'ами__.
Например, ссылка `todoapp://best.todo.com/path`, может быть проассоциирована с мобильными приложениями с todo листами.

Но при этом любое приложение может проассоциировать себя с любым количеством схем, что может быть небезопасным.
Например, мошенническое приложение может притвориться банковским мобильным приложением и будет открываться при переходе по ссылке вместо желаемого.
Или таким образом можно вредить конкурентам, например Яндекс Карты могут ассоциировать с собой схемы вида `2gis:` или `gmaps:` и тогда при открытии deep link'ов, ведущих на другие приложения с картами, вам будет предложен выбор из нескольких приложений, в том числе Яндекс Карт.

__Web Link__, начинающиеся со схем `http:` и `https`, являются частным случаем Deep link'ов.

## App Link
В мобильных операционных системах Android и iOS существуют еще отдельные системы, которые по web link'ам способны определить подходящие приложения.
Они соотносят хост из URI со своим реестром, который хранится на серверах Google и Apple.
Если для хоста в реестре есть ассоциированное приложение, то оно откроется. 
В случае когда приложение не установлено на телефон, то ссылка откроется в обычном браузере.