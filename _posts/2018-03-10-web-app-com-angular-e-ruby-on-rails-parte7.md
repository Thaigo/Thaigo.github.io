---
layout: post
title:  "Web Application com Angular 5 e Ruby on Rails - Parte 7"
date:   2018-03-10 16:40:00 -0300
tags: rest, api, rails, angular, web
categories: [rails, angular]
---

### Criando os Componentes

**1.** Essa página sera nossa pagina inicial, dentro do diretório `app`, vamos criar nosso componente home para a view.
 

 - Para criar nosso component com **AngularCLI** coloque o comando no terminal

```
ng g component home
``` 

 - Na view **home.component.html** vamos adicionar as linhas de codigo abaixo.

<iframe width="100%" height="150" src="//jsfiddle.net/z9tosrmq/2/embedded/html,result/" allowpaymentrequest allowfullscreen="allowfullscreen" frameborder="0"></iframe>


 - No nosso **home.component.ts** vamos adcionar

 ```ts
 import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit {

  private message:string = "Home Page";

  constructor() { }

  ngOnInit() {
  }
}
 ```
Veja a imagem abaixo como deve ficar os nossos objetos que acabamos de criar.

![Screenshot homepage](/static/img/app_angular_rails/homepage.png)

Essa expressão no `message` é conhecida como **Data Binding**, é dessa forma que o Angular faz para manter sincronizado os dados da View com o Component.

### Criando o Component de Cadastro e Edição

**2.** No diretório `app` vamos criar um sub diretório com o nome de product, e dentro do diretório product vamos criar o nosso component cadastro, para criar o component digite:

```
ng g component cadastro
```

 - Na view **cadastro.component.html** vamos adicionar as linhas de codigo abaixo.

 <iframe width="100%" height="500" src="//jsfiddle.net/g006a6fp/embedded/html,result/" allowpaymentrequest allowfullscreen="allowfullscreen" frameborder="0"></iframe>

 - No **cadastro.component.ts** vamos adcionar

 ```ts
import { Component, OnInit } from '@angular/core';

import {Router} from '@angular/router';
import { ActivatedRoute } from '@angular/router';

import { ProductService } from './../../services/product.service';
import { Product } from '../../services/product.model';


import { Observable } from 'rxjs/Observable';

@Component({
  selector: 'app-cadastro',
  templateUrl: './cadastro.component.html',
  styleUrls: ['./cadastro.component.css']
})
export class CadastroComponent implements OnInit {

  private title:string;
  private product:Product = new Product();

  constructor(private productService: ProductService,
              private router: Router,
              private activatedRoute: ActivatedRoute) { }

  ngOnInit() {
    this.activatedRoute.params.subscribe(parametro=>{
 
      if(parametro["id"] == undefined){

        this.title = "Novo Cadastro de Produto";
      }
      else{

        this.title = "Editar Cadastro de Produto";
        this.productService.getProduct(Number(parametro["id"])).subscribe(res => this.product = res);
      }


    });  
  }

   /*FUNÇÃO PARA SALVAR UM NOVO REGISTRO OU ALTERAÇÃO EM UM REGISTRO EXISTENTE */
   salvar(){
    var result;
 
    if (this.product.id){
      result = this.productService.updateProduct(this.product);
    } else {
      result = this.productService.addProduct(this.product);
    }
 
    result.subscribe(data => this.router.navigate(['/products']));
  }

}
```

### Criando o component de Consulta

**3.** No nosso diretório product vamos criar um component chamado consulta

```
ng g component consulta
```

 -  Na view **consulta.component.html** vamos adicionar as linhas de codigo abaixo.

 <iframe width="100%" height="500" src="//jsfiddle.net/zagw30b7/embedded/html/" allowpaymentrequest allowfullscreen="allowfullscreen" frameborder="0"></iframe>

 -  No **consulta.component.ts** vamos adicionar as linhas de codigo abaixo.

