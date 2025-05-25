---
title: "LDAP"
tags:
  - directory
draft: True
---

# LDAP

__LDAP__ - Lightweight Directory Access Protocol - легковесный протокол, построенный на [TCP/IP](./tcp_ip.md), предназначенный для аутентификации, добавления, изменения, удаления и поиска записей.
Чаще всего записями являются пользователи какой-либо корпоративной системы.
LDAP фокусируется на чтении и поиске данных о пользователях и спроектирован для удобства совершения данных операций.
Протокол описан в RFC-4511.

С помощью протокола можно аутентифицировать пользователя какой-либо системы и проверить авторизацию его действий.
Например, вы разрабатываете внутри-корпоративную систему, позволяющую управлять бронированием мест в офисе.
Вы интегрируете свою систему с LDAP-сервером, для аутентификации пользователей.
Также она позволяет вам проводить поиск других пользователей (например, если вы решили забронировать место для своего коллеги).

LDAP хорошо интегрируется с AD - службой каталогов от Microsoft.

Сам LDAP является всего лишь протоколом, но у него есть популярные серверные реализации:
- __OpenLDAP__ - самая популярная открытая реализация
- 389 Directory Server
- ApacheDS

Если требуется поработать с LDAP локально, то может быть полезно приложение Apache Directory Studio.

Структура LDAP является иерархической.
На вершине находится элемент `ROOT`, а под ним элементы:
- `dc` - domain component - чаще всего доменное имя компании, например `dc=google,dc=com`
- `ou` - organization unit - название подразделения в компании
- `cn` - common name - название элемента
- `dn` - distinguished name - уникальное имя записи в каталоге LDAP. Представляет собой список из разделенных запятыми относительно уникальных имен. Например: `uid=vova,ou=bank,dc=posad,dc=ru`

Запись может содержать атрибуты, которые объявлены в классах, к которым она принадлежит.
Классы обозначаются как `objectclass`, а описание их аттрибутов приводится в схемах.
В схеме описывается обязательность аттрибутов.

Данные, хранящиеся на LDAP-сервере, могут быть представлены в формате `.ldif`
```ldif
# Почему-то все записи имеют objectclass: top, видимо так положено

dn: ou=groups,dc=company,dc=org
objectclass: top
objectclass: organizationalUnit
ou: groups

dn: ou=people,dc=company,dc=org
objectclass: top
objectclass: organizationalUnit
ou: people

dn: uid=admin,ou=people,dc=company,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Rod Johnson
sn: Johnson
uid: admin
userPassword: password

dn: uid=user,ou=people,dc=company,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Dianne Emu
sn: Emu
uid: user
userPassword: password

dn: cn=user,ou=groups,dc=company,dc=org
objectclass: top
objectclass: groupOfNames
cn: user
uniqueMember: uid=admin,ou=people,dc=company,dc=org
uniqueMember: uid=user,ou=people,dc=company,dc=org

dn: cn=admin,ou=groups,dc=company,dc=org
objectclass: top
objectclass: groupOfNames
cn: admin
uniqueMember: uid=admin,ou=people,dc=company,dc=org
```


---
## К изучению
- [ ] Стандарт на LDAP: [RFC-4511](https://datatracker.ietf.org/doc/html/rfc4511)
- [ ] [LDAP для ракетостроителей](https://pro-ldap.ru/tr/zytrax/) - перевод бесплатной книги про LDAP