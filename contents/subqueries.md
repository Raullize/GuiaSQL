<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# Subqueries (Subconsultas)

Uma subconsulta (ou subquery) é uma consulta SQL inserida dentro de outra consulta. Elas permitem operações mais complexas e flexíveis, possibilitando usar o resultado de uma consulta como entrada para outra consulta.

## Características Principais

- Aparecem dentro de parênteses `( )`
- Podem retornar um único valor, uma linha, múltiplas linhas ou uma tabela
- Podem ser utilizadas em diversas partes de uma consulta SQL: SELECT, FROM, WHERE, HAVING
- Geralmente executadas antes da consulta principal (de dentro para fora)

## Tipos de Subconsultas

### 1. Subconsulta Escalar (retorna um único valor)

```sql
-- Produtos com preço acima da média
SELECT nome, preco
FROM produtos
WHERE preco > (SELECT AVG(preco) FROM produtos);

-- Pedido mais recente
SELECT *
FROM pedidos
WHERE data_pedido = (SELECT MAX(data_pedido) FROM pedidos);
```

### 2. Subconsulta de Linha (retorna uma única linha com múltiplas colunas)

```sql
-- Produtos com mesmo preço e categoria que o produto 'Notebook Dell XPS'
SELECT nome, preco, categoria_id
FROM produtos
WHERE (preco, categoria_id) = (
    SELECT preco, categoria_id
    FROM produtos
    WHERE nome = 'Notebook Dell XPS'
) AND nome <> 'Notebook Dell XPS';
```

### 3. Subconsulta de Múltiplas Linhas (retorna uma lista de valores)

```sql
-- Produtos que estão em pedidos feitos no último mês
SELECT id, nome, preco
FROM produtos
WHERE id IN (
    SELECT DISTINCT produto_id
    FROM itens_pedido
    JOIN pedidos ON itens_pedido.pedido_id = pedidos.id
    WHERE data_pedido >= DATE_SUB(CURRENT_DATE, INTERVAL 1 MONTH)
);

-- Clientes que nunca fizeram pedidos
SELECT id, nome, email
FROM clientes
WHERE id NOT IN (
    SELECT DISTINCT cliente_id
    FROM pedidos
);
```

### 4. Subconsulta de Tabela (retorna uma tabela derivada)

```sql
-- Top 3 produtos mais vendidos por categoria
SELECT c.nome AS categoria, p.nome AS produto, tv.quantidade_vendida
FROM categorias c
JOIN produtos p ON c.id = p.categoria_id
JOIN (
    SELECT produto_id, SUM(quantidade) AS quantidade_vendida
    FROM itens_pedido
    GROUP BY produto_id
) tv ON p.id = tv.produto_id
WHERE (
    c.id, tv.quantidade_vendida
) IN (
    SELECT p2.categoria_id, SUM(ip.quantidade) AS qtd
    FROM produtos p2
    JOIN itens_pedido ip ON p2.id = ip.produto_id
    GROUP BY p2.categoria_id, p2.id
    ORDER BY p2.categoria_id, qtd DESC
    LIMIT 3
)
ORDER BY c.nome, tv.quantidade_vendida DESC;
```

# Onde Usar Subconsultas

## Na Cláusula SELECT

```sql
-- Mostrar quanto cada produto está acima/abaixo do preço médio
SELECT 
    nome,
    preco,
    (SELECT AVG(preco) FROM produtos) AS preco_medio,
    preco - (SELECT AVG(preco) FROM produtos) AS diferenca_media
FROM produtos
ORDER BY diferenca_media DESC;

-- Mostrar status do estoque para cada produto
SELECT 
    id,
    nome,
    estoque,
    (
        CASE 
            WHEN estoque < (SELECT AVG(estoque) * 0.2 FROM produtos) THEN 'Estoque Crítico'
            WHEN estoque < (SELECT AVG(estoque) * 0.5 FROM produtos) THEN 'Estoque Baixo'
            ELSE 'Estoque Normal'
        END
    ) AS status_estoque
FROM produtos;
```

