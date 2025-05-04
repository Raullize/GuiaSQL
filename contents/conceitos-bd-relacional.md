<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# 🗄️ Conceitos de Banco de Dados Relacional

## Fundamentos do Modelo Relacional

O **modelo relacional** é um modelo de dados que organiza as informações em coleções de tabelas bidimensionais, chamadas "relações". Foi proposto por Edgar F. Codd em 1970 e revolucionou a maneira como os dados são armazenados e acessados em sistemas computacionais.

## Elementos Básicos de um Banco de Dados Relacional

### Tabelas (Relações)

Uma **tabela** (ou relação) é uma estrutura bidimensional composta por linhas e colunas que armazena dados. Cada tabela em um banco de dados relacional representa uma entidade específica, como clientes, produtos ou pedidos.

Características das tabelas:
- Cada tabela tem um nome único no banco de dados
- Não há ordem específica de linhas ou colunas
- Cada célula contém exatamente um valor atômico (indivisível)
- Todas as linhas devem ser diferentes

Exemplo de tabela "Clientes":

| id | nome        | email               | telefone      |
|----|-------------|---------------------|---------------|
| 1  | Ana Silva   | ana@email.com       | (11) 1234-5678 |
| 2  | Bruno Costa | bruno@email.com     | (21) 8765-4321 |
| 3  | Carla Lima  | carla@email.com     | (31) 5555-1234 |

### Registros (Tuplas ou Linhas)

Um **registro** (também chamado de tupla ou linha) representa uma instância única de uma entidade. No exemplo acima, cada linha representa um cliente diferente, com todos os seus atributos.

Características dos registros:
- Cada registro geralmente possui um identificador único (chave primária)
- A ordem dos registros não importa logicamente
- Cada registro deve ser único na tabela (não são permitidas duplicatas exatas)
- O número de registros em uma tabela pode mudar dinamicamente

### Atributos (Colunas ou Campos)

Um **atributo** (também chamado de coluna ou campo) representa uma característica específica de uma entidade. Cada atributo possui:

- Um nome único dentro da tabela
- Um tipo de dados específico (texto, número, data, etc.)
- Potencialmente, restrições em seu conteúdo

Tipos comuns de dados para atributos:
- **INT/INTEGER**: Números inteiros
- **DECIMAL/NUMERIC**: Números com casas decimais
- **VARCHAR/TEXT**: Texto de comprimento variável
- **CHAR**: Texto de comprimento fixo
- **DATE/DATETIME**: Datas e horas
- **BOOLEAN**: Valores verdadeiro/falso
- **BLOB**: Dados binários grandes (imagens, arquivos)

### Domínio

O **domínio** de um atributo é o conjunto de valores válidos que esse atributo pode assumir. Por exemplo:
- O domínio de um atributo "idade" pode ser números inteiros de 0 a 150
- O domínio de um atributo "estado civil" pode ser {'Solteiro', 'Casado', 'Divorciado', 'Viúvo'}

## Conceitos Adicionais

### Chaves

As **chaves** são fundamentais para o modelo relacional, pois estabelecem a unicidade e as relações entre tabelas.

#### Chave Primária (Primary Key)

Uma **chave primária** é um atributo (ou conjunto de atributos) que identifica exclusivamente cada registro em uma tabela. Características:
- Deve ser única para cada registro
- Não pode conter valores nulos
- Geralmente é imutável

Exemplos de chaves primárias:
- ID de cliente
- Número de CPF
- Combinação de código do produto + lote

```sql
CREATE TABLE clientes (
    id INT PRIMARY KEY,
    nome VARCHAR(100),
    email VARCHAR(100)
);
```

#### Chave Estrangeira (Foreign Key)

Uma **chave estrangeira** é um atributo (ou conjunto de atributos) em uma tabela que se refere à chave primária de outra tabela. Ela estabelece uma relação entre as duas tabelas.

