<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# 🔍 Filtros em SQL

Os filtros são elementos fundamentais na linguagem SQL que permitem extrair exatamente os dados necessários de um banco de dados. Utilizando a cláusula `WHERE`, podemos definir condições específicas para selecionar, atualizar ou excluir registros.

## Cláusula WHERE

A cláusula `WHERE` especifica as condições que os registros devem atender para serem incluídos nos resultados de uma consulta.

```sql
SELECT coluna1, coluna2, ... 
FROM tabela
WHERE condição;
```

Exemplos básicos:

```sql
-- Selecionar produtos com preço maior que 100
SELECT nome, preco 
FROM produtos 
WHERE preco > 100;

-- Selecionar clientes de uma cidade específica
SELECT nome, email 
FROM clientes 
WHERE cidade = 'São Paulo';

-- Selecionar pedidos realizados em uma data específica
SELECT id, valor_total 
FROM pedidos 
WHERE data_pedido = '2023-05-15';
```

# ⚙️ Operadores Lógicos e de Comparação

## Operadores de Comparação

Os operadores de comparação permitem estabelecer relações entre valores.

| Operador | Descrição | Exemplo |
|----------|-----------|---------|
| = | Igual a | `WHERE idade = 30` |
| <> ou != | Diferente de | `WHERE status <> 'cancelado'` |
| < | Menor que | `WHERE preco < 50.00` |
| > | Maior que | `WHERE estoque > 100` |
| <= | Menor ou igual a | `WHERE data_entrega <= '2023-12-31'` |
| >= | Maior ou igual a | `WHERE avaliacao >= 4` |

```sql
-- Produtos com preço entre 50 e 100
SELECT nome, preco 
FROM produtos 
WHERE preco >= 50 AND preco <= 100;

-- Clientes que não são de São Paulo
SELECT nome, cidade 
FROM clientes 
WHERE cidade <> 'São Paulo';
```

## Operadores Lógicos

Os operadores lógicos permitem combinar múltiplas condições em uma consulta.

### AND

O operador `AND` retorna verdadeiro quando todas as condições separadas são verdadeiras.

```sql
-- Produtos caros com pouco estoque (situação crítica)
SELECT nome, preco, estoque 
FROM produtos 
WHERE preco > 1000 AND estoque < 5;

-- Pedidos recentes com alto valor
SELECT id, data_pedido, valor_total 
FROM pedidos 
WHERE data_pedido >= '2023-01-01' AND valor_total > 500;
```

### OR

O operador `OR` retorna verdadeiro quando pelo menos uma das condições separadas é verdadeira.

```sql
-- Produtos muito baratos ou muito caros
SELECT nome, preco 
FROM produtos 
WHERE preco < 10 OR preco > 1000;

-- Clientes de São Paulo ou Rio de Janeiro
SELECT nome, cidade 
FROM clientes 
WHERE cidade = 'São Paulo' OR cidade = 'Rio de Janeiro';
```

### NOT

O operador `NOT` nega uma condição.

```sql
-- Produtos que não pertencem à categoria 3
SELECT nome, categoria_id 
FROM produtos 
WHERE NOT categoria_id = 3;
-- Equivalente a: WHERE categoria_id <> 3

-- Pedidos que não estão cancelados nem finalizados
SELECT id, status 
FROM pedidos 
WHERE NOT (status = 'cancelado' OR status = 'entregue');
```

## Combinando Operadores Lógicos

É possível combinar vários operadores lógicos em uma única consulta. Use parênteses para estabelecer a ordem de avaliação.

```sql
-- Produtos de determinadas categorias que estão em promoção ou com baixo estoque
SELECT nome, categoria_id, preco, estoque 
FROM produtos 
WHERE (categoria_id = 1 OR categoria_id = 2) 
      AND (preco_promocional IS NOT NULL OR estoque < 10);

-- Clientes VIP ou novos clientes com pedidos grandes
SELECT c.nome, c.data_cadastro, p.valor_total 
FROM clientes c
JOIN pedidos p ON c.id = p.cliente_id
WHERE (c.vip = TRUE) OR 
      (c.data_cadastro >= '2023-01-01' AND p.valor_total > 1000);
```

# 🧩 Operadores Especiais

## BETWEEN

O operador `BETWEEN` seleciona valores dentro de um intervalo inclusivo.

```sql
-- Produtos com preço entre 50 e 100
SELECT nome, preco 
FROM produtos 
WHERE preco BETWEEN 50 AND 100;
-- Equivalente a: WHERE preco >= 50 AND preco <= 100

-- Pedidos realizados no primeiro trimestre de 2023
SELECT id, data_pedido, valor_total 
FROM pedidos 
WHERE data_pedido BETWEEN '2023-01-01' AND '2023-03-31';
```

## IN

O operador `IN` permite especificar múltiplos valores em uma condição.

