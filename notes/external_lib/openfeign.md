---
title: "OpenFeign"
tags:
  - web
  - external_lib
draft: false
---

# OpenFeign

OpenFeign, он же просто __Feign__ - это Java-библиотека, упрощающая написание [HTTP](../network/http.md)-клиентов.
Она предоставляет декларативный API, основанный на аннотациях и интерфейсах.

Пример описания клиента:
```java
public interface FeignHttpClient {

    @RequestLine("GET /entity/{id}")
    EntityResponse getEntity(@Param("id") int id, @QueryMap Map<String, String> queryParams);

    record EntityResponse(
        String stringValue,
        int intValue,
        boolean booleanValue
    ) {}
}
```

Затем где-нибудь в конфигурации DI надо создать объект клиента:
```java
FeignHttpClient client = Feign.builder()
            // Здесь можно сконфигурировать кучу всего: обработку ошибок, энкодеры, логирование, ретраи и пр.
            .decoder(new JacksonDecoder())
            .target(FeignHttpClient.class, "http://localhost:8080");
```

Feign поддерживает множество Http клиентов:
- Apache HTTP Client
- Ribbon
- Java 11 HTTP Client
- OkHttp

Для их использования нужно подключить отдельные зависимости с адаптерами. 
По умолчанию используется самая примитивная реализация, основанная на классах из пакетов `java.net`.

Работать с [JSON](../formats/json.md) или [XML](../formats/xml.md) сам Feign не умеет, но он позволяет подключить большинство популярных инструментов: [Jackson](./jackson.md), Gson, [JAXB](./jaxb.md) и другие.

Для подключения Feign нужно импортировать core-библиотеку и все используемые адаптеры:
```kotlin
dependencies {
    implementation("io.github.openfeign:feign-core:12.3")
    implementation("io.github.openfeign:feign-okhttp:12.3")
    implementation("io.github.openfeign:feign-jackson:12.3")
    //Чтобы поддерживать все версии консистентными есть еще BOM-ник

    /* ... */
}
```


---
## Интеграция со Spring

todo


---
## К изучению
- [ ] [Документация Feign](https://github.com/OpenFeign/feign)
- [ ] [Документация на Spring Cloud OpenFeign](https://docs.spring.io/spring-cloud-openfeign/docs/current/reference/html/)