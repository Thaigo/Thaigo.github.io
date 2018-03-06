---
layout: post
title:  "Web Application com Angular 5 e Ruby on Rails - Parte 2"
date:   2018-03-06 01:40:00 -0300
tags: rest, api, rails, angular, web
categories: [rails, angular]
---

## Setup Inicial

**1.** Vamos criar nossa api em rails

```
rails new app-backend --api
```

**2.** Agora vamos adicionar a **gem rack-cors** no arquivo `Gemfile` para permitir que os recursos de noosa api sejam acessados por uma página web de um domínio diferente.

```ruby
source 'https://rubygems.org'

git_source(:github) do |repo_name|
  repo_name = "#{repo_name}/#{repo_name}" unless repo_name.include?("/")
  "https://github.com/#{repo_name}.git"
end

gem 'rails', '~> 5.1.5'
gem 'sqlite3'
gem 'puma', '~> 3.7'
gem 'rack-cors'

group :development, :test do
  # Call 'byebug' anywhere in the code to stop execution and get a debugger console
  gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
end

group :development do
  gem 'listen', '>= 3.0.5', '< 3.2'
  # Spring speeds up development by keeping your application running in the background. Read more: https://github.com/rails/spring
  gem 'spring'
  gem 'spring-watcher-listen', '~> 2.0.0'
end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]
```

 - Rode o bundle install no terminal para instalar gem 

**3.** Vamos modificar o arquivo `config/initializers/cors.rb` dessa maneira estamos permitindo que todos os dominio da web possa acessar nossa api

```ruby
Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins '*'

    resource '*',
      headers: :any,
      methods: [:get, :post, :put, :patch, :delete, :options, :head]
  end
end
```