```sql
-- Produtos das categorias 1, 3 e 5
SELECT nome, categoria_id 
FROM produtos 
WHERE categoria_id IN (1, 3, 5);
-- Equivalente a: WHERE categoria_id = 1 OR categoria_id = 3 OR categoria_id = 5

-- Pedidos com status específicos
SELECT id, status 
FROM pedidos 
WHERE status IN ('aguardando_pagamento', 'pago', 'em_separacao');
```

## LIKE

O operador `LIKE` é usado para busca de padrões em texto, com dois caracteres curinga:
- `%` representa zero, um ou múltiplos caracteres
- `_` representa exatamente um caractere

```sql
-- Produtos cujo nome começa com "Smartphone"
SELECT nome, preco 
FROM produtos 
WHERE nome LIKE 'Smartphone%';

-- Produtos com "gamer" em qualquer parte do nome
SELECT nome, preco 
FROM produtos 
WHERE nome LIKE '%gamer%';

-- Clientes com e-mail do Gmail
SELECT nome, email 
FROM clientes 
WHERE email LIKE '%@gmail.com';

-- Produtos com exatamente 5 caracteres no código
SELECT codigo, nome 
FROM produtos 
WHERE codigo LIKE '_____';
```

## IS NULL / IS NOT NULL

Estes operadores verificam se um valor é nulo ou não.

```sql
-- Produtos sem categoria definida
SELECT nome, categoria_id 
FROM produtos 
WHERE categoria_id IS NULL;

-- Clientes com telefone cadastrado
SELECT nome, telefone 
FROM clientes 
WHERE telefone IS NOT NULL;

-- Produtos sem preço promocional
SELECT nome, preco, preco_promocional 
FROM produtos 
WHERE preco_promocional IS NULL;
```

# 🔄 Subconsultas em Filtros

Subconsultas (subqueries) podem ser usadas dentro da cláusula WHERE para criar filtros mais sofisticados.

```sql
-- Produtos com preço acima da média
SELECT nome, preco 
FROM produtos 
WHERE preco > (SELECT AVG(preco) FROM produtos);

-- Clientes que fizeram pedidos no último mês
SELECT nome, email 
FROM clientes 
WHERE id IN (
    SELECT DISTINCT cliente_id 
    FROM pedidos 
    WHERE data_pedido >= DATE_SUB(CURRENT_DATE, INTERVAL 1 MONTH)
);

-- Produtos que nunca foram vendidos
SELECT nome 
FROM produtos 
WHERE id NOT IN (
    SELECT DISTINCT produto_id 
    FROM itens_pedido
);
```

# 📊 Filtros em Operações de Agregação (HAVING)

A cláusula `HAVING` é usada para filtrar resultados de funções de agregação, enquanto `WHERE` filtra registros individuais.

```sql
-- Categorias com mais de 10 produtos
SELECT categoria_id, COUNT(*) as total_produtos 
FROM produtos 
GROUP BY categoria_id 
HAVING total_produtos > 10;

-- Clientes que gastaram mais de 5000 no total
SELECT cliente_id, SUM(valor_total) as total_gasto 
FROM pedidos 
GROUP BY cliente_id 
HAVING total_gasto > 5000;

-- Produtos vendidos em mais de 100 unidades no último mês
SELECT p.id, p.nome, SUM(ip.quantidade) as unidades_vendidas 
FROM produtos p
JOIN itens_pedido ip ON p.id = ip.produto_id
JOIN pedidos pd ON ip.pedido_id = pd.id
WHERE pd.data_pedido >= DATE_SUB(CURRENT_DATE, INTERVAL 1 MONTH)
GROUP BY p.id, p.nome
HAVING unidades_vendidas > 100;
```

# 🌐 Operadores em Diferentes SGBDs

Alguns operadores têm sintaxe específica dependendo do SGBD:

## Concatenação de Texto

```sql
-- MySQL e PostgreSQL
SELECT CONCAT(nome, ' - ', categoria) FROM produtos;

-- SQL Server
SELECT nome + ' - ' + categoria FROM produtos;

-- Oracle
SELECT nome || ' - ' || categoria FROM produtos;
```

## Diferente

```sql
-- MySQL, PostgreSQL, SQLite
SELECT * FROM produtos WHERE categoria_id <> 3;

-- Alternativa comum a todos
SELECT * FROM produtos WHERE categoria_id != 3;
```

## Comparação de Strings Case-Insensitive

```sql
-- MySQL
SELECT * FROM produtos WHERE nome LIKE '%smartphone%' COLLATE utf8mb4_general_ci;

-- PostgreSQL
SELECT * FROM produtos WHERE nome ILIKE '%smartphone%';

-- SQL Server
SELECT * FROM produtos WHERE nome LIKE '%smartphone%' COLLATE SQL_Latin1_General_CP1_CI_AS;
```

# 💡 Dicas e Boas Práticas

1. **Otimização de Filtros**:
   - Filtre primeiro, depois junte (JOIN)
   - Use índices nas colunas frequentemente filtradas
   - Evite usar funções em colunas no WHERE (impede uso de índices)

