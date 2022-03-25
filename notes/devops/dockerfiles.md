---
title: "Dockerfile"
tags:
  - devops
draft: false
---

# Dockerfile

Dockerfile - это текстовый файл со скриптом, который описывает инструкции, используемые для сборки [docker](docker.md)-образа.

Каждая строчка в Dockerfile - это отдельный слой.
Поэтому обычно стараются объединять консольные команды, которые связывает общая идея, в одну, чтобы они все были в одном слое.

- `FROM` - базовый образ, на основе которого будет происходить сборка. Чаще всего какой-то легковесный дистрибутив Linux.
- `ENV` - объявление переменных, которые могут быть переопределены при запуске контейнера.
- `COPY` - копирует файлы из директории проекта в образ.
- `RUN` - консольные команды, которые необходимо выполнить при сборке образа.
- `EXPOSE` - открывает порты
- `CMD` - консольные команды, которые будут выполнены при запуске контейнера
- `WORKDIR` - установка какой-либо директории внутри контейнера в качестве текущей. Аналогично `cd` в bash.
- `ENTRYPOINT` - устанавливает главную команду образа. Чаще всего используется для консольных утилит. Благодаря этому не нужно будет каждый раз писать название утилиты, пытаясь выполнить какую-либо ее команду внутри контейнера
- `VOLUME` - объявляет место хранения во внутренней файловой системе для данных, создаваемых приложением внутри контейнера. Эти данные будут на самом деле храниться во внешней файловой системе

Пример докерфайла утилиты **openapi-cli**:
```dockerfile
FROM node:alpine

WORKDIR /build

# Copy files required for npm install only
COPY package.json package-lock.json /build/
COPY packages/cli/package.json /build/packages/cli/
COPY packages/core/package.json   /build/packages/core/
COPY packages/cli/bin/ /build/packages/cli/bin/

RUN npm ci --no-optional --ignore-scripts

# Copy rest of the files
COPY . /build/
RUN npm run prepare

# Install openapi-cli globally, similar to npm install --global @redocly/openapi-cli
# but the local package is used here
RUN mv -- "$(npm pack packages/cli/)" redocly-openapi-cli.tgz && \
	  npm install --global redocly-openapi-cli.tgz

# npm pack in the previous RUN command does not include these assets
RUN cp packages/cli/src/commands/preview-docs/preview-server/default.hbs /usr/local/lib/node_modules/@redocly/openapi-cli/lib/commands/preview-docs/preview-server/default.hbs && \
	  cp packages/cli/src/commands/preview-docs/preview-server/hot.js /usr/local/lib/node_modules/@redocly/openapi-cli/lib/commands/preview-docs/preview-server/hot.js

# Clean up to reduce image size
RUN npm cache clean --force && rm -rf /build

WORKDIR /spec

ENTRYPOINT [ "openapi" ]
```


---
## К изучению

- [X] [Курс Docker Mastery](https://www.udemy.com/course/docker-mastery/) на Udemy в части касающейся
- [ ] [Официальная документация](https://docs.docker.com/)
