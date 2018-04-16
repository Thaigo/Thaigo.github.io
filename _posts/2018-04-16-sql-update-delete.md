---
layout: post
title:  "SQLite3: Update e Delete"
date:   2018-04-16 01:45:00 -0300
tags: sql, update, delete, db
categories: [sql]
---

- ### Update

Vamos aprender como atualizar dados da tabela usando o **update**. Primeiro fazemos um select na tabela para mostrar todos os dados

```sql
sqlite> select * from pessoas;

1|Freddie Turcotte DDS|football|clint.predovic@schowalter.io
2|Kathryne Kuphal|soccer|jaime@swift.biz
3|Madelynn MacGyver III|hockey|magnolia_bogan@wunsch.info
4|Roselyn Cartwright PhD|baseball|weston@friesentrantow.net
5|Mr. Narciso Kihn|football|tavares.gleason@adams.io
6|Trisha Ruecker|baseball|ervin.gibson@turcottekub.name
7|Miss Billy Jaskolski|lacrosse|laila@nienow.co
8|Miss Allie Lehner|lacrosse|noble.douglas@hammes.co
9|Brandyn Conn|soccer|mellie.wisoky@wisozk.name
10|Carmen Halvorson|soccer|breana_kaulke@conneichmann.name
sqlite> 
```

E agora vamos atualizar nome da pessoa do id = 10

```sql
sqlite> update pessoas
   ...> set nome = 'maria'
   ...> where id = 10;
```

Vamos fazer um select para mostra o nome da pessoa com id = 10 para confirmar o **update**

```sql
sqlite> select nome from pessoas
   ...> where id = 10;
maria
```

Agora vamos atualizar duas colunas da tabela pessoas

```sql
sqlite> update pessoas
   ...> set nome = 'Joao', time = 'Volei'
   ...> where id = 1;
```

Realizado o select para mostrar o resultado

```sql
sqlite> select * from pessoas
   ...> where id = 1;
1|Joao|Volei|clint.predovic@schowalter.io
```

- ### Delete

O **delete** é usado para remover os registros da tabela. Vamos realizar um select para mostrar todos os registros

```sql
sqlite> select * from pessoas;

1|Joao|Volei|clint.predovic@schowalter.io
2|Kathryne Kuphal|soccer|jaime@swift.biz
3|Madelynn MacGyver III|hockey|magnolia_bogan@wunsch.info
4|Roselyn Cartwright PhD|baseball|weston@friesentrantow.net
5|Mr. Narciso Kihn|football|tavares.gleason@adams.io
6|Trisha Ruecker|baseball|ervin.gibson@turcottekub.name
7|Miss Billy Jaskolski|lacrosse|laila@nienow.co
8|Miss Allie Lehner|lacrosse|noble.douglas@hammes.co
9|Brandyn Conn|soccer|mellie.wisoky@wisozk.name
10|maria|soccer|breana_kaulke@conneichmann.name
sqlite> 
```

Vamos Remover o primeiro dado da tabela

```sql
sqlite> delete from pessoas
   ...> where id = 1;

sqlite> select * from pessoas;

2|Kathryne Kuphal|soccer|jaime@swift.biz
3|Madelynn MacGyver III|hockey|magnolia_bogan@wunsch.info
4|Roselyn Cartwright PhD|baseball|weston@friesentrantow.net
5|Mr. Narciso Kihn|football|tavares.gleason@adams.io
6|Trisha Ruecker|baseball|ervin.gibson@turcottekub.name
7|Miss Billy Jaskolski|lacrosse|laila@nienow.co
8|Miss Allie Lehner|lacrosse|noble.douglas@hammes.co
9|Brandyn Conn|soccer|mellie.wisoky@wisozk.name
10|maria|soccer|breana_kaulke@conneichmann.name
sqlite> 
```

Perceba que o primeiro registro com nome joão foi excluido com sucesso.

- ### Final

Até o proximo Tutorial.
