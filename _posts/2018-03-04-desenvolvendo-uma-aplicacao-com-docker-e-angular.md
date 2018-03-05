---
layout: post
title:  "Desenvolvendo uma Aplicação com Docker e Angular"
date:   2018-03-04 00:50:00 -0300
categories: [docker, angular]
---

**1.** Primeiro rode o comando no prompt de sua preferencia para instalçao do angular CLI

```
sudo npm install -g @angular/cli --allow-root
```

**2.** Para gerar o projeto rode:

```
ng new myapp
```

**3.** Dentro do projeto crie um novo arquivo chamado **Dockerfile** e coloque nele:

```
FROM node:9.2.0-slim
ENV INSTALL_PATH /myapp
RUN npm install -g @angular/cli
RUN mkdir -p $INSTALL_PATH
WORKDIR $INSTALL_PATH
COPY . .
RUN npm install
CMD ["npm", "start"]
```

**4.** Dentro do projeto crie um novo arquivo chamado **docker-compose.yml** e coloque nele:

```
version: '2'
services:
  website:
    build: .
    ports:
      - '4200:4200'
    volumes:
      - '.:/myapp'
```

**5.** No **package.json** em `scripts` start altere “ng serve” por:

```
ng serve -H 0.0.0.0
```

**6.** Rode no console

```
docker-compose build
```

**7.** Rode no console para subir a aplicação

```
docker-compose up
```

**8.** Para ver o projeto no brower visite **http://localhost:4200**