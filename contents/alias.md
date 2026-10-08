<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# Alias (Apelidos) em SQL

## O que são Alias?

**Alias** (apelidos) são nomes temporários atribuídos a tabelas, colunas ou expressões em uma consulta SQL. Eles são úteis para:

- Tornar consultas mais legíveis e concisas
- Simplificar referências a nomes longos
- Evitar ambiguidades em consultas com múltiplas tabelas
- Fornecer nomes significativos para colunas calculadas

## Sintaxe de Alias

### Alias para Colunas

Existem duas formas de definir alias para colunas:

```sql
-- Usando a palavra-chave AS (recomendado)
SELECT nome AS nome_completo FROM clientes;

-- Sem a palavra-chave AS (mais conciso)
SELECT nome nome_completo FROM clientes;
```

Se o alias contiver espaços ou caracteres especiais, use aspas:

```sql
-- Com aspas para espaços ou caracteres especiais
SELECT nome AS "Nome do Cliente", 
       data_nascimento AS "Data de Nascimento" 
FROM clientes;
```

### Alias para Tabelas

Alias para tabelas são especialmente úteis em joins:

```sql
-- Usando AS (recomendado)
SELECT c.nome, p.descricao
FROM clientes AS c
JOIN pedidos AS p ON c.id = p.cliente_id;

-- Sem AS (mais conciso)
SELECT c.nome, p.descricao
FROM clientes c
JOIN pedidos p ON c.id = p.cliente_id;
```

## Aplicações Práticas

### 1. Simplificando Consultas com Joins

Alias de tabelas tornam joins muito mais legíveis:

```sql
-- Sem alias (verboso e repetitivo)
SELECT clientes.nome, pedidos.codigo, itens_pedido.quantidade, produtos.descricao
FROM clientes
JOIN pedidos ON clientes.id = pedidos.cliente_id
JOIN itens_pedido ON pedidos.id = itens_pedido.pedido_id
JOIN produtos ON itens_pedido.produto_id = produtos.id;

-- Com alias (mais limpo e fácil de ler)
SELECT c.nome, p.codigo, ip.quantidade, prod.descricao
FROM clientes c
JOIN pedidos p ON c.id = p.cliente_id
JOIN itens_pedido ip ON p.id = ip.pedido_id
JOIN produtos prod ON ip.produto_id = prod.id;
```

### 2. Usando Alias em Expressões Calculadas

Alias são essenciais para nomear colunas resultantes de cálculos:

```sql
SELECT 
    nome,
    preco,
    quantidade,
    preco * quantidade AS valor_total,
    (preco * quantidade) * 0.9 AS valor_com_desconto
FROM produtos;
```

### 3. Usando Alias com Funções de Agregação

```sql
SELECT 
    departamento,
    COUNT(*) AS total_funcionarios,
    AVG(salario) AS media_salarial,
    MAX(salario) AS maior_salario,
    MIN(salario) AS menor_salario
FROM funcionarios
GROUP BY departamento;
```

### 4. Auto-Joins com Alias

Quando precisamos unir uma tabela a ela mesma, os alias são obrigatórios:

```sql
-- Encontrar funcionários e seus gerentes
SELECT 
    f.nome AS funcionario, 
    g.nome AS gerente
FROM funcionarios f
JOIN funcionarios g ON f.gerente_id = g.id;
```

### 5. Subqueries com Alias

```sql
SELECT p.nome, p.categoria, 
       (SELECT AVG(preco) FROM produtos WHERE categoria = p.categoria) AS media_categoria
FROM produtos p
WHERE p.preco > (SELECT AVG(preco) FROM produtos);
```

## Boas Práticas

1. **Use nomes de alias significativos** que representem o que a tabela ou coluna contém
2. **Seja consistente** com seus alias ao longo da consulta
3. **Use alias curtos mas descritivos** para tabelas (ex: 'c' para clientes, 'p' para produtos)
4. **Sempre use AS** para maior clareza (embora seja opcional)
5. **Prefira aspas duplas** para alias com espaços
6. **Qualifique colunas** com o alias da tabela para evitar ambiguidades

## Erros Comuns

- Esquecer de qualificar colunas com o mesmo nome em diferentes tabelas
- Usar alias muito genéricos (a, b, c) que não comunicam o propósito
- Referenciar um alias da cláusula SELECT dentro de um WHERE (não funciona em todas as implementações)
- Tentar usar o alias definido no SELECT em uma cláusula GROUP BY ou WHERE (não suportado por todos os bancos de dados)

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/>