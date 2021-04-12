---
title: "Получение значений из properties файлов в Spring"
tags:
  - spring
draft: false
---

Некоторые параметры бинов можно определить в [properties-файле](../java/properties.md).

Для того чтобы считать данные из файла, необходимо указать Spring на properties-файл одним из способов:

- прописать в applicationContext.xml
```xml
<context:property-placeholder location="fileName.properties"/>
```
- поставить над классом аннотацию `@PropertySource` (обычно над классом - точкой входа в приложения, либо над конфигурационным классом)
```java
@PropertySource("classpath:customConfig.properties")
public class AppConfig { /* */ }
```
- создать бин класса `ResourceBundleMessageSource` и указать для него properties-файл, из которого будет производиться чтение
```xml
<bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource" >
    <property name="basenames" value="messages" /> <!-- value - это имя properties-файла -->
</bean>
```
Этот подход обычно используется для считывания из properties-файлов сообщений ошибок валидации.

При этом файл обычно располагается в директории `/resources`.

Доставать значения из файла можно с помощью выражений, похожих на [Expression Language](../jsp/expression_language.md), которые называются SpEL и выглядят так: `${keyName}`.

Использовать SpEL можно как в xml-файле, так и в Java-коде в аннотации `@Value`:
```java
@Value("${roles.Moderator}")
private String moderatorRole;
```