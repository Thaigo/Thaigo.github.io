---
layout: post
title:  "App Clone Trello"
date:   2018-04-22 19:30:00 -0300
tags: rails, trello, to_do, list
categories: [rails]
---

 - Nesse Tutorial vou mostrar como é simples criar um clone do Trello veja o video de exemplo.

<iframe width="100%" height="315" src="https://www.youtube.com/embed/spRl_34te7A" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

 - ### Setup Inicial

**1.** Vamos criar um novo projeto

 ```
 rails new to_do
 cd to_do
 ```

**2.** Instalando Bootstrap e Font-Awesome

 ```
 yard add bootstrap
 ```

**3.** Vai em `app/assets/stylesheets/application.css` e coloque:

 ```
 *= require bootstrap/dist/css/bootstrap
 ```

**4.** Para instalar o font-awesome vai em `app/views/layouts/application.html.erb` e Adcione abaixo da TAG </body>

 ```bash
 <%= javascript_include_tag 'https://use.fontawesome.com/releases/v5.0.10/js/all.js' %>
 ```

  - ### Model

**1.** Vamos criar um scaffold task

```
rails g scaffold task content:text state completed_at:datetime
```

**2.** Altere o `db/migrate` **_create_tasks.rb** para

```ruby
class CreateTasks < ActiveRecord::Migration[5.1]
  def change
    create_table :tasks do |t|
      t.text :content
      t.string :state, default: 'to_do'
      t.datetime :completed_at

      t.timestamps
    end
  end
end
```
**3.** Rode a migrate para criar o banco de dados.

```
rails db:migrate
```

**4.** Em `app/models/task.rb` Adicione.

```ruby
class Task < ApplicationRecord
  def completed?
    !completed_at.blank?
  end
end
```

  - ### Controller

**1.** Vai em `app/controllers/tasks_controllers.rb` e coloque:

```ruby
class TasksController < ApplicationController
  before_action :set_task, only: [:show, :edit, :update, :destroy, :change, :complete]


  def change
    @task.update_attributes(state: params[:state])
    redirect_to tasks_path, notice: 'Task status was successfully changed.' 
  end

  def complete
    @task.update_attribute(:completed_at, Time.now)
    redirect_to root_url, notice: "Task completed"
  end
  
  # GET /tasks
  # GET /tasks.json
  def index
    @to_do = Task.where(state: 'to_do')
    @doing = Task.where(state: 'doing')
    @done = Task.where(state: 'done')
  end

  # GET /tasks/1
  # GET /tasks/1.json
  def show
  end

  # GET /tasks/new
  def new
    @task = Task.new
  end

  # GET /tasks/1/edit
  def edit
  end

  # POST /tasks
  # POST /tasks.json
  def create
    @task = Task.new(task_params)

    respond_to do |format|
      if @task.save
        format.html { redirect_to @task, notice: 'Task was successfully created.' }
        format.json { render :show, status: :created, location: @task }
      else
        format.html { render :new }
        format.json { render json: @task.errors, status: :unprocessable_entity }
      end
    end
  end

  # PATCH/PUT /tasks/1
  # PATCH/PUT /tasks/1.json
  def update
    respond_to do |format|
      if @task.update(task_params)
        format.html { redirect_to @task, notice: 'Task was successfully updated.' }
        format.json { render :show, status: :ok, location: @task }
      else
        format.html { render :edit }
        format.json { render json: @task.errors, status: :unprocessable_entity }
      end
    end
  end

  # DELETE /tasks/1
  # DELETE /tasks/1.json
  def destroy
    @task.destroy
    respond_to do |format|
      format.html { redirect_to tasks_url, notice: 'Task was successfully destroyed.' }
      format.json { head :no_content }
    end
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_task
      @task = Task.find(params[:id])
    end

    # Never trust parameters from the scary internet, only allow the white list through.
    def task_params
      params.require(:task).permit(:content, :state)
    end
end
```

  - ### Routes

**1.** Altera as rotas em `config/routes.rb` para:

```ruby
Rails.application.routes.draw do
  resources :tasks do 
    member do 
      put :change
      patch :complete
    end 
  end 
  
  root to: 'tasks#index'
end
```

  - ### Views

**1.** Altere a `app/views/tasks/index.html.erb` e coloque: 

```bash
<p id="notice"><%= notice %></p>


<%= link_to 'New Task', new_task_path %>


<div class="row">
  <div class="col-md-4">
    <h1>Aberto</h1>
      <%= render @to_do %>
  </div>

  <div class="col-md-4">
    <h1>Em Analise</h1>
      <%= render @doing %>
  </div>

  <div class="col-md-4">
    <h1>Concluído</h1>
      <%= render @done %>      
  </div>

</div>
```

**2.** Crie uma partial chamado **_task.html.erb** no diretorio tasks e adicione

```bash
<div class = "panel panel-info">
       <div class = "panel-heading">
         <%= link_to edit_task_path(task) do %>
           Created: <%= time_ago_in_words(task.created_at) %> ago
         <% end %>
       </div>
        
       <div class = "panel-body">
         <div class="row">        
             <% if task.state == 'done'%>  
               <% if task.completed? %>
                 <div class="col-md-4">
                   <%= link_to complete_task_path(task), method: :patch do %> 
                     <i style="opacity: 0.4" class="fa fa-check" ></i>
                   <% end %> 
                 </div> 
                 <div>
                   <p style="opacity: 0.4" ><strike><%= task.content %> </strike></p>
                 </div>
                  
                 <div class="col-md-4">
                   <%= link_to task_path(task), method:
                         :delete, data: { confirm: "voce tem certeza?"} do %>
                     <i class="fa fa-trash"></i>	
                    <% end %>  
                  </div>
                <% else %>
                  <div class="col-md-4">
                    <%= link_to complete_task_path(task), method: :patch do %> 
                      <i class="fa fa-check" ></i>
                    <% end %> 
                  </div> 
                  <div>
                    <p><%= task.content %></p>
                  </div>
                  
                  <div class="col-md-4">
                    <%= link_to task_path(task), method:
                         :delete, data: { confirm: "voce tem certeza?"} do %>
                      <i class="fa fa-trash"></i>	
                    <% end %>  
                  </div>
                <% end %>
              <% else %>
                <div class = "panel-body">
                  <%= task.content %>
                </div>
              <% end %>
          </div> 
        </div>

        <div class = "panel-footer">
          <div class = "btn-group btn-group-justified">

            <% if task.state == 'doing' %>

              <%= link_to  change_task_path(task, state:"to_do"),
                    method: :put, class: 'btn btn-info btn-block' do %>
                <i class="fa fa-arrow-left"></i>
              <% end %>


              <%= link_to  change_task_path(task, state:"done"),
                    method: :put, class: 'btn btn-info btn-block' do %>
                <i class="fa fa-arrow-right"></i>
              <% end %>

           
            <% elsif task.state == 'done' %>
              
             <%= link_to '<i class="fa fa-arrow-left"></i>'.html_safe,
                   change_task_path(task, state:"doing"),
                   method: :put, class: 'btn btn-info btn-block' %>

            <% else %>

              <%= link_to '<i class="fa fa-arrow-right"></i>'.html_safe,
                   change_task_path(task, state:"doing"),
                   method: :put, class: 'btn btn-info btn-block' %>
            
            <% end %>

          </div>
        </div>
      </div>   
```

  - ### Final 

Rode o projeto

```
rails server
```

Chegamos ao final até o proximo tutorial.

**[Codigo-Fonte](https://github.com/Thaigo/Rails-AppTrello)**