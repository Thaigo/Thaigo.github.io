---
layout: post
title:  "Web Application com Angular 5 e Ruby on Rails - Parte 3"
date:   2018-03-06 18:10:00 -0300
tags: rest, api, rails, angular, web
categories: [rails, angular]
---

## Rest API

Vamos construir nossa rest api usando o helper scaffold do rails que nos ajuda a criar 
model, controller e actions que precisamos.

para usar o scaffold use o comando no terminal

```
rails g scaffold product name active:boolean
```


Depois de gerar a scaffold ele gera uma migrate em `db/migrate` ela é resposavel por criar as tabelas no banco dados

```ruby
class CreateProducts < ActiveRecord::Migration[5.1]
  def change
    create_table :products do |t|
      t.string :name
      t.boolean :active

      t.timestamps
    end
  end
end
```

Para gerar a migrate para o banco use o comando

```
rails db:migrate
```
![Screenshot db:migrate](/static/img/app_angular_rails/rails_dbmigrate.png)

Agora com o banco de dados gerado podemos ver em `app/controller/products_controller.rb` todas as actions do **CRUD** ja pronto e criado respondendo em json.

```ruby
class ProductsController < ApplicationController
  before_action :set_product, only: [:show, :update, :destroy]

  # GET /products
  def index
    @products = Product.all

    render json: @products
  end

  # GET /products/1
  def show
    render json: @product
  end

  # POST /products
  def create
    @product = Product.new(product_params)

    if @product.save
      render json: @product, status: :created, location: @product
    else
      render json: @product.errors, status: :unprocessable_entity
    end
  end

  # PATCH/PUT /products/1
  def update
    if @product.update(product_params)
      render json: @product
    else
      render json: @product.errors, status: :unprocessable_entity
    end
  end

  # DELETE /products/1
  def destroy
    @product.destroy
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_product
      @product = Product.find(params[:id])
    end

    # Only allow a trusted parameter "white list" through.
    def product_params
      params.require(:product).permit(:name, :active)
    end
end
```

## Testando o nosso serviço Rest


 - Salvando um novo registro

Para testar o nosso serviço vamos usar o aplicativo que se chama [Postman](https://www.getpostman.com/apps)
suba o projeto rails no terminal

```
rails server
```

vamos aos testes, com [Postman](https://www.getpostman.com/apps) aberto vamos adicionar a URL http://localhost:3000/products e vamos informar o valor de request abaixo.

```json
{
  "name": "Produto 1"
}
```

Depois vamos clicar no botão **POST** e vamos ter o resultado como mostra a imagem abaixo, quando esse método é executado estamos chamando a operação salvar do nosso serviço **REST**, cadastre mais alguns registros antes de ir para o próximo passo.

![Screenshot salvando](/static/img/app_angular_rails/salvando.png)

 - Atualizando um registro

 Agora vamos informar a request abaixo e depois vamos clicar em **PUT** para editar um registro, altere a URL para http://localhost:3000/products/1. ou :id que esta na casdastrado no banco de dados

 ```json
{  
  "name": "Produto B",
  "active": true
}
```

![Screenshot atualizando](/static/img/app_angular_rails/atualizando.png)

 - Excluindo um registro

 Para excluir um registro vamos passar na URL http://localhost:3000/products/2 a chave do registro 2 que vai ser excluido, depois vamos selecionar a opção **DELETE** e clicar em **SUBMIT**, e então vamos ter o resultado como mostra a imagem abaixo.

![Screenshot excluindo](/static/img/app_angular_rails/excluindo.png)

 - Selecionado um registro

Para selecionar um registro vamos passar o código na URL http://localhost:3000/products/3 Seleciona o Metodo **GET** e clicar em **SEND** como mostra a imagem abaixo.

![Screenshot seleciona1](/static/img/app_angular_rails/Seleciona1.png)

- Selecionando todos o registro

Selecionando todos os registros vamos colocar a URL http://localhost:3000/products Seleciona o Metodo **GET** e clicar em **SEND** como mostra a imagem abaixo.

![Screenshot selecionaTodos](/static/img/app_angular_rails/SelecionaTodos.png)

Enfim encerramos nossa parte do backend testando todo o nosso CRUD, na proxima parte do tutorial  vamos começar a criar nossa app-frontend com Angular 5 até a proxima.
