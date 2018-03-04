---
layout: post
title:  "Desenvolvendo uma Aplicação com Docker e Rails"
date:   2018-02-19 02:50:45 -0300
categories: [ruby, docker]
---



 - Primeiro rode o comando no prompt de sua preferencia para criação da aplicação

```
docker run -it --rm --user "$(id -u):$(id -g)" -v "$PWD":/usr/src/app -w /usr/src/app rails rails new --skip-bundle myapp --database=postgresql
```


 - Cria um arquivo chamado **dockerfile** na raiz do seu projeto e cole:

```python
FROM ruby:2.3-slim
# Instala nossas dependencias
RUN apt-get update && apt-get install -qq -y --no-install-recommends \
      build-essential nodejs libpq-dev imagemagick
# Seta nosso path
ENV INSTALL_PATH /myapp
# Cria nosso diretório
RUN mkdir -p $INSTALL_PATH
# Seta o nosso path como o diretório principal
WORKDIR $INSTALL_PATH
# Copia o nosso Gemfile para dentro do container
COPY Gemfile ./
# Seta o path para as Gems
ENV BUNDLE_PATH /box
# Copia nosso código para dentro do container
COPY . .
```



 - Cria um arquivo chamado **docker-compose.yml** na raiz do projeto e cole o codigo

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
    build: .
    command: bash start
    ports:
      - '3000:3000'
    volumes:
      - '.:/myapp'
    volumes_from:
    - box

  box:
    image: busybox
    volumes:
      - /box

volumes:  
  postgres:
  box:
```



 - Agora crie um arquivo chamado start na raiz do seu projeto e coloque nele:

```
# Instala as Gems
bundle check || bundle install
# Roda nosso servidor
bundle exec puma -C config/puma.rb
```



 - Substitua o arquivo chamado **database.yml** na pasta `config` e cole:

```
default: &default
  adapter: postgresql
  encoding: unicode
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  host: postgres
  user: postgres

development:
  <<: *default
  database: myapp_development

test:
  <<: *default
  database: myapp_test

production:
  <<: *default
  database: myapp_production
```



 - Atualiza o arquivo `docker-compose.yml` com

```
docker-compose build
```

 - Rode o bundle install 

```
docker-compose run --rm website bundle install
```

 - Crie o banco de dados

```
docker-compose run --rm website bundle exec rake db:create
```

 - Suba o projeto

```
docker-compose up
```

 - Abra o navegar no `localhost:3000`

Pronto Parabens Voce Conseguiu!!