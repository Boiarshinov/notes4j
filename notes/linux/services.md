---
title: "Linux services"
tags:
  - linux
draft: false
---

# Сервисы

__Сервис__ в Linux - это набор [процессов](./processes.md), выполняющих общую работу.
Например, в сервис [docker](../devops/docker.md) входит большое количество различных процессов: dockerd, docker-containerd, docker-proxy и другие.

Еще одно название сервисов, часто встречающееся в русскоязычной литературе - это __служба__.

Службы могут запускаться вместе с системой.
Для этого у служб имеется флаг `enabled`.

Управлением службами в большинстве дистрибутивов Linux занимается подсистема __systemd__.


## systemctl

`systemctl` - это командная утилита, позволяющая взаимодействовать с systemd.

Вывести список всех сервисов:
```bash
❯ systemctl list-units --type=service
UNIT                     LOAD   ACTIVE SUB     DESCRIPTION
bluetooth.service        loaded active running Bluetooth service
console-setup.service    loaded active exited  Set console font and keymap
cron.service             loaded active running Regular background program processing daemon
dbus.service             loaded active running D-Bus System Message Bus
docker.service           loaded active running Docker Application Container Engine
```

Много интересной информации можно получить о сервисе с помощью опции `status`.
Она выведет все процессы, запущенные в рамках сервиса, их PID, настройки службы и другое:
```bash
❯ systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
   Active: active (running) since Fri 2022-11-11 23:17:26 MSK; 1 years 6 months ago
     Docs: https://docs.docker.com
 Main PID: 501 (dockerd)
    Tasks: 82
   CGroup: /system.slice/docker.service
           ├─ 501 /usr/sbin/dockerd -H fd://
           ├─ 596 docker-containerd --config /var/run/docker/containerd/containerd.toml --log-level info
           ├─1248 /usr/sbin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 443 -container-ip 172.19.0.2 -container-port 443
           ├─1267 /usr/sbin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 80 -container-ip 172.19.0.2 -container-port 80
           ├─1336 docker-containerd-shim -namespace moby -workdir /var/lib/docker/containerd/daemon/io.containerd.runtime.v1.linux/moby/4da4e6a36c83c5f9187346
           ├─1338 docker-containerd-shim -namespace moby -workdir /var/lib/docker/containerd/daemon/io.containerd.runtime.v1.linux/moby/d67004cb0e8e7b0c3b71ad
           ├─1406 nginx: master process nginx -g daemon off;
           ├─1424 mysqld --transaction-isolation=READ-COMMITTED --binlog-format=ROW
           └─1814 nginx: worker process
```

`systemctl` позволяет управлять состоянием служб.
Для этого используются команды:
- `start` - запуск службы
- `stop` - остановка службы
- `restart` - перезапуск службы. Часто используется после изменения конфигурации сервиса, чтобы перезапустить сервис с новыми настройками

```bash
systemctl restart nginx
```


---
## Журналирование

События, происходящие со службами, логируются.
Но не стоит путать эти логи с логами самого сервиса.
Какой-нибудь nginx пишет свои логи в `/var/log` и эти логи не отражаются в systemd.
В systemd записываются логи, связанные с состоянием службы: происхождение какие-то событий, получение сигналов, невозможность получить доступ к файлу и так далее.

Для просмотра логов служб используется утилита `journalctl`.
```bash
sudo journalctl -f -u docker
```


---
## К изучению

- [ ] [Systemd](https://www.computernetworkingnotes.com/linux-tutorials/systemd-units-explained-with-types-and-states.html)
