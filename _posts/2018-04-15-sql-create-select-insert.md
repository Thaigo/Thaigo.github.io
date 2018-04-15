---
layout: post
title:  "SQLite3: Create Insert e Select"
date:   2018-04-15 01:30:00 -0300
tags: rails, devise, cancancan, rolify, web
categories: [rails]
---

 - ### Instalando SQlite3

Para instalar é necessario já ter o Ruby e bundler instalado na maquina e entao é so rodar

```
gem install sqlite3
```

 - ### Criando um banco de dados

 Quando você digita sqlite3 com um nome de arquivo que termina em .sqlite3, a gem sqlite3 criará automaticamente um banco de dados vazio.

 ```sql
sqlite3 db_music.sqlite3
SQLite version 3.19.3 2017-06-08 14:26:16
Enter ".help" for usage hints.
```

 - ### Criando uma tabela

```sql
sqlite> CREATE TABLE pessoas (
   ...> id int primary key not null,
   ...> nome char(20),
   ...> sobrenome char(20),
   ...> email char(30) not null);
```

 - ### Mostrando as tabelas do banco de dados

```sql
sqlite> .tables
usuarios
sqlite> 
```

- ### Inserindo dados

```sql
sqlite> INSERT INTO pessoas(id, nome, sobrenome, email)
   ...> VALUES(1, 'maria', 'kenan', 'maria@email.com');
```

Agora vamos contar quantos registros tem na tabela

```sql
sqlite> select count(*) from pessoas;
1
```

 - ### Recebendo dados de todas as colunas

```sql
sqlite> select * from pessoas;
1|maria|kenan|maria@email.com
```

Vamos inserir um segundo registro para tabela pessoas

```sql
sqlite> INSERT INTO pessoas(id, nome, sobrenome, email)
   ...> VALUES(2, 'Dosean', 'Pesa', 'dosean@email.com');

sqlite> select * from pessoas;
1|maria|kenan|maria@email.com
2|Dosean|Pesa|dosean@email.com
```

 - ### Recebendo dados de multiplas colunas

Para receber dados de varias colunas, especifica o nome das colunas separados por virgula, como o exemplo abaixo:

```sql
sqlite> select nome, sobrenome from pessoas;
maria|kenan
Dosean|Pesa
```

 - ### Inserindo mais dados na tabela

```sql
sqlite> INSERT INTO pessoas(id, nome, sobrenome, email)
   ...> VALUES(3, 'Redtine', 'Maxred', 'redtine@email.com');
sqlite> INSERT INTO pessoas(id, nome, sobrenome, email)
   ...> VALUES(4, 'maria', 'Max', 'maria@email.com');
sqlite> INSERT INTO pessoas(id, nome, sobrenome, email)
   ...> VALUES(5, 'Lafson', 'Fred', 'lafson@email.com');
sqlite> select * from pessoas;
1|maria|kenan|maria@email.com
2|Dosean|Pesa|dosean@email.com
3|Redtine|Maxred|redtine@email.com
4|maria|Max|maria@email.com
5|Lafson|Fred|lafson@email.com
sqlite> 
```

 - ### Limitando o numero de Resultados

Para limitar o numero de resultados use a palavra chave 'limit'

```sql
 sqlite> select * from pessoas limit 3;
1|maria|kenan|maria@email.com
2|Dosean|Pesa|dosean@email.com
3|Redtine|Maxred|redtine@email.com
sqlite> 
```
 - ### Recebendo dados Diferentes

Temos 5 registros no total na nossa tabela

```sql
sqlite> select * from pessoas;
1|maria|kenan|maria@email.com
2|Dosean|Pesa|dosean@email.com
3|Redtine|Maxred|redtine@email.com
4|maria|Max|maria@email.com
5|Lafson|Fred|lafson@email.com
```

Se quisermos mostrar somente registro diferentes utilizamos a palavra chave 'distinct'

```sql
sqlite> select DISTINCT(nome) from pessoas;
maria
Dosean
Redtine
Lafson
```

Perceba que o registro maria foi apresentado somente uma unica vez.

 - ### Final

 Até o proximo Tutorial