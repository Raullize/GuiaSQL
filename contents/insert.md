<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# 📝 C - Inserção de Dados (INSERT)

## O que é o Comando INSERT?

O comando **INSERT** é usado para adicionar novos registros em uma tabela. Este comando faz parte da categoria **DML** (Data Manipulation Language) do SQL e representa o "C" (Create) no acrônimo CRUD (Create, Read, Update, Delete).

## Sintaxe Básica

Existem duas formas principais de usar o comando INSERT:

### 1. Especificando colunas

```sql
INSERT INTO nome_tabela (coluna1, coluna2, coluna3, ...)
VALUES (valor1, valor2, valor3, ...);
```

### 2. Inserindo em todas as colunas (na ordem da definição da tabela)

```sql
INSERT INTO nome_tabela
VALUES (valor1, valor2, valor3, ...);
```

## Exemplos Práticos

### Inserção simples com colunas específicas

```sql
INSERT INTO clientes (nome, email, telefone)
VALUES ('João Silva', 'joao@email.com', '(11) 98765-4321');
```

### Inserção em todas as colunas

```sql
INSERT INTO produtos
VALUES (1, 'Monitor LCD 24"', 'Eletrônicos', 899.90, 15);
```

### Inserindo múltiplos registros de uma vez

```sql
INSERT INTO funcionarios (nome, cargo, salario)
VALUES 
    ('Ana Santos', 'Desenvolvedora', 5800.00),
    ('Carlos Ferreira', 'Designer', 4500.00),
    ('Mariana Costa', 'Gerente de Projeto', 7200.00);
```

## Inserção com AUTO_INCREMENT

Em tabelas com colunas de auto incremento (como IDs), você pode omitir esse valor ou usar NULL:

```sql
-- Omitindo a coluna de ID
INSERT INTO categorias (nome, descricao)
VALUES ('Eletrônicos', 'Produtos eletrônicos em geral');

-- Usando NULL para o valor de auto incremento
INSERT INTO categorias (id, nome, descricao)
VALUES (NULL, 'Vestuário', 'Roupas e acessórios');
```

## INSERT com Subconsultas

Você pode inserir dados usando os resultados de outra consulta:

```sql
-- Inserir produtos com desconto de outra tabela
INSERT INTO produtos_promocao (produto_id, nome, preco_promocional)
SELECT id, nome, preco * 0.85
FROM produtos
WHERE categoria = 'Eletrônicos';
```

## INSERT IGNORE

No MySQL, você pode usar o `INSERT IGNORE` para ignorar erros e continuar a inserção:

```sql
-- Ignora duplicação de chave primária e continua inserindo outros registros
INSERT IGNORE INTO clientes (id, nome, email)
VALUES 
    (1, 'Pedro Alves', 'pedro@email.com'),
    (2, 'Camila Lima', 'camila@email.com');
```

## INSERT ... ON DUPLICATE KEY UPDATE

Esta sintaxe permite atualizar um registro caso ele já exista:

```sql
INSERT INTO produtos (codigo, nome, quantidade)
VALUES ('P001', 'Teclado Mecânico', 10)
ON DUPLICATE KEY UPDATE quantidade = quantidade + 10;
```

## DEFAULT VALUES

Você pode inserir um registro usando valores padrão para todas as colunas:

```sql
-- Em algumas implementações SQL
INSERT INTO log_acesso DEFAULT VALUES;

-- No MySQL
INSERT INTO log_acesso () VALUES ();
```

## Valores Especiais e Funções

```sql
-- Usando funções para a data atual
INSERT INTO pedidos (cliente_id, data_pedido, status)
VALUES (103, CURRENT_DATE(), 'Pendente');

-- No MySQL
INSERT INTO acessos (usuario_id, data_hora, ip)
VALUES (201, NOW(), '192.168.1.1');
```

## Boas Práticas

1. **Sempre especifique as colunas** para evitar problemas com alterações futuras na estrutura da tabela
2. **Valide os dados** antes de inserir para evitar erros de restrição
3. **Use transações** para inserções múltiplas relacionadas
4. **Tenha cuidado com strings**: use aspas simples e escape caracteres especiais
5. **Evite inserções em massa** muito grandes, divida em lotes menores

## Erros Comuns

- Tentar inserir um valor que viola uma restrição (ex: chave primária duplicada)
- Omitir colunas NOT NULL sem valor padrão
- Inserir tipos de dados incompatíveis
- Ordem incorreta dos valores se não especificar as colunas

## Diferenças entre MySQL, PostgreSQL e SQLite

Embora a sintaxe básica seja semelhante, há algumas diferenças:

- **MySQL**: Oferece `INSERT IGNORE` e `ON DUPLICATE KEY UPDATE`
- **PostgreSQL**: Usa `ON CONFLICT` em vez de `ON DUPLICATE KEY`
- **SQLite**: Tem sintaxe mais simples e menos opções avançadas

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/> 