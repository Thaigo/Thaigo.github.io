---
layout: post
title:  "Web Application com Angular 5 e Ruby on Rails - Parte 4"
date:   2018-03-09 01:15:00 -0300
tags: rest, api, rails, angular, web
categories: [rails, angular]
---

## Criando o projeto com Angular CLI

 O Angular CLI facilita a criação de uma aplicação, Ele gera componentes, rotas, serviços e etc. Voce ja deve ter o Node e NPM instalado. depois que tivermos realizado a instalação do Node e do NPM devemos executar o comando abaixo para instalar o Angular CLI.

```
npm install -g @angular/cli
```

Depois do Angular CLI instalado vamos agora criar um novo projeto, para isso basta executar o comando abaixo.

```
ng new app-frontend
```

Com o projeto criado vamos executar o comando abaixo para entrar no diretório da nossa aplicação.

```
cd app-frontend
```

E para executar nossa aplicação vamos usa o comando

```
ng serve
```
![Screenshot ng_serve](/static/img/app_angular_rails/ng_serve.png)

Agora abra o navegador na e digite a url http://localhost:4200 e se você teve o mesmo resultado significa que seu projeto foi criado com sucesso.

![Screenshot welcome](/static/img/app_angular_rails/welcome.png)

Bom, nessa parte do nosso tutorial aprendemos a criar um projeto com Angular CLI, na próxima parte do nosso tutorial vamos criar os objetos necessários para acessar o nosso serviço REST, então até a próxima.


