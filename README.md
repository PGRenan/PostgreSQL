# PostgreSQL
 Este projeto faz parte do plano de estudos elaborado pela Alura para o programa de formação Desenvolve (3ª edição), trilha de dados, em parceria com a Boticário.

# PostgreSQL

Criando a estrutura de uma tabela; realizando consultas; adicionando dados no banco; atualizando e apagando dados; filtrando os registros; ordenando os dados; e juntando registros de tabelas.

1. [Primeiros passos com PostgreSQL](#1-primeiros-passos-com-postgresql)
2. [Executando operações CRUD](#2-executando-operações-crud)
3. [Consultas com filtros](#3-consultas-com-filtros)
4. [Trabalhando com relacionamentos](#4-trabalhando-com-relacionamentos)
5. [Usando CASCADE](#5-usando-cascade)
6. [Avançando com consultas](#6-avançando-com-consultas)

Saiba mais sobre o curso [aqui](https://cursos.alura.com.br/course/introducao-postgresql-primeiros-passos) ou acompanhe minhas anotações abaixo. ⬇️

## 1. Primeiros passos com PostgreSQL

### **Criando um banco de dados**

***SGBD utilizado no curso: [PostgreSQL](https://www.postgresql.org/)***

Para criar um banco de dados no PostgreSQL, basta utilizar um dos códigos abaixo:

```sql
CREATE DATABASE alura;

CREATE DATABASE alura
    WITH
    OWNER = postgres
    ENCODING = 'UTF8'
    CONNECTION LIMIT = -1;
```

Já para excluir um banco de dados, utiliza-se o `DROP DATABASE alura;`.

### **Criando uma tabela**

Antes de criar uma tabela, adicionando suas colunas, é necessário saber que tipos de dados elas receberão. Na documentação do PostgreSQL existe uma lista com principais tipo de dados que as colunas das tabelas recebem. Veja a lista [aqui](https://www.postgresql.org/docs/current/datatype.html) e os tipos mais comuns com suas características, logo abaixo:

Tipo      | Descrição
--------- | -------------------------------
integer   | números inteiros
serial    | números inteiros com incremento
real      | números decimais fixos
numeric   | números decimais variáveis
char      | textos fixos
varchar   | textos variáveis
text      | textos longos
boolean   | verdadeiro ou falso
date      | data do calendário
time      | hora do dia
timestamp | data e hora

```sql
CREATE TABLE aluno (
	id SERIAL,
	nome VARCHAR(255),
	cpf CHAR(11),
	observacao TEXT,
	idade INTEGER,
	dinheiro NUMERIC(10,2),
	altura REAL,
	ativo BOOLEAN,
	data_nascimento DATE,
	hora_aula TIME,
	matricula TIMESTAMP
);
```

Após criar a tabela é possível visualizá-la com o comando `SELECT * FROM aluno;`. Mesmo que ainda sem dados inseridos, pode-se visualizar as colunas criadas.

## 2. Executando operações CRUD

### **Incluindo um registro na tabela**

O comando `INSERT INTO ... VALUES (...)` insere dados na tabela. Isso pode ser feito declarando as colunas e seus respectivos valores ou apenas declarando os valores na ordem em que aparecem na tabela.

```sql
INSERT INTO aluno (
    nome,
    cpf,
    observacao,
    idade,
    dinheiro,
    altura,
    ativo,
    data_nascimento,
    hora_aula,
    matricula
) VALUES (
    'Diogo',
    '12345678901',
    'Mussum Ipsum, cacilds vidis litro abertis. Quem manda na minha terra sou euzis!Nec orci ornare consequat. Praesent lacinia ultrices consectetur. Sed non ipsum felis.Admodum accumsan disputationi eu sit. Vide electram sadipscing et per.Em pé sem cair, deitado sem dormir, sentado sem cochilar e fazendo pose.',
    35,
    100.50,
    1.81,
    TRUE,
    '1984-08-27',
    '17:30:00',
    '2020-02-08 12:32:45'
);
```

### **Atualizando um registro na tabela**

A atualização serve para modificar os valores de uma linha e recebe o comando `UPDATE ... SET ... WHERE ...`. No caso abaixo, todos os dados foram alterados, mas poderia ser apenas um. Importante indicar que registro da tabela será alterado.

```sql
UPDATE aluno SET
    nome = 'Nico',
    cpf = '10987654321',
    observacao = 'Teste',
    idade = 38,
    dinheiro = 15.23,
    altura = 1.90,
    ativo = FALSE,
    data_nascimento = '1980-01-15',
    hora_aula = '13:00:00',
    matricula = '2020-01-02 15:00:00'
WHERE id = 1;
```

### **Excluindo um registro da tabela**

No caso de querer excluir um registro, pode-se usar o comando `DELETE FROM ... WHERE ...`, sendo imprescindível indicar que registro será deletado, pois sem o where, todos os registros serão excluídos.

```sql
DELETE FROM aluno WHERE nome = 'Nico';
```

## 3. Consultas com filtros

### **Selecionando colunas específicas da tabela**

Além de consultar todos os registros de uma tabela, é possível selecionar que campos retornar na consulta. Basta indicar o nome da coluna no lugar do asterisco.

```sql
-- Consultando 1 campo específico
SELECT nome FROM aluno;

-- Consultando 2 e 3 campos
SELECT nome, idade FROM aluno;
SELECT nome, idade, matricula FROM aluno;

-- Consultando e renomeando campos
SELECT
	nome "Nome do aluno",
	idade,
	matricula AS quando_se_matriculou
FROM aluno;
```

No último exemplo acima foram utilizadas duas formas de renomear as colunas de uma tabela, ao realizar uma consulta. Quando colocado entre aspas duplas, pode-se dar espaço entre as palavras. Caso não use as aspas, deve-se unir as palavras com underscore. O comando `AS` é responsável pela ação de renomear.

Ps. O nome dos campos na tabela permanece o mesmo, apenas o nome da coluna na consulta será alterado.

### **Filtrando registros de campos do tipo texto**

O filtro `WHERE` pode ser utilizado de diversas formas. Pode-se usar tanto operadores quanto outros comandos para complementar a consulta.

```sql
-- Inserindo mais registros na tabela para trabalhar tais consultas
INSERT INTO aluno (nome) VALUES ('Vinicius Dias');
INSERT INTO aluno (nome) VALUES ('Nico Steppat');
INSERT INTO aluno (nome) VALUES ('João Roberto');
INSERT INTO aluno (nome) VALUES ('Diego');

-- Filtrando nomes com operadores
SELECT * FROM aluno WHERE nome = 'Diogo';   -- igual
SELECT * FROM aluno WHERE nome != 'Diogo';  -- diferente
SELECT * FROM aluno WHERE nome <> 'Diogo';  -- diferente também

-- Filtrando nomes com like
SELECT * FROM aluno WHERE nome LIKE 'Diogo';     -- semelhante a
SELECT * FROM aluno WHERE nome NOT LIKE 'Diogo'; -- diferente de

-- Filtrando nomes complementos do like
SELECT * FROM aluno WHERE nome LIKE 'Di_go';    -- qualquer letra onde está o underscore
SELECT * FROM aluno WHERE nome LIKE 'D%';       -- qualquer nome que comece com D
SELECT * FROM aluno WHERE nome LIKE '%s';       -- qualquer nome que termine com s
SELECT * FROM aluno WHERE nome LIKE '% %';      -- qualquer nome que contenha espaço
SELECT * FROM aluno WHERE nome LIKE '%i%a%';    -- quando nome que possua i e a em quaquer posição
```

### **Filtrando registros de campos do tipo numérico, data e booleano**

Quando um registro é inserido com valores nulos, ou seja, não é inserido determinado campo do registro e ele fica em branco, pode-se consultar esses valores ou o inverso filtrando da seguinte forma:

    SELECT * FROM aluno WHERE cpf IS NULL;
    SELECT * FROM aluno WHERE cpf IS NOT NULL;

No caso de consultas com registros de outros tipos que não texto, pode ser utilizar outros operadores e comandos para realizar a consulta como, por exemplo, maior e menor que, e verdadeiro ou falso.

```sql
-- Filtrando se existe valor igual, maior, menor ou diferente do valor da consulta
SELECT * FROM aluno WHERE nome = 35;
SELECT * FROM aluno WHERE nome <> 35;
SELECT * FROM aluno WHERE nome != 35;
SELECT * FROM aluno WHERE nome >= 35;
SELECT * FROM aluno WHERE nome <= 35;
SELECT * FROM aluno WHERE nome > 35;
SELECT * FROM aluno WHERE nome < 35;

-- Filtrando uma faixa de valores
SELECT * FROM aluno WHERE nome BETWEEN 30 AND 40;

-- Filtrando se o registro é verdadeiro ou falso
SELECT * FROM aluno WHERE ativo = TRUE;
SELECT * FROM aluno WHERE ativo = FALSE;

-- Filtrando se existe tal registro no campo
SELECT * FROM aluno WHERE ativo IS NULL;
```

### **Filtrando utilizando operadores E e OU**

Os operadores E e OU servem para comparar registros ao realizar consultas. Eles funcionam como a tabela verdade e para cada comparação, trazem um resultado diferente.

```sql
-- O nome precisa começar com D e o CPF não pode ser nulo
SELECT * FROM aluno WHERE
    nome LIKE 'D%' AND
    cpf IS NOT NULL;

-- Vai trazer um registro ou outro, se ambos estiverem, traz o que aparecer primeiro
SELECT * FROM aluno WHERE
	nome LIKE 'Diogo' OR
	nome LIKE 'Rodrigo';

-- Traz registros que contenham as duas cláusulas
SELECT * FROM aluno WHERE
	nome LIKE 'Nico' AND
	nome LIKE 'Steppat';
```

***Conjunção (E) na tabela verdade:***

A     | B     | A^B
:---: | :---: | :---:
V     | V     | V
V     | F     | F
F     | V     | F
F     | F     | F

***Disjunção (OU) na tabela verdade:***

A     | B     | AvB
:---: | :---: | :---:
V     | V     | V
V     | F     | V
F     | V     | V
F     | F     | F

## 4. Trabalhando com relacionamentos

### **Criando tabela com chave primária**

Ao criar uma tabela e definir seus campos, alguns comandos podem ser atribuídos às colunas para adicionar características que diferenciem os registros da tabela.

```sql
-- Criando uma tabela e inserindo dados nulos
CREATE TABLE curso (
	id INTEGER,
	nome VARCHAR(255)
);

INSERT INTO curso (id, nome) VALUES (NULL, NULL);

SELECT * FROM curso;

-- Recriando a tabela, impedindo inserção de dados nulos
DROP TABLE curso;

CREATE TABLE curso (
	id INTEGER NOT NULL,
	nome VARCHAR(255) NOT NULL
);

INSERT INTO curso (id, nome) VALUES (1, 'HTML');
INSERT INTO curso (id, nome) VALUES (1, 'CSS');

SELECT * FROM curso;

-- Definindo o campo id como único, impedindo repetição
DROP TABLE curso;

CREATE TABLE curso (
	id INTEGER NOT NULL UNIQUE,
	nome VARCHAR(255) NOT NULL
);

INSERT INTO curso (id, nome) VALUES (1, 'HTML');
INSERT INTO curso (id, nome) VALUES (2, 'CSS');
INSERT INTO curso (id, nome) VALUES (3, 'JavaScript');

SELECT * FROM curso;

-- Definindo o campo id como chave primária
DROP TABLE curso;

CREATE TABLE curso (
	id INTEGER NOT NULL PRIMARY KEY,
	nome VARCHAR(255) NOT NULL
);

INSERT INTO curso (id, nome) VALUES (1, 'HTML');
INSERT INTO curso (id, nome) VALUES (2, 'CSS');
INSERT INTO curso (id, nome) VALUES (3, 'JavaScript');

SELECT * FROM curso;
```

A chave primária serve para relacionar uma tabela com outra, o campo que recebe a chave primária será o conector dos registros. Podendo, assim, relacionar a tabela aluno com a tabela curso.

### **Criando tabela com chave estrangeira**

A chave estrangeira serve para extender a ligação entre tabelas. A chave primária de uma tabela é utilizada em outra tabela para referenciar um registro.

```sql
-- Recriando a tabela aluno com chave primária
DROP TABLE aluno;

CREATE TABLE aluno (
	id SERIAL PRIMARY KEY,
	nome VARCHAR(255) NOT NULL
);

INSERT INTO aluno (nome) VALUES ('Diogo');
INSERT INTO aluno (nome) VALUES ('Vinicius');

-- Criando uma tabela que relaciona cursos com alunos
CREATE TABLE aluno_curso (
	aluno_id INTEGER,
	curso_id INTEGER,
	PRIMARY KEY (aluno_id, curso_id)
);

INSERT INTO aluno_curso (aluno_id, curso_id) VALUES (1,1);
INSERT INTO aluno_curso (aluno_id, curso_id) VALUES (2,1);

SELECT * FROM aluno WHERE id = 1;
SELECT * FROM aluno WHERE id = 2;
SELECT * FROM curso WHERE id = 1;

-- Recriando a tabela, adicionando chaves estrangeiras
DROP TABLE aluno_curso;

CREATE TABLE aluno_curso (
	aluno_id INTEGER,
	curso_id INTEGER,
	PRIMARY KEY (aluno_id, curso_id),
	
	FOREIGN KEY (aluno_id) REFERENCES aluno (id),
	FOREIGN KEY (curso_id) REFERENCES curso (id)
);

INSERT INTO aluno_curso (aluno_id, curso_id) VALUES (1,1);
INSERT INTO aluno_curso (aluno_id, curso_id) VALUES (2,1);

-- Se não existe o aluno 3 na tabela aluno, retorna erro
INSERT INTO aluno_curso (aluno_id, curso_id) VALUES (3,1);
```

### **Consultas com relacionamentos**

As consultas com relacinamento são feitas a partir do comando `JOIN ... ON ...`. Ele serve para unir os registros de uma tabela e criar relatórios mais precisos com os dados obtidos.

```sql
-- Inserindo um aluno existente em outro curso
INSERT INTO aluno_curso (aluno_id, curso_id) VALUES (2,2);

-- Consultando registros com a união das tabelas existentes
SELECT
	aluno.nome as "Nome do aluno",
	curso.nome as "Nome do curso"
FROM aluno
JOIN aluno_curso
	ON aluno_curso.aluno_id = aluno.id
JOIN curso
	ON curso.id = aluno_curso.curso_id;
```

### **Left, Right, Cross e Full joins**

Outros tipos de junções trazem resultados diferentes nas consultas. Eles são utilizados quando se quer dados mais precisos ou quando se quer filtrar registros específicos.

```sql
-- Pega o nome dos alunos e os cursos que eles fazem ou se não fazem
SELECT aluno.nome as "Nome do aluno", curso.nome as "Nome do curso"
FROM aluno
LEFT JOIN aluno_curso ON aluno_curso.aluno_id = aluno.id
LEFT JOIN curso ON curso.id = aluno_curso.curso_id;

-- Pega os cursos e mostra os alunos que fazem cada um
SELECT aluno.nome as "Nome do aluno", curso.nome as "Nome do curso"
FROM aluno
RIGHT JOIN aluno_curso ON aluno_curso.aluno_id = aluno.id
RIGHT JOIN curso ON curso.id = aluno_curso.curso_id;

-- Pega tudo, ambos alunos e cursos
SELECT aluno.nome as "Nome do aluno", curso.nome as "Nome do curso"
FROM aluno
FULL JOIN aluno_curso ON aluno_curso.aluno_id = aluno.id
FULL JOIN curso ON curso.id = aluno_curso.curso_id;

-- Mostra todas as possibilidades de cursos que cada um pode fazer
SELECT aluno.nome as "Nome do aluno", curso.nome as "Nome do curso"
FROM aluno
CROSS JOIN curso;
```

## 5. Usando CASCADE

### **DELETE CASCADE**

O cascade altera a forma de trabalhar as chaves estrangeiras de modo que seja possível deletar registros de ambas as tabelas pela chave primária. No modo restrito isso não é possível, pois não se consegue excluir apenas de uma tabela, retornando erro.

```sql
DROP TABLE aluno_curso;

CREATE TABLE aluno_curso (
	aluno_id INTEGER,
	curso_id INTEGER,
	PRIMARY KEY (aluno_id, curso_id),
	
	FOREIGN KEY (aluno_id) REFERENCES aluno (id) ON DELETE CASCADE,
	FOREIGN KEY (curso_id) REFERENCES curso (id) ON DELETE CASCADE
);

INSERT INTO aluno_curso (aluno_id, curso_id) VALUES (1,1);
INSERT INTO aluno_curso (aluno_id, curso_id) VALUES (2,1);
INSERT INTO aluno_curso (aluno_id, curso_id) VALUES (3,1);
INSERT INTO aluno_curso (aluno_id, curso_id) VALUES (1,3);

SELECT * FROM aluno_curso;

DELETE FROM aluno WHERE id = 1;

SELECT
	aluno.nome as "Nome do aluno",
	curso.nome as "Nome do curso"
FROM aluno
JOIN aluno_curso
	ON aluno_curso.aluno_id = aluno.id
JOIN curso
	ON curso.id = aluno_curso.curso_id;
```

### **UPDATE CASCADE**

Assim como no delete cascade, o update cascade reflete as alterações de uma tabela em outra que esteja relacionada pela chave estrangeira.

```sql
DROP TABLE aluno_curso;

CREATE TABLE aluno_curso (
	aluno_id INTEGER,
	curso_id INTEGER,
	PRIMARY KEY (aluno_id, curso_id),
	
	FOREIGN KEY (aluno_id) REFERENCES aluno (id)
	ON DELETE CASCADE
	ON UPDATE CASCADE,
	
	FOREIGN KEY (curso_id) REFERENCES curso (id)
	ON DELETE CASCADE
	ON UPDATE CASCADE
);

INSERT INTO aluno_curso (aluno_id, curso_id) VALUES (2,1);
INSERT INTO aluno_curso (aluno_id, curso_id) VALUES (3,1);

SELECT * FROM aluno_curso;

SELECT
	aluno.id AS "ID do aluno",
	aluno.nome AS "Nome do aluno",
	curso.id AS "ID do curso",
	curso.nome AS "Nome do curso"
FROM aluno
JOIN aluno_curso
	ON aluno_curso.aluno_id = aluno.id
JOIN curso
	ON curso.id = aluno_curso.curso_id;

UPDATE aluno SET id = 10 WHERE id = 2;
```

## 6. Avançando com consultas

### **Ordenando as consultas**

O comando `ORDER BY ...` organiza os registros em ordem ascendente ou descendente e de acordo com o nome ou posição da coluna. Além disso, ordena também de acordo com a tabela, caso haja junção entre duas ou mais tabelas na consulta em questão. Sendo possível, também, ordernar o registro de várias colunas.

```sql
CREATE TABLE funcionario (
	id SERIAL PRIMARY KEY,
	matricula VARCHAR(10),
	nome VARCHAR(255),
	sobrenome VARCHAR(255)
);

INSERT INTO funcionario (matricula, nome, sobrenome) VALUES ('M001', 'Diogo', 'Mascarenhas');
INSERT INTO funcionario (matricula, nome, sobrenome) VALUES ('M002', 'Vinícius', 'Dias');
INSERT INTO funcionario (matricula, nome, sobrenome) VALUES ('M003', 'Nico', 'Steppat');
INSERT INTO funcionario (matricula, nome, sobrenome) VALUES ('M004', 'João', 'Roberto');
INSERT INTO funcionario (matricula, nome, sobrenome) VALUES ('M005', 'Diogo', 'Mascarenhas');
INSERT INTO funcionario (matricula, nome, sobrenome) VALUES ('M006', 'Alberto', 'Martins');

-- Ordenando pelo nome da coluna
SELECT * FROM funcionario ORDER BY nome;
SELECT * FROM funcionario ORDER BY nome DESC;
SELECT * FROM funcionario ORDER BY nome, matricula;

-- Ordenando pela posição da coluna
SELECT * FROM funcionario ORDER BY 4;
SELECT * FROM funcionario ORDER BY 3, 4, 2;

-- Mesclando a ordenação
SELECT * FROM funcionario ORDER BY 4 DESC, nome DESC, 2 ASC;

-- Ordenando de acordo com a tabela
SELECT * FROM funcionario ORDER BY funcionario.nome;
```

### **Limitando as consultas**

É possível limitar uma consulta e existem várias formas para isso. O comando `LIMIT` é uma delas, ele traz a quantidade de registros indicados e nada mais que isso. É possível ainda combinar ele com o comando `OFFSET` para determinar que ponto esse limite irá começar a contar.

```sql
-- Traz todos os registros ordenado pela coluna nome
SELECT * FROM funcionario ORDER BY nome;

-- Traz apenas os 5 primeiros registros ordernados por nome
SELECT * FROM funcionario ORDER BY nome LIMIT 5;

-- Traz 5 registros ordenados por nome a partir da posição 0 e da posição 3
SELECT * FROM funcionario ORDER BY id LIMIT 5 OFFSET 0;
SELECT * FROM funcionario ORDER BY id LIMIT 5 OFFSET 3;
```

### **Funções de agregação**

As funções de agregação realizam operações que agrupam dados nas consultas para trazer valores específicos nos registros.

- `COUNT` Retorna a quantidade de registros
- `SUM` Retorna a soma dos registros
- `MAX` Retorna o maior valor dos registros
- `MIN` Retorna o menor valor dos registros
- `AVG` Retorna a média dos registros

```sql
SELECT
	COUNT(id),
	SUM(id),
	MAX(id),
	MIN(id),
	AVG(id),
	ROUND(AVG(id),2),
	ROUND(AVG(id),0)
FROM funcionario;
```

O comando `ROUND` arredonda um valor que está retornando uma dízima periódica para quantas casas decimais for definida em seu uso. Podendo retornar, inclusive, um número inteiro no registro.

Outras funções de agregação podem ser vistas [aqui](https://www.postgresql.org/docs/12/functions-aggregate.html).

### **Agrupando consultas**

É possível agrupar o registros de uma consulta de duas formas. A primeira é trazendo valores distintos, a segundo é agrupando pela coluna.

```sql
-- O distinct traz valores repetidos em um único registro
SELECT DISTINCT nome FROM funcionario ORDER BY nome;
SELECT DISTINCT nome, sobrenome FROM funcionario ORDER BY nome;

-- Não é possível contar os registros sem agrupá-los antes
SELECT nome, COUNT(id) FROM funcionario GROUP BY nome ORDER BY nome;

-- É necessário agrupar todas as colunas que foram definidas na consulta
SELECT nome, sobrenome, COUNT(id) FROM funcionario GROUP BY 1, 2 ORDER BY nome;

-- Essa consulta retornará os cursos a quantidade de alunos matriculados neles
SELECT curso.nome, COUNT(aluno.id) FROM aluno
JOIN aluno_curso ON aluno.id = aluno_curso.aluno_id
JOIN curso ON curso.id = aluno_curso.curso_id
GROUP BY 1 ORDER BY 1;
```

### **Filtrando consultas agrupadas**

Quando se quer filtrar campos, utiliza-se o comando `WHERE`. Porém quando se quer filtrar funções de agregação, utiliza-se o comando `HAVING`.

```sql
-- Traz registros sem alunos
SELECT curso.nome, COUNT(aluno.id) FROM curso
LEFT JOIN aluno_curso ON aluno_curso.curso_id = curso.id
LEFT JOIN aluno ON aluno.id = aluno_curso.aluno_id
GROUP BY 1 HAVING COUNT(aluno.id) = 0;

-- Traz registros com pelo menos 1 aluno
SELECT curso.nome, COUNT(aluno.id) FROM curso
LEFT JOIN aluno_curso ON aluno_curso.curso_id = curso.id
LEFT JOIN aluno ON aluno.id = aluno_curso.aluno_id
GROUP BY 1 HAVING COUNT(aluno.id) > 0;

-- Conta funcionarios com nomes únicos
SELECT nome, COUNT(id) FROM funcionario
GROUP BY nome HAVING COUNT(id) = 1;

-- Conta funcionarios com nome duplicado
SELECT nome, COUNT(id) FROM funcionario
GROUP BY nome HAVING COUNT(id) > 1;
```

⬆️ [Voltar ao topo](#postgresql) ⬆️