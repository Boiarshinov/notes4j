---
title: "Шаблоны AntPathMatcher"
tags:
  - spring
draft: false
---

Шаблоны AntPathMatcher очень похожи на [регулярные выражения](../java/regexp.md), но имеют другую область применения.

Они предназначены для описания шаблонов путей. Это может использоваться при описании директорий или URL. В Spring Framework они используются для описания путей в Spring Security.

Spring позаимствовал эту идею у сборщика Ant.

### Синтаксис

- `?` - один любой символ
- `*` - ноль или больше любых символов, описывающих одну директорию
- `**` - ноль или больше любых директорий в пути
- `/` - разделитель между директориями и поддиректориями / файлами или между частями URL.

Примеры использования смотри в Javadoc'е.

---
## К изучению
- [ ] Javadoc на класс `AntPathMatcher`: https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/util/AntPathMatcher.html