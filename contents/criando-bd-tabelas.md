<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# Criando e Gerenciando Bancos de Dados

A criação de bancos de dados e tabelas é uma das primeiras tarefas ao desenvolver um sistema que utiliza SQL. Neste guia, vamos abordar desde a criação básica até técnicas mais avançadas de modelagem e implementação.

## Criação de Bancos de Dados

A criação de um banco de dados é realizada com o comando `CREATE DATABASE`. Este comando inicializa um novo banco de dados vazio onde você poderá criar suas tabelas e outros objetos.

```sql
-- Sintaxe básica
CREATE DATABASE nome_do_banco;

-- Exemplo prático
CREATE DATABASE loja_virtual;
```

### Com Opções Específicas

Em sistemas mais complexos, você pode especificar opções adicionais na criação:

```sql
-- MySQL: Especificar conjunto de caracteres e colação
CREATE DATABASE loja_virtual
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;

-- PostgreSQL: Especificar proprietário e codificação
CREATE DATABASE loja_virtual
WITH OWNER = admin
ENCODING = 'UTF8'
LC_COLLATE = 'pt_BR.UTF-8'
LC_CTYPE = 'pt_BR.UTF-8';
```

### Verificação Prévia

Uma boa prática é verificar se o banco já existe antes de tentar criá-lo:

```sql
-- MySQL
CREATE DATABASE IF NOT EXISTS loja_virtual;

-- PostgreSQL
DO $$
BEGIN
    IF NOT EXISTS (SELECT FROM pg_database WHERE datname = 'loja_virtual') THEN
        CREATE DATABASE loja_virtual;
    END IF;
END $$;
```

### Selecionando o Banco para Uso

Após criar o banco, você deve selecioná-lo para uso:

```sql
-- MySQL
USE loja_virtual;

-- PostgreSQL
\c loja_virtual;

-- SQL Server
USE loja_virtual;
```

## Alterando e Removendo Bancos de Dados

```sql
-- Alterar características do banco (MySQL)
ALTER DATABASE loja_virtual
CHARACTER SET = utf8mb4
COLLATE = utf8mb4_unicode_ci;

-- Remover banco de dados (⚠️ Cuidado: operação irreversível)
DROP DATABASE loja_virtual;

-- Versão mais segura
DROP DATABASE IF EXISTS loja_virtual;
```

# Criando e Gerenciando Tabelas

As tabelas são os principais objetos de armazenamento em um banco de dados relacional. Vamos explorar diferentes aspectos da criação de tabelas.

## Sintaxe Básica

```sql
CREATE TABLE nome_tabela (
    coluna1 tipo_dados [restrições],
    coluna2 tipo_dados [restrições],
    ...
    [restrições_de_tabela]
);
```

## Exemplo Completo

```sql
CREATE TABLE produtos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    descricao TEXT,
    preco DECIMAL(10,2) NOT NULL CHECK (preco > 0),
    categoria_id INT,
    estoque INT DEFAULT 0,
    data_cadastro TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    ativo BOOLEAN DEFAULT TRUE,
    CONSTRAINT fk_categoria FOREIGN KEY (categoria_id) 
        REFERENCES categorias(id) ON DELETE SET NULL
);
```

## Tipos de Dados Comuns

O SQL oferece diversos tipos de dados para diferentes necessidades. Aqui estão os mais utilizados:

### Numéricos

| Tipo | Descrição | Exemplo |
|------|-----------|---------|
| INT / INTEGER | Números inteiros | ID de produto |
| DECIMAL(p,s) | Números decimais com precisão 'p' e escala 's' | Preço |
| FLOAT / DOUBLE | Números de ponto flutuante | Cálculos científicos |
| TINYINT | Inteiros pequenos (0-255) | Flags, status |
| BOOL / BOOLEAN | Valores booleanos (verdadeiro/falso) | Status ativo |

### Textuais

| Tipo | Descrição | Exemplo |
|------|-----------|---------|
| CHAR(n) | Texto de tamanho fixo (n caracteres) | Códigos de país (BR, US) |
| VARCHAR(n) | Texto de tamanho variável (até n caracteres) | Nomes, emails |
| TEXT | Texto longo sem limite predefinido | Descrições, artigos |
| ENUM | Lista de valores possíveis | Status ('pendente', 'aprovado', 'recusado') |

### Data e Hora

| Tipo | Descrição | Exemplo |
|------|-----------|---------|
| DATE | Apenas data (YYYY-MM-DD) | Data de nascimento |
| TIME | Apenas hora (HH:MM:SS) | Horário de expediente |
| DATETIME | Data e hora | Agendamento |
| TIMESTAMP | Momento preciso (com fuso horário) | Log de eventos |

### Outros

| Tipo | Descrição | Exemplo |
|------|-----------|---------|
| BLOB | Dados binários | Imagens, arquivos |
| JSON | Dados no formato JSON | Configurações, atributos dinâmicos |
| UUID | Identificador único universal | IDs distribuídos |

## Restrições (Constraints)

As restrições garantem a integridade dos dados:

```sql
CREATE TABLE clientes (
    id INT AUTO_INCREMENT,
    cpf VARCHAR(14) UNIQUE,
    nome VARCHAR(100) NOT NULL,
    data_nascimento DATE,
    limite_credito DECIMAL(10,2) DEFAULT 0 CHECK (limite_credito >= 0),
    email VARCHAR(100),
    PRIMARY KEY (id),
    CONSTRAINT uk_email UNIQUE (email)
);
```

Principais restrições:
- **PRIMARY KEY**: Identifica unicamente cada registro
- **FOREIGN KEY**: Estabelece relação com outra tabela
- **UNIQUE**: Garante que não existam valores duplicados
- **NOT NULL**: Obriga o preenchimento do campo
- **DEFAULT**: Define um valor padrão
- **CHECK**: Valida o valor segundo uma condição
- **INDEX**: Melhora a performance das consultas

## Tabelas Temporárias

Para operações temporárias ou processamento intermediário:

```sql
-- Tabela temporária que existe apenas na sessão atual
CREATE TEMPORARY TABLE temp_resultados (
    id INT,
    valor DECIMAL(10,2),
    data_calculo TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Após uso, você pode removê-la explicitamente
DROP TEMPORARY TABLE IF EXISTS temp_resultados;
```

## Alteração de Tabelas

As necessidades mudam com o tempo e você precisará alterar suas tabelas:

```sql
-- Adicionar coluna
ALTER TABLE produtos ADD COLUMN fabricante VARCHAR(100);

-- Remover coluna
ALTER TABLE produtos DROP COLUMN fabricante;

-- Modificar tipo/propriedades de coluna
ALTER TABLE produtos MODIFY COLUMN nome VARCHAR(150) NOT NULL;

-- Renomear coluna
ALTER TABLE produtos CHANGE COLUMN nome nome_produto VARCHAR(150) NOT NULL;

-- Adicionar restrição
ALTER TABLE produtos ADD CONSTRAINT fk_fornecedor 
    FOREIGN KEY (fornecedor_id) REFERENCES fornecedores(id);

-- Remover restrição
ALTER TABLE produtos DROP CONSTRAINT fk_fornecedor;

-- Renomear tabela
ALTER TABLE produtos RENAME TO produtos_catalogo;
```

## Clonagem e Arquivamento de Tabelas

Para backups, testes ou arquivamento:

```sql
-- Criar cópia da estrutura sem dados
CREATE TABLE produtos_bkp LIKE produtos;

-- Criar cópia com dados
CREATE TABLE produtos_bkp AS SELECT * FROM produtos;

-- Criar cópia parcial com dados filtrados
CREATE TABLE produtos_inativos AS 
SELECT * FROM produtos WHERE ativo = FALSE;
```

# Modelagem Eficiente de Tabelas

## Normalização vs. Desnormalização

É crucial encontrar o equilíbrio entre normalização (eliminação de redundâncias) e performance:

```sql
-- Tabela normalizada (3FN)
CREATE TABLE produtos (
    id INT PRIMARY KEY,
    nome VARCHAR(100),
    preco DECIMAL(10,2),
    categoria_id INT,
    FOREIGN KEY (categoria_id) REFERENCES categorias(id)
);

-- Tabela parcialmente desnormalizada para relatórios
CREATE TABLE vendas_consolidadas (
    id INT PRIMARY KEY,
    data_venda DATE,
    produto_id INT,
    produto_nome VARCHAR(100),  -- Desnormalização intencional
    categoria_nome VARCHAR(50), -- Desnormalização intencional
    quantidade INT,
    valor_unitario DECIMAL(10,2),
    valor_total DECIMAL(10,2)
);
```

## Particionamento de Tabelas

Para tabelas muito grandes, o particionamento pode melhorar o desempenho:

```sql
-- MySQL: Particionamento por faixa de valores
CREATE TABLE historico_vendas (
    id INT,
    data_venda DATE,
    valor DECIMAL(10,2),
    cliente_id INT,
    PRIMARY KEY (id, data_venda)
) 
PARTITION BY RANGE (YEAR(data_venda)) (
    PARTITION p2020 VALUES LESS THAN (2021),
    PARTITION p2021 VALUES LESS THAN (2022),
    PARTITION p2022 VALUES LESS THAN (2023),
    PARTITION p2023 VALUES LESS THAN (2024),
    PARTITION future VALUES LESS THAN MAXVALUE
);
```

## Tabelas com Herança (PostgreSQL)

Para modelar hierarquias:

```sql
-- Tabela base
CREATE TABLE pessoas (
    id SERIAL PRIMARY KEY,
    nome VARCHAR(100),
    email VARCHAR(100),
    data_nascimento DATE
);

-- Tabela especializada que herda (PostgreSQL)
CREATE TABLE funcionarios (
    data_contratacao DATE,
    salario DECIMAL(10,2),
    departamento_id INT
) INHERITS (pessoas);
```

# Boas Práticas

1. **Nomenclatura consistente**: Use nomes claros e significativos
   ```sql
   -- Bom
   CREATE TABLE produtos_pedidos (
       produto_id INT,
       pedido_id INT,
       quantidade INT
   );
  
   -- Evitar
   CREATE TABLE pp (
       pid INT,
       oid INT,
       q INT
   );
   ```

