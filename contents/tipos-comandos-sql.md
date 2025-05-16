<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# 🔍 Visão Geral dos Tipos de Comandos SQL

SQL (Structured Query Language) organiza seus comandos em categorias específicas, cada uma com um propósito distinto no gerenciamento de bancos de dados relacionais. Esta categorização facilita a compreensão da linguagem e a aplicação correta dos comandos.

## As Quatro Categorias Principais

1. **DDL**: Data Definition Language (Linguagem de Definição de Dados)
2. **DML**: Data Manipulation Language (Linguagem de Manipulação de Dados)
3. **DCL**: Data Control Language (Linguagem de Controle de Dados)
4. **TCL**: Transaction Control Language (Linguagem de Controle de Transações)

Vamos explorar cada categoria em detalhes.

# 📐 DDL - Data Definition Language

DDL é utilizada para definir e modificar a estrutura dos objetos no banco de dados.

## Principais Comandos DDL:

### CREATE
Cria novos objetos no banco de dados.

```sql
-- Criar banco de dados
CREATE DATABASE loja_virtual;

-- Criar tabela
CREATE TABLE produtos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    descricao TEXT,
    preco DECIMAL(10,2) NOT NULL,
    estoque INT DEFAULT 0,
    data_cadastro TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Criar índice
CREATE INDEX idx_nome_produto ON produtos(nome);

-- Criar view
CREATE VIEW produtos_disponiveis AS
    SELECT id, nome, preco FROM produtos
    WHERE estoque > 0;
```

### ALTER
Modifica objetos existentes no banco de dados.

```sql
-- Adicionar coluna
ALTER TABLE produtos ADD categoria_id INT;

-- Modificar tipo/tamanho de coluna
ALTER TABLE produtos MODIFY nome VARCHAR(150) NOT NULL;

-- Adicionar chave estrangeira
ALTER TABLE produtos 
ADD CONSTRAINT fk_categoria 
FOREIGN KEY (categoria_id) 
REFERENCES categorias(id);
```

### DROP
Remove objetos do banco de dados.

```sql
-- Remover tabela
DROP TABLE produtos;

-- Remover banco de dados
DROP DATABASE loja_virtual;

-- Remover índice
DROP INDEX idx_nome_produto ON produtos;
```

### TRUNCATE
Remove todos os registros de uma tabela, mas mantém sua estrutura.

```sql
TRUNCATE TABLE logs_acesso;
```

# 📝 DML - Data Manipulation Language

DML é utilizada para manipular os dados armazenados nas tabelas do banco de dados.

## Principais Comandos DML:

### SELECT
Recupera dados de uma ou mais tabelas.

```sql
-- Selecionar todos os campos e registros
SELECT * FROM produtos;

-- Selecionar campos específicos com condição
SELECT nome, preco, estoque 
FROM produtos 
WHERE preco < 100.00 
ORDER BY nome;

-- Selecionar com junção de tabelas
SELECT p.nome, c.nome AS categoria
FROM produtos p
JOIN categorias c ON p.categoria_id = c.id;
```

### INSERT
Adiciona novos registros em uma tabela.

```sql
-- Inserir um registro
INSERT INTO produtos (nome, descricao, preco, estoque)
VALUES ('Notebook Dell', 'Notebook Dell Inspiron 15', 3999.90, 10);

-- Inserir múltiplos registros
INSERT INTO produtos (nome, preco, estoque)
VALUES 
    ('Mouse sem fio', 99.90, 50),
    ('Teclado mecânico', 299.90, 30),
    ('Fone de ouvido', 149.90, 25);
```

### UPDATE
Modifica registros existentes em uma tabela.

```sql
-- Atualizar um campo para todos os registros
UPDATE produtos SET preco = preco * 1.10;

-- Atualizar múltiplos campos com condição
UPDATE produtos 
SET estoque = estoque - 1, 
    ultima_venda = CURRENT_TIMESTAMP
WHERE id = 5;
```

### DELETE
Remove registros de uma tabela.

```sql
-- Remover com condição
DELETE FROM produtos WHERE estoque = 0;

-- Remover todos os registros (cuidado!)
DELETE FROM produtos;
```

# 🔐 DCL - Data Control Language

DCL é utilizada para controlar os aspectos de permissão e acesso aos dados no sistema.

## Principais Comandos DCL:

### GRANT
Concede privilégios a usuários.

```sql
-- Conceder privilégios específicos
GRANT SELECT, INSERT ON loja_virtual.produtos TO 'usuario_vendas'@'localhost';

-- Conceder todos os privilégios
GRANT ALL PRIVILEGES ON loja_virtual.* TO 'admin'@'localhost';

-- Conceder privilégio com opção de repasse
GRANT SELECT ON loja_virtual.produtos TO 'gerente'@'localhost' WITH GRANT OPTION;
```

