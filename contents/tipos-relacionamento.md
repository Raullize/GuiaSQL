<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# Tipos de Relacionamento

## Relacionamentos em Bancos de Dados Relacionais

Em um banco de dados relacional, as tabelas não existem isoladamente - elas se relacionam entre si para representar conexões entre diferentes entidades. Estes relacionamentos são fundamentais para modelar dados do mundo real, onde objetos estão interligados de várias maneiras.

O relacionamento entre tabelas é estabelecido através de **chaves primárias** e **chaves estrangeiras**, permitindo a criação de diferentes tipos de associações.

## Principais Tipos de Relacionamento

Existem três tipos fundamentais de relacionamentos em bancos de dados relacionais:

### 1. Relacionamento Um para Um (1:1)

No relacionamento **um para um (1:1)**, cada registro em uma tabela está associado a no máximo um registro em outra tabela, e vice-versa.

#### Características:

- É o tipo menos comum de relacionamento
- Geralmente usado para separar informações por motivos de:
  - Organização lógica
  - Performance
  - Segurança (separar dados sensíveis)
  - Lidar com atributos opcionais

#### Exemplo:

Uma tabela **Funcionário** e uma tabela **Dados_Bancários**, onde cada funcionário possui exatamente uma conta bancária e cada conta bancária pertence a exatamente um funcionário.

#### Implementação:

```sql
-- Tabela principal
CREATE TABLE funcionarios (
    id INT PRIMARY KEY,
    nome VARCHAR(100),
    email VARCHAR(100),
    cargo VARCHAR(50)
);

-- Tabela relacionada (1:1)
CREATE TABLE dados_bancarios (
    id INT PRIMARY KEY,
    funcionario_id INT UNIQUE,  -- A restrição UNIQUE garante a relação 1:1
    banco VARCHAR(50),
    agencia VARCHAR(10),
    conta VARCHAR(20),
    FOREIGN KEY (funcionario_id) REFERENCES funcionarios(id)
);
```

#### Diagrama:

```
Funcionário (1) ────────── (1) Dados Bancários
```

### 2. Relacionamento Um para Muitos (1:N)

No relacionamento **um para muitos (1:N)**, cada registro em uma tabela (lado "um") pode estar associado a vários registros em outra tabela (lado "muitos"), mas cada registro na segunda tabela está associado a apenas um registro na primeira tabela.

#### Características:

- É o tipo mais comum de relacionamento
- Representa hierarquias naturais e associações do mundo real
- A chave estrangeira fica na tabela do lado "muitos"

#### Exemplo:

Uma tabela **Departamento** e uma tabela **Funcionário**, onde cada departamento pode ter vários funcionários, mas cada funcionário pertence a apenas um departamento.

#### Implementação:

```sql
-- Tabela do lado "um"
CREATE TABLE departamentos (
    id INT PRIMARY KEY,
    nome VARCHAR(50),
    localizacao VARCHAR(100)
);

-- Tabela do lado "muitos"
CREATE TABLE funcionarios (
    id INT PRIMARY KEY,
    nome VARCHAR(100),
    email VARCHAR(100),
    departamento_id INT,  -- Chave estrangeira para o departamento
    FOREIGN KEY (departamento_id) REFERENCES departamentos(id)
);
```

#### Diagrama:

```
Departamento (1) ────────── (N) Funcionário
```

Outros exemplos comuns de relacionamentos 1:N:
- Cliente (1) → (N) Pedidos
- Autor (1) → (N) Livros
- Categoria (1) → (N) Produtos

### 3. Relacionamento Muitos para Muitos (N:N)

No relacionamento **muitos para muitos (N:N)**, cada registro em ambas as tabelas pode estar associado a vários registros na outra tabela.

#### Características:

- Requer uma tabela intermediária (tabela de junção ou associativa)
- A tabela intermediária geralmente contém as chaves primárias de ambas as tabelas relacionadas
- Pode conter atributos adicionais específicos da relação

#### Exemplo:

Uma tabela **Aluno** e uma tabela **Curso**, onde cada aluno pode estar matriculado em vários cursos e cada curso pode ter vários alunos.

#### Implementação:

