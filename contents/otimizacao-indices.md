<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# Otimização de Consultas SQL

A otimização de consultas SQL é uma habilidade essencial para desenvolvedores e administradores de bancos de dados. Uma consulta bem otimizada pode fazer diferença entre um sistema responsivo e um que trava constantemente sob carga.

## Por que otimizar consultas?

- **Melhor desempenho**: Reduz o tempo de resposta das consultas
- **Maior escalabilidade**: Suporta mais usuários e operações simultâneas
- **Economia de recursos**: Menor utilização de CPU, memória e I/O
- **Melhor experiência do usuário**: Aplicações mais rápidas e responsivas
- **Menor custo**: Redução de recursos computacionais em ambientes de nuvem

# Entendendo o Plano de Execução

O plano de execução é o roteiro que o mecanismo do banco de dados segue para executar uma consulta. Analisar esse plano é o primeiro passo para identificar problemas de desempenho.

## Como visualizar o plano de execução

```sql
-- MySQL/MariaDB
EXPLAIN SELECT * FROM clientes WHERE cidade = 'São Paulo';

-- PostgreSQL
EXPLAIN ANALYZE SELECT * FROM clientes WHERE cidade = 'São Paulo';

-- SQL Server
SET SHOWPLAN_ALL ON;
GO
SELECT * FROM clientes WHERE cidade = 'São Paulo';
GO
SET SHOWPLAN_ALL OFF;

-- Oracle
EXPLAIN PLAN FOR SELECT * FROM clientes WHERE cidade = 'São Paulo';
SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);
```

## Interpretando o plano de execução

Ao analisar o plano de execução, procure por:

1. **Varreduras de tabela (Table Scans)**: Indicam que o banco está lendo a tabela inteira
2. **Métodos de junção**: Nested loops, hash joins, merge joins
3. **Uso de índices**: Quais índices estão sendo usados e como
4. **Operações de ordenação (Sorts)**: Podem ser caras em termos de recursos
5. **Cardinalidade**: Estimativa de quantas linhas cada operação vai produzir

Exemplo de análise (MySQL):

```
+----+-------------+----------+------+---------------+------+---------+------+------+-------------+
| id | select_type | table    | type | possible_keys | key  | key_len | ref  | rows | Extra       |
+----+-------------+----------+------+---------------+------+---------+------+------+-------------+
|  1 | SIMPLE      | clientes | ALL  | NULL          | NULL | NULL    | NULL | 1000 | Using where |
+----+-------------+----------+------+---------------+------+---------+------+------+-------------+
```

Neste exemplo, `type: ALL` e `key: NULL` indicam uma varredura completa de tabela (sem uso de índice), o que pode ser ineficiente para tabelas grandes.

# Índices: A Chave para Consultas Rápidas

Os índices são estruturas que melhoram a velocidade das operações em tabelas. Funcionam como um índice de livro, permitindo localizar dados rapidamente sem percorrer a tabela inteira.

## Tipos de Índices

### 1. Índice de Coluna Única

```sql
-- Criação de índice em uma coluna
CREATE INDEX idx_clientes_email ON clientes(email);
```

### 2. Índice Composto (Múltiplas Colunas)

```sql
-- Índice em múltiplas colunas
CREATE INDEX idx_produtos_cat_preco ON produtos(categoria_id, preco);
```

### 3. Índice Único

```sql
-- Índice que não permite valores duplicados
CREATE UNIQUE INDEX idx_usuarios_username ON usuarios(username);
```

### 4. Índice de Texto Completo (Full-Text)

```sql
-- MySQL
CREATE FULLTEXT INDEX idx_produtos_descricao ON produtos(descricao);

-- PostgreSQL
CREATE INDEX idx_produtos_descricao_fulltext ON produtos USING GIN (to_tsvector('portuguese', descricao));

-- SQL Server
CREATE FULLTEXT INDEX ON produtos(descricao) KEY INDEX PK_produtos;
```

### 5. Índices Espaciais

```sql
-- MySQL
CREATE SPATIAL INDEX idx_locais_coordenadas ON locais(coordenadas);

-- PostgreSQL
CREATE INDEX idx_locais_coordenadas ON locais USING GIST(coordenadas);
```

