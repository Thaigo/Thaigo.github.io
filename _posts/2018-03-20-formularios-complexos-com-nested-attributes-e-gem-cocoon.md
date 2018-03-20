---
layout: post
title:  "Formulários complexos com Nested Attributes e Gem Cocoon"
date:   2018-03-20 18:15:00 -0300
tags: rails, form, cocoon, web, nested_attributes
categories: [rails]
---

Nesse Tutorial vou abordar a utlização dos [Form Helpers](http://guides.rubyonrails.org/form_helpers.html) em rails, Associaçoes [Has_many :through](http://guides.rubyonrails.org/association_basics.html#the-has-many-through-association) para relacionamento entre tabelas e para completar vamos utilizar a [Gem Cocoon](http://guides.rubyonrails.org/form_helpers.html) com [Active Record Nested Attributes](http://api.rubyonrails.org/classes/ActiveRecord/NestedAttributes/ClassMethods.html) para demonstrar cenarios onde possuimos Objetos Pai e Objetos Filhos.

## O que vamos construir?

Primeiro vamos Visualizar o nosso relacionamento de tabelas.

![Screenshot relationships](/static/img/post-cocoon/relationships.png)

Nos vamos contruir uma aplicação para cadastro e consultas de carros, abordarei alguns form helpers para contrução de views e o recurso chamado [Nested Attributes](http://api.rubyonrails.org/classes/ActiveRecord/NestedAttributes/ClassMethods.html) onde vamos aninhar um objeto dentro de outro objeto. Fico Curioso?

Veja o Video rápido de demonstração e se gostou vamos ao codigo.

<iframe width="100%" height="315" src="https://www.youtube.com/embed/WQK57dIxrcc" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>



 - ### Basic Setup

Criando nossa aplicação rails com Sqlite como banco de dados

 ```
 rails new formcar
 ```

 - ### Cocoon Setup

O Cocoon usa uma dependencia chamada [jQuery](https://github.com/rails/jquery-rails)  para instação do [jQuery](https://github.com/rails/jquery-rails)  existe duas opção apartir da versao 5 do rails via **Yarn** ou via **Gemfile**

Se deseja usar Yarn é necessario ja ter o Yarn instalado antes na maquina. Para instalar o **Yarn** [Clique aqui](https://yarnpkg.com/en/)

Instalando com **Yarn** use o comando no terminal:

```
yard add jquery
```

Instalando via **Gemfile** `jQuery` e `Cocoon` adicione no arquivo as linhas abaixo

```ruby
...
gem 'jquery-rails'
gem 'coccon'
...
```
Instale usando o comando.
```
bundle install
```

Agora em `app/assets/javascripts/application.js` adicione

```
//= require jquery
//= require cocoon
```

- ### Models and Relationships

Agora vamos usar a imagem que vimos acima para construir nossas tabelas no banco de dados

1. **Model Car**
```
rails g resource car date_car:date name new:boolean type_car:integer
```
2. **Model Variety**
```
rails g model variety name
```
3. **Model Row**
```
rails g model row variery:references
```
4. **Model Car_Row**
```
rails g model car_row row:references car:references
```

Rode as Migrations

```
rails db:migrate
```

Agora em `app/models/car.rb` e adicione as linha de codigo abaixo:

```ruby
class Car < ApplicationRecord
  has_many :car_rows, dependent: :destroy
  has_many :rows, through: :car_rows, dependent: :destroy

  enum type_car: { Sedan: 0, Hatch: 1 }

  accepts_nested_attributes_for :car_rows, reject_if: :all_blank, allow_destroy: true
end
```
Em `app/models/car_row.rb` e adicione as linha de codigo abaixo:

```ruby
class CarRow < ApplicationRecord
  belongs_to :row
  belongs_to :car

  accepts_nested_attributes_for :row, reject_if: :all_blank, allow_destroy: true
end
```
Em `app/models/row.rb` e adicione as linha de codigo abaixo:
```ruby
class Row < ApplicationRecord
  belongs_to :variety
  has_many :car_rows, dependent: :destroy
  has_many :cars, through: :car_rows, dependent: :destroy
end
```

- ### Controllers

Vamos criar a controller `app/controllers/cars_controller.rb` responsavel por controlar a lógica da aplicação e atua fazendo a ponte entre as **Views** e o **Models**

```ruby
class CarsController < ApplicationController

  def index
    @cars = Car.all     
  end

  def new
    @car = Car.new
  end

  def create
    @car = Car.new(car_params)
    if @car.save
      redirect_to root_url
    else
      render new
    end
  end

  def destroy
    @car = Car.find(params[:id])
    @car.destroy
    redirect_to root_url

  end

  private

  def car_params
    params.require(:car).permit(:name, :date_car, :new, :type_car, 
                   :car_rows_attributes => [:id, :car_id, :row_id, :_destroy, 
                   :row_attributes => [:id, :variety_id]])
  end
end
```

- ### Routes

Precisamos adicionar uma rota default em `config/routes.rb`
```ruby
Rails.application.routes.draw do
  resources :cars
  root "cars#index"
end
```

- ### Views 

Entre no diretorio **app/views/cars** e crie um arquivo chamado **`new.html.erb`** e adicione o codigo

<iframe src="https://pastebin.com/embed_iframe/1hFQBQnF" style="border:none;width:100%"></iframe>

Crie uma arquivo chamado **`_form.html.erb`**

<iframe src="https://pastebin.com/embed_iframe/WEM6ZnGL" style="border:none;width:100%"></iframe>

Crie um arquivo chamado **`_car_row_fields.html.erb`**

<iframe src="https://pastebin.com/embed_iframe/bvXDxtyy" style="border:none;width:100%"></iframe>

Crie um arquivo chamado **`index.html.erb`**

<iframe src="https://pastebin.com/embed_iframe/Fx1by2fq" style="border:none;width:100%"></iframe>

 - Altere o arquivo **app/assets/stylesheets/application.css** para **application.scss** e adicione o import

 ```scss
 @import "cars";
 ```

Em **app/assets/stylesheets/cars.scss** adicione 

```css
.style-9{
  max-width: 450px;
  background: #FAFAFA;
  padding: 30px;
  margin: 50px auto;
  box-shadow: 1px 1px 25px rgba(0, 0, 0, 0.35);
  border-radius: 10px;
  border: 6px solid #305A72;
}

.field {
  padding: 10px;
  margin: 5px auto;
}

.actions {
  margin: 5px auto;
}

table {
	font-family: verdana,arial,sans-serif;
	font-size:14px;
	color: #333333;
	border-width: 1px;
	border-color: #999999;
	border-collapse: collapse;
}
table th {
	background-color:#b3b3cc;
	border-width: 1px;
	padding: 8px;
	border-style: solid;
	border-color: #a9c6c9;
}
table tr {
  background-color:#f0f0f5;  
}

table td {
	border-width: 1px;
	padding: 8px;
	border-style: solid;
	border-color: #a9c6c9;
}
```

Agora vai em `app/views/layouts/application.html.erb` e adicione

```html
<body>
  <div class="style-9">   
    <%= yield %>  
  </div>  
</body>
```

- ### Final 

Agora levante o servidor e pronto!
```
rails s
```

Chegamos ao fim do nosso tutorial agora realize teste faça melhorias. E Até a proxima.



