<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# 📝 Comentários em SQL

## O que são Comentários em SQL?

**Comentários** são trechos de texto inseridos em um script SQL que são ignorados pelo interpretador durante a execução. Eles servem para documentar o código, explicar a lógica, fornecer contexto e facilitar a manutenção.

Os comentários em SQL são essenciais para garantir que outros desenvolvedores (ou você mesmo no futuro) possam entender o propósito e o funcionamento das consultas e scripts.

## Tipos de Comentários em SQL

SQL suporta dois tipos principais de comentários:

### 1. Comentários de Linha Única

Comentários que abrangem apenas uma linha, começando com dois traços (`--`).

```sql
-- Este é um comentário de linha única
SELECT * FROM clientes; -- Este comentário está após um comando
```

### 2. Comentários de Múltiplas Linhas

Comentários que podem abranger várias linhas, começando com `/*` e terminando com `*/`.

```sql
/* Este é um comentário
   que abrange múltiplas
   linhas */
SELECT 
    nome,
    email,
    /* Estas colunas são importantes
       para o relatório de marketing */
    cidade,
    estado
FROM clientes;
```

## Boas Práticas para Uso de Comentários

### 1. Cabeçalhos de Scripts

Use comentários para identificar o propósito, autor, data e outras informações importantes no início de scripts.

```sql
/*
 * Script: relatorio_vendas_mensal.sql
 * Autor: João Silva
 * Data: 2023-05-15
 * Descrição: Gera relatório de vendas consolidado por mês
 * Uso: Este script deve ser executado no primeiro dia de cada mês
 */

-- Definição de variáveis
SET @ano = 2023;
SET @mes = 4;
```

### 2. Seções do Script

Use comentários para separar seções lógicas do script.

```sql
-- ==============================
-- TABELAS TEMPORÁRIAS
-- ==============================
CREATE TEMPORARY TABLE temp_vendas AS
SELECT * FROM vendas WHERE YEAR(data) = 2023;

-- ==============================
-- CÁLCULOS PRINCIPAIS
-- ==============================
SELECT 
    produto_id,
    SUM(quantidade) AS total_vendido
FROM temp_vendas
GROUP BY produto_id;
```

### 3. Explicações de Lógica Complexa

Use comentários para explicar consultas complexas ou lógica não óbvia.

```sql
-- Calculamos a média móvel de 3 meses usando subconsultas correlacionadas
SELECT 
    data,
    valor,
    (
        SELECT AVG(valor) 
        FROM vendas v2 
        WHERE v2.data BETWEEN DATE_SUB(v1.data, INTERVAL 2 MONTH) AND v1.data
        AND v2.produto_id = v1.produto_id
    ) AS media_movel
FROM vendas v1;
```

### 4. Documentação de Decisões

Use comentários para explicar por que determinadas decisões foram tomadas.

```sql
-- Usamos LEFT JOIN em vez de INNER JOIN para incluir clientes sem pedidos
-- Requisito do departamento financeiro (ticket #1234)
SELECT 
    c.nome,
    COUNT(p.id) AS total_pedidos
FROM clientes c
LEFT JOIN pedidos p ON c.id = p.cliente_id
GROUP BY c.nome;
```

### 5. Versões e Alterações

Use comentários para documentar alterações no script ao longo do tempo.

```sql
/*
 * Histórico de alterações:
 * 2023-05-15 - João - Versão inicial
 * 2023-06-10 - Maria - Adicionada condição para filtrar por região
 * 2023-07-22 - Pedro - Otimização da consulta principal
 */
```

## Comentários em Diferentes SGBDs

A maioria dos sistemas de gerenciamento de banco de dados suporta os formatos padrão de comentários (`--` e `/* */`), mas alguns têm recursos adicionais:

### MySQL

```sql
-- Comentário de linha única
# Também é um comentário de linha única (específico do MySQL)
/* Comentário de múltiplas linhas */
```

### SQL Server

```sql
-- Comentário de linha única
/* Comentário de múltiplas linhas */
```

### PostgreSQL

```sql
-- Comentário de linha única
/* Comentário de múltiplas linhas */
```

### Oracle

```sql
-- Comentário de linha única
/* Comentário de múltiplas linhas */
-- Comentário de documentação para objetos (específico do Oracle)
COMMENT ON TABLE clientes IS 'Tabela com informações de clientes';
COMMENT ON COLUMN clientes.email IS 'Email principal para contato';
```

## Dicas Adicionais

### 1. Comentários para Depuração

Use comentários para comentar temporariamente partes do código durante a depuração.

```sql
SELECT 
    id,
    nome,
    email
    -- ,telefone  -- Temporariamente removido por questões de performance
    -- ,endereco  -- Não necessário para este relatório
FROM clientes;
```

### 2. Identificação de Final de Blocos

Em scripts longos, use comentários para marcar o final de blocos complexos.

```sql
BEGIN
    -- Várias linhas de lógica
    
    IF condição THEN
        -- Mais lógica
    END IF; -- Fim do IF condição
    
END; -- Fim do bloco principal
```

### 3. Comentários TODOs

Use comentários para marcar itens que precisam ser revisados ou modificados no futuro.

```sql
-- TODO: Otimizar esta consulta, está muito lenta com grandes volumes
-- FIXME: Corrigir o cálculo de desconto
-- REVIEW: Verificar se esta abordagem está correta
```

## O que Evitar em Comentários

1. **Comentários óbvios**: Evite comentar o que o código já expressa claramente
2. **Comentários desatualizados**: Atualize os comentários quando alterar o código
3. **Comentários excessivos**: Nem toda linha precisa de um comentário
4. **Informações sensíveis**: Não inclua senhas, tokens ou dados confidenciais
5. **Ofensas ou humor inadequado**: Mantenha os comentários profissionais

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/> 