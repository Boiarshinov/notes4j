---
title: "Распределенные системы"
tags: 
  - distributed_system
draft: false
---

# Распределенные системы

Распределенные системы - это системы, взаимодействующие части которых находятся на разных вычислительных устройствах.

Распределенные системы создаются, когда:
- система по своей природе должна быть распределенной. Например, мессенджер
- для увеличения надежности
- для ускорения (положить данные ближе к пользователям)
- для выполнения сложных задач, с которыми не может справиться одна машина

Распределенные системы подвержены следующим проблемам:
- пропажа связи между частями системы
- выход из строя частей системы (падение ноды)

При общении частей распределенной системы, возникают задержки, обусловленные временными затратами на передачу данных.
Эти задержки называются **latency**.
При оценке быстродействия распределенных систем можно ориентироваться на следующие цифры:
- ~ 1 мс - части системы находятся в одном датацентре
- ~ 100 мс - части системы находятся на разных континентах


## RPC

Обращение к удаленной части системы зачастую хотят представить как обращение к внутренней.
Такое обращение называется [RPC](inter_process_communication.md) - Remote Procedure Call.
Для того чтобы упростить RPC вызов в коде, используются различные RPC-фреймворки.


## Проблемы распределенных систем

### Проблема двух генералов

Проблема, свойственная всем распределенным системам формулируется так:

Город осаждается двумя армиями.
Одна армия находится с одной стороны от города, другая армия - с другой.
Город возможно захватить только если напасть одновременно.
Если напасть порознь, то город сможет отбиться, и второй армии уже не удастся его захватить.
Руководят армиями генералы, и они пытаются договориться о времени атаки, отправляя друг другу гонцов.
Гонцов могут перехватить по дороге.

В таких условиях рождается проблема: 
Пусть первый генерал отправляет гонца с указанием времени атаки.
Гонец прибегает ко второму генералу, сообщает ему время, и бежит обратно, чтобы донести, что послание доставлено.
Если гонца перехватят, то первый генерал не будет знать на каком этапе его перехватили: до встречи со вторым генералом или после.
Поэтому первый генерал решает, что он пойдет в атаку только если гонец вернется.

Но теперь второй генерал попадает в ту же ситуацию.
Он знает, что первый генерал пойдет в атаку, если гонец вернется, но не знает ничего о судьбе гонца.
Таким образом они никогда не смогут договориться.

Можно переложить эту проблему на IT следующим образом: 
Пусть есть интернет магазин, который для оплаты заказа обращается к стороннему платежному шлюзу.
Магазин хочет отправлять заказ только если произошла оплата.
Платежный шлюз не хочет списывать деньги, если не уверен, что магазин отправит заказ.

В данном случае проблема решается просто - платежный шлюз должен списывать деньги всегда.
Магазин же, если не дождался ответа от шлюза при первом запросе, должен дополнительным запросом узнать статус оплаты.
И только после этого начать собирать заказ.


### Проблема византийских генералов

Проблема византийских генералов, является развитием проблемы двух генералов.
Пусть теперь генералов не 2, а от 3 и более.
Они все так же пытаются договориться о времени встречи.
Гонцов теперь перехватить невозможно.
Но среди генералов есть предатели - они могут врать другим генералам.
Причем предатели еще и знают друг о друге и могут устраивать сговоры.

Все это очень похоже на игру "Мафия" или на Among Us.

Существует доказательство, что для того чтобы генералам вычислить предателей и все-таки договориться о времени атаки, нужно, чтобы общее количество генералов
превышало количество предателей согласно следующей формуле.
```
allGeneralsCount > 3 * traitorsCount + 1
```

Если генералы могут использовать криптографию, то общее необходимое количество сокращается, но общение между ними становится очень сложным.


### Проецируя на информационные системы

Перекладывая на информационные системы:
- проблема двух генералов - про проблемы в сетевом взаимодействии
- проблема византийских генералов - про проблемы в общении между сервисами или нодами одного приложения

В реальном мире в распределенных системах возникают оба вида проблем.


## Модель распределенной системы

Модель распределенной системы стоит на 3 китах:
- поведение сети 
- поведение частей системы (нод)
- работа со временем

### Поведение сети

Связь между двумя частями одной системы может быть трех видов:
1. __Надежная связь__ - если сообщение отправлено, то оно обязательно дойдет до адресата. Сообщения могут приходить не в том порядке, в котором были отправлены.
2. __Fair loss связь__ - сообщения могут быть потеряны, задублированы и приходить в ином порядке. Если делать повторные попытки, то когда-нибудь (__eventually__) сообщения дойдут.
3. __Произвольная (компроментированная) связь__ - сообщения могут быть изменены, потеряны, задублированы, отправлены на сторону, дополнены.

К счастью, с помощью различных ухищрений свести все к 1-ой группе.
Для того чтобы привести 2-ую группу к первой, нужно добавить ретраи и дедупликацию.
Для того чтобы привести третью группу ко второй, нужно добавить шифрование сообщений (TLS).
```
  3 - Arbitrary links
    + TLS
= 2 - Fair loss links
    + retry & deduplication
= 1 - Reliable links    
```

### Поведение нод

