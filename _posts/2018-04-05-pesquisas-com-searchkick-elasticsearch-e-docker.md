---
layout: post
title:  "Pesquisas com Searchkick, Elasticsearch e Docker"
date:   2018-04-05 04:25:00 -0300
tags: rails, searchkick, elasticsearch, docker, web
categories: [rails]
---

Nesse tutorial vou mostrar como realizar busca de dados utilizando [Searchkick](https://github.com/https://github.com/ankane/searchkicknsarno/knock) e [Elasticsearch](https://www.elastic.co/) com [Docker](https://www.docker.com/) em Ruby on Rails


## O que vamos construir?

Nos vamos construir uma aplicação simples onde teremos uma lista com varios livros onde sera possivel as pesquisar pelo seu titulo.

Veja uma rapida demonstração da aplicação.

<iframe width="100%" height="315" src="https://www.youtube.com/embed/cqMUWB9OPHE" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>


 - ## Setup Inicial com Docker

 Já existe um poste sobre **Docker** nesse blog para saber mais [Clique aqui](https://thaigo.github.io/ruby/docker/desenvolvendo-uma-aplicacao-com-docker-e-rails)

 Altere somente o `docker-compose.yml` para usar o serviço do **ElasticSearch**

 ```ruby
 version: '3'

services:
  postgres:
    image: 'postgres:9.5'
    volumes:
      - 'postgres:/var/lib/postgresql/data'
  
  website:
    depends_on:
      - 'postgres'  
      - 'elasticsearch'    
    build: .
    command: bash start.sh
    ports:
      - '3000:3000'
    volumes:
      - '.:/myapp'
    volumes_from:
    - box
    environment:
      ELASTICSEARCH_URL: elasticsearch:9200
  
  elasticsearch:
    image: "elasticsearch:5"
    ports:
      - "9200:9200"
    volumes:
      - elastic:/usr/share/elasticsearch/data
    environment:
      - Des.network.host=0.0.0.0

  box:
    image: busybox
    volumes:
      - /box

volumes:  
  postgres:
  box:
  elastic:
```

Rode o build

```
docker-compose build
```

No Gemfile adicione as gems

```ruby
...

gem 'searchkick'


group :development, :test do
  # Call 'byebug' anywhere in the code to stop execution and get a debugger console
  gem 'byebug', platform: :mri
  gem 'faker'
end

...

```
Rode um bundle install

```
docker-compose run --rm website bundle install
```

Crie o Banco de dados
```
docker-compose run --rm website bundle exec rake db:create
```

 - ## Model

Vamos criar a model book e popular o banco de dados com o faker

```
docker-compose run --rm website bundle exec rails generate model book title
```

Rode a migrate
```
docker-compose run --rm website bundle exec rake db:migrate
```


Agora em `db/seeds.rb` adicione o codigo abaixo onde vamos popular o banco com varios titulos de livros fake

```ruby
books = (1..90).map do
  Book.create!(
    title: Faker::Book.title    
  )
end

puts 'Books created'
```

Rode o Seed
```
docker-compose run --rm website bundle exec rake db:seed
```

- ## Controller

Vamos criar a controller books

```
docker-compose run --rm website bundle exec rails generate controller books index
```

Em `app/controllers/books_controller.rb` crie o metodo show

```ruby
class BooksController < ApplicationController
  def index
    
  end

  def show
    @book = Book.find(params[:id])
  end
end
```

Em `config/routes.rb` adicione

```ruby
Rails.application.routes.draw do
  resources :books 
  
  root to: 'books#index'  
end
```

- ## Views

Em `app/views/books/index.html.erb` adicione

```
<table>
  <thead>
    <tr>
      <th>ID</th>  
      <th>Title</th>  
      <th colspan="3"></th>   
    </tr>
  </thead>

  <tbody>
    <% @books.each do |book| %>
      <tr>
        <td><%= book.id %></td>
        <td><%= book.title %></td>  
        <td><%= link_to 'Show Book', book %></td>      
      </tr>
    <% end %>
  </tbody>
</table>
```

Em `app/views/books` crie um arquivo chamado **show.html.erb** e adicione
```
<p>
  <strong>Title:</strong>
  <%= @book.title %>
</p>

<%= link_to 'Open PDF' %>
```

- ## Searchkick e ElasticSearch

Em `app/models/book.rb` coloque as linha de codigo

```ruby
class Book < ApplicationRecord

  searchkick word: [:title]
  
  def search_data
    {
      title: title      
    }
  end
end
```
Em `app/controllers/books_controller.rb` coloque as linha de codigo

```ruby
def index
    search = params[:q].present? ? params[:q] : nil
    @books = if search
      Book.search params[:q], fields: ["title"]      
    else
      Book.all
    end
  end
```

Em `app/views/books/index.html.erb` adicione acima da tag table

```
<h3>Books</h3>
<%= form_tag books_path, method: :get do %>
  <div >     
    <%= text_field_tag :q, nil, placeholder: "Search"  %>
    <%= submit_tag 'Search'  %>
    <div >
      <%= link_to 'Clear', root_url %>
      
    </div>
  </div>
<% end %>
```

- ## Final

Rode o projeto
```
docker-compose up
```

Chegamos ao fim desse tutorial faça testes realize melhorias comente aqui abaixo e até a proxima.
