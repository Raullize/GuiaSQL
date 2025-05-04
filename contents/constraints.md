<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# 🔑 Constraints (Restrições)

## O que são Constraints?

**Constraints** (restrições) são regras aplicadas às colunas ou tabelas em um banco de dados relacional que controlam os dados que podem ser inseridos, modificados ou excluídos. Elas garantem a integridade, precisão e confiabilidade dos dados.

As constraints são fundamentais para manter a consistência e a qualidade dos dados em um banco de dados.

## Tipos de Constraints

### 1. PRIMARY KEY (Chave Primária)

Identifica exclusivamente cada registro em uma tabela. Não permite valores NULL e deve ser única.

```sql
-- Definindo PRIMARY KEY na criação da tabela
CREATE TABLE clientes (
    id INT PRIMARY KEY,
    nome VARCHAR(100),
    email VARCHAR(100)
);

-- Alternativa com nome da constraint
CREATE TABLE clientes (
    id INT,
    nome VARCHAR(100),
    email VARCHAR(100),
    CONSTRAINT pk_clientes PRIMARY KEY (id)
);

-- Adicionando PRIMARY KEY a uma tabela existente
ALTER TABLE clientes
ADD PRIMARY KEY (id);
```

### 2. FOREIGN KEY (Chave Estrangeira)

Estabelece relação entre duas tabelas. Garante que os valores em uma coluna correspondam a valores existentes na tabela referenciada.

```sql
-- Definindo FOREIGN KEY na criação da tabela
CREATE TABLE pedidos (
    id INT PRIMARY KEY,
    cliente_id INT,
    data_pedido DATE,
    FOREIGN KEY (cliente_id) REFERENCES clientes(id)
);

-- Alternativa com nome da constraint e comportamentos
CREATE TABLE pedidos (
    id INT PRIMARY KEY,
    cliente_id INT,
    data_pedido DATE,
    CONSTRAINT fk_cliente FOREIGN KEY (cliente_id)
    REFERENCES clientes(id)
    ON DELETE CASCADE
    ON UPDATE RESTRICT
);

-- Adicionando FOREIGN KEY a uma tabela existente
ALTER TABLE pedidos
ADD CONSTRAINT fk_cliente FOREIGN KEY (cliente_id)
REFERENCES clientes(id);
```

#### Opções de ON DELETE e ON UPDATE:

- **CASCADE**: Quando o registro pai é excluído/atualizado, os registros filhos também são
- **RESTRICT/NO ACTION**: Impede a exclusão/atualização do registro pai se existirem registros filhos relacionados
- **SET NULL**: Define valores NULL nas colunas que fazem referência ao registro pai
- **SET DEFAULT**: Define o valor padrão nas colunas que fazem referência ao registro pai

### 3. UNIQUE (Valor Único)

Garante que todos os valores em uma coluna sejam diferentes.

```sql
-- Definindo UNIQUE na criação da tabela
CREATE TABLE usuarios (
    id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE,
    username VARCHAR(50)
);

-- Alternativa com nome da constraint
CREATE TABLE usuarios (
    id INT PRIMARY KEY,
    email VARCHAR(100),
    username VARCHAR(50),
    CONSTRAINT uk_email UNIQUE (email)
);

-- Definindo UNIQUE para múltiplas colunas
CREATE TABLE produtos (
    id INT PRIMARY KEY,
    codigo VARCHAR(20),
    lote VARCHAR(20),
    CONSTRAINT uk_codigo_lote UNIQUE (codigo, lote)
);

-- Adicionando UNIQUE a uma tabela existente
ALTER TABLE usuarios
ADD CONSTRAINT uk_username UNIQUE (username);
```

### 4. NOT NULL (Não Nulo)

Garante que uma coluna não aceite valores NULL.

```sql
-- Definindo NOT NULL na criação da tabela
CREATE TABLE funcionarios (
    id INT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    cargo VARCHAR(50) NOT NULL,
    departamento VARCHAR(50)
);

-- Modificando uma coluna para NOT NULL
ALTER TABLE funcionarios
MODIFY departamento VARCHAR(50) NOT NULL;
```