2. **Documentação em comentários**: Documente suas tabelas e colunas
   ```sql
   -- MySQL
   CREATE TABLE transacoes (
       id INT AUTO_INCREMENT PRIMARY KEY COMMENT 'Identificador único da transação',
       valor DECIMAL(10,2) COMMENT 'Valor monetário da transação',
       tipo ENUM('crédito', 'débito') COMMENT 'Tipo de operação financeira'
   ) COMMENT 'Registro de todas as operações financeiras do sistema';
   ```

3. **Indexação adequada**: Crie índices para campos frequentemente filtrados
   ```sql
   CREATE TABLE pedidos (
       id INT PRIMARY KEY,
       cliente_id INT,
       data DATE,
       status VARCHAR(20),
       valor_total DECIMAL(10,2),
       INDEX idx_cliente (cliente_id),
       INDEX idx_data (data),
       INDEX idx_status (status)
   );
   ```

4. **Chaves primárias eficientes**: Escolha tipos adequados para chaves primárias
   ```sql
   -- Para alta performance e distribuição
   CREATE TABLE logs (
       id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
       -- ou
       id UUID DEFAULT UUID(),
       timestamp TIMESTAMP,
       evento VARCHAR(100)
   );
   ```

5. **Scripts reproduzíveis**: Use verificações para tornar scripts idempotentes
   ```sql
   -- Pode ser executado múltiplas vezes sem erro
   CREATE DATABASE IF NOT EXISTS meu_banco;
   USE meu_banco;
  
   DROP TABLE IF EXISTS produtos;
   CREATE TABLE produtos (
       id INT PRIMARY KEY,
       nome VARCHAR(100)
   );
   ```

# Exemplos Práticos de Esquemas

## Sistema de E-commerce

```sql
-- Banco de dados
CREATE DATABASE ecommerce CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE ecommerce;

-- Tabelas principais
CREATE TABLE categorias (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(50) NOT NULL,
    descricao TEXT,
    categoria_pai_id INT,
    FOREIGN KEY (categoria_pai_id) REFERENCES categorias(id)
);

CREATE TABLE produtos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    sku VARCHAR(20) UNIQUE,
    nome VARCHAR(100) NOT NULL,
    descricao TEXT,
    preco DECIMAL(10,2) NOT NULL,
    preco_promocional DECIMAL(10,2),
    estoque INT NOT NULL DEFAULT 0,
    categoria_id INT,
    data_cadastro TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (categoria_id) REFERENCES categorias(id)
);

CREATE TABLE clientes (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    senha VARCHAR(255) NOT NULL,
    cpf VARCHAR(14) UNIQUE,
    telefone VARCHAR(20),
    data_nascimento DATE,
    data_cadastro TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE enderecos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    cliente_id INT NOT NULL,
    tipo ENUM('residencial', 'comercial', 'entrega') DEFAULT 'entrega',
    logradouro VARCHAR(100) NOT NULL,
    numero VARCHAR(20),
    complemento VARCHAR(50),
    bairro VARCHAR(50) NOT NULL,
    cidade VARCHAR(50) NOT NULL,
    estado CHAR(2) NOT NULL,
    cep VARCHAR(9) NOT NULL,
    principal BOOLEAN DEFAULT FALSE,
    FOREIGN KEY (cliente_id) REFERENCES clientes(id) ON DELETE CASCADE
);

CREATE TABLE pedidos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    cliente_id INT NOT NULL,
    endereco_entrega_id INT NOT NULL,
    data_pedido TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    status ENUM('aguardando_pagamento', 'pago', 'em_separacao', 'enviado', 'entregue', 'cancelado') DEFAULT 'aguardando_pagamento',
    valor_produtos DECIMAL(10,2) NOT NULL,
    valor_frete DECIMAL(10,2) NOT NULL,
    valor_desconto DECIMAL(10,2) DEFAULT 0,
    valor_total DECIMAL(10,2) GENERATED ALWAYS AS (valor_produtos + valor_frete - valor_desconto) STORED,
    FOREIGN KEY (cliente_id) REFERENCES clientes(id),
    FOREIGN KEY (endereco_entrega_id) REFERENCES enderecos(id)
);

CREATE TABLE itens_pedido (
    pedido_id INT NOT NULL,
    produto_id INT NOT NULL,
    quantidade INT NOT NULL,
    preco_unitario DECIMAL(10,2) NOT NULL,
    subtotal DECIMAL(10,2) GENERATED ALWAYS AS (quantidade * preco_unitario) STORED,
    PRIMARY KEY (pedido_id, produto_id),
    FOREIGN KEY (pedido_id) REFERENCES pedidos(id) ON DELETE CASCADE,
    FOREIGN KEY (produto_id) REFERENCES produtos(id)
);

-- Índices para otimização
CREATE INDEX idx_produtos_categoria ON produtos(categoria_id);
CREATE INDEX idx_pedidos_cliente ON pedidos(cliente_id);
CREATE INDEX idx_pedidos_status ON pedidos(status);
CREATE INDEX idx_produtos_nome ON produtos(nome);
```

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/>