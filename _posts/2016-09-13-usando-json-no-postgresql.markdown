---
published: true
title: Usando JSON no PostgreSQL
layout: post
---
No PostgreSQL temos alguns tipos de dados e funções que podem ser muito úteis no nosso dia a dia. Uma delas é o jsonb, existem outros tipos como array, json, hstore. 

O PostgreSQL tem dois tipo de json, o JSON e o JSONB. A principal diferença é que O JSON armazena uma cópia exata do texto, todas as funções de processamento devem fazer um parser para cada execução, enquanto jsonb é armazenado num formato binário decomposto ou já parseado, isso faz com que seja um pouco mais lento a entrada, devido a sobrecarga de conversão adicionado, mas significativamente mais rápida com o processo, uma vez que não é necessário fazer o reparsing. JSONB também suporta indexação, que pode ser uma vantagem significativa.

Existem diversas tecnologias e banco de dados especializados em armazenar dados não estruturados. O PostgreSQL evoluiu muito nesse sentido, para atender essa necessidade cada vez mais real nas empresas de software. Se você quer armazenar apenas json em seu banco de dados, talvez o PostgreSQL não seja adequado para a sua necessidade, mas se você precisa armazenar algum dado especifico da sua aplicação nesse formato, te encorajo fortemente a utilizar o jsonb. As consultas nesses campos tendem a ter um desempenho consideravél. Espero que esses exemplo possam mostrar o quanto o jsonb é funcional e poderoso.

Abaixo vamos ver algumas funções interessantes, que a mairia das pessoas não conhece. 


#Verificando se existe um atributo:

> postgres=# select '{"a":1, "b":2}'::jsonb ? 'b';
 ?column? 
----------
 t
(1 row)


Realizando uma busca:

postgres=# select '{"a":1, "b":2}'::jsonb @> '{"b":2}'::jsonb;
 ?column? 
----------
 t
(1 row)


Removendo a posição 1:

postgres=# select '["a", "b"]'::jsonb - 1;
 ?column? 
----------
 ["a"]
(1 row)


Concatenando:

postgres=# select '["a", "b"]'::jsonb || '["c", "d"]'::jsonb;
       ?column?       
----------------------
 ["a", "b", "c", "d"]
(1 row)


Removendo um atributo: 

postgres=# select '{"a": "b"}'::jsonb - 'a';
 ?column? 
----------
 {}
(1 row)



Retornando tuplas a partir de um jsonb:

postgres=# select * from json_each('{"a":"foo", "b":"foo2"}');
 key | value 
-----+-------
 a   | "foo"
 b   | "foo2"
(2 rows)



Retornando um valor de um jsonb:

postgres=# select json_extract_path_text('{"json1":{"node1":0},"json2":{"node1":666,"node2":"foo"}}','json2', 'node2');
------------------------
 foo
(1 row)


Convertendo jsob para um record:

postgres=# select * from json_to_record('{"column1":1,"column2":[1,2,3],"column3":"foo"}') as x(column1 int, column2 text, column3 text);

 column1 | column2 | column3 
---------+---------+---------
       1 | [1,2,3] | foo
(1 row)