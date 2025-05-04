<!-- Cabeçalho -->
<div align="center">
  <img src="https://img.shields.io/badge/SQL-00758F?style=for-the-badge&logo=mysql&logoColor=white" />
  <h1>Modelo Entidade-Relacionamento e Normalização</h1>
</div>

---

# 📊 Modelo Entidade-Relacionamento (MER)

O Modelo Entidade-Relacionamento (MER) é uma técnica de modelagem conceitual utilizada para projetar bancos de dados relacionais. Criado por Peter Chen em 1976, este modelo proporciona uma representação gráfica das estruturas de dados e suas relações.

## Principais componentes do MER:

### 1. Entidades
- **Definição**: Representam objetos do mundo real (físicos ou conceituais)
- **Representação**: Retângulos no diagrama
- **Exemplos**: Cliente, Produto, Pedido, Funcionário

### 2. Atributos
- **Definição**: Características que descrevem as entidades
- **Representação**: Elipses ligadas às entidades
- **Tipos de atributos**:
  - **Simples**: Não podem ser divididos (Ex: CPF)
  - **Compostos**: Podem ser divididos (Ex: Endereço)
  - **Multivalorados**: Podem ter múltiplos valores (Ex: Telefones)
  - **Derivados**: Calculados a partir de outros atributos (Ex: Idade calculada da data de nascimento)
  - **Identificadores (Chave primária)**: Atributos que identificam unicamente uma entidade

### 3. Relacionamentos
- **Definição**: Associações entre entidades
- **Representação**: Losangos conectando entidades
- **Exemplo**: Um Cliente *realiza* um Pedido

### 4. Cardinalidade
- **Definição**: Indica quantas instâncias de uma entidade podem estar associadas a quantas instâncias de outra entidade
- **Tipos**:
  - **1:1 (um para um)**: Um cliente tem um único endereço de cobrança
  - **1:N (um para muitos)**: Um departamento tem vários funcionários
  - **N:N (muitos para muitos)**: Vários alunos cursam várias disciplinas

## Exemplo de Diagrama MER

```
+-------------+       +--------------+       +-------------+
|   CLIENTE   |       |    PEDIDO    |       |   PRODUTO   |
+-------------+       +--------------+       +-------------+
| *ID_Cliente |<----->| *ID_Pedido   |       | *ID_Produto |
| Nome        |  1:N  | Data         |  N:M  | Nome        |
| Email       |       | Valor_Total  |<----->| Preço       |
| Telefone    |       | ID_Cliente   |       | Estoque     |
+-------------+       +--------------+       +-------------+
                           |
                           | 1:N
                           v
                     +-------------+
                     | ITEM_PEDIDO |
                     +-------------+
                     | *ID_Item    |
                     | ID_Pedido   |
                     | ID_Produto  |
                     | Quantidade  |
                     | Preço_Unit  |
                     +-------------+
```

# 🧩 Normalização de Bancos de Dados

A normalização é um processo de organização de dados em um banco de dados que visa eliminar redundâncias e dependências problemáticas, reduzindo a possibilidade de anomalias de dados.

## Formas Normais

### 1. Primeira Forma Normal (1FN)
- **Regra**: Cada coluna deve conter apenas valores atômicos (indivisíveis)
- **Proibição**: Não pode haver múltiplos valores em uma única célula
- **Exemplo**: Separar múltiplos telefones em tabelas distintas ao invés de armazená-los em uma única coluna

**Antes da 1FN**:
```sql
CREATE TABLE Clientes (
    ID INT PRIMARY KEY,
    Nome VARCHAR(100),
    Telefones VARCHAR(100) -- "123456789, 987654321" (problema)
);
```

**Depois da 1FN**:
```sql
CREATE TABLE Clientes (
    ID INT PRIMARY KEY,
    Nome VARCHAR(100)
);

CREATE TABLE Telefones (
    ID INT AUTO_INCREMENT PRIMARY KEY,
    Cliente_ID INT,
    Telefone VARCHAR(20),
    FOREIGN KEY (Cliente_ID) REFERENCES Clientes(ID)
);
```

### 2. Segunda Forma Normal (2FN)
- **Regra**: Estar em 1FN e todos os atributos não-chave devem depender totalmente da chave primária
- **Aplicação**: Relevante apenas para tabelas com chaves compostas
- **Exemplo**: Remover atributos que dependem apenas de parte da chave primária

