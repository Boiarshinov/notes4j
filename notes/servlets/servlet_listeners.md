---
title: "Слушатели сервлетов"
tags:
  - servlet
  - java_ee
  - web
draft: false
---

Данная тема непосредственно связана с [сервлетами](servlet.md)

|     |     |     |
| --- | --- | --- |
| **Интерфейс слушателя** | **Класс события** | **Предназначение** |
| ServletContextAttributeListener | ServletContextAttributeEvent | Получает события при добавлении или изменении атрибута в ServletContext |
| HttpSessionListener | HttpSessionEvent | Получает событие при создании или инвалидировании сессии. Может использоваться для отслеживания количества активных юзеров |
| ServletRequestListener | ServletRequestEvent | Получает событие каждый раз, когда приходит запрос. Используется для логирования |
| ServletRequestAttributeListener | ServletRequestAttributeEvent | Получает событие, когда добавляются атрибуты к реквесту |
| HttpSessionBindingListener | HttpSessionBindingEvent | Получает событие, когда объекты класса атрибута привязываются или удаляются от сессии. Используется для оповещения объектов класса атрибута об их использовании. Интерфейс обычно реализуется классами, которые выступают в качестве атрибутов. |
| HttpSessionAttributeListener | HttpSessionBindingEvent | Получает событие, когда добавляются или изменяются атрибуты сессии |
| ServletContextListener | ServletContextEvent | Получает события при развертывании и выключении веб-приложения. Используется для подключения ресурсов. Подробнее тут: [Развертывание веб-приложений](./deployment_descriptor.md) |
| HttpSessionActivationListener | HttpSessionEvent | Получает событие, когда сессия мигрирует с одной виртуальной машины (сервера) на другую |

---
## ServletContextAttributeListener

<mark>todo</mark>

---
## HttpSessionListener

Интерфейс обычно реализуется каким-либо специальным классом.

Методы:
- `void sessionCreated(HttpSessionEvent)` - при открытии сессии
- `void sessionDestroyed(HttpSessionEvent)` - при инвалидировании сессии

Класс должен быть зарегистрирован в дескрипторе развертывания
```xml
<listener>
    <listener-class>
        org.artybor.MyHttpSessionListener
    </listener-class>
</listener>
```

## HttpSessionEvent
Методы:
- `HttpSession getSession()` - возвращает

---
## ServletRequestListener

<mark>todo</mark>

---
## ServletRequestAttributeListener

<mark>todo</mark>

---
## HttpSessionBindingListener

Используется для оповещения объектов класса атрибута об их использовании. Интерфейс обычно реализуется классами, которые выступают в качестве атрибутов сессии.

Методы:
- `void valueBoud(HttpSessionBindingEvent)` - сообщает объекту класса, который используется в качестве атрибута, что его добавили в список атрибутов сессии.
- `void valueUnbound(HttpSessionBindingEvent)` - сообщает объекту класса, который используется в качестве атрибута, что его исключили из списка атрибутов сессии.

## HttpSessionBindingEvent extends HttpSessionEvent
Методы:
- `HttpSession getSession()` - возвращает объект сессии, в которой произошли изменения атрибута
- `String getName()` - возвращает имя атрибута, который стриггерил появление события
- `Object getValue()` - возвращает значение атрибута, которое было ДО изменения, стриггерившего событие

---
## HttpSessionAttributeListener

Используется, если необходимо знать, когда к сессиям добавляются (изменяются, удаляются) атрибуты. Для реализации интерфейса пишется специальный класс.

Методы:
- `void attributeAdded(HttpSessionBindingEvent)` - при создании атрибута сессии
- `void attributeRemoved(HttpSessionBindingEvent)` - при удалении атрибута сессии
- `void attributeReplaced(HttpSessionBindingEvent)` - при изменении атрибута сессии

Класс должен быть зарегистрирован в дескрипторе развертывания.
```xml
<listener>
    <listener-class>
        org.artybor.MyHttpSessionAttributeListener
    </listener-class>
</listener>
```

Про класс события написано выше.

---
## ServletContextListener

Слушатель используется для [подключения ресурсов](deployment_descriptor.md)

---
## HttpSessionActivationListener

Интерфейс используется в случаях, когда необходимо знать, что сессия мигрирует с одной ноды на другую.

Интерфейс может быть реализован как специальным классом, так и классом атрибута.

Методы:
- `void sessionWillPassivate(HttpSessionEvent)` - вызывается перед миграцией сессии с одной виртуальной машины на другую. Тут можно подготовить атрибуты сессии к миграции.
- `void sessionDidActive(HttpSessionEvent)` - вызывается сразу после миграции сессии с одной виртуальной машины на другую. Здесь можно подготовить атрибуты сессии для вызова из java-кода.

Если классы атрибутов реализуют интерфейс `Serializable`, то необходимость в использовании этого слушателя отпадает, вся подготовка к передаче с одной ноды на другую будет происходить автоматически.

??? Класс должен быть зарегистрирован в дескрипторе развертывания.

---
## К изучению
- [X] Глава 6 Head First Servlets and JSP
