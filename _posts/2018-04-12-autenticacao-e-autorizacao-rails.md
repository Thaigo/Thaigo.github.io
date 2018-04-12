---
layout: post
title:  "Autenticação e Autorização com Devise, CanCanCan e Rolify"
date:   2018-04-12 04:25:00 -0300
tags: rails, devise, cancancan, rolify, web
categories: [rails]
---

Neste tutorial, mostrarei como é simples autenticar e autorizar sua aplicação usando as populares gems rails: [Devise](https://github.com/plataformatec/devise), [CanCanCan](https://github.com/CanCanCommunity/cancancan) e [Rolify](https://github.com/RolifyCommunity/rolify).



## O que vamos construir?

Nos vamos constuir uma plataforma de aprovações de ticket onde teremos 3 perfis de usuario o `Admin` o `Supervisor` e o `Operador`.

 - O **Admin** tem permissão de gerenciar todos os recursos e conceder permissões de acesso
 - O **Supervisor** tem permissão para aprovar e reprovar tickets dos Operadores
 - O **Operador** tem permissao para criar ticket e ler seus proprios tickets abertos

 Veja uma rapida Demonstração da aplicação.

 <iframe width="100%" height="315" src="https://www.youtube.com/embed/J2Krq9T-KXQ" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>


 - ## Basic Setup

Crie um novo projeto

```
rails new auth 
cd auth
```

Instale jquery e Materialize com Yarn

```
yarn add jquery
yarn add materialize-css
```

Em `app/assets/javascripts/application.js` adicione no inicio

```js
//= require jquery
```

Em `app/assets/stylesheets/application.css` adicione

```
*= require bootstrap/dist/css/bootstrap
```

 - ## Install Devise

 Vamos adicionar no Arquivo **GemFile** a gem devise e instalar com `bundle install`

```ruby
gem 'devise'
```

Agora vamos instalar
```
rails g devise:install
```

Para criar o Devise User
```
rails g devise User
```

Atualizar o banco de dados

```
rails db:migrate
```

 - ## Models

Agora vamos criar o model ticket

```
rails g scaffold Ticket title status:integer user:references
```

Em `db/migrate/create_ticket` altere o atributo status para

```ruby
t.integer :status, default: 0
```

Rode a migrate
```
rails db:migrate
```

Vamos realizar o relacionamento entre user has_many tickets

No `models/user.rb`

```ruby
class User < ApplicationRecord 
 
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable

  
  has_many :tickets
```

No `models/ticket.rb`

```ruby
class Ticket < ApplicationRecord
  belongs_to :user
  
  enum status: { submitted: 0, approved: 1, rejected: 2 }
end
```

Em `controller/tickets_controller.rb` adicione a autenticação do devise

```ruby
class TicketsController < ApplicationController
  before_action :authenticate_user!
end
```

 - ## CanCanCan e Rolify

Adicione as gems em **Gemfile** e instale com `bundle install`

```ruby
gem 'cancancan'
gem 'rolify'
```

Vamos criar o ability do cancancan

```
rails g cancan:ability
```

Vamos rodar o generator pra criar as roles do rolify

```
rails g rolify Role User
```

Rode a migrate
```
rails db:migrate
```

Em `models/ticket.rb` adicione

```ruby
class Ticket < ApplicationRecord
  belongs_to :user
  resourcify

  enum status: { submitted: 0, approved: 1, rejected: 2 }
end
```

Em `controller/tickets_controller.rb` adicione 

```ruby
class TicketsController < ApplicationController
  before_action :authenticate_user!
  load_and_authorize_resource
end
```

No `models/user.rb` adicione

```ruby
class User < ApplicationRecord 
  Rolify

  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable

  
  has_many :tickets
```

 - ## Roles e Abilities

 Vamos definir as permissoes de acesso em `models/ability.rb`

 ```ruby
 class Ability
  include CanCan::Ability

  def initialize(user)
    if user.nil?
      user = User.new
    end

    if user.has_role? :admin
      can :manage, :all    
    elsif user.has_role? :supervisor
      can [:create, :read, :edit, :rejected, :approve], Ticket   
    elsif user.has_role? :operator
      can [:create, :read, :destroy], Ticket, user_id: user.id 
    end      
  end
end
```

Agora vamos criar um usuario admin do sistema em `db/seeds.rb` adicione

```ruby
User.create(email: "admin@test.com", 
                    password: "123456",
                    password_confirmation: "123456")
```

Rode no terminal
```
rails db:seed
```

Com o usuario criado vamos dar permissao de admin via console no terminal digite

```
rails c
```

Agora vamos setar a permissão

```
user = User.last
user.add_role :admin
```

Vamos checar se a permissao de admin funciona

![Screenshot auth](/static/img/auth/post_auth.png)


Agora vamos definir a role padrao para todos os novos usuarios criado no sistema. 


No `models/user.rb` adicione

```ruby
class User < ApplicationRecord 
  Rolify

  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable

  
  has_many :tickets

  after_create :assign_role

  def assign_role
    add_role(:operator)
  end
```

Definimos que todo usuario criado tera uma role operator por padrão


 - ## Controllers


Vamos adicionar as actions de aprovado e reprovado em `controllers/tickets_controller.rb` adicione 

```ruby
class TicketsController < ApplicationController
  ...

   def approve
    set_ticket
    @ticket.approved!
    redirect_to root_path, notice: "The Ticket has been approved"
  end

  def rejected
    set_ticket
    @ticket.rejected!
    redirect_to root_path, notice: "The Ticket has been rejected"
  end

  def new
    @ticket = current_user.tickets.build
  end

  def create
    @ticket = current_user.tickets.build(ticket_params)

    respond_to do |format|
      if @ticket.save
        format.html { redirect_to @ticket, notice: 'Ticket was successfully created.' }
        format.json { render :show, status: :created, location: @ticket }
      else
        format.html { render :new }
        format.json { render json: @ticket.errors, status: :unprocessable_entity }
      end
    end
  end

  ...
end
```

 - ## Controllers Admin Page

Para area de admin vamos criar uma controler chamado admin

```
rails g controller admin
```

Em `controllers/admin_controller.rb` coloque as linhas de codigo abaixo

```ruby
class AdminController < ApplicationController
  before_action :authorize
  before_action :authenticate_user!  

  # Lista todos o usuarios
  def index
    @users = User.all
  end

  # Adiciona Role Supervisor
  def supervisor    
    remove_last_role
    @user.add_role(:supervisor)
    redirect_to '/admin', notice: 'Role Supervisor was successfully updated.'
  end

  # Adiciona Role Operator
  def operator    
    remove_last_role
    @user.add_role(:operator)
    redirect_to '/admin', notice: 'Role Operator was successfully updated.'
  end
  

  private

  # Autoriza somente acesso com role admin page /admin
  def authorize
    if !current_user.has_role? :admin      
      redirect_to root_url
    end
  end

  # Remove a ultima role do usuario
  def remove_last_role
    @user = User.find(params[:id])
    @user.roles = []
  end  
end
```

- ## Routes

Altere o `config/routes.rb` para:

```ruby
Rails.application.routes.draw do
  
  resources :tickets do 
  	member do 
      get :approve
      get :rejected
  	end
  end 

  resources :admin, only:[:index, :update] do 
  	member do 
      get :operator 
      get :supervisor      
  	end
  end  

  devise_for :users
  
  root "tickets#index"
end
```

- ## Layout Role Application

Mostrando a role do usuario em todas as paginas

Em `views/layouts/application.html.erb` adicione

```bash
<html>
  <head>
    <title>Auth</title>
    <%= csrf_meta_tags %>

    <%= stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= stylesheet_link_tag  'https://fonts.googleapis.com/icon?family=Material+Icons' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>

  <body>
    <p>
      <%= if !current_user.nil?
            current_user.email + ' (' + current_user.roles.pluck(:name).join(",") + ') '
          end
      %>
    </p>
    <div class="container">
      <%= yield %>
    </div>
  </body>
</html>
```

Os operators nao pode ver os tickets de outros operators entao vamos prevenir que isso aconteça em `controllers/tickets_controller.rb` adicione a index

```ruby
  def index
    @tickets = Ticket.where(user: current_user) 
               unless current_user.has_role?(:admin) || current_user.has_role?(:supervisor)
    @ability = Ability.new(current_user)
  end
```

- ## Helper

Vamos definir um helper para visualização do atributos status em `helpers/application_helper.rb` adicione

```ruby
module ApplicationHelper
  def status_label status
    status_span_generator status
  end

  def status_span_generator status
    case status
      when 'submitted'
       content_tag(:span, status.titleize, class: "blue-text text-darken-4")
      when 'approved'
       content_tag(:span, status.titleize, class: "green-text text-darken-4")
      when 'rejected'
       content_tag(:span, status.titleize, class: "red-text text-darken-4")         
    end
  end
end

```

- ## Views

Agora `views/tickets/index.html.erb` coloque as linhas de codigo

```bash
<p id="notice"><%= notice %></p>

<h4>Tickets</h4>

<%= link_to new_ticket_path, method: :get, class: "btn-floating waves-light blue" do %>
  <i class="material-icons">fiber_new</i>
<% end %>


<br>

    <div class="row">
      <% @tickets.each do |ticket| %>
      <% if @ability.can? :read, Ticket, user_id: current_user.id %>
        <div class="col s4 l4 m4">
          <div class="card light-blue lighten-2">
            <div class="card-content white-text">
              <span class="card-title"><td><%= ticket.title %></td></span>
                <p><%= status_label ticket.status %></p>  <p><%= ticket.user.email %></p> 
            </div>
            <div class="card-action">
              <% if @ability.can? :edit, Ticket %>
                <td> <%= link_to 'rejected', 
                        rejected_ticket_path(ticket),
                        class: "btn waves-effect waves-light red" %></td>

                <td> <%= link_to 'Approve',
                        approve_ticket_path(ticket),
                        class: "btn waves-effect waves-light green" %></td>
              <% end %>
              <hr>
              <% if @ability.can? :destroy, Ticket %>
                <td>
                  <%= link_to ticket, method: :delete,
                               class: "btn-floating halfway-fab waves-effect waves-light red",
                               data: { confirm: "Tem certeza que deseja Excluir" } do %>
                    <i class="material-icons">delete</i>
                  <% end %>
                </td>
              <% end %>
            </div>
          </div>
        </div>
      <% end %>    
      <% end %>
    </div>

<%= link_to 'Logout', destroy_user_session_path, method: :delete %>
```

Vamos criar nossa view da area admin em `views/admin/index.html.erb` adicione

```bash
<p id="notice"><%= notice %></p>
<h3>Area Admin</h3>

<table class="centered striped">
  <thead>
    <tr>
      <th>Email</th>
      <th>Role</th>      
    </tr>
  </thead>

  <tbody>
    <% @users.each do |user| %> 
        <tr>
          <td><%= user.email %></td>
          <td><%= user.roles.last.name %></td>
          <td><%= link_to 'Make Supervisor', supervisor_admin_path(user),
                                             class: "btn deep-purple darken-4" %></td>       

          <td><%= link_to 'Make Operator', operator_admin_path(user),
                                           class: "btn light-blue darken-4" %></td>            
        </tr>      
    <% end %>
  </tbody>
</table>
```

- ## Final

Rode o projeto

```
rails server
```

Chegamos ao final desse tutorial, faça melhorias realize testes e até o proximo tutorial.

**[Codigo-Fonte](https://github.com/Thaigo/Rails-Auth)**






