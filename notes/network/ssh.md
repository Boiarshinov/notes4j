---
title: "SSH"
tags:
  - network
  - security
draft: false
---

# SSH

__SSH__ - Secure SHell - протокол безопасного доступа к удаленным системам.

Обычно данный протокол используется для доступа к терминалу удаленного сервера с ОС Linux.

Для различных операционных систем существуют различные клиенты SSH:

- Linux
    - Terminal - в Linux SSH идет из коробки в терминале.
- Mac OS
    - Terminal - на маках SSH тоже идет из коробки (потому что Mac OS - это UNIX-подобная система)
    - iTerm2 - программка, расширяющая возможности терминала
- Windows
    - PuTTY - самый простой и популярный и бесплатный клиент
    - Xshell - более продвинутый, но платный для коммерческого использования клиент
    - OpenSSH - клиент, предустановленный в Windows 10

---
## SSH-ключи

Для доступа к удаленному серверу обычно используется SSH-ключ, который имеет открытую и закрытую часть:

- public key - хранится на сервере в открытом виде. Генерируется клиентом. Может быть разослан клиентом кому угодно.
- private key - хранится только на стороне клиента

*Шифрование* - шифрование с помощью публичного ключа, расшифровка с помощью приватного ключа. Т.е. прочитать сообщение может только владелец приватного ключа.

*Электронная подпись* - шифрование с помощью приватного ключа, расшифровка с помощью публичного ключа. Т.е. сообщение может прочитать кто угодно, владеющий публичным ключом, но при этом гарантируется, что сообщение отправлено конкретным пользователем.

---
## Вход на сервер

Для того чтобы войти удаленный сервер через SSH нужно выполнить в терминале команду: 
```
ssh <login>@<host:port>
```

При подключении сервер высылает слепок ключа (key fingerprint), чтобы клиент мог убедиться, что он подключается к правильному хосту.
При первом подключении пользователь должен проверить слепок самостоятельно.
Также сервер высылает список поддерживаемых [алгоритмов шифрования](../security/cipher.md) и [криптографических хэшей](../security/crypto_hash_function.md).
Клиентская часть приложения SSH сравнивает список алгоритмов со своими и выбирает те, которые будут использоваться для общения.
После этого клиент и сервер с помощью [протокола Диффи-Хеллмана](../security/diffie_hellman.md) создают симметричный ключ шифрования, который будет использоваться для данной сессии обмена данными.

Существует два способа аутентификации:
- пароль
- пара приватный-публичный ключ

При аутентификации по паролю пользователя попросят ввести пароль в терминал.

Для аутентификации по паре ключей нужно сначала их сгенерировать с помощью специальной утилиты.

---
## Генерация ключей SSH

Для генерации ключей можно использовать клиенты вроде PuTTY, либо можно сгенерировать в терминале:
```shell
ssh-keygen
```

Кейген предложит задать пароль для ключа, но это необязательно (хотя крайне желательно). Ключи будут находиться в папке /home/username/.ssh

- id_rsa - приватный ключ
- id_rsa.pub - публичный ключ

Публичный ключ можно открывать любым текстовым редактором и отправлять текст (который и является ключом) куда угодно.


---
## К изучению
- [X] Видео-лекция от Хекслет: https://www.youtube.com/watch?v=sbVYRf_6Hvg
- [X] [Вики](https://ru.wikipedia.org/wiki/SSH)