---
layout: post
title:  "Rails API - Autenticando com JWT"
date:   2018-04-02 02:55:00 -0300
tags: rails, jwt, authentication, web, json, web_token, api
categories: [rails]
---

Nesse tutorial vamos abordar uma solução simples de autenticação para Rails API usando [Knock](https://github.com/nsarno/knock) que é baseado em Json Web Tokens


### Para saber mais sobre JWT

Clique aqui - [JSON Web Tokens](https://jwt.io/introduction/) 

 - ### Setup Inicial

Crie o projeto como API em rails
```
rails new appjwt --api
cd appjwt
```

Coloque as gems no arquivo **GemFile** e instale com bundle install

```ruby
gem 'bcrypt', '~> 3.1.7'
gem 'knock'
```

 - ### Models

Crie a model user para criar os usuarios que vai logar no sistema

```
rails g model user name email password_digest
```

Agora vamos gerar o arquivo `knock.rb` que fica em **config/initializers** onde contem todas as opções de configurações da gem

```
rails generate knock:install
```

Em `app/models/user.rb` vamos configurar nosso model para scriptar as senhas dos usuarios no banco de dados

```ruby
class User < ActiveRecord::Base
  has_secure_password
end
```



 - ### Controllers

Vamos criar um controler que sera protegida de acessos nao autorizados.

```
rails g controller pages index
```

Agora vamos prover uma maneira dos usuarios logar para isso vamos gerar a controller do knock

```
rails generate knock:token_controller user
```

 - ### Routes

Adicione em `config/routes.rb`

```ruby
Rails.application.routes.draw do
  get '/page' => 'pages#index'

  post 'user_token' => 'user_token#create'  
end
```

 - ### Knock

Em `app/controllers/applicationcontroller.rb` adicione

```ruby
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  include Knock::Authenticable
end
```

Em `app/controllers/pagescontroller.rb` adicione

```ruby
class PagesController < ApplicationController
  before_action :authenticate_user
  
  def index
  end
end
```

 - ### Testes

No terminal digite o comando para entrar no console:

```
rails c
```

Crie um usuario

```ruby
User.create(name: 'user1', email: 'user1@email.com', password: 'asdfasdf')
```

Agora vai em `test/fixtures/users.yml` e adicione

```yml
one:
  id: 1
  name: user1
  email: user1@email.com
  password_digest: asdfasdf
```

Em `test/models/user_test.rb` coloque o codigo

```ruby
require 'test_helper'

class UserTest < ActiveSupport::TestCase
  test 'when user exists' do
    def authenticated_header
      token = Knock::AuthToken.new(payload: { sub: users(:one).id }).token

      {
        'Authorization': "Bearer #{token}"
      }

      

        get pages_resources_url, headers: authenticated_header

        assert_response :success
                
    end     
  end
end
```

Use o comando para testar

```
rails test test/models/user_test.rb
```
![Screenshot test](/static/img/post_jwt/test.png)

 - ### Testando com Curl

Vamos realizar o teste seu logar com usuario e senha

```
curl localhost:3000/page
```
Verifica o acesso negado # Completed 401 Unauthorized

![Screenshot test2](/static/img/post_jwt/test2.png)

Agora vamos logar com usuario e senha

```
curl -X POST -d 'auth[email]=user1@email.com&auth[password]=asdfasdf' http://localhost:3000/user_token
```
![Screenshot test3](/static/img/post_jwt/test3.png) 

Foi gerado o token com sucesso Agora vamos passar o token para autenticar 
```
curl -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE1MjI3MzI5MjcsInN1YiI6MX0.DECZg60F-3cbCPY3ius67N-4FKEScHsp40yEDNM7w78" localhost:3000/page
```

![Screenshot test4](/static/img/post_jwt/test4.png) 

Autenticado com sucesso. # Completed 200 OK


 - ### Final

 Chegamos ao final desse tutorial se gosto comente aqui abaixo e até a proxima.