```sql
CREATE TABLE pedidos (
    id INT PRIMARY KEY,
    cliente_id INT,
    data_pedido DATE,
    FOREIGN KEY (cliente_id) REFERENCES clientes(id)
);
```

![Exemplo de Relacionamento](https://upload.wikimedia.org/wikipedia/commons/thumb/d/da/Diagrama_ER.svg/500px-Diagrama_ER.svg.png)

### Índices

**Índices** são estruturas especiais que melhoram a velocidade de recuperação de dados em tabelas. Funcionam como um "índice remissivo" de um livro.

```sql
-- Criar um índice no campo email para buscas mais rápidas
CREATE INDEX idx_email ON clientes(email);
```

Tipos de índices:
- **Índice único**: Garante que não haverá valores duplicados
- **Índice composto**: Baseado em múltiplas colunas
- **Índice de texto completo**: Para busca de texto

### Restrições (Constraints)

**Restrições** são regras aplicadas às tabelas para garantir a integridade dos dados:

- **NOT NULL**: O atributo não pode ser nulo
- **UNIQUE**: O valor deve ser único na tabela
- **CHECK**: O valor deve atender a uma condição específica
- **DEFAULT**: Define um valor padrão

```sql
CREATE TABLE produtos (
    id INT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    preco DECIMAL(10,2) CHECK (preco > 0),
    estoque INT DEFAULT 0
);
```

## Normalização

A **normalização** é o processo de organizar as tabelas e atributos de um banco de dados relacional para minimizar a redundância de dados e melhorar a integridade. As principais formas normais são:

1. **Primeira Forma Normal (1FN)**: Elimina grupos repetitivos e garante que cada célula contenha apenas um valor
2. **Segunda Forma Normal (2FN)**: Elimina dependências parciais de chaves primárias compostas
3. **Terceira Forma Normal (3FN)**: Elimina dependências transitivas

Um banco de dados em 3FN geralmente é considerado bem normalizado para a maioria das aplicações.

## Visões (Views)

Uma **visão** é uma tabela virtual baseada no resultado de uma consulta SQL. As visões:
- Não armazenam dados físicos
- São recalculadas toda vez que são acessadas
- Podem simplificar consultas complexas
- Podem ser usadas para controlar acesso a dados

```sql
CREATE VIEW clientes_ativos AS
SELECT id, nome, email
FROM clientes
WHERE status = 'Ativo';
```

## Integridade Referencial

A **integridade referencial** garante que as relações entre tabelas permaneçam válidas:
- Uma chave estrangeira deve referenciar uma chave primária existente
- Ações como DELETE ou UPDATE em chaves primárias podem ser definidas para:
  - CASCADE: Propaga a alteração para as chaves estrangeiras
  - RESTRICT: Impede a alteração se houver chaves estrangeiras dependentes
  - SET NULL: Define as chaves estrangeiras como nulas

```sql
CREATE TABLE pedidos (
    id INT PRIMARY KEY,
    cliente_id INT,
    FOREIGN KEY (cliente_id) 
    REFERENCES clientes(id)
    ON DELETE RESTRICT
    ON UPDATE CASCADE
);
```

## Transações

Uma **transação** é uma unidade lógica de trabalho que deve ser completamente executada ou completamente desfeita. Transações seguem o princípio ACID:

- **Atomicidade**: Todas as operações são realizadas ou nenhuma é
- **Consistência**: O banco de dados permanece em estado válido
- **Isolamento**: Transações são isoladas umas das outras
- **Durabilidade**: Após confirmada, uma transação persiste mesmo em caso de falha

```sql
-- Início da transação
BEGIN TRANSACTION;

-- Operações
UPDATE contas SET saldo = saldo - 1000 WHERE id = 1;
UPDATE contas SET saldo = saldo + 1000 WHERE id = 2;

-- Se tudo correr bem, confirma as alterações
COMMIT;

-- Se houver problemas, desfaz as alterações
-- ROLLBACK;
```

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/> 