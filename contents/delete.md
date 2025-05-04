<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# ❌ D - Remoção de Dados (DELETE)

## O que é o Comando DELETE?

O comando **DELETE** é utilizado para remover registros existentes de uma tabela. Ele faz parte da categoria DML (Data Manipulation Language) do SQL e representa o "D" (Delete) no acrônimo CRUD (Create, Read, Update, Delete).

O DELETE permite remover um ou mais registros de uma tabela, geralmente com base em uma condição especificada.

## Sintaxe Básica

```sql
DELETE FROM nome_tabela
[WHERE condição];
```

⚠️ **AVISO IMPORTANTE**: Se omitir a cláusula WHERE, o comando DELETE removerá TODOS os registros da tabela!

## Exemplos Básicos

### Deletar um registro específico

```sql
-- Remove o cliente com ID 25
DELETE FROM clientes
WHERE id = 25;
```

### Deletar múltiplos registros

```sql
-- Remove todos os produtos da categoria 'Descontinuados'
DELETE FROM produtos
WHERE categoria = 'Descontinuados';
```

### Deletar com múltiplas condições

```sql
-- Remove pedidos antigos que foram cancelados
DELETE FROM pedidos
WHERE status = 'Cancelado' 
AND data_pedido < '2022-01-01';
```

## DELETE com Subconsultas

### Deletar com base em dados de outra tabela

```sql
-- Remove todos os pedidos de clientes inativos
DELETE FROM pedidos
WHERE cliente_id IN (
    SELECT id FROM clientes
    WHERE status = 'Inativo'
);
```

### Deletar usando EXISTS

```sql
-- Remove produtos que não têm vendas
DELETE FROM produtos p
WHERE NOT EXISTS (
    SELECT 1 FROM vendas v
    WHERE v.produto_id = p.id
);
```

## DELETE com JOIN (MySQL)

No MySQL, você pode usar JOINs diretamente com DELETE:

```sql
-- Remove pedidos com pagamentos recusados
DELETE p
FROM pedidos p
JOIN pagamentos pg ON p.id = pg.pedido_id
WHERE pg.status = 'Recusado';
```

## DELETE com LIMIT (MySQL)

```sql
-- Remove apenas os 10 registros mais antigos
DELETE FROM logs
ORDER BY data_criacao
LIMIT 10;
```

## Truncar uma Tabela

Quando você precisa remover **todos** os registros de uma tabela de forma mais eficiente, use TRUNCATE:

```sql
-- Remove todos os logs (mais rápido que DELETE)
TRUNCATE TABLE logs;
```

**Diferenças entre DELETE e TRUNCATE:**
1. TRUNCATE é mais rápido para grandes volumes de dados
2. TRUNCATE não pode ter cláusula WHERE
3. TRUNCATE reinicia contadores de auto incremento
4. TRUNCATE não dispara triggers
5. TRUNCATE é uma operação DDL, não pode ser usada com transações em alguns SGBDs

## DELETE vs DROP

- **DELETE**: Remove registros da tabela
- **TRUNCATE**: Remove todos os registros, mas mantém a estrutura da tabela
- **DROP**: Remove a tabela inteira, incluindo sua estrutura

```sql
-- Remove apenas os dados
DELETE FROM tabela;

-- Remove todos os dados mais rapidamente
TRUNCATE TABLE tabela;

-- Remove a tabela inteira
DROP TABLE tabela;
```

## Soft Delete

Em muitos sistemas, é preferível não excluir permanentemente os dados, mas marcá-los como excluídos:

```sql
-- Em vez de DELETE
UPDATE clientes
SET status = 'Excluído', data_exclusao = NOW()
WHERE id = 25;

-- Consulta que "esconde" clientes excluídos
SELECT * FROM clientes WHERE status != 'Excluído';
```

## DELETE em Cascata

Ao excluir um registro que tem registros relacionados em outras tabelas, você pode:

1. **Usar restrições de chave estrangeira com ON DELETE CASCADE:**

```sql
-- Criação da tabela com comportamento em cascata
CREATE TABLE pedidos (
    id INT PRIMARY KEY,
    cliente_id INT,
    FOREIGN KEY (cliente_id) 
    REFERENCES clientes(id) ON DELETE CASCADE
);

-- Agora, quando você excluir um cliente, todos seus pedidos serão excluídos automaticamente
DELETE FROM clientes WHERE id = 10;
```

2. **Excluir manualmente em ordem correta:**

```sql
-- Primeiro exclui os registros filhos
DELETE FROM itens_pedido WHERE pedido_id = 100;
-- Depois exclui o registro pai
DELETE FROM pedidos WHERE id = 100;
```

## Boas Práticas

1. **SEMPRE use WHERE** a menos que realmente queira excluir todos os registros
2. **Teste primeiro com SELECT** para verificar quais registros serão excluídos
3. **Use transações** para operações complexas ou em massa
4. **Considere soft delete** para dados importantes
5. **Pense nas dependências** e como outras tabelas serão afetadas
6. **Mantenha backups** antes de operações de exclusão grandes

```sql
-- Exemplo de uso de transação para delete seguro
START TRANSACTION;

-- Verifique o que será excluído
SELECT * FROM produtos WHERE ultima_venda < '2020-01-01';

-- Execute o delete
DELETE FROM produtos WHERE ultima_venda < '2020-01-01';

-- Verifique se o resultado é o esperado
-- Se estiver tudo certo
COMMIT;
-- Caso contrário
-- ROLLBACK;
```

## Armadilhas e Erros Comuns

- **Omitir WHERE**: deletar todos os registros da tabela acidentalmente
- **Condição incorreta**: deletar registros errados
- **Ignorar restrições de chave estrangeira**: tentar excluir registros que têm referências em outras tabelas
- **Não considerar o impacto no desempenho**: exclusões em massa podem ser lentas e bloquear a tabela

## Diferenças entre SGBDs

- **MySQL**: Suporta `ORDER BY` e `LIMIT` no DELETE, DELETE com JOIN
- **PostgreSQL**: Permite usar `USING` para junções e suporta `RETURNING` para ver registros excluídos
- **SQL Server**: Usa `TOP` em vez de `LIMIT`, permite CTEs com DELETE
- **Oracle**: Usa sintaxe específica para DELETE com junções e subconsultas

```sql
-- PostgreSQL: DELETE com RETURNING
DELETE FROM produtos 
WHERE estoque = 0 
RETURNING id, nome;

-- SQL Server: DELETE com TOP
DELETE TOP(10) FROM logs
WHERE data < '2023-01-01';
```

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/> 