**Antes da 2FN**:
```sql
CREATE TABLE Vendas (
    Produto_ID INT,
    Pedido_ID INT,
    Produto_Nome VARCHAR(100), -- Depende apenas de Produto_ID, não da chave completa
    Quantidade INT,
    PRIMARY KEY (Produto_ID, Pedido_ID)
);
```

**Depois da 2FN**:
```sql
CREATE TABLE Produtos (
    ID INT PRIMARY KEY,
    Nome VARCHAR(100)
);

CREATE TABLE Vendas (
    Produto_ID INT,
    Pedido_ID INT,
    Quantidade INT,
    PRIMARY KEY (Produto_ID, Pedido_ID),
    FOREIGN KEY (Produto_ID) REFERENCES Produtos(ID)
);
```

### 3. Terceira Forma Normal (3FN)
- **Regra**: Estar em 2FN e não ter dependências transitivas (atributos não-chave dependendo de outros atributos não-chave)
- **Exemplo**: Separar atributos que dependem de outros atributos não-chave

**Antes da 3FN**:
```sql
CREATE TABLE Funcionarios (
    ID INT PRIMARY KEY,
    Nome VARCHAR(100),
    Departamento_ID INT,
    Departamento_Nome VARCHAR(100) -- Depende de Departamento_ID, não da chave primária
);
```

**Depois da 3FN**:
```sql
CREATE TABLE Departamentos (
    ID INT PRIMARY KEY,
    Nome VARCHAR(100)
);

CREATE TABLE Funcionarios (
    ID INT PRIMARY KEY,
    Nome VARCHAR(100),
    Departamento_ID INT,
    FOREIGN KEY (Departamento_ID) REFERENCES Departamentos(ID)
);
```

### 4. Forma Normal de Boyce-Codd (BCNF)
- **Regra**: Extensão da 3FN que trata casos especiais de dependências funcionais
- **Condição**: Para cada dependência funcional X → Y, X deve ser uma superchave

### 5. Quarta Forma Normal (4FN)
- **Regra**: Estar em BCNF e não ter dependências multivaloradas
- **Aplicação**: Remover dependências entre conjunto de valores

### 6. Quinta Forma Normal (5FN)
- **Regra**: Estar em 4FN e não ter dependências de junção
- **Aplicação**: Muito teórica e raramente aplicada

## Vantagens da Normalização

1. **Redução de redundância**: Menos duplicação de dados
2. **Melhoria na integridade dos dados**: Menos inconsistências
3. **Menor espaço de armazenamento**: Economia de recursos
4. **Modelo mais flexível**: Facilita alterações futuras
5. **Consultas mais simples**: Estrutura de dados mais clara

## Desvantagens da Normalização

1. **Desempenho**: Pode exigir mais JOINs para consultas complexas
2. **Complexidade**: Banco mais fragmentado
3. **Desenvolvimento**: Pode tornar o desenvolvimento inicial mais lento

## Equilíbrio: Desnormalização Estratégica

Em alguns casos, especialmente em sistemas com alto volume de leituras e consultas complexas, a desnormalização estratégica (violação intencional das formas normais) pode ser aplicada para melhorar o desempenho:

```sql
-- Tabela desnormalizada para relatórios de vendas
CREATE TABLE Relatorio_Vendas (
    Pedido_ID INT,
    Cliente_Nome VARCHAR(100), -- Duplicação intencional
    Produto_Nome VARCHAR(100), -- Duplicação intencional
    Quantidade INT,
    Valor_Total DECIMAL(10,2)
);
```

# 💡 Boas Práticas

1. **Normalizar durante o projeto**: Comece com um banco normalizado até a 3FN
2. **Avaliar requisitos de performance**: Considere desnormalização apenas quando necessário
3. **Criar índices adequados**: Otimize consultas sem sacrificar a estrutura
4. **Documentar decisões**: Registre onde e por que você decidiu desnormalizar
5. **Revisar periodicamente**: O modelo de dados evolui com a aplicação

---

<!-- Rodapé -->
<div align="center">
  <p>✨ Desenvolvido com foco na clareza e aplicação prática ✨</p>
  <img src="https://img.shields.io/badge/SQL-00758F?style=for-the-badge&logo=mysql&logoColor=white" />
</div> 