---
layout: post
title:  "Web Application com Angular 5 e Ruby on Rails - Parte 6"
date:   2018-03-10 02:20:00 -0300
tags: rest, api, rails, angular, web
categories: [rails, angular]
---

## Serviço REST com Angular.

 - Vamos deixar pronto os objetos necessários para acessarmos o nosso serviço REST que criamos em um passo anterior no nosso app-backend, dentro diretório `src/app` vamos criar um outro diretório com o nome de **services**

```
mkdir services
```

 - Agora dentro do diretorio `services` vamos criar um arquivo chamado **product.model.ts** e adicionar a linha de codigo abaixo

```ts
export class Product {
  id:number
  name:string
  active:boolean
}
```

![Screenshot product_model](/static/img/app_angular_rails/product_model.png)

 - No diretório services vamos adicionar um arquivo com o nome de **config.service.ts**, e nesse arquivo vamos adicionar a linha de codigo abaixo, essa classe vai ter apenas a URL do nosso serviço.

```ts
export class ConfigService {
    private urlService: string;

    constructor() {
        this.urlService = 'http://localhost:3000/products';
    }

    getUrlService(): string {
        return this.urlService;
    }
}
```

 - Ainda dentro da pasta `services` agora vamos criar a classe responsável por realizar as requests no nosso serviço REST, Vamos adicionar um arquivo com o nome de product.service.ts, e depois vamos deixar nosso arquivo com as linhas de codigo abaixo:

Criando o service via AngularCLI no terminal

```
ng g service product
```

Entre no arquivo e adcione.

```ts
import { Injectable } from '@angular/core';
import { Http } from '@angular/http';
import { Headers } from '@angular/http';
import { RequestOptions } from '@angular/http';

import 'rxjs/add/operator/map';
import 'rxjs/add/operator/do';
import 'rxjs/add/operator/catch';
import { Observable } from 'rxjs/Rx';

import { Product } from '../services/product.model';
import { ConfigService } from './config.service';

@Injectable()
export class ProductService {

  private baseUrlService: string = '';
  private headers: Headers;
  private options: RequestOptions;

  constructor(private http: Http,
    private configService: ConfigService) {

    //SETANDO A URL DO SERVIÇO REST QUE VAI SER ACESSADO 
    this.baseUrlService = configService.getUrlService() + '/';

    //ADICIONANDO O JSON NO HEADER 
    this.headers = new Headers({ 'Content-Type': 'application/json;charset=UTF-8' });
    this.options = new RequestOptions({ headers: this.headers });
  }

  //CONSULTA TODAS AS Products CADASTRADAS
  getProducts() {
    return this.http.get(this.baseUrlService).map(res => res.json());
  }

  //ADICIONA UMA NOVA Product
  addProduct(product: Product) {

    return this.http.post(this.baseUrlService, JSON.stringify(product), this.options)
      .map(res => res.json());
  }
  //EXCLUI UMA Product
  deleteProduct(id: number) {

    return this.http.delete(this.baseUrlService + id).map(res => res.json());
  }

  //CONSULTA UMA Product PELO CÓDIGO
  getProduct(id: number) {

    return this.http.get(this.baseUrlService + id).map(res => res.json());
  }

  //ATUALIZA INFORMAÇÕES DA Product
  updateProduct(product: Product) {

    return this.http.put(this.baseUrlService + '/' + product.id, { 'product': product })
      .map(res => res.json());
  }

}
```

 - Agora vamos importar o nosso serviço a propriedade providers do `NgModule` que fica em **app.module.ts**, assim estamos informando quem são os nosso provedores de serviços, também precisamos adicionar as dependêcias que estamos usando nas nossas classes provedoras, em **app.module.ts** adicione as linha codigo abaixo:

```ts
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { Http, HttpModule } from '@angular/http';
import { MaterializeModule } from 'angular2-materialize';

import { AppComponent } from './app.component';

import { ConfigService } from './services/config.service';
import { ProductService } from './services/product.service';


@NgModule({
  declarations: [
    AppComponent    
  ],
  imports: [
    BrowserModule,
    HttpModule,    
    MaterializeModule
    
  ],
  providers: [ConfigService, ProductService],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

![Screenshot overview](/static/img/app_angular_rails/overview.png)

 - Pronto, nossa classe de acesso ao serviço esta pronta, na próxima parte vamos criar os nossos componentes com as views para a realização do CRUD, então até a próxima.