## Na Cláusula FROM

A subconsulta na cláusula FROM cria uma "tabela derivada" (ou "tabela virtual"):

```sql
-- Resumo de vendas por categoria
SELECT 
    c.nome AS categoria,
    v.total_produtos,
    v.total_vendido,
    v.ticket_medio
FROM categorias c
JOIN (
    SELECT 
        p.categoria_id,
        COUNT(DISTINCT p.id) AS total_produtos,
        SUM(ip.quantidade) AS total_vendido,
        ROUND(SUM(ip.quantidade * ip.preco_unitario) / SUM(ip.quantidade), 2) AS ticket_medio
    FROM produtos p
    JOIN itens_pedido ip ON p.id = ip.produto_id
    GROUP BY p.categoria_id
) v ON c.id = v.categoria_id
ORDER BY v.total_vendido DESC;
```

## Na Cláusula WHERE

A subconsulta no WHERE é usada para filtrar resultados baseados em uma condição:

```sql
-- Produtos que venderam acima da média
SELECT p.nome, SUM(ip.quantidade) AS total_vendido
FROM produtos p
JOIN itens_pedido ip ON p.id = ip.produto_id
GROUP BY p.id, p.nome
HAVING SUM(ip.quantidade) > (
    SELECT AVG(total) FROM (
        SELECT SUM(quantidade) AS total
        FROM itens_pedido
        GROUP BY produto_id
    ) AS vendas_por_produto
);

-- Clientes que fizeram mais de 3 pedidos
SELECT id, nome, email
FROM clientes
WHERE (
    SELECT COUNT(*) FROM pedidos
    WHERE pedidos.cliente_id = clientes.id
) > 3;
```

## Na Cláusula HAVING

```sql
-- Categorias onde pelo menos um produto vendeu mais de 100 unidades
SELECT c.nome, COUNT(p.id) AS total_produtos
FROM categorias c
JOIN produtos p ON c.id = p.categoria_id
GROUP BY c.id, c.nome
HAVING EXISTS (
    SELECT 1
    FROM itens_pedido ip
    JOIN produtos p2 ON ip.produto_id = p2.id
    WHERE p2.categoria_id = c.id
    GROUP BY ip.produto_id
    HAVING SUM(ip.quantidade) > 100
);
```

# Operadores com Subconsultas

## Operador IN

Verifica se um valor está contido no conjunto de resultados da subconsulta:

```sql
-- Produtos que foram vendidos em maio de 2023
SELECT id, nome, preco
FROM produtos
WHERE id IN (
    SELECT DISTINCT produto_id
    FROM itens_pedido ip
    JOIN pedidos p ON ip.pedido_id = p.id
    WHERE p.data_pedido BETWEEN '2023-05-01' AND '2023-05-31'
);
```

## Operador NOT IN

Verifica se um valor NÃO está contido no conjunto de resultados da subconsulta:

```sql
-- Clientes que nunca compraram produtos da categoria 'Eletrônicos'
SELECT id, nome, email
FROM clientes
WHERE id NOT IN (
    SELECT DISTINCT p.cliente_id
    FROM pedidos p
    JOIN itens_pedido ip ON p.id = ip.pedido_id
    JOIN produtos pr ON ip.produto_id = pr.id
    WHERE pr.categoria_id = (
        SELECT id FROM categorias WHERE nome = 'Eletrônicos'
    )
);
```

## Operador EXISTS

Verifica se a subconsulta retorna algum resultado (retorna verdadeiro se existir pelo menos uma linha no resultado):

```sql
-- Produtos que foram vendidos pelo menos uma vez
SELECT id, nome
FROM produtos p
WHERE EXISTS (
    SELECT 1
    FROM itens_pedido
    WHERE produto_id = p.id
);

-- Clientes que fizeram pelo menos um pedido acima de R$ 1000
SELECT id, nome, email
FROM clientes c
WHERE EXISTS (
    SELECT 1
    FROM pedidos
    WHERE cliente_id = c.id AND valor_total > 1000
);
```

## Operador NOT EXISTS

