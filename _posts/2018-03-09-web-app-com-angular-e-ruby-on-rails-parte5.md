---
layout: post
title:  "Web Application com Angular 5 e Ruby on Rails - Parte 5"
date:   2018-03-09 02:40:00 -0300
tags: rest, api, rails, angular, web
categories: [rails, angular]
---

## Instalando Angular Materialize CSS

 - ### [Materialize Documentação](http://materializecss.com/)

A sua documentação é vasta e de fácil entendimento, com uma curva de aprendizagem bem pequena. Há no site oficial do Materialize vários exemplos de como aplicar o Materialize.

Lá, você também aprende como aplicar cada um dos componentes em seu projeto, facilitando bastante a sua vida como programador.

**1.** Para instalar MaterializeCSS e angular2-materialize atraves do NPM rode o comando abaixo no terminal

```
npm install materialize-css --save
npm install angular2-materialize --save
```

jQuery 2.2 and Hammer.JS também sao requeridos

```
npm install jquery@^2.2.4 --save
npm install hammerjs --save
```

**2.** Depois de instalado é necessario editar o arquivo **angular-cli.json** que esta na raiz do projeto e adicione no `styles`

```
"../node_modules/materialize-css/dist/css/materialize.css"
```

![Screenshot style](/static/img/app_angular_rails/style.png)

**3.** Agora adicione dentro do array `scripts` as seguintes linhas de codigo

```
"../node_modules/jquery/dist/jquery.js",
"../node_modules/hammerjs/hammer.js",
"../node_modules/materialize-css/dist/js/materialize.js"
```

![Screenshot scripts](/static/img/app_angular_rails/scripts.png)


**4.** Agora vamos na pasta **src/app/app.module.ts** e vamos importar `angular2-materialize`.

![Screenshot import_materialize](/static/img/app_angular_rails/import_materialize.png)

**4.** Por fim adicione essa linha no header do **index.html**

```html
<link href="http://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
```

![Screenshot index](/static/img/app_angular_rails/index.png)

Nesse tutorial aprendemos a instalar o Materialize CSS no angular e no proximo tutorial vamos criar os objetos necessários para acessar o nosso serviço REST. Obrigado e até a proxima.