```sql
-- Ruim (não usa índice da coluna data)
SELECT * FROM pedidos WHERE YEAR(data_pedido) = 2023;

-- Melhor (pode usar índice)
SELECT * FROM pedidos WHERE data_pedido BETWEEN '2023-01-01' AND '2023-12-31';
```

2. **Evite NOT e OR em bancos de dados muito grandes**:
   - Eles geralmente impedem o uso eficiente de índices
   - Reescreva consultas quando possível

```sql
-- Menos eficiente
SELECT * FROM produtos WHERE categoria_id != 5;

-- Mais eficiente
SELECT * FROM produtos WHERE categoria_id < 5 OR categoria_id > 5;
```

3. **Use subconsultas com critério**:
   - Subconsultas podem ser lentas em grandes conjuntos de dados
   - Prefira JOINs quando possível

```sql
-- Subconsulta (pode ser menos eficiente)
SELECT * FROM clientes 
WHERE id IN (SELECT cliente_id FROM pedidos WHERE valor_total > 1000);

-- JOIN (geralmente mais eficiente)
SELECT DISTINCT c.* FROM clientes c
JOIN pedidos p ON c.id = p.cliente_id
WHERE p.valor_total > 1000;
```

4. **Considere a ordenação de avaliação dos operadores lógicos**:
   - O operador AND é avaliado antes do OR
   - Use parênteses para evitar ambiguidades

```sql
-- Sem parênteses (pode causar resultados inesperados)
SELECT * FROM produtos 
WHERE categoria_id = 1 OR categoria_id = 2 AND preco < 100;

-- Com parênteses (clarifica a intenção)
SELECT * FROM produtos 
WHERE (categoria_id = 1 OR categoria_id = 2) AND preco < 100;
```

5. **Evite o `SELECT *` em produção**:
   - Especifique as colunas necessárias
   - Reduz o tráfego de rede e melhora a performance

```sql
-- Evite em ambiente de produção
SELECT * FROM clientes WHERE cidade = 'São Paulo';

-- Preferível
SELECT id, nome, email, telefone FROM clientes WHERE cidade = 'São Paulo';
```

# 📝 Exemplos Práticos Avançados

## Consulta com Múltiplos Filtros Complexos

```sql
-- Produtos em promoção, com bom estoque, de categorias populares, 
-- que foram vendidos no último mês
SELECT p.id, p.nome, p.preco, p.preco_promocional, p.estoque
FROM produtos p
WHERE p.preco_promocional IS NOT NULL
AND p.estoque >= 20
AND p.categoria_id IN (1, 2, 5, 7)
AND EXISTS (
    SELECT 1 FROM itens_pedido ip
    JOIN pedidos pd ON ip.pedido_id = pd.id
    WHERE ip.produto_id = p.id
    AND pd.data_pedido >= DATE_SUB(CURRENT_DATE, INTERVAL 1 MONTH)
)
ORDER BY (p.preco - p.preco_promocional) DESC;
```

## Filtros Dinâmicos (Exemplo de Aplicação)

Em aplicações reais, muitas vezes construímos consultas SQL dinamicamente:

```sql
-- Pseudocódigo de como construir filtros dinamicamente
DECLARE sql_query TEXT;
SET sql_query = 'SELECT id, nome, preco FROM produtos WHERE 1=1';

IF categoria_id IS NOT NULL THEN
    SET sql_query = CONCAT(sql_query, ' AND categoria_id = ', categoria_id);
END IF;

IF preco_min IS NOT NULL THEN
    SET sql_query = CONCAT(sql_query, ' AND preco >= ', preco_min);
END IF;

IF preco_max IS NOT NULL THEN
    SET sql_query = CONCAT(sql_query, ' AND preco <= ', preco_max);
END IF;

IF nome_busca IS NOT NULL THEN
    SET sql_query = CONCAT(sql_query, " AND nome LIKE '%", nome_busca, "%'");
END IF;

EXECUTE sql_query;
```

## Consulta Segmentada para Relatório de Vendas

```sql
SELECT 
    CASE 
        WHEN p.preco < 100 THEN 'Baixo valor'
        WHEN p.preco BETWEEN 100 AND 500 THEN 'Médio valor'
        WHEN p.preco > 500 THEN 'Alto valor'
        ELSE 'Sem classificação'
    END AS segmento,
    COUNT(DISTINCT ip.pedido_id) AS total_pedidos,
    SUM(ip.quantidade) AS unidades_vendidas,
    SUM(ip.quantidade * ip.preco_unitario) AS valor_total
FROM produtos p
JOIN itens_pedido ip ON p.id = ip.produto_id
JOIN pedidos pd ON ip.pedido_id = pd.id
WHERE pd.status = 'entregue'
AND pd.data_pedido BETWEEN '2023-01-01' AND '2023-12-31'
GROUP BY segmento
ORDER BY valor_total DESC;
```

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/> 