```ts
import { Component, OnInit } from '@angular/core';

import {Router} from '@angular/router';

import { ProductService } from './../../services/product.service';
import { Product } from '../../services/product.model';


@Component({
  selector: 'app-consulta',
  templateUrl: './consulta.component.html',
  styleUrls: ['./consulta.component.css']
})
export class ConsultaComponent implements OnInit {

  private products: Product[] = [];
  private title:string

  constructor(private productService: ProductService,
              private router: Router) { }

  ngOnInit() {
    this.title = "Registros Produtos";
 
      /*CHAMA O SERVIÇO E RETORNA TODAS AS PESSOAS CADASTRADAS */
    this.productService.getProducts().subscribe(res => this.products = res);
  }

  // Apaga o produto
  deleteProduct(products) {
    if (confirm("Você tem certeza que quer deletar a questions " + products.title)) {
      var index = this.products.indexOf(products);
      this.products.splice(index, 1);
 
      this.productService.deleteProduct(products.id)
        .subscribe(null);
    }
  }
}
```

### Criando o component Menu

**3.** No nosso diretorio `app` vamos criar mais um component chamado menu conforme abaixo:

```
ng g component menu
```

 - Entre no diretorio menu e no arquivo **menu.component.html** adicione as linhas abaixo

<iframe width="100%" height="300" src="//jsfiddle.net/jz4guxmg/embedded/html,result/" allowpaymentrequest allowfullscreen="allowfullscreen" frameborder="0"></iframe>

O **Data Binding** chamado `routerLink`, esse Data Binding vai ser responsável por chamar nossos Componentes atráves das rotas que vamos criar mais a frente.

 - Agora no **app.component.html** que fica no diretorio `app` adicione as linhas de codigo

 ```html
 <!--AQUI ESTA CHAMADO O MENU COMPONENET-->
 <app-menu></app-menu>

<div>  
  <br>
  <br>
  <!--AQUI VAI SER RENDERIZADO AS NOSSAS VIEWS/COMPONENTS-->
  <router-outlet></router-outlet>
</div>
 ```

### Criando as Rotas

**4.** Vamos criar as rotas que vamos usar na nossa aplicação, para isso no diretório `src` vamos criar um arquivo com o nome de **app.routes.ts**, depois vamos adcionar a linhas de codigo abaixo:

```ts
import { ModuleWithProviders }  from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
 
 
import { ConsultaComponent } from './app/product/consulta/consulta.component'; 
import { CadastroComponent } from './app/product/cadastro/cadastro.component'; 
import { HomeComponent } from './app/home/home.component';
 
const appRoutes: Routes = [
    { path: 'home',                    component: HomeComponent },
    { path: '',                        component: HomeComponent },
    { path: 'products',                component: ConsultaComponent },
    { path: 'products/new',            component: CadastroComponent },    
    { path: 'products/:id',            component: CadastroComponent }
 
];
 
export const routing: ModuleWithProviders = RouterModule.forRoot(appRoutes);
```

 - Agora vamos abrir o nosso arquivo **app.module.ts** e vamos deixar ele como mostra a imagem abaixo.

```ts
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { Http, HttpModule } from '@angular/http';
import { FormsModule } from '@angular/forms';

import { MaterializeModule } from 'angular2-materialize';

import { AppComponent } from './app.component';
import { HomeComponent } from './home/home.component';
import { ConsultaComponent } from './products/consulta/consulta.component';
import { CadastroComponent } from './products/cadastro/cadastro.component';
import { MenuComponent } from './menu/menu.component';

import { routing } from './../app.routes';

import { ConfigService } from './services/config.service';
import { ProductService } from './services/product.service';


@NgModule({
  declarations: [
    AppComponent,
    HomeComponent,
    CadastroComponent,
    ConsultaComponent,
    MenuComponent
  ],
  imports: [
    BrowserModule,
    HttpModule,
    FormsModule,
    MaterializeModule,
    routing
  ],
  providers: [ConfigService, ProductService],
  bootstrap: [AppComponent]
})
export class AppModule { }
```
 - A estrutura do nosso projeto entao fico assim

 ![Screenshot estrutura](/static/img/app_angular_rails/estrutura.png)

 - Agora já temos o nosso serviço REST e a nossa aplicação pronta, na próxima parte vamos realizar alguns testes para ver se está tudo funcionando, então até a próxima.
