---
layout: post
title:  "Criando um Chat com Rails usando Action Cable"
date:   2018-03-29 18:55:00 -0300
tags: action_cable, rails, websockets, web
categories: [rails]
---

Nesse tutorial vamos abordar os recursos de [Action Cable](http://guides.rubyonrails.org/action_cable_overview.html) e [Active Job](http://edgeguides.rubyonrails.org/active_job_basics.html) O **Action Cable** possui integração com protocolo de comunicação **WebSocket** que, comparado ao HTTP, oferece ótimos novos recursos que iremos ver a seguir.

### O que é Websockets ?

WebSockets é um protocolo que permite ao cliente e ao servidor manter uma conexão aberta, permitindo que eles transmitam dados diretamente entre si. O cliente se inscreve em uma conexão websocket aberta no servidor e quando há novas informações, o servidor transmite os dados e os clientes inscritos o recebem. Dessa forma, tanto o servidor quanto o cliente sabem sobre o estado dos dados e podem sincronizar facilmente as alterações à medida que elas ocorrem.

Veja um rapido video de demonstração que iremos construir.

<iframe width="100%" height="315" src="https://www.youtube.com/embed/--8NMOF_mMk" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

- ### Basic Setup

Criando o projeto
```
rails new mychat
cd mychat
```

Adicionando no **Gemfile** e instale com bundle install
```ruby
gem 'jquery-rails'
```

Adicione em `app/assets/javascripts/application.js`

```js
//= require jquery
```

Instalando bootstrap com Yarn

```
yarn add bootstrap
```

Adicione em `app/assets/stylesheets/application.css`

```
*= require bootstrap/dist/css/bootstrap
```

- ### Model

Criando o modelo chamado de message que contera o atributo conteudo e nome do usuario.

```
rails g model message name content:text
```

Gerando a migration 
```
rails db:migrate
```

- ### Controller

Vamos gerar a controler messages responsavel pela criação das mensagens

```
rails g controller messages
```
```ruby
class MessagesController < ApplicationController
  def create
    @message = Message.create(params[:message].permit!)    
  end
end
```

vamos gerar a controler users responsavel por mostrar as mensagens
```
rails g controller users chat
```
```ruby
class UsersController < ApplicationController
  def chat
    @messages = Message.all
  end
end
```
- ### Routes

Vamos criar nossas rotas em `config/routes.rb`

```ruby
Rails.application.routes.draw do
  resources :messages, only: [:create]
  get :chat, to: 'users#chat'
  root 'users#chat' 
end
```

- ### Views

Em `app/views/messages` crie um partial **_message.html.erb** e adicione

```
<div class="container">
  <div class="row">
    <div class="col-md-6 offset-md-3">
      <div class="panel panel-primary">
        <div class="panel-body">
          <div class="chat-body clearfix">
            <div class="header">
              <strong class="primary-font">
                <%= message.name %>
              </strong>
              <small class="pull-right text-muted">
                <span class="glyphicon glyphicon-time"></span>
                <%= message.created_at.strftime('%b %d %Y %l:%M %p') %>
              </small>
            </div>

            <p class="p-2 mb-2 bg-success text-white">
              <%= message.content %>
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
```

Em `app/views/messages` crie um arquivo chamado **create.js.erb** e adicione

```js
$('#message_content').val('');
$('#message_content').focus();
```

No diretorio `app/views/users` crie um arquivo chamado **chat.html.erb**

```
<div class="container">
  <div class="panel-heading">
    <h3 class="panel-title">Chat</h3>
  </div>
  <ul class='list-group messages'>
    <%= render @messages %>
  </ul>

  <%= form_for Message.new, 
               html: { class: 'form-horizontal' }, remote: true do |f| %>
    
    <div class="form-row">

      <div class="col-sm-3">
        <%= f.text_field :name, placeholder: 'Enter Username', 
                                class: 'form-control' %>
      </div>

      <div class="col-sm-7">
        <%= f.text_field :content, placeholder: 'Enter Message', 
                                   class: 'form-control' %>
      </div>
      <div class="col-sm-2">
        <%= f.submit 'Send', class: 'btn btn-primary' %>
      </div>
    </div>
  <% end %>
</div>
```

- ### [Action Cable](http://guides.rubyonrails.org/action_cable_overview.html)

Adicione em `app/views/layouts/application.html.erb` a tag
```r
<%= action_cable_meta_tag %>
```

Crie o Channel chat

```
rails g channel chat
```

Adicione em `config/routes.rb`

```ruby
mount ActionCable.server => '/cable'
```

Em app/channels/chat_channel.rb

- **stream** fornece o mecanismo pelo qual os canais encaminham o conteúdo

```ruby
class ChatChannel < ApplicationCable::Channel
  def subscribed
     stream_from "chat_channel"
  end

  def unsubscribed
    # Any cleanup needed when channel is unsubscribed
  end  
end

```

Em `app/assets/javascripts/channels` altera **chat.coffee** para **chat.js**

```js
App.chat = App.cable.subscriptions.create("ChatChannel", {
  connected: function() {
    // Called when the subscription is ready for use on the server
  },

  disconnected: function() {
    // Called when the subscription has been terminated by the server
  },

  received: function(data) {
    // Called when there's incoming data on the websocket for this channel
    $('.messages').prepend(data['message']);
  },
});
```

- ### [Active Job](http://edgeguides.rubyonrails.org/active_job_basics.html)

Criando um Job

```
rails g job ChatBroadcast
```

Adicione em `app/models/message.rb`

```ruby
after_create_commit { ChatBroadcastJob.perform_later(self) }
```

Em `app/jobs/chat_broadcast_job.rb` 

```ruby
class ChatBroadcastJob < ApplicationJob
  queue_as :default

  def perform(message)
    ActionCable.server.broadcast 'chat_channel', message: render_message(message)
  end

  private
  def render_message(message)
    MessagesController.render(partial: 'message', locals: {message: message}).squish
  end
end
```
 - ### Final

 Rode o projeto
 ```
 rails s
 ```

 Chegamos ao final desse tutorial realize teste faça melhorias comente o que acho e até a proxima.




