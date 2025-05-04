<!-- Cabeçalho -->
<div align="center">
  <img src="https://img.shields.io/badge/SQL-00758F?style=for-the-badge&logo=mysql&logoColor=white" />
  <h1>Transações e Controle de Concorrência</h1>
</div>

---

# ⚡ Transações em SQL

Uma **transação** é uma sequência de operações SQL que é executada como uma única unidade de trabalho. Ela segue o princípio "tudo ou nada" - ou todas as operações são bem-sucedidas e persistidas no banco de dados, ou nenhuma alteração é aplicada.

## 🎯 Princípios ACID

As transações seguem quatro princípios fundamentais conhecidos como **ACID**:

### Atomicidade (Atomicity)
- A transação é tratada como uma única unidade indivisível
- Ou todas as operações são executadas, ou nenhuma é
- Se qualquer parte falhar, toda a transação é revertida (rollback)

### Consistência (Consistency)
- Uma transação leva o banco de dados de um estado válido para outro estado válido
- Todas as regras de integridade são respeitadas (restrições, triggers, etc.)
- O banco não fica em um estado intermediário inconsistente

### Isolamento (Isolation)
- Transações concorrentes são isoladas umas das outras
- Alterações feitas por uma transação não são visíveis para outras até o commit
- Diferentes níveis de isolamento determinam o grau de visibilidade das operações

### Durabilidade (Durability)
- Uma vez que uma transação foi confirmada (commit), as alterações são permanentes
- As mudanças persistem mesmo em caso de falha do sistema ou queda de energia
- Implementado através de logs de transação e outros mecanismos de persistência

## 📝 Comandos de Controle de Transação

### BEGIN TRANSACTION (Iniciar Transação)

Inicia uma nova transação:

```sql
-- Sintaxe padrão SQL
BEGIN TRANSACTION;

-- MySQL
START TRANSACTION;

-- PostgreSQL e SQL Server também aceitam
BEGIN;
```

### COMMIT (Confirmar Transação)

Confirma todas as alterações feitas durante a transação atual:

```sql
COMMIT;
```

### ROLLBACK (Reverter Transação)

Desfaz todas as alterações feitas durante a transação atual:

```sql
ROLLBACK;
```

### SAVEPOINT (Ponto de Restauração)

Cria um ponto de restauração dentro de uma transação:

```sql
-- Criar um savepoint
SAVEPOINT nome_do_savepoint;

-- Reverter até um savepoint específico
ROLLBACK TO SAVEPOINT nome_do_savepoint;
```

## 🧩 Exemplo Prático de Transação

Considere um cenário de transferência bancária:

```sql
-- Iniciar transação
BEGIN TRANSACTION;

-- Verificar saldo da conta origem
DECLARE @saldo_origem DECIMAL(10,2);
SELECT @saldo_origem = saldo FROM contas WHERE id = 101;

-- Verificar se há saldo suficiente
IF @saldo_origem >= 1000.00 THEN
    -- Debitar da conta origem
    UPDATE contas 
    SET saldo = saldo - 1000.00 
    WHERE id = 101;
    
    -- Creditar na conta destino
    UPDATE contas 
    SET saldo = saldo + 1000.00 
    WHERE id = 202;
    
    -- Registrar a transação no histórico
    INSERT INTO historico_transacoes (conta_origem, conta_destino, valor, data)
    VALUES (101, 202, 1000.00, CURRENT_TIMESTAMP);
    
    -- Confirmar transação
    COMMIT;
ELSE
    -- Reverter transação se não houver saldo
    ROLLBACK;
END IF;
```

# 🔄 Controle de Concorrência

O controle de concorrência gerencia o acesso simultâneo aos dados por múltiplas transações, evitando problemas de integridade e consistência.

## 🚨 Problemas de Concorrência

### Leitura Suja (Dirty Read)
Ocorre quando uma transação lê dados que foram modificados por outra transação ainda não confirmada (não commitada):

```
Transação A: UPDATE contas SET saldo = 5000 WHERE id = 1;
Transação B: SELECT saldo FROM contas WHERE id = 1; -- Lê 5000
Transação A: ROLLBACK; -- Volta ao valor original
```

### Leitura Não-Repetível (Non-Repeatable Read)
Ocorre quando uma transação relê os mesmos dados e encontra valores diferentes:

```
Transação A: SELECT saldo FROM contas WHERE id = 1; -- Lê 1000
Transação B: UPDATE contas SET saldo = 2000 WHERE id = 1; COMMIT;
Transação A: SELECT saldo FROM contas WHERE id = 1; -- Agora lê 2000
```

### Leitura Fantasma (Phantom Read)
Ocorre quando uma transação executa a mesma consulta mais de uma vez e obtém um conjunto diferente de linhas:

```
Transação A: SELECT * FROM produtos WHERE preco > 100; -- Retorna 10 produtos
Transação B: INSERT INTO produtos VALUES (...); -- Adiciona produto com preço > 100
Transação A: SELECT * FROM produtos WHERE preco > 100; -- Retorna 11 produtos
```

### Atualização Perdida (Lost Update)
Ocorre quando duas transações modificam o mesmo dado, e a última sobrescreve as alterações da primeira:

