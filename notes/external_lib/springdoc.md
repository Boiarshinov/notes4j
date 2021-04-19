---
title: "Springdoc"
tags:
  - spring
  - web
draft: false
---

**Springdoc** - это одна из [библиотек](../spring/openapi_integration.md), позволяющих генерировать OpenAPI описание REST сервиса по существующему коду spring-приложения. 

Библиотека берет на себя следующие задачи:
- создание OpenAPI документации на REST сервис по существующим эндпойнтам;
- чтение аннотаций из пакета `io.swagger` и слияние информации из них со спринговыми компонентами;
- создание Swagger UI, позволяющего в интерактивной форме взаимодействовать с существующими эндпойнтами;


Данная библиотека не является официальной частью [Spring Framework](../spring/spring.md) и не поддерживается компанией Pivotal.

Документация создается на основе Java-кода, однако ее можно дополнять с помощью специальных аннотаций.

Документация генерируется в несколько разных форматов:

- html - `<host>/swagger-ui.html`
- json - `<host>/v3/api-docs`
- yaml - `<host>/v3/api-docs.yaml`

Процесс генерации можно гибко конфигурировать с помощью настроек, указываемых в `application.properties` / `application.yml`.

Названия настроек, касающихся документации, начинаются с springdoc.

Общая конфигурация OpenApi производится в конфигурационном классе с помощью создания бина OpenApi.

```java
@Configuration
@OpenAPIDefinition
public class OpenApiConfig {

    @Bean
    public OpenAPI publicApi() {
        return new OpenAPI()
            .info(new Info()
                .title("Фронтальное решение кабинета агента")
                .version("0.0.1")
            );
    }
}
```

---
## К изучению

- [X] Официальный сайт библиотеки: [https://springdoc.org](https://springdoc.org/)/
- [X] Библиотека на github: https://github.com/springdoc/springdoc-openapi
- [X] Статья на Baeldung: https://www.baeldung.com/spring-rest-openapi-documentation
- [X] Видео от разработчиков: [https://www.youtube.com/watch?v=utRxyPfFlDw&feature=youtu.be&ab_channel=SpringI%2FO](https://www.youtube.com/watch?v=utRxyPfFlDw&feature=youtu.be&ab_channel=SpringI/O)