```sql
-- Primeira tabela principal
CREATE TABLE alunos (
    id INT PRIMARY KEY,
    nome VARCHAR(100),
    email VARCHAR(100)
);

-- Segunda tabela principal
CREATE TABLE cursos (
    id INT PRIMARY KEY,
    nome VARCHAR(100),
    carga_horaria INT
);

-- Tabela intermediária (junção)
CREATE TABLE matriculas (
    aluno_id INT,
    curso_id INT,
    data_matricula DATE,
    nota DECIMAL(4,2),
    PRIMARY KEY (aluno_id, curso_id),  -- Chave primária composta
    FOREIGN KEY (aluno_id) REFERENCES alunos(id),
    FOREIGN KEY (curso_id) REFERENCES cursos(id)
);
```

#### Diagrama:

```
Aluno (N) ────────── [Matrícula] ────────── (N) Curso
```

Outros exemplos comuns de relacionamentos N:N:
- Produtos (N) ↔ (N) Pedidos
- Atores (N) ↔ (N) Filmes
- Médicos (N) ↔ (N) Pacientes

## Representação de Relacionamentos em Diagramas ER

Os relacionamentos são frequentemente visualizados em **Diagramas Entidade-Relacionamento (ER)**, onde:

- Retângulos representam entidades (tabelas)
- Linhas representam relacionamentos
- A cardinalidade é indicada por símbolos nas extremidades das linhas

### Notações Comuns

#### Notação Pé de Galinha (Crow's Foot)

```
Um para Um (1:1):      ──────┬┬──────
Um para Muitos (1:N):   ──────┬<──────
Muitos para Muitos (N:N): ──────><──────
```

#### Notação UML (Unified Modeling Language)

```
Um para Um (1:1):      ────1─────1────
Um para Muitos (1:N):   ────1─────*────
Muitos para Muitos (N:N): ────*─────*────
```

## Considerações Práticas e Boas Práticas

### Para Relacionamentos 1:1

- Considere se realmente precisa de duas tabelas separadas
- Coloque a chave estrangeira na tabela que tem mais probabilidade de ter valores nulos
- Use restrição UNIQUE na coluna de chave estrangeira

### Para Relacionamentos 1:N

- Sempre coloque a chave estrangeira na tabela do lado "muitos"
- Indexe a coluna de chave estrangeira para melhor desempenho
- Considere ações referenciais (ON DELETE, ON UPDATE) adequadas

```sql
CREATE TABLE pedidos (
    id INT PRIMARY KEY,
    cliente_id INT,
    data_pedido DATE,
    FOREIGN KEY (cliente_id) REFERENCES clientes(id)
    ON DELETE RESTRICT  -- Impede a exclusão de cliente com pedidos
    ON UPDATE CASCADE   -- Atualiza o ID cliente nos pedidos se o ID do cliente mudar
);
```

### Para Relacionamentos N:N

- A tabela de junção geralmente recebe um nome que combina as duas entidades ou descreve a relação
- Considere adicionar campos relevantes à relação na tabela de junção
- A chave primária geralmente é composta pelas duas chaves estrangeiras

## Relacionamentos Auto-Referenciados

Um caso especial é o **relacionamento auto-referenciado**, onde uma tabela se relaciona consigo mesma.

### Exemplo:

Uma tabela **Funcionário** onde cada funcionário pode ter um gerente (que também é um funcionário).

```sql
CREATE TABLE funcionarios (
    id INT PRIMARY KEY,
    nome VARCHAR(100),
    gerente_id INT,
    FOREIGN KEY (gerente_id) REFERENCES funcionarios(id)
);
```

Outros exemplos de relacionamentos auto-referenciados:
- Categorias com subcategorias
- Comentários com respostas
- Estrutura organizacional hierárquica

## Relacionamentos Identificadores vs Não-Identificadores

### Relacionamento Identificador

A chave estrangeira faz parte da chave primária da tabela dependente.

```sql
CREATE TABLE itens_pedido (
    pedido_id INT,
    produto_id INT,
    quantidade INT,
    preco_unitario DECIMAL(10,2),
    PRIMARY KEY (pedido_id, produto_id),
    FOREIGN KEY (pedido_id) REFERENCES pedidos(id),
    FOREIGN KEY (produto_id) REFERENCES produtos(id)
);
```

### Relacionamento Não-Identificador

A chave estrangeira NÃO faz parte da chave primária da tabela dependente.

```sql
CREATE TABLE pedidos (
    id INT PRIMARY KEY,
    cliente_id INT,  -- Relacionamento não-identificador
    data_pedido DATE,
    FOREIGN KEY (cliente_id) REFERENCES clientes(id)
);
```

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/>