---
title: "Цифровые сертификаты"
tags:
  - security
draft: false
---

# Цифровые сертификаты

Когда два актора в интернете хотят обменяться чувствительной информацией, им необходимо передать друг другу свои публичные ключи для [шифрования](cipher.md).
Такая передача по незащищенному каналу подвержена атаке __man in the middle__.
Можно, конечно, отправить доверенное лицо (а лучше несколько), которые передадут публичный ключ.
Такие доверенные лица называются _офицеры безопасности_.
Но в условиях дикого интернета такое невозможно.
Поэтому были придуманы __центры сертификации__.

Центр сертификации выдает определенному домену __цифровой сертификат__, подтверждающий, что этот домен владеет конкретным публичным ключом.
Сертификаты действительны выдаются на конечный срок, т.к. владелец домена может поменяться.
Цифровой сертификат подписывается сертификационным центром. 
Публичные ключи главных сертификационных центров широко известны, многие из них даже встроены в операционные системы.

Так как большое количество сайтов желают завести свой сертификат, то нагрузка на сертификационные центры получается очень высокой. 
Поэтому сертификаты могут выстраивать цепочки: центр сертификации подписывает ключ Google, а Google - ключи своих проектов.
Сертификат, которые стоит в начале цепочки называется _корневым_.

В цифровых сертификатах записано много различной метаинформации. 
Что конкретно туда записывается определяется стандартом `X.509`.
Сертификаты представляют собой файлы с расширением: `.cer`, `.pem`, `.p7b` или др.

### Самоподписанные сертификаты

Если сертификат подписывается самим владельцем публичного ключа, то такой сертификат называется самоподписанным.
Все корневые сертификаты являются самоподписанными.

Самоподписанные сертификаты широко используются внутри одной компании и между компаниями, которые могут обменяться между собой сертификатами с помощью офицеров безопасности.


## Существующие центры сертификации
Самыми известными центрами сертификации являются:
- VeriSign - одна из первых компаний
- DigiCert - выдал сертификаты Wikipedia, Twitter и др.
- [Lets Encrypt](https://letsencrypt.org/) - некоммерческий центр сертификации, который существенно упростил переход с [http](../network/http.md) на https. Почти весь интернет использует их сертификаты


---
## К изучению
- [X] [Wiki](https://en.wikipedia.org/wiki/Public_key_certificate)
- [X] [X.509 Wiki](https://ru.wikipedia.org/wiki/X.509)