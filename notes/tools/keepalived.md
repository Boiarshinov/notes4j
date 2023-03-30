---
title: "keepalived"
tags:
  - tool
  - high_available
draft: false
---

# Keepalived

**Keepalived** - это демон Linux, который предназначен для высокой доступности серверов и для [балансировки нагрузки](../architecture/load_balancer.md).

Keepalived использует протокол **VRRP**.
Данный протокол позволяет создавать **виртуальный** (**плавающий**) IP адрес (VIP), который будет слушать несколько реальных хостов.
Из группы хостов только один является *мастером* и обрабатывает все запросы.
Остальные хосты, слушающие VIP, называются бэкап-роутер.
Если мастер выходит из строя, то все запросы начинает обрабатывать бэкап-роутер с наивысшим приоритетом.

Keepalived должен быть установлен на всех серверах, которые 

### Установка
```shell
apt-get install keepalived
```

Keepalived требует некоторых дополнительных утилит.


### Конфигурация

Конфигурация на keepalived описывается в файле `/etc/keepalived/keepalived.conf`

```vrrp
vrrp_instance VI_1 {        # Идентификатор виртуальной сети
    state MASTER            # MASTER или BACKUP
    interface eth0          # Имя интерфейса. [чтобы увидеть имя интерфейса: $ ip a ]
    virtual_router_id 51
    priority 101            # Чем больше число, тем выше приоритет. У мастера число должно быть меньше, чем у бэкапов
    advert_int 1
    unicast_src_ip 192.168.0.100 # IP хоста, на котором поднят keepalived
    unicast_peer {
        192.168.0.101       # IP хоста бэкап ноды
    }
    authentication {
        auth_type PASS
        auth_pass 1111      # Не больше 8 символов
    }
    virtual_ipaddress {
        192.168.0.50        # виртуальный ip-адрес.
    }
}
```




---
## К изучению

- [ ] [Документация на keepalived](https://keepalived.readthedocs.io/en/latest/introduction.html)
- [ ] [RFC-2338 на VRRP](https://datatracker.ietf.org/doc/html/rfc2338)