```
Transação A: SELECT estoque FROM produtos WHERE id = 1; -- Lê 100
Transação B: SELECT estoque FROM produtos WHERE id = 1; -- Lê 100
Transação A: UPDATE produtos SET estoque = 90 WHERE id = 1;
Transação B: UPDATE produtos SET estoque = 95 WHERE id = 1; -- Sobrescreve a mudança de A
```

## 🔒 Níveis de Isolamento

Os níveis de isolamento definem o grau de visibilidade das alterações entre transações concorrentes:

### READ UNCOMMITTED (Menos Restritivo)
- Permite leituras sujas, não repetíveis e fantasmas
- Oferece o maior desempenho, mas o menor nível de isolamento
- Raramente usado em ambientes de produção

```sql
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
```

### READ COMMITTED
- Evita leituras sujas
- Permite leituras não repetíveis e fantasmas
- Nível padrão em muitos SGBDs (PostgreSQL, SQL Server)

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

### REPEATABLE READ
- Evita leituras sujas e não repetíveis
- Ainda permite leituras fantasmas
- Nível padrão no MySQL/MariaDB

```sql
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
```

### SERIALIZABLE (Mais Restritivo)
- Evita todos os problemas de concorrência
- Emula execução em série das transações
- Menor desempenho, máxima integridade

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

## 📊 Comparação dos Níveis de Isolamento

| Nível de Isolamento | Leitura Suja | Leitura Não-Repetível | Leitura Fantasma | Desempenho |
|---------------------|--------------|------------------------|------------------|------------|
| READ UNCOMMITTED    | Sim          | Sim                    | Sim              | Melhor     |
| READ COMMITTED      | Não          | Sim                    | Sim              | Bom        |
| REPEATABLE READ     | Não          | Não                    | Sim              | Médio      |
| SERIALIZABLE        | Não          | Não                    | Não              | Pior       |

## 🔐 Bloqueios (Locks)

Os SGBDs implementam diversos tipos de bloqueios para controlar o acesso concorrente:

### Bloqueio Compartilhado (Shared Lock)
- Usado para operações de leitura
- Várias transações podem ter bloqueios compartilhados simultaneamente
- Impede que outras transações obtenham bloqueios exclusivos

### Bloqueio Exclusivo (Exclusive Lock)
- Usado para operações de escrita
- Apenas uma transação pode ter um bloqueio exclusivo
- Impede que outras transações obtenham qualquer tipo de bloqueio

### Bloqueio de Linha (Row Lock)
- Bloqueia apenas linhas específicas
- Permite maior concorrência
- Usado em operações que afetam poucas linhas

### Bloqueio de Tabela (Table Lock)
- Bloqueia uma tabela inteira
- Reduz a sobrecarga de gerenciamento de bloqueios
- Usado em operações que afetam muitas linhas

## 🚀 Otimização de Transações

Para melhorar o desempenho e reduzir problemas de concorrência:

### 1. Mantenha transações curtas
- Reduza a duração das transações
- Diminui a probabilidade de conflitos
- Melhora o throughput geral

### 2. Use o nível de isolamento adequado
- Escolha o mais baixo que atenda aos requisitos de consistência
- Considere diferentes níveis para diferentes partes da aplicação

### 3. Ordene operações de forma consistente
- Acesse tabelas na mesma ordem em todas as transações
- Ajuda a evitar deadlocks

### 4. Evite interações com o usuário durante transações
- Não inicie transações até ter todos os dados necessários
- Não aguarde input do usuário dentro de uma transação

### 5. Use transações apenas quando necessário
- Nem todas as operações precisam de transações
- Consultas de leitura simples geralmente não precisam

# 🧠 Padrões de Controle de Concorrência

## Controle de Concorrência Otimista
- Assume que conflitos são raros
- Não bloqueia recursos durante a transação
- Verifica conflitos apenas no momento do commit
- Geralmente usa timestamps ou números de versão

```sql
-- Exemplo usando número de versão
UPDATE produtos
SET estoque = estoque - 1, versao = versao + 1
WHERE id = 101 AND versao = 5;

-- Se nenhuma linha for afetada, houve conflito
```

## Controle de Concorrência Pessimista
- Assume que conflitos são frequentes
- Bloqueia recursos durante toda a transação
- Previne conflitos antes que aconteçam
- Implementado através de locks no banco de dados

```sql
-- Bloqueio explícito (PostgreSQL)
SELECT * FROM produtos WHERE id = 101 FOR UPDATE;

-- Isso impede que outras transações modifiquem este registro
```

# 📚 Recursos Adicionais

- [Documentação oficial do MySQL sobre transações](https://dev.mysql.com/doc/refman/8.0/en/sql-transactional-statements.html)
- [Documentação do PostgreSQL sobre controle de concorrência](https://www.postgresql.org/docs/current/mvcc.html)
- [Transaction Processing: Concepts and Techniques](https://www.amazon.com/Transaction-Processing-Concepts-Techniques-Management/dp/1558601902) - Livro clássico sobre o assunto
- [Princípios ACID explicados - IBM Developer](https://developer.ibm.com/articles/acid-properties-of-transactions/)

---

<!-- Rodapé -->
<div align="center">
  <p>
    <a href="../README.md">🏠 Voltar para o Início</a>
  </p>
  <img src="https://img.shields.io/badge/SQL-00758F?style=for-the-badge&logo=mysql&logoColor=white" />
</div> 