### 6. Índices Funcionais

```sql
-- Índice em expressão/função
CREATE INDEX idx_clientes_nome_lower ON clientes(LOWER(nome));
```

## Estratégias para Criação de Índices

1. **Colunas em cláusulas WHERE**: Índices em colunas frequentemente usadas em filtros
   ```sql
   CREATE INDEX idx_pedidos_status ON pedidos(status);
   ```

2. **Colunas em cláusulas JOIN**: Índices em colunas usadas para junções entre tabelas
   ```sql
   CREATE INDEX idx_pedidos_cliente_id ON pedidos(cliente_id);
   ```

3. **Colunas em cláusulas ORDER BY**: Índices em colunas usadas para ordenação
   ```sql
   CREATE INDEX idx_produtos_preco ON produtos(preco);
   ```

4. **Colunas em cláusulas GROUP BY**: Índices em colunas usadas para agrupamento
   ```sql
   CREATE INDEX idx_vendas_data ON vendas(data_venda);
   ```

5. **Ordem das colunas em índices compostos**: Coloque primeiro as colunas mais seletivas e/ou mais usadas em condições de igualdade
   ```sql
   -- Para consultas como: WHERE categoria_id = 5 AND preco > 100
   CREATE INDEX idx_cat_preco ON produtos(categoria_id, preco);
   ```

## Quando os Índices NÃO São Usados

Os índices podem ser ignorados pelo otimizador de consultas em várias situações:

1. **Funções em colunas indexadas**:
   ```sql
   -- Índice em 'data_criacao' não será usado
   SELECT * FROM pedidos WHERE YEAR(data_criacao) = 2023;
  
   -- Melhor alternativa
   SELECT * FROM pedidos WHERE data_criacao BETWEEN '2023-01-01' AND '2023-12-31';
   ```

2. **Operadores de negação**:
   ```sql
   -- Menos eficiente
   SELECT * FROM produtos WHERE categoria_id != 5;
  
   -- Mais eficiente
   SELECT * FROM produtos WHERE categoria_id < 5 OR categoria_id > 5;
   ```

3. **Operador OR entre colunas diferentes**:
   ```sql
   -- Pode não usar índices eficientemente
   SELECT * FROM clientes WHERE cidade = 'São Paulo' OR estado = 'RJ';
  
   -- Alternativa usando UNION
   SELECT * FROM clientes WHERE cidade = 'São Paulo'
   UNION
   SELECT * FROM clientes WHERE estado = 'RJ';
   ```

4. **Expressões LIKE com curinga no início**:
   ```sql
   -- Não usa índice eficientemente
   SELECT * FROM produtos WHERE nome LIKE '%smartphone%';
  
   -- Para busca de texto, considere índices full-text
   SELECT * FROM produtos
   WHERE MATCH(nome, descricao) AGAINST('smartphone' IN NATURAL LANGUAGE MODE);
   ```

# Técnicas de Otimização de Consultas

## 1. Selecione Apenas as Colunas Necessárias

```sql
-- Menos eficiente
SELECT * FROM produtos WHERE categoria_id = 5;

-- Mais eficiente
SELECT id, nome, preco FROM produtos WHERE categoria_id = 5;
```

## 2. Limite o Número de Resultados

```sql
-- MySQL, PostgreSQL, SQL Server
SELECT * FROM produtos ORDER BY data_cadastro DESC LIMIT 20;

-- Oracle
SELECT * FROM produtos ORDER BY data_cadastro DESC FETCH FIRST 20 ROWS ONLY;
```

## 3. Use JOINs Adequadamente

```sql
-- Menos eficiente (subconsulta)
SELECT p.*, 
    (SELECT nome FROM categorias c WHERE c.id = p.categoria_id) AS categoria
FROM produtos p;

-- Mais eficiente (JOIN)
SELECT p.*, c.nome AS categoria
FROM produtos p
JOIN categorias c ON p.categoria_id = c.id;
```

## 4. Evite Subconsultas Correlacionadas

```sql
-- Menos eficiente
SELECT c.nome, 
    (SELECT COUNT(*) FROM pedidos p WHERE p.cliente_id = c.id) AS total_pedidos
FROM clientes c;

-- Mais eficiente
SELECT c.nome, COUNT(p.id) AS total_pedidos
FROM clientes c
LEFT JOIN pedidos p ON c.id = p.cliente_id
GROUP BY c.id, c.nome;
```

