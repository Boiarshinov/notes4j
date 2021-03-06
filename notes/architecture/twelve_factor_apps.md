---
title: "12-ти факторные приложения"
tags: 
draft: false
---

# 12-ти факторные приложения

12-ти факторные приложения - это приложения, которые обещают выполнение некоторых гарантий при выполнении 12 условий.

Манифест 12-ти факторных приложений придумали владельцы сервиса [Heroku](https://www.heroku.com/home).

Гарантии:
- Время погружения новых разработчиков в проект будет минимальным;
- Приложения будет легко переносить между разными средами;
- Приложения будет легко мигрировать в облако;
- Облегчает внедрение Continious Deployment;
- Можно масштабировать горизонтально.

Условия:
- Кодовая база
- Зависимости
- Конфигурация
- Сторонние сервисы
- Сборка, релиз, рантайм
- Процессы
- Привязка портов
- Многопоточность
- Утилизируемость
- Совместимость стендов разработки и прода
- Логирование
- Задачи администрирования

### Кодовая база
Нужно, чтобы один и тот же код мог быть развернут на разных средах: от локальной машины разработчика до прода.

Также кодовая база должна храниться в системе контроля версий, чтобы можно было иметь историю релизов, иметь возможность выкатывать разные релизы на разные стенды, и при необходимости откатить приложение до более ранней версии.

### Зависимости
Приложение не должно полагаться на существование каких-либо пакетов в операционной системе.

> Все свое ношу с собой

Зависимости приложения должны быть объявлены в виде манифеста.
Благодаря этому его сможет полностью собрать менеджер зависимостей, который самостоятельно выкачает все необходимые зависимости из какого-нибудь репозитория (nexus, artifactory, maven central).

### Конфигурация
Все настройки приложения, которые указывают на внешние зависимости, должны быть вынесены в конфигурацию приложения.
Примеры таких настроек:
- host'ы внешних сервисов
- логины и пароли от внешних сервисов
- включение / отключение моков

Таким образом можно, не меняя кодовую базу, поднимать приложения на разных средах, что в итоге увеличивает скорость поставки кода до прода.

### Сторонние сервисы
Приложение должно разрабатываться так, чтобы сторонние сервисы можно было поменять.
К сторонним сервисам в том числе относятся:
- базы данных
- очереди сообщений
- REST сервисы
- прочие

По сути это значит, что все конфиги подключения к сторонним сервисам должны быть не захардкожены, а вынесены в конфиг.

### Сборка, релиз, рантайм
Должно быть выделено 3 стадии жизненного цикла приложения:
- сборка - компиляция исходного кода и его сборка в пакет
- релиз - объединение сборки с переменнами среды конкретного окружения
- рантайм - запуск в среде выполнения

При этом запрещается изменение кода приложения в рантайме.
То есть всякие фичи вроде горячего подключения классов в JVM запрещены.
Любое изменение кода должно пройти все 3 стадии.

### Процессы
Приложение должно быть stateless.
Каждый запрос должен обрабатываться независимо.
Никаких sticky sessions!

Если хочется что-нибудь похранить, то следует использовать базы данных или внешние кэши.

### Привязка портов
Сервисы должны экспортироваться через привязку портов. <mark>что это значит?</mark>

Приложение должно включать в себя веб-сервер.

### Многопоточность
Приложение должно уметь самостоятельно обрабатывать несколько запросов конкурентно.
Также приложение должно быть можно запускать в нескольких инстансах (горизонтальное масштабирование).

### Утилизируемость
Приложения должны быстро стартовать (не более нескольких секунд), для того чтобы гибко подстраивать количество инстансов под входящую нагрузку.

Остановка приложения не должна приводить к критическим последствием. 
Приложение должно отпустить все захваченные ресурсы.

### Совместимость стендов разработки и прода
Стенды, на которых развернуто приложение, должны быть максимально похожи друг на друга.
- время между деплоем новой версии приложения на разные среды должно быть минимальным.
- запуск деплоя должен осуществляться разработчиком.
- инфраструктура различных стендов должна быть максимально похожей.
- должны использоваться одни и те же внешние сервисы (а не так, что на dev-стенде Postgres, а на проде - Oracle)

### Логирование
Приложение должно писать логи только в стандартный вывод без буферизации.
В файлы оно писать не должно!

Из stdout уже можно настроить выгрузку логов в какой-нибудь единый центр их хранения.
Например, с помощью Splunk в ElasticSearch.

### Задачи администрирования
Разовые задачи администрирования должны запускаться с помощью скриптов.
Код скриптов должен лежать в репозитории рядом с кодом основного приложения.


---
## К изучению
- [X] [Официальный манифест](https://12factor.net/ru/), переведенный на русский язык