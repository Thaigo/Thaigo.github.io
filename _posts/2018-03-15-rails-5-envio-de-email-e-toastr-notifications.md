---
layout: post
title:  "Rails 5 - Envio de Email e Toastr Notifications"
date:   2018-03-15 19:20:00 -0300
tags: email, toastr, rails, web
categories: [rails]
---

Nesse tutorial abordarei o basico de [Action Mailer](http://guides.rubyonrails.org/action_mailer_basics.html) para envio de email simples e usar o [Toastr](http://codeseven.github.io/toastr/) flash notifications para aparecer uma mensagem de sucesso agradavel na tela apos a criação dos tickets

## O que vamos construir?

Nos vamos construir uma simples aplicação de aberturas de tickets usando Rails 5 integrado com Mailcatcher para testar os recebimento de emails, abordarei os metodos basicos para criação dos tickets, configuração do [Action Mailer](http://guides.rubyonrails.org/action_mailer_basics.html) e notificaçoes com [Toastr](http://codeseven.github.io/toastr/). 

Veja o resultado final no video e se gostou vamos começar e escrever algum código! 

<iframe width="100%" height="315" src="https://www.youtube.com/embed/OZ7VNGmRnA0" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>


 - ### Basic Setup

Começaremos criando um novo aplicativo Rails 5 a partir do zero. Usaremos o SQLITE como nosso sistema de banco de dados:

```
rails new ticketnow
```

 - ### Mailcatcher setup

Para instalção do [Mailcatcher](https://mailcatcher.me/) use o comando no terminal

**1.** gem install mailcatcher

**2.** mailcatcher

**3.** Vai para http://localhost:1080

![Screenshot mailcatcher](/static/img/mail/mailcatcher.png)

**4.** Para enviar email atraves do smtp://localhost:1025, entre no diretorio `config/environments/development.rb` e adcione as seguintes linhas de codigo abaixo.

```
config.action_mailer.smtp_settings = { address: "localhost", port: 1025 }
config.action_mailer.default_url_options = { host: "localhost:3000" }
```

 - ### Resource setup

Então precisamos criar a model ticket para preencher o banco de dados com alguns dados

```
rails g resource ticket name email title description:text
```

Vamos rodar as migrations

```
rails db:migrate
```

 - ### Toastr Notifications setup

**1.** Entre no site do [Toastr](http://codeseven.github.io/toastr/) e faz o download do zip e entao copie e cole o arquivo **toastr.min.js** e **toastr.js.map** dentro de `app/assets/javascript` e dentro de `app/assets/stylesheets` cole o arquivo **toastr.css**

**2.** No diretorio `app/controllers/application_controller.rb` adicione a linha

```ruby
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  add_flash_types :success
end
```

**3.** Crie um diretorio chamado shared dentro **app/views**

**4.** dentro de shared crie um arquivo chamado `_flash_messages.html.erb` e adcione as linhas abaixo:

```ruby
<% if flash.present? %>
  <div id='flash'>
    <%= javascript_tag do %>
      <% if flash[:success] %>
        var $toastContent = $('<span><%= flash[:success] %></span>');
        toastr.success($toastContent)
      <% end %> 
    <% end %>
  </div>
<% end %>
```

**5.** No diretorio `app/views/layouts/application.html.erb` adicione

```html
<body>    
  <%= render :partial => 'shared/flash_messages', :flash => flash %>
  <%= yield %>
</body>
```  

 - ### Controller

No diretorio **app/controllers/tickets_controller.rb** vamos adcionar as linhas abaixo `atenção` a linha comentada **send email** vamos altera-la mais a frente.

```ruby
class TicketsController < ApplicationController
  def new
    @ticket = Ticket.new    
  end

  def create
    @ticket = Ticket.new(ticket_params)
    if @ticket.save
      #send email
      redirect_to @ticket, success: 'successfully_created'  
    else
      render :new
    end
  end

  def show
    @ticket = Ticket.find(params[:id])
  end

  private
   
    def ticket_params
      params.require(:ticket).permit(:title, :description, :email, :name)
    end
end
```

 - ### Routes

Precisamos definir uma rota default agora 

**1.** Entre no diretorio **config/routes.rb** e adcione as linhas

```ruby
Rails.application.routes.draw do
  resources :tickets
  root 'tickets#new'
end
```

 - ### Views 

**1.** Criaremos 3 aquivos no diretorio **app/views/tickets** chamados `new.html.erb` , `show.html.erb` e `_form.html.erb`

**3.** No `new.html.erb` adicione a linha

```
<%= render 'form', ticket: @ticket %>
```

**3.** No arquivo `_form.html.erb` adicione as linhas abaixo

```html
<div class="form-style-10">
  <h1>Ticket Now!
    <span>The ticket will be sent to your email for confirmation!</span>
  </h1>
  <%= form_with(model: ticket, local: true) do |f| %>
    <form>
      <div class="section">
        <span>1</span>Title &amp; Description
      </div>
        <div class="inner-wrap">
          <label>Your Name
            <%= f.text_field :name %>
          </label>

          <label>Email
            <%= f.text_field :email %>
          </label>

          <label>Title
            <%= f.text_field :title %>
          </label>
          
          <label>Description
            <%= f.text_area :description %>
          </label>
        </div>
        <div class="button-section">
          <%= f.submit "Create Ticket" %>
        </div>        
    </form>
    <% end %>
</div>
```

**4.** No arquivo `show.html.erb` adicione as linhas abaixo

```html
<div class="form-style-10">
    <h1>Your Ticket here! </h1>
  <div class="section">
    <span><%= @ticket.id %></span>Details
  </div>
  <div class="inner-wrap">
    <p>
      <strong>Title:</strong>
      <%= @ticket.title %>
    </p>

    <p>
      <strong>Description:</strong>
      <%= @ticket.description %>
    </p>

    <%= link_to 'Back', root_path %>
  </div>
</div>
```

- ### CSS

Vamos adicionar o CSS a nossa aplicação 
 
**1.** Dentro do diretorio **app/assets/stylesheets/aplication.css** adcione as linhas:

```css
.form-style-10{
    width:450px;
    padding:30px;
    margin:40px auto;
    background: #FFF;
    border-radius: 10px;
    -webkit-border-radius:10px;
    -moz-border-radius: 10px;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.13);
    -moz-box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.13);
    -webkit-box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.13);
}
.form-style-10 .inner-wrap{
    padding: 30px;
    background: #F8F8F8;
    border-radius: 6px;
    margin-bottom: 15px;
}
.form-style-10 h1{
    background: #2A88AD;
    padding: 20px 30px 15px 30px;
    margin: -30px -30px 30px -30px;
    border-radius: 10px 10px 0 0;
    -webkit-border-radius: 10px 10px 0 0;
    -moz-border-radius: 10px 10px 0 0;
    color: #fff;
    text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.12);
    font: normal 30px 'Bitter', serif;
    -moz-box-shadow: inset 0px 2px 2px 0px rgba(255, 255, 255, 0.17);
    -webkit-box-shadow: inset 0px 2px 2px 0px rgba(255, 255, 255, 0.17);
    box-shadow: inset 0px 2px 2px 0px rgba(255, 255, 255, 0.17);
    border: 1px solid #257C9E;
}
.form-style-10 h1 > span{
    display: block;
    margin-top: 2px;
    font: 13px Arial, Helvetica, sans-serif;
}
.form-style-10 label{
    display: block;
    font: 13px Arial, Helvetica, sans-serif;
    color: #888;
    margin-bottom: 15px;
}
.form-style-10 input[type="text"],
.form-style-10 input[type="date"],
.form-style-10 input[type="datetime"],
.form-style-10 input[type="email"],
.form-style-10 input[type="number"],
.form-style-10 input[type="search"],
.form-style-10 input[type="time"],
.form-style-10 input[type="url"],
.form-style-10 input[type="password"],
.form-style-10 textarea,
.form-style-10 select {
    display: block;
    box-sizing: border-box;
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    width: 100%;
    padding: 8px;
    border-radius: 6px;
    -webkit-border-radius:6px;
    -moz-border-radius:6px;
    border: 2px solid #fff;
    box-shadow: inset 0px 1px 1px rgba(0, 0, 0, 0.33);
    -moz-box-shadow: inset 0px 1px 1px rgba(0, 0, 0, 0.33);
    -webkit-box-shadow: inset 0px 1px 1px rgba(0, 0, 0, 0.33);
}

.form-style-10 .section{
    font: normal 20px 'Bitter', serif;
    color: #2A88AD;
    margin-bottom: 5px;
}
.form-style-10 .section span {
    background: #2A88AD;
    padding: 5px 10px 5px 10px;
    position: absolute;
    border-radius: 50%;
    -webkit-border-radius: 50%;
    -moz-border-radius: 50%;
    border: 4px solid #fff;
    font-size: 14px;
    margin-left: -45px;
    color: #fff;
    margin-top: -3px;
}
.form-style-10 input[type="button"], 
.form-style-10 input[type="submit"]{
    background: #2A88AD;
    padding: 8px 20px 8px 20px;
    border-radius: 5px;
    -webkit-border-radius: 5px;
    -moz-border-radius: 5px;
    color: #fff;
    text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.12);
    font: normal 30px 'Bitter', serif;
    -moz-box-shadow: inset 0px 2px 2px 0px rgba(255, 255, 255, 0.17);
    -webkit-box-shadow: inset 0px 2px 2px 0px rgba(255, 255, 255, 0.17);
    box-shadow: inset 0px 2px 2px 0px rgba(255, 255, 255, 0.17);
    border: 1px solid #257C9E;
    font-size: 15px;
}
.form-style-10 input[type="button"]:hover, 
.form-style-10 input[type="submit"]:hover{
    background: #2A6881;
    -moz-box-shadow: inset 0px 2px 2px 0px rgba(255, 255, 255, 0.28);
    -webkit-box-shadow: inset 0px 2px 2px 0px rgba(255, 255, 255, 0.28);
    box-shadow: inset 0px 2px 2px 0px rgba(255, 255, 255, 0.28);
}
.form-style-10 .privacy-policy{
    float: right;
    width: 250px;
    font: 12px Arial, Helvetica, sans-serif;
    color: #4D4D4D;
    margin-top: 10px;
    text-align: right;
}
```

- ### Action Mailer

Agora vamos gerar o nosso mailer responsavel pelo envio e email da nossa aplicação.
 
**1.** no terminal rode o comando.

```
rails g mailer ticket_mailer ticket_confirmation
```

**2.** No diretorio **app/mailers/ticket_mailer.rb** adicione as linhas abaixo

```ruby
class TicketMailer < ApplicationMailer
  default from: "ticketnow@domain.com"
 
  def ticket_confirmation(ticket)
    @ticket = ticket

    mail to: ticket.email, subject: "Ticket Confirmation #{@ticket.id}"
  end
end
```

**2.** Agora no diretorio **app/views/ticket_mailer/ticket_confirmation.html.erb** adicione

```html
<h1>Ticket NOW!!</h1>

<p>
  <%= @ticket.name %>, Your ticket number is <%= @ticket.id %> thank you!
</p>

<p>
  See your ticket details <%= link_to "HERE", url_for(@ticket) %>
</p>
```

**3.** E no diretorio **app/views/ticket_mailer/ticket_confirmation.text.erb** adicione

```
Ticket NOW!!
<%= @ticket.name %>, Your ticket number is <%= @ticket.id %> thank you!
See your ticket details <%= link_to "HERE", url_for(@ticket) %>
```

**4.** Agora no diretorio **app/controllers/tickets_controller.rb** na linha comentada **#send mail** altere para 

```ruby
TicketMailer.ticket_confirmation(@ticket).deliver
```

![Screenshot sendemail](/static/img/mail/sendemail.png)

- ### Final

Encerramos por aqui nosso tutorial agora é com voce realize testes na aplicação faça melhorias e comente aqui abaixo o que achou. Parabéns se voce chego até aqui e até a proxima.