Verifica se a subconsulta NÃO retorna nenhum resultado:

```sql
-- Produtos que nunca foram vendidos
SELECT id, nome
FROM produtos p
WHERE NOT EXISTS (
    SELECT 1
    FROM itens_pedido
    WHERE produto_id = p.id
);
```

## Operadores de Comparação (>, <, =, >=, <=, <>)

Com subconsultas escalares (retornam um único valor):

```sql
-- Produtos mais caros que o preço médio
SELECT id, nome, preco
FROM produtos
WHERE preco > (SELECT AVG(preco) FROM produtos);

-- Clientes com mais pedidos que a média
SELECT c.id, c.nome, COUNT(p.id) AS total_pedidos
FROM clientes c
JOIN pedidos p ON c.id = p.cliente_id
GROUP BY c.id, c.nome
HAVING COUNT(p.id) > (
    SELECT AVG(pedidos_por_cliente) 
    FROM (
        SELECT COUNT(id) AS pedidos_por_cliente
        FROM pedidos
        GROUP BY cliente_id
    ) AS t
);
```

## Operador ALL

Compara um valor com todos os valores retornados pela subconsulta:

```sql
-- Produtos mais caros que TODOS os produtos da categoria 'Acessórios'
SELECT id, nome, preco
FROM produtos
WHERE preco > ALL (
    SELECT preco
    FROM produtos
    WHERE categoria_id = (
        SELECT id FROM categorias WHERE nome = 'Acessórios'
    )
);
```

## Operador ANY/SOME

Compara um valor com qualquer um dos valores retornados pela subconsulta:

```sql
-- Produtos mais caros que PELO MENOS UM produto da categoria 'Premium'
SELECT id, nome, preco
FROM produtos
WHERE preco > ANY (
    SELECT preco
    FROM produtos
    WHERE categoria_id = (
        SELECT id FROM categorias WHERE nome = 'Premium'
    )
);
```

# Subconsultas Correlacionadas

Uma subconsulta correlacionada é aquela que depende da consulta externa. Ela é executada uma vez para cada linha processada pela consulta externa.

```sql
-- Produtos cujo preço está 20% acima da média de sua categoria
SELECT p.id, p.nome, p.preco, c.nome AS categoria
FROM produtos p
JOIN categorias c ON p.categoria_id = c.id
WHERE p.preco > (
    SELECT AVG(preco) * 1.2
    FROM produtos
    WHERE categoria_id = p.categoria_id
);

-- Clientes que fizeram pedidos com valor acima da média de todos os seus próprios pedidos
SELECT DISTINCT c.id, c.nome
FROM clientes c
JOIN pedidos p ON c.id = p.cliente_id
WHERE p.valor_total > (
    SELECT AVG(valor_total)
    FROM pedidos
    WHERE cliente_id = c.id
);
```

# Casos de Uso Avançados

## Consultas Hierárquicas

Encontrar todos os itens em uma estrutura de categoria multinível:

```sql
-- Todas as subcategorias da categoria 'Eletrônicos' (hierarquia de um nível)
SELECT id, nome
FROM categorias
WHERE categoria_pai_id = (
    SELECT id FROM categorias WHERE nome = 'Eletrônicos'
);

-- Recursão em bancos que suportam CTE (Common Table Expressions)
WITH RECURSIVE CategoriaHierarquia AS (
    -- Caso base
    SELECT id, nome, categoria_pai_id, 1 AS nivel
    FROM categorias
    WHERE nome = 'Eletrônicos'
    
    UNION ALL
    
    -- Parte recursiva
    SELECT c.id, c.nome, c.categoria_pai_id, ch.nivel + 1
    FROM categorias c
    JOIN CategoriaHierarquia ch ON c.categoria_pai_id = ch.id
)
SELECT id, nome, nivel
FROM CategoriaHierarquia
ORDER BY nivel, nome;
```

## Atualizações com Subconsultas

