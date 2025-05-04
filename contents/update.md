<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# ✏️ U - Atualização de Dados (UPDATE)

## O que é o Comando UPDATE?

O comando **UPDATE** é utilizado para modificar dados existentes em uma tabela. Ele faz parte da categoria DML (Data Manipulation Language) e representa o "U" (Update) no acrônimo CRUD (Create, Read, Update, Delete).

Este comando permite alterar valores de uma ou mais colunas em um ou mais registros de uma tabela, com base em condições específicas.

## Sintaxe Básica

```sql
UPDATE nome_tabela
SET coluna1 = valor1, coluna2 = valor2, ...
[WHERE condição];
```

⚠️ **AVISO IMPORTANTE**: Se omitir a cláusula WHERE, o comando UPDATE alterará TODOS os registros da tabela!

## Exemplos Básicos

### Atualização simples de um registro

```sql
-- Atualiza o telefone de um cliente específico
UPDATE clientes
SET telefone = '(11) 98765-4321'
WHERE id = 10;
```

### Atualização de múltiplas colunas

```sql
-- Atualiza endereço e email de um cliente
UPDATE clientes
SET 
    endereco = 'Av. Paulista, 1000',
    email = 'maria@newemail.com',
    data_atualizacao = NOW()
WHERE id = 25;
```

### Atualização com operadores aritméticos

```sql
-- Aumenta o preço de todos os produtos em 10%
UPDATE produtos
SET preco = preco * 1.10;

-- Aumenta o salário em 15% para um departamento específico
UPDATE funcionarios
SET salario = salario * 1.15
WHERE departamento = 'Vendas';
```

## UPDATE com Subconsultas

### Atualização baseada em dados de outra tabela

```sql
-- Atualiza o status dos pedidos com base na tabela de pagamentos
UPDATE pedidos
SET status = 'Pago'
WHERE id IN (
    SELECT pedido_id 
    FROM pagamentos 
    WHERE status = 'Confirmado'
);
```

### Atualização com subconsulta no SET

```sql
-- Atualiza o preço de produtos para a média da sua categoria
UPDATE produtos p
SET preco = (
    SELECT AVG(preco) 
    FROM produtos 
    WHERE categoria = p.categoria
)
WHERE preco = 0;
```

## UPDATE com JOIN

No MySQL, você pode atualizar dados de uma tabela com base em outra usando JOIN:

```sql
-- Atualiza o status de cliente para VIP para quem tem compras acima de 5000
UPDATE clientes c
JOIN (
    SELECT cliente_id, SUM(valor) as total
    FROM pedidos
    GROUP BY cliente_id
    HAVING SUM(valor) > 5000
) t ON c.id = t.cliente_id
SET c.categoria = 'VIP';
```

## UPDATE com CASE

```sql
-- Define o nível de estoque com base na quantidade
UPDATE produtos
SET status_estoque = 
    CASE 
        WHEN quantidade = 0 THEN 'Indisponível'
        WHEN quantidade < 10 THEN 'Baixo'
        WHEN quantidade < 50 THEN 'Médio'
        ELSE 'Alto'
    END;
```

## UPDATE com LIMIT

No MySQL, você pode limitar o número de registros atualizados:

```sql
-- Atualiza apenas os 10 primeiros produtos
UPDATE produtos
SET em_promocao = 1
WHERE categoria = 'Eletrônicos'
ORDER BY preco DESC
LIMIT 10;
```

## UPDATE com Tabela Temporária

```sql
-- Cria tabela temporária com dados calculados
CREATE TEMPORARY TABLE temp_precos AS
SELECT id, preco * 0.9 AS novo_preco
FROM produtos
WHERE categoria = 'Vestuário';

-- Atualiza produtos com base na tabela temporária
UPDATE produtos p
JOIN temp_precos t ON p.id = t.id
SET p.preco = t.novo_preco;

-- Remove a tabela temporária
DROP TEMPORARY TABLE temp_precos;
```

## Boas Práticas

1. **SEMPRE use WHERE** a menos que realmente queira atualizar todos os registros
2. **Teste primeiro com SELECT** para verificar quais registros serão afetados
3. **Use transações** para operações complexas ou em massa
4. **Mantenha um registro de alterações** (versão anterior/nova) quando relevante
5. **Considere o bloqueio de tabelas** durante atualizações grandes em sistemas ocupados
6. **Atualize em lotes** para tabelas muito grandes

```sql
-- Exemplo de uso de transação para update seguro
START TRANSACTION;

UPDATE produtos
SET preco = preco * 1.05
WHERE categoria = 'Eletrônicos';

-- Verificar se tudo está correto
SELECT * FROM produtos WHERE categoria = 'Eletrônicos';

-- Se estiver tudo certo
COMMIT;
-- Caso contrário
-- ROLLBACK;
```

## Armadilhas e Erros Comuns

- **Omitir WHERE**: atualizar todos os registros da tabela acidentalmente
- **Condição incorreta**: atualizar registros errados
- **Violação de restrição**: tentar definir valores que violam constraints
- **Consultas lentas**: updates em massa sem índices adequados podem ser lentos
- **Bloqueios**: atualizações grandes podem bloquear a tabela por muito tempo

## Diferenças entre SGBDs

- **MySQL**: Suporta `ORDER BY` e `LIMIT` no UPDATE, UPDATE com JOIN
- **PostgreSQL**: Sintaxe diferente para update com joins, usando `FROM`
- **SQLite**: Sintaxe mais simples, sem algumas funcionalidades avançadas
- **SQL Server**: Usa `TOP` em vez de `LIMIT`

```sql
-- PostgreSQL: UPDATE com JOIN
UPDATE produtos p
SET preco = p.preco * 1.1
FROM categorias c
WHERE p.categoria_id = c.id AND c.nome = 'Eletrônicos';
```

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/> 