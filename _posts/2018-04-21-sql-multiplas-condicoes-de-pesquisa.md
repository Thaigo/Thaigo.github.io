---
layout: post
title:  "SQLite3: Pesquisas Avançadas"
date:   2018-04-21 03:20:00 -0300
tags: sql, where, operator, db
categories: [sql]
---

- ### O Operador **NOT**

O **NOT** é um operador de negação que sempre é usando em conjunto com outro operador

```sql
sqlite> select * from produtos where NOT pessoa_id = 1;

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
Recebemos uma lista sem a pessoa com ID = 1


- ### O Operador **IN**

O operador **IN** é usado para especifica um range de valores dentro de uma condição **WHERE**

```sql
sqlite> select pessoa_id, nome from produtos where pessoa_id IN (3,4,5);

3|Heavy Duty Copper Shirt
4|Sleek Rubber Car
5|Durable Paper Table
```

- ### O Operador **OR**

O Operador **OR** é usado para recuperar os valores que correspondem a qualquer condição dentro da cláusula **WHERE**

```sql
select data from produtos where pessoa_id = 7 OR pessoa_id = 8;

2017-10-24
2018-08-08
2018-04-01
2019-01-28
2017-11-21
```

- ### O Operador **AND** com **OR** 

Um exemplo para combinar valores logicos.

```sql
select id, pessoa_id, nome, data
   ...> from produtos
   ...> where (pessoa_id = 10 OR pessoa_id = 11)
   ...> and data > 2018-04-01;

15|10|Rustic Bronze Clock|2017-09-05
```

- ### O Operador **AND**

O operador **AND** mostra os valores se a condição dentro do **WHERE** for verdadeiro

```sql
select * from produtos where pessoa_id = 1 and data > 0;

1|Fantastic Linen Computer|2019-02-12|1
2|Mediocre Steel Chair|2019-04-15|1
3|Enormous Paper Gloves|2017-10-04|1
```
- ### Final

Até o proximo Tutorial.