## 5. Use Tabelas Temporárias para Operações Complexas

```sql
-- Criando tabela temporária
CREATE TEMPORARY TABLE tmp_vendas_mensais AS
SELECT 
    DATE_FORMAT(data_venda, '%Y-%m') AS mes,
    SUM(valor) AS total_vendas
FROM vendas
GROUP BY mes;

-- Usando a tabela temporária
SELECT mes, total_vendas,
    total_vendas / LAG(total_vendas) OVER (ORDER BY mes) - 1 AS variacao_percentual
FROM tmp_vendas_mensais
ORDER BY mes;

-- Removendo a tabela temporária
DROP TEMPORARY TABLE tmp_vendas_mensais;
```

## 6. Use EXISTS ao invés de IN para Grandes Conjuntos

```sql
-- Pode ser lento para grandes conjuntos
SELECT * FROM clientes 
WHERE id IN (SELECT cliente_id FROM pedidos WHERE valor > 1000);

-- Geralmente mais eficiente
SELECT * FROM clientes c
WHERE EXISTS (SELECT 1 FROM pedidos p WHERE p.cliente_id = c.id AND p.valor > 1000);
```

## 7. Particione Tabelas Grandes

```sql
-- MySQL: Particionar por data
CREATE TABLE vendas (
    id INT,
    data_venda DATE,
    valor DECIMAL(10,2),
    PRIMARY KEY (id, data_venda)
)
PARTITION BY RANGE (YEAR(data_venda)) (
    PARTITION p2021 VALUES LESS THAN (2022),
    PARTITION p2022 VALUES LESS THAN (2023),
    PARTITION p2023 VALUES LESS THAN (2024),
    PARTITION future VALUES LESS THAN MAXVALUE
);
```

## 8. Use Hints Quando Necessário (Com Cautela)

```sql
-- MySQL: Forçar uso de índice
SELECT * FROM produtos USE INDEX (idx_categoria_preco)
WHERE categoria_id = 5 AND preco > 100;

-- SQL Server: Sugestão de índice
SELECT * FROM produtos WITH (INDEX(idx_categoria_preco))
WHERE categoria_id = 5 AND preco > 100;

-- Oracle: Sugestão de índice
SELECT /*+ INDEX(p idx_categoria_preco) */ * 
FROM produtos p
WHERE categoria_id = 5 AND preco > 100;
```

# Monitoramento e Manutenção

## 1. Identifique Consultas Lentas

```sql
-- MySQL: Habilitar log de consultas lentas
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 1; -- Em segundos
```

## 2. Atualize Estatísticas do Banco

```sql
-- MySQL
ANALYZE TABLE clientes, pedidos, produtos;

-- PostgreSQL
ANALYZE clientes;
ANALYZE pedidos;
ANALYZE produtos;

-- SQL Server
UPDATE STATISTICS dbo.clientes;
UPDATE STATISTICS dbo.pedidos;
UPDATE STATISTICS dbo.produtos;

-- Oracle
EXEC DBMS_STATS.GATHER_TABLE_STATS('schema', 'clientes');
```

## 3. Reconstrua Índices Fragmentados

```sql
-- MySQL (InnoDB): Otimização de tabela
OPTIMIZE TABLE produtos;

-- PostgreSQL
REINDEX TABLE produtos;

-- SQL Server
ALTER INDEX ALL ON produtos REBUILD;

-- Oracle
ALTER INDEX idx_produtos_cat_preco REBUILD;
```

## 4. Monitore o Uso de Índices

```sql
-- MySQL: Estatísticas de uso de índices
SELECT * FROM information_schema.statistics WHERE table_name = 'produtos';

-- PostgreSQL
SELECT * FROM pg_stat_user_indexes WHERE relname = 'produtos';
```

# Antipadrões Comuns

