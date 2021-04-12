---
title: "Spring OpenApi документация"
tags:
  - spring
  - web
draft: false
---

OpenAPI - спецификация, устанавливающая способ описания REST API

Swagger - туллинг вокруг OpenAPI, генерирующий html-странички, позволяющие изучать REST API в интерактивной форме.

Для генерации openAPI спецификации по существующим Spring контроллерам и создания Swagger UI есть специальные библиотеки:

- Springfox - весьма популярная в прошлом библиотека. Перестала поддерживаться и не работает с новыми версиями Spring Boot.
- Springdoc - набирающая популярность библиотека

---
## Springdoc

<mark>Вынести в отдельную заметку</mark>

Данная библиотека не является официальной частью Spring Framework и не поддерживается компанией Pivotal.

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
- [X] Разница между OpenAPI и Swagger: https://swagger.io/blog/api-strategy/difference-between-swagger-and-openapi/