---
title: "Развертывание веб-приложений"
tags:
  - java_ee
  - servlets
  - web
draft: false
---

При старте веб-приложения просыпается контейнер сервлетов и начинает читать дескриптор развертывания.

---

## Дескриптор развертывания

Дескриптор развертывания web-приложения - это xml-файл, в котором прописываются данные необходимые для деплоя приложения:
- Описание сервлетов и их параметров
- Введение параметров контекста
- Описание слушателей контекста
- Настройка сессий

Дескриптор развертывания описывается в файле `web.xml`.

*Ремарка*: начиная с servlet API 3.0 файл web.xml стал необязательным, все сервлеты можно генерировать с помощью аннотаций прямо в классе сервлета.

## Описание сервлетов и их параметров

При описании сервлета задается имя сервлета, класс, в котором он описан и URL, по которому к нему будет осуществляться доступ
```xml
<servlet>
    <servlet-name>FirstServlet</servlet-name>
    <servlet-class>FirstServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>FirstServlet</servlet-name>
    <url-pattern>/hello-world</url-pattern>
</servlet-mapping>
```

Два обязательных элемента:
- `<servlet>` - связывает между собой имя существующего класса, описывающего сервлет, и имя сервлета, которое может быть любым.
- `<servlet-mapping>` - связывает между собой имя сервлета и URL, по которому будет осуществляться доступ к сервлету

Необязательные элементы:
- `<init-param>` - описывает параметры для каждого конкретного сервлета
```xml
<servlet>
    <servlet-name>...</servlet-name>
    <servlet-class>...</servlet-class>

    <init-param>
        <param-name>...</param-name>
        <param-value>...</param-value>
    </init-param>
</servlet>
```

Чтобы получить значения параметров в коде сервлета необходимо вызвать цепочку методов `getServletConfig().getInitParameter(String parameterName)`.

## Введение параметров контекста

Параметры контекста - это параметры, значения которых будут распространятся на все приложение. Параметры контекста описываются в дескрипторе развертывания с помощью тега `<context-param>`.
```xml
<context-param>
    <param-name>...</param-name>
    <param-value>...</param-value>
</context-param>
```

В коде сервлета можно получить значение параметра с помощью цепочки методов `getServletContext().getInitParameter(String name)`

## Описание слушателей контекста 

[Слушатели контекста](servlet_listeners.md) описываются, чтобы при старте веб-приложения поднять необходимые ресурсы и совершить необходимые действия.

Слушатели описываются в дескрипторе развертывания с помощью тега `<listener>`, внутри которого указывается класс, который унаследован от `ServletContextListener` и инициализирует веб-приложение.
```xml
<listener>
    <listener-class>...</listener-class>
<listener>
```

## Настройка сессий

Сессии настраиваются с помощью тега `<session-config>`
```xml
<session-config>
    <session-timeout>15</session-timeout>
</session-config>
```

Стандартная продолжительность сессии устанавливается в минутах.

## Обработка ошибок на страницах

В дескрипторе развертывания можно указать на какую страницу отправлять клиента с помощью тега `<error-page>` при возникновении ошибки, тип которой указывается в теге `<exception-type>`.

```xml
<error-page>
    <exception-type>java.lang.Throwable</exception-type>
    <location>/errorPage.jsp</location>
</error-page>
```

Либо можно указать тип ошибки HTTP-протокола с помощью тега `<error-code>`:
```xml
<error-page>
    <error-code>404</error-code>
    <location>/notFoundError.jsp</location>
</error-page>
```

---
## Инициализация веб-приложения

Для инициализации каких-либо ресурсов при старте приложения используется слушатель *ServletContextListener*, который следит за тем, как начинается развертывание приложения через дескриптор развертывания и может инициализировать базу данных и другие ресурсы до момента начала работы приложения.

### Класс ServletContextListener
Класс необходимо отнаследовать и реализовать его методы.

Объект класса создается самостоятельно контейнером сервлетов при развертывании приложения. Для этого необходимо сослаться на класс в дескрипторе развертывания.

Методы:
- `void contextInitialized(ServletContextEvent)` - метод переопределяется и в нем описываются действия, необходимые при запуске веб-приложения: открытие ресурсов и проч.
- `void contextDestroyed(ServletContextEvent)` - метод переопределяется и в нем описываются действия, необходимые при завершении веб-приложения: закрытие ресурсов и проч.

### Класс ServletContextEvent

Объект данного класса передается в методы класса ServletContextListener и содержат в себе информацию о дескрипторе развертывания

Методы:
- `ServletContext getServletContext()` - возвращает объект ServletContext, из которого можно вытащить информацию о параметрах, приведенных в дескрипторе развертывания.
```java
class MyServletContextListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        DataBaseHelper dataBase = new DataBaseHelper();
        ServletContext context = servletContextEvent.getServletContext();
        context.setAttribute("database", dataBase);
    }

    @Override
    public void contextDestroyed(ServletContextEvent servletContextEvent) {

    }
}
```

---
## К изучению
- [X] Глава 5 Head First Servlets and JSP