1. **SELECT * em produção**: Aumenta I/O e tráfego de rede
2. **Uso excessivo de funções em WHERE**: Impede uso de índices
3. **JOINs sem índices**: Causa varreduras completas de tabela
4. **Transações longas**: Bloqueiam recursos por muito tempo
5. **Muitos índices**: Tornam operações de escrita (INSERT/UPDATE/DELETE) mais lentas
6. **Uso de OR com diferentes colunas**: Limita a eficácia dos índices
7. **Consultas com subselects múltiplos**: Aumenta a complexidade de processamento

# Estudo de Caso: Otimizando uma Consulta Complexa

## Consulta Original (Não Otimizada)

```sql
SELECT 
    c.nome AS cliente,
    p.id AS pedido,
    p.data_pedido,
    SUM(ip.quantidade * ip.preco_unitario) AS valor_total,
    (SELECT nome FROM status_pedido WHERE id = p.status_id) AS status,
    GROUP_CONCAT(pr.nome) AS produtos
FROM 
    clientes c,
    pedidos p,
    itens_pedido ip,
    produtos pr
WHERE 
    YEAR(p.data_pedido) = 2023
    AND c.id = p.cliente_id
    AND p.id = ip.pedido_id
    AND ip.produto_id = pr.id
    AND pr.categoria_id IN (SELECT id FROM categorias WHERE departamento_id = 3)
GROUP BY 
    c.nome, p.id, p.data_pedido
HAVING 
    valor_total > 1000
ORDER BY 
    p.data_pedido DESC;
```

## Consulta Otimizada

```sql
-- Índices necessários
CREATE INDEX idx_pedidos_data ON pedidos(data_pedido);
CREATE INDEX idx_pedidos_cliente ON pedidos(cliente_id);
CREATE INDEX idx_itens_pedido ON itens_pedido(pedido_id, produto_id);
CREATE INDEX idx_produtos_categoria ON produtos(categoria_id);
CREATE INDEX idx_categorias_depto ON categorias(departamento_id);

-- Consulta otimizada
SELECT 
    c.nome AS cliente,
    p.id AS pedido,
    p.data_pedido,
    SUM(ip.quantidade * ip.preco_unitario) AS valor_total,
    sp.nome AS status,
    GROUP_CONCAT(pr.nome) AS produtos
FROM 
    pedidos p
    JOIN clientes c ON c.id = p.cliente_id
    JOIN status_pedido sp ON sp.id = p.status_id
    JOIN itens_pedido ip ON p.id = ip.pedido_id
    JOIN produtos pr ON ip.produto_id = pr.id
    JOIN categorias cat ON pr.categoria_id = cat.id
WHERE 
    p.data_pedido BETWEEN '2023-01-01' AND '2023-12-31'
    AND cat.departamento_id = 3
GROUP BY 
    c.nome, p.id, p.data_pedido, sp.nome
HAVING 
    valor_total > 1000
ORDER BY 
    p.data_pedido DESC
LIMIT 100;
```

### Melhorias implementadas:

1. **Substituição de produto cartesiano por JOINs explícitos**
2. **Substituição de função em data por comparação com intervalo (permite uso do índice)**
3. **Substituição de subconsulta no SELECT por JOIN**
4. **Substituição de subconsulta no WHERE por JOIN**
5. **Adição de LIMIT para evitar conjuntos de resultados muito grandes**
6. **Criação de índices adequados para todas as junções e filtros**

## Análise de Desempenho

Uma consulta como esta pode passar de vários segundos ou até minutos de tempo de execução para milissegundos após a otimização adequada.

# Dicas Avançadas

1. **Desnormalize estrategicamente**: Em sistemas de análise, alguma desnormalização controlada pode melhorar o desempenho de leitura
2. **Use views materializadas**: Para relatórios frequentes, considere views materializadas (atualizadas periodicamente)
3. **Considere caching**: Armazene resultados de consultas frequentes em cache (Redis, Memcached)
4. **Avalie sharding**: Para bancos enormes, considere dividir dados entre múltiplos servidores
5. **Separe ambientes OLTP e OLAP**: Use bancos diferentes para transações e análises

---

<!-- Rodapé -->
<div align="center">
  <p>Desenvolvido com foco na clareza e aplicação prática </p>
  <img src="https://img.shields.io/badge/SQL-00758F?style=for-the-badge&logo=mysql&logoColor=white" />
</div>

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/>