### REVOKE
Remove privilégios concedidos a usuários.

```sql
-- Revogar privilégios específicos
REVOKE INSERT ON loja_virtual.produtos FROM 'usuario_vendas'@'localhost';

-- Revogar todos os privilégios
REVOKE ALL PRIVILEGES ON loja_virtual.* FROM 'usuario_temp'@'localhost';
```

# 🔄 TCL - Transaction Control Language

TCL é utilizada para gerenciar as mudanças feitas por comandos DML, permitindo o agrupamento lógico de operações.

## Principais Comandos TCL:

### COMMIT
Salva permanentemente todas as transações realizadas desde o último COMMIT ou ROLLBACK.

```sql
START TRANSACTION;
    UPDATE contas SET saldo = saldo - 1000 WHERE cliente_id = 101;
    UPDATE contas SET saldo = saldo + 1000 WHERE cliente_id = 202;
COMMIT;
```

### ROLLBACK
Desfaz todas as transações realizadas desde o último COMMIT.

```sql
START TRANSACTION;
    DELETE FROM produtos;  -- Ops! Erro grave
ROLLBACK;  -- Desfaz a exclusão acidental
```

### SAVEPOINT
Cria pontos dentro de uma transação para os quais você pode fazer ROLLBACK.

```sql
START TRANSACTION;
    UPDATE produtos SET preco = preco * 1.05 WHERE categoria_id = 1;
    SAVEPOINT aumento_categoria_1;
    
    UPDATE produtos SET preco = preco * 1.08 WHERE categoria_id = 2;
    SAVEPOINT aumento_categoria_2;
    
    -- Ops, aumento excessivo na categoria 2
    ROLLBACK TO aumento_categoria_1;
    
    -- Continua com outras operações
    UPDATE produtos SET preco = preco * 1.03 WHERE categoria_id = 3;
COMMIT;
```

### SET TRANSACTION
Define características para a transação.

```sql
SET TRANSACTION READ ONLY;  -- Transação apenas leitura
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;  -- Define nível de isolamento
```

# 📊 Comparação entre Categorias de Comandos SQL

| Categoria | Finalidade | Comandos Principais | Atua sobre |
|-----------|------------|---------------------|------------|
| **DDL** | Definir estrutura | CREATE, ALTER, DROP, TRUNCATE | Objetos do banco (tabelas, índices) |
| **DML** | Manipular registros | SELECT, INSERT, UPDATE, DELETE | Dados armazenados |
| **DCL** | Controlar acesso | GRANT, REVOKE | Permissões e segurança |
| **TCL** | Gerenciar transações | COMMIT, ROLLBACK, SAVEPOINT | Integridade transacional |

# 💡 Boas Práticas

1. **Use controle de transações**: Sempre use transações para operações críticas que envolvam múltiplas tabelas
2. **Privilégios mínimos**: Conceda apenas os privilégios necessários aos usuários
3. **Cuidado com comandos destrutivos**: Tenha cautela com DROP, DELETE sem WHERE e TRUNCATE
4. **Backups antes de DDL**: Faça backups antes de executar comandos DDL em ambientes de produção
5. **Comentários em scripts**: Documente suas intenções em scripts SQL complexos

```sql
-- Exemplo de script bem documentado
-- Atualização de preços para promoção de verão - 15/01/2023
START TRANSACTION;

-- Aumento de 5% nos eletrônicos (exceto notebooks)
UPDATE produtos 
SET preco = preco * 1.05 
WHERE categoria_id = 1 AND subcategoria_id != 3;

-- Verificação dos produtos afetados
SELECT COUNT(*) FROM produtos 
WHERE categoria_id = 1 AND subcategoria_id != 3;

-- Confirmação das alterações
COMMIT;
```

# 🌐 Dialetos SQL

É importante observar que existem pequenas variações na sintaxe entre diferentes SGBDs:

## MySQL/MariaDB:
```sql
-- Criação de usuário e privilégios
CREATE USER 'usuario'@'localhost' IDENTIFIED BY 'senha';
GRANT SELECT ON banco.* TO 'usuario'@'localhost';
```

## PostgreSQL:
```sql
-- Criação de usuário e privilégios
CREATE USER usuario WITH PASSWORD 'senha';
GRANT SELECT ON ALL TABLES IN SCHEMA public TO usuario;
```

## SQL Server:
```sql
-- Criação de usuário e privilégios
CREATE LOGIN usuario WITH PASSWORD = 'senha';
CREATE USER usuario FOR LOGIN usuario;
GRANT SELECT TO usuario;
```

## Oracle:
```sql
-- Criação de usuário e privilégios
CREATE USER usuario IDENTIFIED BY senha;
GRANT SELECT ANY TABLE TO usuario;
```

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/> 