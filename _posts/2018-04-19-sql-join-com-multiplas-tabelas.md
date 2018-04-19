---
layout: post
title:  "SQLite3: JOIN com Multiplas Tabelas"
date:   2018-04-19 02:35:00 -0300
tags: sql, join, db
categories: [sql]
---

- ### SQL Join e Relacionamentos

Voce pode fazer joins com quantas tabelas voce quiser veja o relacionamento das tabelas do nosso exemplo

![Screenshot multiplas_tabelas](/static/img/join/multiplas_tabelas.png)

Primeiro vamos listar todos os dados das tres tabelas

Listando tabela pessoas

```sql
sqlite> select * from pessoas;

1|Marianne Wiegand|basketball|janea@crist.name
2|Domenico Kuhn|lacrosse|itzel@andersonhalvorson.io
3|Duncan Cole|basketball|terrence@wunsch.net
4|Mrs. Dwight Jakubowski|lacrosse|cortez.cartwright@hansensteuber.biz
5|Orval Walter|rugby|arnaldo.willms@monahan.io
6|Novella Beatty|basketball|orin@champlin.co
7|Alba Pfannerstill|rugby|hanna@oberbrunnerhane.com
8|Xander Blick|basketball|bertrand_konopelski@bauch.com
9|Hugh Casper|hockey|andres_gleichner@faheyframi.info
10|Charity Wisoky III|rugby|kareem@gleason.net
```

Listando tabela produtos

```sql
sqlite> select * from produtos;

1|Fantastic Linen Computer|2019-02-12|1
2|Mediocre Steel Chair|2019-04-15|1
3|Enormous Paper Gloves|2017-10-04|1
4|Sleek Granite Wallet|2019-01-23|2
5|Heavy Duty Copper Shirt|2017-05-10|3
6|Sleek Rubber Car|2018-07-14|4
7|Durable Paper Table|2018-12-22|5
8|Enormous Linen Coat|2017-10-24|7
9|Synergistic Wooden Table|2017-10-10|6
10|Enormous Cotton Knife|2018-08-08|7
11|Enormous Leather Keyboard|2018-04-01|7
12|Heavy Duty Linen Knife|2019-01-28|8
13|Lightweight Aluminum Keyboard|2017-11-21|8
14|Rustic Iron Keyboard|2018-02-12|9
15|Rustic Bronze Clock|2017-09-05|10
```

Listando tabela itens

```sql
sqlite> select * from itens;

1||49.91|5|1
2||64.9|3|10
3||81.76|2|2
4||69.84|5|4
5||27.47|3|5
6||0.99|1|8
7||17.43|3|11
8||63.37|3|9
9||91.21|1|3
10||45.84|4|12
11||27.35|2|1
12||7.44|1|13
13||16.76|4|2
14||33.95|1|3
15||29.38|4|6
```

Agora vamos fazer o join das tres tabelas para mostrar o nome da pessoa nome do produto preço do iten quantidade de itens e o pedido = 2.

```sql
sqlite> select n.nome, p.nome, i.quantidade, i.preco, i.pedido
   ...> from pessoas n, produtos p, itens i
   ...> where p.pessoa_id = n.id
   ...> and i.produto_id = p.id
   ...> and pedido = 2;
   
Marianne Wiegand|Mediocre Steel Chair||81.76|2
Marianne Wiegand|Fantastic Linen Computer||27.35|2
```

Eu defini n para pessoas e p para produtos para evitar ambigüidade no momento de definiar as cláusulas where com suas respectivas chaves estrangeiras e chaves primarias ja que pessoas e produtos possuem a coluna nome.

- ### Final

Até o proximo Tutorial.


