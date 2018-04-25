---
layout: post
title:  "Consumindo REST API em Json-Server com Rails"
date:   2018-04-25 02:40:00 -0300
tags: rails, rest, api, json-server
categories: [rails]
---

Nesse Tutorial vamos criar uma REST API com **[Json-Server](https://github.com/typicode/json-server)** e consumir os dados com Rails


- ### O que é Json-Server ?

**[Json-Server](https://github.com/typicode/json-server)** é um projeto que ajuda voce a criar um REST API com as operações de CRUD muito rapido não requer codificação nenhuma, voce so precisa gravar um arquivo de configuração para ela na qual veremos no tutorial a seguir.


- ### Setup Inicial

Crie um projeto com **npm init**  e instale o json-server

```
npm install --save json-server
```

No **package.json** altere o script para:

```js
...
  "scripts": {
    "json:server": "json-server -p 3009 --watch db.json"
  },
...
```

O server vai rodar no porta 3009 e o arquivo que conterá os dados será o db.json

- ### Arquivo JSON

Crie um novo arquivo com o nome de **db.json** e adicione os seguintes dados:

```json
{
  "users": [
    {
      "id": "1",
      "firstName": "John",
      "lastName": "Galt",
      "email": "john@gmail.com"      
    },
    {
      "id": "2",
      "firstName": "Luiza",
      "lastName": "Galt",
      "email": "luiza@gmail.com"      
    }
  ]
}
```

Esses objetos conterá toda uma estrutura de CRUD criada automaticamente.

- Rode o servidor

```
npm run json:server
```

![Screenshot json-server](/static/img/post-json-server/json_server.png)


- ### Testando API com Postman

O Postman é fácil de usar. Para iniciar uma solicitação GET, preencha a url como você pode ver na imagem a seguir. Clique no botão Send e você receberá a resposta no formato JSON

![Screenshot postman](/static/img/post-json-server/postman.png)


- ### Consumindo API Com Rails

Vamos criar um novo projeto em rails

```
rails new apprails
cd apprails
```

No **GemFile** adicione a gem rest-client 

```ruby
gem 'rest-client'
```
instale usando bundle 

- ### Controller

Crie uma controler chamado home

```
rails g controller home
```

Em `app/controllers/home_controller.rb` Adicione:

```ruby
class HomeController < ApplicationController
  require 'rest-client'
  require 'json'

  BASE_URL = "http://localhost:3009"

  def index
    value = RestClient.get "#{BASE_URL}/users"

    @users = JSON.parse(value, :symbolize_names => true)
  end

  def new
    
  end

  def create
    url = "#{BASE_URL}/users"
    payload = params.to_json
    value = RestClient::Resource.new(url)
    begin
      value.post payload, :content_type => "application/json"
      flash[:notice] = "User saved"
      redirect_to root_url
    rescue Exception => e
      flash[:error] = "User failed to save"
      render :new
    end
  end

  def edit
    url = "#{BASE_URL}/users/#{params[:id]}"
    value = RestClient::Resource.new(url)
    
    users = value.get
    @user = JSON.parse(users, :symbolize_names => true)
  end

  def update
    url = "#{BASE_URL}/users/#{params[:id]}"
    payload = params.to_json
    value = RestClient::Resource.new(url)

    begin
      value.put payload, :content_type => "application/json"
      flash[:notice] = "User Updated"      
    rescue Exception => e
      flash[:error] = "User failed to save"
    end
      redirect_to root_url
  end
  
  def destroy
    url = "#{BASE_URL}/users/#{params[:id]}"
    value = RestClient::Resource.new(url)
    
    begin
      value.delete
      flash[:notice] = "User Deleted" 
    rescue Exception => e
      flash[:error] = "User failed to save"
    end
      redirect_to root_url        
  end 
end
```

- ### Rotas

Altere as rotas em config/routes.rb

```ruby
Rails.application.routes.draw do
  resources :home
  root to: 'home#index'
end
```

- ### Views

Crie a view **index.html.erb** no diretorio home e adicione:

```bash
<p id="notice"><%= notice %></p>

<%= link_to "New User", new_home_path %>

<%if @users.present? %>
  <p>List Users</p>
  <table width="100%">
    <tr>
      <td>ID</td>
      <td>first_name</td>
      <td>last_name</td>
      <td>email</td>
      <td>Action</td>
    </tr>
    
    <%@users.each do |u|%>
    <tr>
      <td><%=u[:id]%></td>
      <td><%=u[:firstName]%></td>
      <td><%=u[:lastName]%></td>
      <td><%=u[:email]%></td>      
      <td>
        <%= link_to "Edit", edit_home_path(u[:id]) %> |
        <%= link_to "Delete", home_path(u[:id]),
                     method: :delete, data: { confirm: 'Are you sure?' } %>
      </td>
    </tr>
   <%end%>
  </table>
<%else%>
  <p>No User is Found</p>
<%end%>
```

Crie a view **new.html.erb** no diretorio home e adicione:

```bash
<%= form_tag home_index_path, method: :post do %>
  First Name: <%= text_field_tag :firstName%> <br/>
  Last Name: <%= text_field_tag :lastName%> <br/>
  Email: <%= text_field_tag :email%> <br/>

  <%= submit_tag "save"%>
<% end %>
```
Crie a view **edit.html.erb** no diretorio home e adicione:

```bash
<%= form_tag home_path, method: :put do %>
  First Name: <%= text_field_tag :firstName, @user[:firstName] %> <br/>
  Last Name: <%= text_field_tag :lastName, @user[:lastName] %> <br/>
  Email: <%= text_field_tag :email, @user[:email] %> <br/>

  <%= submit_tag "update"%>
<% end %>
```

- ### Final

Rode o projeto

```
rails server
```

Realize todos os teste de CRUD, Chegamos ao final do nosso tutorial. Até a proxima.

**[Codigo-Fonte](https://github.com/Thaigo/Rails_Json-Server)**