- __correct__ - нода всегда работает и работает правильно
- __fail-stop__ - нода упала и больше никогда не вернется в строй (сгорел сервер)
- __fail-recovery__ - нода упала, но она скоро рестартанет. Потеряны только те данные, которые лежали в оперативной памяти
- __fail-arbitrary__ (византийское) - нода имеет непредсказуемое поведение. В том числе ее поведение может содержать злой умысел

### Работа со временем

- __синхронное__ - известен верхний предел задержки ответа.
- __частично синхронное__ - большую часть времени система функционирует как синхронная, но иногда ответ не приходит в установленные таймауты
- __асинхронное__ - нет никаких гарантий по времени выполнения задачи / получению ответа. Вплоть до того, что задача может быть не выполнена никогда / ответ никогда не придет.

При разработке систем лучше никогда не считать, что система имеет синхронное поведение. 
Потому что если она когда-нибудь не уложится в установленные таймауты, то поведение всей системы может привести к катастрофическим проблемам.
Лучше воспринимать системы, объявленные синхронными, как частично синхронные.

Скорее всего синхронные системы вообще невозможны из-за возможных проблем с сетью и проблем с производительностью самой системы (например из-за сборки мусора в Java-приложении).


## Доступность систем

Доступность системы - это относительная величина: какую часть времени система является доступной.
Обычно к системам предъявляют требования по доступности в процентах:
- 99 % - не более 3.7 дня простоя в год
- 99.9 % - не более 8.8 часов простоя в год
- 99.99 % - не более 53 минут простоя в год
- 99.999 % - не более 5.3 минуты простоя в год

В IT-шном сленге обычно говорят "доступность нашей системы - 4 девятки".

Зачастую доступность системы становится одним из SLO.
<mark>Вынести в отдельную заметку: </mark>

- **SLA** - Service-Level Agreement - соглашение об уровне обслуживания. 
  Представляет собой юридический документ, возможно часть договора между платежеспособным клиентом и компанией, владеющей продуктом.
- **SLO** - Service-Level Objective - цель уровня обслуживания. 
  Например, какой процент запросов должен быть обработан быстрее чем за заданный таймаут в течении какого-то промежутка времени:
  > 99.9 % запросов в течение дня должно быть обработано быстрее, чем за 200 мс
- **SLI** - Service-Level Indicator - метрика, отслеживающая выполнение SLO.


## Fault tolerance

Чтобы система была устойчива к различным проблемам, нужно проектировать ее толерантной к падениям (**Fault tolerance**).
Это значит, что система должна оставаться доступной даже при выходе из строя отдельных частей.

Для этого система должна уметь выявлять отказавшие части и заменять их.
Выявление упавших нод производится с помощью детектора.

**Fault detector** может регулярно опрашивать ноды системы, и если нода не отвечает в отведенный таймаут, то признавать ее упавшей.
Так можно делать, если нода является синхронной, а сетевое взаимодействие надежным, чего не бывает в настоящих системах.
Поэтому все fault detector'ы являются __eventually perfect__.
Это значит, что в большинстве случаев они правильно признают ноду упавшей, но иногда они ошибаются и могут признать рабочую ноду упавшей, а упавшую - рабочей.

## Модели консистентности
- Линеаризуемость - самая строгая модель консистентности
- Eventually consistency - обещание, что когда-нибудь данные в системе станут консистентны
  
### Линеаризуемость
Линеаризуемость - это свойство распределенной системы отдавать данные так, как будто у нее существует всего одна нода.
Т.е. любая операция чтения всегда возвращает самое свежее состояние данных.

Линеаризуемость - это самый строгий вид консистентности для распределенных систем.

Для того чтобы добиться линеаризуемости в распределенных системах используется ABD алгоритм, либо [Total order broadcast](broadcasting.md) с compare-and-swap операцией.

У линеаризуемости есть существенный недостаток - она сильно бьет по перфомансу, из-за того что нужно блокировать запросы на чтения до тех пор, пока в системе не образуется консенсус. 
Также линеаризуемость мешает масштабированию кластера, т.к. при наличии лидера, он становится бутылочным горлышком для всей системы.

### Eventually consistency
Модель называется eventually consistency, потому что при ней кластер почти никогда не бывает в консистентном состоянии, но если бы в нем не происходило бы никаких событий, то рано или поздно он бы пришел в консистентное состояние.

Если распределенная система построена на модели eventually consistency, то каждая нода будет при чтении отдавать данные на основе только своего состояния.

Для eventually consistency важно чтобы итоговое состояние не зависело от порядка получения событий. Т.е. две реплики, получившие события в разном порядке, должны оказаться в одном состоянии.
Также в такой модели нужно уметь решать конфликты, которые образуются при конкурентном обновлении данных на разных нодах.


---
## К изучению

- [X] [Видео-курс от Мартина Клеппмана](https://www.youtube.com/watch?v=UEAMfLPZZhE&list=PLeKd45zvjcDFUEv_ohr_HdUFe97RItdiB&ab_channel=MartinKleppmann)
- [X] [SLA, SLO, SLI](https://www.atlassian.com/ru/incident-management/kpis/sla-vs-slo-vs-sli)