```sql
-- Atualizar preço promocional para produtos com baixa venda
UPDATE produtos p
SET preco_promocional = preco * 0.85
WHERE p.id IN (
    SELECT produto_id
    FROM (
        SELECT ip.produto_id, SUM(ip.quantidade) AS total_vendido
        FROM itens_pedido ip
        JOIN pedidos pd ON ip.pedido_id = pd.id
        WHERE pd.data_pedido >= DATE_SUB(CURRENT_DATE, INTERVAL 3 MONTH)
        GROUP BY ip.produto_id
        HAVING total_vendido < 5
    ) AS produtos_baixa_venda
);
```

## Inserção com Subconsultas

```sql
-- Inserir registros em tabela de produtos recomendados
INSERT INTO produtos_recomendados (produto_id, produto_recomendado_id, relevancia)
SELECT p1.id, p2.id, COUNT(*) AS relevancia
FROM produtos p1
JOIN itens_pedido ip1 ON p1.id = ip1.produto_id
JOIN pedidos pd ON ip1.pedido_id = pd.id
JOIN itens_pedido ip2 ON pd.id = ip2.pedido_id
JOIN produtos p2 ON ip2.produto_id = p2.id
WHERE p1.id <> p2.id
AND NOT EXISTS (
    SELECT 1 FROM produtos_recomendados
    WHERE produto_id = p1.id AND produto_recomendado_id = p2.id
)
GROUP BY p1.id, p2.id
HAVING COUNT(*) > 5
ORDER BY p1.id, relevancia DESC;
```

## Exclusão com Subconsultas

```sql
-- Excluir produtos sem vendas nos últimos 12 meses e com estoque zero
DELETE FROM produtos
WHERE estoque = 0
AND id NOT IN (
    SELECT DISTINCT produto_id
    FROM itens_pedido ip
    JOIN pedidos p ON ip.pedido_id = p.id
    WHERE p.data_pedido >= DATE_SUB(CURRENT_DATE, INTERVAL 12 MONTH)
);
```

# Boas Práticas e Otimização

1. **Evite subconsultas desnecessárias**: Em muitos casos, JOINs podem ser mais eficientes.

```sql
-- Menos eficiente (subconsulta)
SELECT p.nome, p.preco
FROM produtos p
WHERE p.categoria_id IN (
    SELECT id FROM categorias WHERE nome = 'Eletrônicos'
);

-- Mais eficiente (JOIN)
SELECT p.nome, p.preco
FROM produtos p
JOIN categorias c ON p.categoria_id = c.id
WHERE c.nome = 'Eletrônicos';
```

2. **Cuidado com subconsultas correlacionadas**: Elas são executadas uma vez para cada linha da consulta externa, o que pode ser muito lento em grandes conjuntos de dados.

3. **Use EXISTS ao invés de IN para grandes conjuntos de dados**:

```sql
-- Menos eficiente com grandes conjuntos de dados
SELECT c.nome
FROM clientes c
WHERE c.id IN (SELECT cliente_id FROM pedidos);

-- Mais eficiente para grandes conjuntos de dados
SELECT c.nome
FROM clientes c
WHERE EXISTS (SELECT 1 FROM pedidos p WHERE p.cliente_id = c.id);
```

4. **Utilize índices adequadamente**: Certifique-se de que as colunas usadas nas condições de junção e filtragem nas subconsultas tenham índices.

5. **Materialize tabelas temporárias para subconsultas complexas**:

```sql
-- Em MySQL, usando tabela temporária para consultas complexas
CREATE TEMPORARY TABLE tmp_vendas_produto AS
SELECT produto_id, SUM(quantidade) AS total_vendido
FROM itens_pedido
GROUP BY produto_id;

SELECT p.nome, p.preco, v.total_vendido
FROM produtos p
JOIN tmp_vendas_produto v ON p.id = v.produto_id
WHERE v.total_vendido > 100;

DROP TEMPORARY TABLE tmp_vendas_produto;
```

6. **Evite subconsultas na cláusula SELECT para consultas que retornam muitas linhas**:

