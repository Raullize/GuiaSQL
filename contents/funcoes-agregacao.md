<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# 📊 Funções de Agregação

## O que são Funções de Agregação?

**Funções de agregação** são operações SQL que realizam cálculos em conjuntos de valores, retornando um único valor resumido. Elas são fundamentais para análise de dados e geração de relatórios, permitindo extrair estatísticas e métricas importantes de grandes conjuntos de dados.

## Principais Funções de Agregação

### COUNT()

A função `COUNT()` retorna o número de linhas ou valores não nulos em um conjunto de resultados.

```sql
-- Contar todos os registros
SELECT COUNT(*) FROM funcionarios;

-- Contar valores não nulos em uma coluna específica
SELECT COUNT(email) FROM funcionarios;

-- Contar valores distintos
SELECT COUNT(DISTINCT departamento) FROM funcionarios;
```

### SUM()

A função `SUM()` calcula a soma dos valores em uma coluna numérica.

```sql
-- Soma de todos os salários
SELECT SUM(salario) FROM funcionarios;

-- Soma por departamento
SELECT departamento, SUM(salario) AS total_salarios
FROM funcionarios
GROUP BY departamento;
```

### AVG()

A função `AVG()` calcula a média aritmética dos valores em uma coluna numérica.

```sql
-- Média de todos os salários
SELECT AVG(salario) FROM funcionarios;

-- Média de idade por departamento
SELECT departamento, AVG(idade) AS media_idade
FROM funcionarios
GROUP BY departamento;
```

### MIN() e MAX()

As funções `MIN()` e `MAX()` retornam o valor mínimo e máximo, respectivamente, de uma coluna.

```sql
-- Menor e maior salário
SELECT MIN(salario) AS menor_salario, MAX(salario) AS maior_salario
FROM funcionarios;

-- Data do funcionário mais antigo e mais recente
SELECT MIN(data_contratacao) AS mais_antigo, MAX(data_contratacao) AS mais_recente
FROM funcionarios;
```

## GROUP BY

O comando `GROUP BY` é frequentemente usado com funções de agregação para agrupar o conjunto de resultados por uma ou mais colunas.

```sql
-- Contagem de funcionários por departamento
SELECT departamento, COUNT(*) AS total_funcionarios
FROM funcionarios
GROUP BY departamento;

-- Média de salário por departamento e cargo
SELECT departamento, cargo, AVG(salario) AS media_salarial
FROM funcionarios
GROUP BY departamento, cargo;
```

## HAVING

A cláusula `HAVING` filtra os resultados de grupos após a aplicação de funções de agregação.

```sql
-- Departamentos com mais de 5 funcionários
SELECT departamento, COUNT(*) AS total
FROM funcionarios
GROUP BY departamento
HAVING COUNT(*) > 5;

-- Departamentos onde a média salarial é maior que 5000
SELECT departamento, AVG(salario) AS media_salarial
FROM funcionarios
GROUP BY departamento
HAVING AVG(salario) > 5000;
```

## Funções de Agregação Avançadas

### STDDEV() e VARIANCE()

Calcula o desvio padrão e a variância dos valores.

```sql
-- Desvio padrão dos salários
SELECT STDDEV(salario) FROM funcionarios;

-- Variância dos salários por departamento
SELECT departamento, VARIANCE(salario)
FROM funcionarios
GROUP BY departamento;
```

### GROUP_CONCAT()

Concatena valores de um grupo em uma única string (disponível no MySQL).

```sql
-- Lista de funcionários por departamento
SELECT departamento, GROUP_CONCAT(nome SEPARATOR ', ') AS funcionarios
FROM funcionarios
GROUP BY departamento;
```

## Casos de Uso Práticos

### Análise de vendas

```sql
-- Total de vendas por mês
SELECT 
    YEAR(data_venda) AS ano,
    MONTH(data_venda) AS mes,
    SUM(valor) AS total_vendas,
    COUNT(*) AS numero_vendas,
    AVG(valor) AS ticket_medio
FROM vendas
GROUP BY YEAR(data_venda), MONTH(data_venda)
ORDER BY ano, mes;
```

### Relatório de produtos

```sql
-- Análise de estoque por categoria
SELECT 
    categoria,
    COUNT(*) AS total_produtos,
    SUM(quantidade) AS total_estoque,
    MIN(preco) AS menor_preco,
    MAX(preco) AS maior_preco,
    AVG(preco) AS preco_medio
FROM produtos
GROUP BY categoria
HAVING total_estoque > 0
ORDER BY total_estoque DESC;
```

## Boas Práticas

1. Sempre use nomes claros para as colunas resultantes com `AS`
2. Tenha cuidado com valores NULL nas funções de agregação
3. Combine GROUP BY com ORDER BY para resultados mais organizados
4. Use HAVING para filtrar grupos, não WHERE (que filtra antes do agrupamento)
5. Lembre-se que todas as colunas no SELECT que não são funções de agregação devem estar no GROUP BY

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/> 