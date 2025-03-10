---
title: "Domain Driven Design"
tags: 
  - architecture
draft: true
---

# Domain Driven Design

__DDD__ - Domain Driven Design - это подход к проектированию информационных систем, основанный на анализе бизнес-домена.

В DDD используется следующая терминология:

__Domain__ - область деловой активности (бизнеса). Например: авиаперелеты.

__Subdomain__ - подобласть деловой активности. Совокупность всех субдоменов образует домен.

__Domain model__ - система абстракций, включающая только те аспекты предметной области, которые преобладают при решении конкретных бизнес задач.
То есть доменная модель игнорирует те признаки бизнес сущностей, которые не важны для выполнения бизнес задачи. 
Например, для перевода денег между банками может игнорироваться информация о том, кто сейчас является директором этого банка, какой у него логотип и т.д.

__Bounded Context__ - контекст, внутри которого существует доменная модель.
Когда бизнес разрастается может появиться необходимость подробить доменную модель на несколько контекстов.
Например, в приложении по сделкам с недвижимостью доменная модель квартиры может быть разбита на две модели: одна для контекста продажи квартиры, другая для контекста сдачи в аренду.
Доменные модели квартиры в этих контекстах будут обладать разным набором свойств.

__Aggregate__ - кластер доменных сущностей, которые рассматриваются как единое целое с точки зрения изменения данных.
Агрегат состоит из Entity и Value Object.

__Value Object__ - это объект, который можно идентифицировать по составу его значений.
У Value Object как правило нет суррогатных идентификаторов.
Примеры Value Object: координаты объекта на карте, цвет, адрес, индикатор подозрительности операции (fraudScore).

__Entity__ (сущность) - это объект, который идентифицируется по некоторому индентификатору.
Две сущности с одинаковым набором полей, но с разными идентификаторами все еще будут двумя разными сущностями.

__Domain Event__ - доменное событие - это событие, произошедшее с сущностью. 
На события произошедшие с доменной сущностью могут реагировать другие части системы.



