<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# 🔍 R - Consulta de Dados (SELECT)

## O que é o Comando SELECT?

O comando **SELECT** é utilizado para consultar dados em uma tabela de banco de dados. É o comando SQL mais utilizado e representa o "R" (Read) no acrônimo CRUD (Create, Read, Update, Delete).

Um SELECT bem estruturado permite extrair exatamente os dados necessários de uma ou mais tabelas, aplicando filtros, ordenação e agrupamentos conforme necessário.

## Sintaxe Básica

```sql
SELECT coluna1, coluna2, ...
FROM tabela
[WHERE condição]
[GROUP BY colunas]
[HAVING condição_de_grupo]
[ORDER BY colunas [ASC|DESC]]
[LIMIT quantidade [OFFSET deslocamento]];
```

## Consultas Simples

### Selecionando todas as colunas

```sql
-- Seleciona todos os dados de clientes
SELECT * FROM clientes;
```

### Selecionando colunas específicas

```sql
-- Seleciona apenas nome e email
SELECT nome, email FROM clientes;
```

### Usando WHERE para filtrar resultados

```sql
-- Clientes da cidade de São Paulo
SELECT nome, telefone FROM clientes
WHERE cidade = 'São Paulo';

-- Produtos com preço maior que 100
SELECT nome, preco FROM produtos
WHERE preco > 100;
```

## Operadores em Condições WHERE

### Operadores de comparação

```sql
-- Maior que, menor que, igual, diferente
SELECT * FROM produtos WHERE preco > 50;
SELECT * FROM produtos WHERE preco <= 100;
SELECT * FROM pedidos WHERE status = 'Entregue';
SELECT * FROM funcionarios WHERE departamento <> 'Vendas';
```

### Operadores lógicos

```sql
-- AND: ambas condições precisam ser verdadeiras
SELECT * FROM produtos 
WHERE preco > 100 AND categoria = 'Eletrônicos';

-- OR: pelo menos uma condição precisa ser verdadeira
SELECT * FROM clientes 
WHERE cidade = 'Rio de Janeiro' OR cidade = 'São Paulo';

-- NOT: nega a condição
SELECT * FROM pedidos 
WHERE NOT status = 'Cancelado';
```

### IN e NOT IN

```sql
-- Clientes de determinadas cidades
SELECT * FROM clientes 
WHERE cidade IN ('São Paulo', 'Rio de Janeiro', 'Belo Horizonte');

-- Produtos que não são destas categorias
SELECT * FROM produtos 
WHERE categoria NOT IN ('Vestuário', 'Calçados');
```

### BETWEEN

```sql
-- Produtos em uma faixa de preço
SELECT * FROM produtos 
WHERE preco BETWEEN 50 AND 150;

-- Pedidos em um período
SELECT * FROM pedidos 
WHERE data_pedido BETWEEN '2023-01-01' AND '2023-12-31';
```

### LIKE para padrões de texto

```sql
-- Nomes que começam com "A"
SELECT * FROM clientes WHERE nome LIKE 'A%';

-- Emails que contêm "gmail"
SELECT * FROM clientes WHERE email LIKE '%gmail%';

-- Produtos que terminam com "plus"
SELECT * FROM produtos WHERE nome LIKE '%plus';

-- Nomes com exatamente 5 caracteres (cada _ representa um caractere)
SELECT * FROM produtos WHERE nome LIKE '_____';
```

### IS NULL e IS NOT NULL

```sql
-- Clientes sem telefone cadastrado
SELECT * FROM clientes WHERE telefone IS NULL;

-- Produtos com descrição preenchida
SELECT * FROM produtos WHERE descricao IS NOT NULL;
```

## Ordenação de Resultados

```sql
-- Ordem ascendente (padrão)
SELECT * FROM produtos ORDER BY preco;

-- Ordem descendente
SELECT * FROM produtos ORDER BY preco DESC;

-- Ordenação por múltiplas colunas
SELECT * FROM funcionarios 
ORDER BY departamento ASC, salario DESC;
```

## Limitando Resultados

```sql
-- Primeiros 10 produtos
SELECT * FROM produtos LIMIT 10;

-- 10 produtos a partir do 20º
SELECT * FROM produtos LIMIT 10 OFFSET 20;

-- Sintaxe alternativa no MySQL: LIMIT [offset,] quantidade
SELECT * FROM produtos LIMIT 20, 10;
```

## Funções e Expressões

```sql
-- Calculando valores
SELECT nome, preco, quantidade, preco * quantidade AS valor_total
FROM produtos;

-- Funções de texto
SELECT UPPER(nome) AS nome_maiusculo, 
       LOWER(email) AS email_minusculo
FROM clientes;

-- Funções de data
SELECT nome, DATE_FORMAT(data_nascimento, '%d/%m/%Y') AS data_formatada
FROM clientes;
```

## DISTINCT para Valores Únicos

```sql
-- Listar categorias de produtos sem repetição
SELECT DISTINCT categoria FROM produtos;

-- Combinações únicas
SELECT DISTINCT cidade, estado FROM clientes;
```

## Subconsultas

```sql
-- Produtos com preço acima da média
SELECT * FROM produtos
WHERE preco > (SELECT AVG(preco) FROM produtos);

-- Clientes que fizeram pedidos no último mês
SELECT * FROM clientes
WHERE id IN (
    SELECT DISTINCT cliente_id FROM pedidos
    WHERE data_pedido >= DATE_SUB(CURRENT_DATE, INTERVAL 1 MONTH)
);
```

## Consultas Avançadas

### CASE...WHEN para valores condicionais

```sql
SELECT 
    nome,
    preco,
    CASE 
        WHEN preco < 50 THEN 'Barato'
        WHEN preco BETWEEN 50 AND 150 THEN 'Médio'
        ELSE 'Caro'
    END AS faixa_preco
FROM produtos;
```

### Funções de Janela (Window Functions)

```sql
-- Rank de produtos por preço em suas categorias
SELECT 
    nome,
    categoria,
    preco,
    RANK() OVER (PARTITION BY categoria ORDER BY preco DESC) AS posicao
FROM produtos;
```

## Boas Práticas

1. **Evite `SELECT *`** em ambientes de produção, selecione apenas as colunas necessárias
2. **Use índices** para consultas frequentes
3. **Escreva consultas claras** com identação e quebras de linha
4. **Nomeie colunas calculadas** com alias descritivos
5. **Evite subconsultas muito aninhadas** que dificultam a leitura
6. **Prefira JOINs** a subconsultas quando adequado

## Otimização de Consultas

- Use EXPLAIN para analisar como a consulta será executada
- Adicione índices nas colunas frequentemente usadas em WHERE e JOIN
- Evite funções em colunas do WHERE que impeçam o uso de índices
- Limite o número de registros quando possível
- Considere materializar consultas complexas frequentes

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/> 