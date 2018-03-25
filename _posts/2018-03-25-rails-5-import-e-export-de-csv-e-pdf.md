---
layout: post
title:  "Rails 5 - Importar e Exportar Arquivos CSV e PDF"
date:   2018-03-25 04:45:00 -0300
tags: rails, csv, pdf, web
categories: [rails]
---

Nesse tutorial vou mostrar como importar e exportar arquivos CSV e PDF para dentro da sua aplicação em rails.

## O que vamos construir?

Nos vamos construir uma aplicação simples onde podemos salvar as notas dos alunos e depois baixar um pdf com seu resultado dos testes

Veja uma rapida desmonstração do que sera criado.

<iframe width="100%" height="315" src="https://www.youtube.com/embed/8TdoP0QSJeY" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

- ### Basic Setup

Crie o Projeto
```
rails new importcsv
```

Adicione o **require** em `config/application.rb`
```ruby
require "csv"
```

Adicione em **GemFile** e instale com bundle install
```ruby
gem 'wicked_pdf'
```

Agora crie o initializer
```
rails generate wicked_pdf
```

Adicione em **GemFile** e instale com bundle install
```ruby
gem 'wkhtmltopdf-binary'
```

Instalando **Boostrap** com **Yarn**

```
yarn add bootstrap
```

Em **`app/assets/stylesheets/application.css`**
```
*= require bootstrap/dist/css/bootstrap
```


- ### Models and Controllers

Crie um Scaffold 
```
rails g scaffold student name test1:float test2:float test3:float final_grade:float
```

Gera a Migration
```
rails db:migrate
```

Em **`app/controllers/students_controller.rb`**
```ruby
...
def import
    Student.import(params[:file])
    redirect_to root_url, notice: "File Imported"
end

  # GET /students
  # GET /students.json
  def index
    @students = Student.all
    respond_to do |format|
      format.html
      format.csv { 
        send_data @students.to_csv(['name',
                                    'test1',
                                    'test2',
                                    'test3',
                                    'final_grade']) 
      }     
    end
  end

  # GET /students/1
  # GET /students/1.json
  def show
    respond_to do |format|
      format.html      
      format.pdf { render template: 'students/boletim', pdf: 'Boletim' }
    end
  end
...
```

Em **`app/models/student.rb`**
```ruby
class Student < ApplicationRecord
  validates :name, :test1, :test2, :test3, presence: true

  def self.to_csv(fields = column_names, options = {})
    CSV.generate(options) do |csv|
      csv << fields
      all.each do |student|
        csv << student.attributes.values_at(*fields)
      end
    end
  end

  def self.import(file)
    CSV.foreach(file.path, headers: true) do |row|
      student_hash = row.to_hash
      student = find_or_create_by!(name: student_hash['name'], 
                                   test1: student_hash['test1'],
                                   test2: student_hash['test2'],
                                   test3: student_hash['test3'],
                                   final_grade: student_hash['final_grade'])
      
      student.update_attributes(student_hash)
    end    
  end
  
  def note
    (test1 + test2 + test3) / 3
  end
  
end
```

- ### Routes

Em **`config/routes.rb`**
```ruby
Rails.application.routes.draw do
  resources :students do
    collection { post :import }
  end
  
  root 'students#index'
end
```

- ### Views

Crie um arquivo chamado **boletim.pdf.erb** em `app/views/students`

<iframe src="https://pastebin.com/embed_iframe/cLNMfGHh" style="border:none;width:100%"></iframe>

Em `app/views/layouts/application.html.erb`

<iframe src="https://pastebin.com/embed_iframe/C6Hb29ud" style="border:none;width:100%"></iframe>

Em `app/views/students/_form.html.erb`

<iframe src="https://pastebin.com/embed_iframe/tMvngQqD" style="border:none;width:100%"></iframe>

Em `app/views/students/index.html.erb`

<iframe src="https://pastebin.com/embed_iframe/AQv08VXf" style="border:none;width:100%"></iframe>

Em `app/views/students/show.html.erb`

<iframe src="https://pastebin.com/embed_iframe/JsZ7KPg1" style="border:none;width:100%"></iframe>


- ### Final

Rode a aplicação e digite no navegador localhost:3000
```
rails s
```

Terminamos aqui nosso tutorial se gosto comente aqui abaixo, Realize teste faça melhorias e até a proxima.


