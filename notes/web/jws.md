---
title: "JWS"
tags:
  - web
  - security
draft: false
---

# JWS - JSON web signature

**JWS** - JSON Web Signature - стандарт на цифровую подпись для данных, представленных в формате [JSON](../formats/json.md).
Стандарт JWS входит в комплект стандартов JOSE.

Существует несколько видов JWS:
- компактный вид
- полный JSON вид
- сокращенный JSON вид

Исходные данные в JWS всегда кодируются в [base64url](../formats/base64.md).
Причины в стандарте не объяснены, но скорее всего это делается для того, чтобы ни у кого не было соблазна отформатировать JSON после того, как он был подписан.
Хотя в итоге это приводит к увеличению трафика, т.к. каждый символ base64 кодирует только 6 бит информации.

### Компактный вид
```
<header>.<payload>.<signature>
```

- `header` - метаданные о подписи, закодированные в base64url
- `payload` - исходные данные, закодированные в base64url
- `signature` - подпись, закодированная в base64url

В хэдере обычно представлена информация о том какой алгоритм шифрования был использован, и/или идентификаторе ключа шифрования.

В таком виде JWS чаще всего используется в JWT для серверов аутентификации.

### Полный JSON вид
Используется в тех случаях, когда данные подписываются несколькими ключами (`kid` - это id ключа)
```json
{
    "payload": "<base64 encoded payload>",
    "signatures": [
        {
            "protected": "<not mandatory base64 encoded protected header>",
            "header": {
                "kid": "1"
            },
            "signature": "<base64 encoded signature>"
        },
        {
            "protected": "<not mandatory base64 encoded protected header>",
            "header": {
                "kid": "2"
            },
            "signature": "<base64 encoded signature>"
        }
    ]
}
```

- `payload` - исходные данные, закодированные в base64url
- `protected` - метаданные о подписи, закодированные в base64url, если они есть
- `header` - метаданные о подписи в формате JSON, если они есть
- `signature` - подпись, закодированная в base64url

### Сокращенный JSON вид
```json
{
    "payload": "<base64 encoded payload>",
    "protected": "<not mandatory base64 encoded protected header>",
    "header": {
        "alg": "<signature algorithm>"
    },
    "signature": "<base64 encoded signature>"
}
```



---
## К изучению

- [X] Стандарт [RFC-7515](https://datatracker.ietf.org/doc/html/rfc7515) на JWS