### 5. CHECK (Verificação)

Garante que os valores em uma coluna satisfaçam uma condição específica.

```sql
-- Definindo CHECK na criação da tabela
CREATE TABLE produtos (
    id INT PRIMARY KEY,
    nome VARCHAR(100),
    preco DECIMAL(10,2) CHECK (preco > 0),
    quantidade INT CHECK (quantidade >= 0)
);

-- Alternativa com nome da constraint
CREATE TABLE funcionarios (
    id INT PRIMARY KEY,
    nome VARCHAR(100),
    salario DECIMAL(10,2),
    CONSTRAINT chk_salario CHECK (salario >= 1100)
);

-- CHECK com múltiplas condições
CREATE TABLE alunos (
    id INT PRIMARY KEY,
    nome VARCHAR(100),
    idade INT,
    nota DECIMAL(3,1),
    CONSTRAINT chk_aluno CHECK (idade >= 18 AND nota BETWEEN 0 AND 10)
);

-- Adicionando CHECK a uma tabela existente
ALTER TABLE produtos
ADD CONSTRAINT chk_preco_positivo CHECK (preco > 0);
```

### 6. DEFAULT (Valor Padrão)

Define um valor padrão para uma coluna quando nenhum valor é especificado.

```sql
-- Definindo DEFAULT na criação da tabela
CREATE TABLE pedidos (
    id INT PRIMARY KEY,
    cliente_id INT,
    status VARCHAR(20) DEFAULT 'Pendente',
    data_criacao DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Adicionando DEFAULT a uma coluna existente
ALTER TABLE pedidos
ALTER COLUMN status SET DEFAULT 'Pendente';
```

## Gerenciando Constraints

### Removendo uma constraint

```sql
-- Remover PRIMARY KEY
ALTER TABLE clientes
DROP PRIMARY KEY;

-- Remover FOREIGN KEY
ALTER TABLE pedidos
DROP CONSTRAINT fk_cliente;

-- Remover UNIQUE
ALTER TABLE usuarios
DROP CONSTRAINT uk_email;

-- Remover CHECK
ALTER TABLE produtos
DROP CONSTRAINT chk_preco_positivo;
```

### Desabilitando e habilitando constraints (SQL Server)

```sql
-- Desabilitar todas as constraints de uma tabela
ALTER TABLE pedidos NOCHECK CONSTRAINT ALL;

-- Habilitar todas as constraints de uma tabela
ALTER TABLE pedidos CHECK CONSTRAINT ALL;

-- Desabilitar uma constraint específica
ALTER TABLE pedidos NOCHECK CONSTRAINT fk_cliente;

-- Habilitar uma constraint específica
ALTER TABLE pedidos CHECK CONSTRAINT fk_cliente;
```

## Boas Práticas

1. **Nomeie suas constraints**: Usar nomes descritivos facilita a manutenção
2. **Use NOT NULL para colunas essenciais**: Garante que dados importantes sejam sempre fornecidos
3. **Defina PRIMARY KEY para todas as tabelas**: Cada tabela deve ter uma identificação única
4. **Use FOREIGN KEY para garantir integridade referencial**: Evita registros órfãos
5. **Aplique CHECK para validar regras de negócio**: Garante que os dados sigam regras específicas
6. **Use DEFAULT para valores comuns**: Economiza tempo na inserção e garante consistência

## Dicas de Performance

- Constraints podem afetar a performance de operações INSERT, UPDATE e DELETE
- PRIMARY KEY e FOREIGN KEY geralmente criam índices automaticamente
- CHECK e UNIQUE podem impactar a performance em tabelas grandes
- Considere o uso de triggers como alternativa a constraints complexas quando necessário

## Diferenças Entre SGBDs

- **MySQL (InnoDB)**: Suporta todas as constraints principais
- **PostgreSQL**: Implementação robusta de todas as constraints
- **SQL Server**: Suporta desabilitação temporária de constraints
- **SQLite**: Implementação limitada de algumas constraints
- **Oracle**: Suporta todas as constraints e adiciona recursos avançados

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/> 