```sql
-- Menos eficiente (subconsulta executada para cada linha)
SELECT 
    c.nome,
    (SELECT COUNT(*) FROM pedidos WHERE cliente_id = c.id) AS total_pedidos
FROM clientes c;

-- Mais eficiente (JOIN)
SELECT 
    c.nome,
    COUNT(p.id) AS total_pedidos
FROM clientes c
LEFT JOIN pedidos p ON c.id = p.cliente_id
GROUP BY c.id, c.nome;
```

7. **Tenha cuidado com NOT IN quando há valores nulos**:

```sql
-- Pode não retornar resultados esperados se houver NULLs
SELECT nome FROM produtos
WHERE categoria_id NOT IN (SELECT id FROM categorias WHERE ativo = 0);

-- Melhor alternativa
SELECT nome FROM produtos
WHERE categoria_id IN (SELECT id FROM categorias WHERE ativo = 1);

-- Ou
SELECT nome FROM produtos p
WHERE NOT EXISTS (SELECT 1 FROM categorias c 
                  WHERE c.id = p.categoria_id AND c.ativo = 0);
```

# Exemplos Completos

## Análise de Clientes

```sql
-- Clientes VIP: Que gastaram mais que a média e fizeram pelo menos 3 pedidos
SELECT 
    c.id,
    c.nome,
    c.email,
    COUNT(p.id) AS num_pedidos,
    SUM(p.valor_total) AS valor_total_gasto
FROM clientes c
JOIN pedidos p ON c.id = p.cliente_id
GROUP BY c.id, c.nome, c.email
HAVING 
    COUNT(p.id) >= 3
    AND SUM(p.valor_total) > (
        SELECT AVG(total_por_cliente) 
        FROM (
            SELECT SUM(valor_total) AS total_por_cliente
            FROM pedidos
            GROUP BY cliente_id
        ) AS gastos
    )
ORDER BY valor_total_gasto DESC;
```

## Recomendação de Produtos

```sql
-- Produtos frequentemente comprados juntos com o produto 'Smartphone XYZ'
SELECT 
    p.id,
    p.nome,
    COUNT(*) AS frequencia_conjunto
FROM produtos p
JOIN itens_pedido ip ON p.id = ip.produto_id
WHERE ip.pedido_id IN (
    -- Pedidos que contêm o produto "Smartphone XYZ"
    SELECT DISTINCT ip2.pedido_id
    FROM itens_pedido ip2
    JOIN produtos p2 ON ip2.produto_id = p2.id
    WHERE p2.nome = 'Smartphone XYZ'
)
AND p.nome <> 'Smartphone XYZ'
GROUP BY p.id, p.nome
ORDER BY frequencia_conjunto DESC
LIMIT 5;
```

## Dashboard de Vendas

```sql
-- Comparativo de vendas: atual vs mesmo período do ano anterior
SELECT 
    YEAR(p.data_pedido) AS ano,
    MONTH(p.data_pedido) AS mes,
    SUM(p.valor_total) AS valor_total,
    (
        SELECT SUM(valor_total) 
        FROM pedidos
        WHERE YEAR(data_pedido) = YEAR(p.data_pedido) - 1
        AND MONTH(data_pedido) = MONTH(p.data_pedido)
    ) AS valor_ano_anterior,
    ROUND(
        (SUM(p.valor_total) / (
            SELECT SUM(valor_total) 
            FROM pedidos
            WHERE YEAR(data_pedido) = YEAR(p.data_pedido) - 1
            AND MONTH(data_pedido) = MONTH(p.data_pedido)
        ) - 1) * 100, 
    2) AS variacao_percentual
FROM pedidos p
WHERE 
    p.status = 'entregue'
    AND data_pedido >= DATE_SUB(CURRENT_DATE, INTERVAL 12 MONTH)
GROUP BY YEAR(p.data_pedido), MONTH(p.data_pedido)
ORDER BY ano, mes;
```

---

<!-- Rodapé -->
<div align="center">
  <p>Desenvolvido com foco na clareza e aplicação prática </p>
  <img src="https://img.shields.io/badge/SQL-00758F?style=for-the-badge&logo=mysql&logoColor=white" />
</div>

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/>