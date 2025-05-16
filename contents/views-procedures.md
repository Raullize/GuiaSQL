<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# 🔍 Views em SQL

Uma View (ou visão) no SQL é uma tabela virtual baseada no resultado de uma consulta SQL. As views contêm linhas e colunas, assim como tabelas reais, mas não armazenam dados fisicamente - elas são criadas dinamicamente quando consultadas.

## Vantagens das Views

- **Simplificação de consultas complexas**: Encapsula lógica complicada
- **Segurança de dados**: Restringe acesso a colunas/linhas específicas
- **Independência de dados**: Proteção contra mudanças no esquema subjacente
- **Consistência de dados**: Garante que todos usem a mesma definição
- **Personalização de dados**: Formata dados de acordo com necessidades específicas

## Criação de Views

### Sintaxe básica

```sql
CREATE [OR REPLACE] VIEW nome_da_view AS
SELECT colunas
FROM tabelas
WHERE condições;
```

### Exemplos Práticos

#### 1. View Simples

```sql
-- View para relatório de produtos ativos
CREATE VIEW produtos_ativos AS
SELECT id, nome, preco, estoque
FROM produtos
WHERE status = 'ativo';

-- Consultando a view
SELECT * FROM produtos_ativos;
```

#### 2. View com JOIN de Múltiplas Tabelas

```sql
-- View para detalhes de pedidos
CREATE VIEW detalhes_pedidos AS
SELECT 
    p.id AS pedido_id, 
    p.data_pedido, 
    c.nome AS cliente,
    c.email,
    SUM(ip.quantidade * ip.preco_unitario) AS valor_total,
    COUNT(ip.produto_id) AS quantidade_itens
FROM pedidos p
JOIN clientes c ON p.cliente_id = c.id
JOIN itens_pedido ip ON p.id = ip.pedido_id
GROUP BY p.id, p.data_pedido, c.nome, c.email;

-- Consultando a view
SELECT * FROM detalhes_pedidos
WHERE valor_total > 1000
ORDER BY data_pedido DESC;
```

#### 3. View com Subconsultas

```sql
-- View de clientes VIP (com gasto acima da média)
CREATE VIEW clientes_vip AS
SELECT 
    c.id, 
    c.nome, 
    c.email,
    SUM(p.valor_total) AS total_gasto
FROM clientes c
JOIN pedidos p ON c.id = p.cliente_id
GROUP BY c.id, c.nome, c.email
HAVING SUM(p.valor_total) > (
    SELECT AVG(valor_total) FROM (
        SELECT cliente_id, SUM(valor_total) AS valor_total
        FROM pedidos
        GROUP BY cliente_id
    ) AS gastos_clientes
);
```

## Tipos Especiais de Views

### 1. View Materializadas (Disponíveis em PostgreSQL, Oracle)

Uma view materializada armazena fisicamente os resultados da consulta, atualizando-os periodicamente.

```sql
-- PostgreSQL
CREATE MATERIALIZED VIEW relatorio_vendas_mensal AS
SELECT 
    DATE_TRUNC('month', data_venda) AS mes,
    categoria_id,
    SUM(valor) AS total_vendas,
    COUNT(*) AS num_vendas
FROM vendas
JOIN produtos ON vendas.produto_id = produtos.id
GROUP BY mes, categoria_id;

-- Atualizar a view materializada
REFRESH MATERIALIZED VIEW relatorio_vendas_mensal;
```

### 2. View Atualizáveis (Updatable Views)

São views que permitem operações de INSERT, UPDATE e DELETE sob certas condições.

```sql
-- View atualizável
CREATE VIEW funcionarios_ti AS
SELECT id, nome, cargo, salario, departamento_id
FROM funcionarios
WHERE departamento_id = 5;

-- Possível fazer UPDATE nesta view
UPDATE funcionarios_ti
SET salario = salario * 1.1
WHERE cargo = 'Desenvolvedor Sênior';
```

## Alteração e Remoção de Views

```sql
-- Alterando uma view
ALTER VIEW produtos_ativos AS
SELECT id, nome, preco, estoque, categoria_id
FROM produtos
WHERE status = 'ativo' AND estoque > 0;

-- Removendo uma view
DROP VIEW IF EXISTS produtos_ativos;
```

## Limitações das Views

1. **Performance**: Views complexas podem ser mais lentas que consultas diretas
2. **Restrições em views atualizáveis**: Nem todas as views permitem modificação de dados
3. **Limitações de índices**: Em geral, views não têm índices próprios (exceto materializadas)

# 📋 Procedimentos Armazenados

Procedimentos armazenados (Stored Procedures) são conjuntos de instruções SQL compiladas e armazenadas no banco de dados que podem ser executadas posteriormente.

## Vantagens dos Procedimentos Armazenados

- **Desempenho**: São pré-compilados e otimizados pelo SGBD
- **Segurança**: Permitem controle de acesso granular
- **Redução de tráfego de rede**: Apenas a chamada é transmitida, não toda a consulta
- **Reutilização**: O mesmo código pode ser usado por múltiplas aplicações
- **Manutenção**: Alterar a lógica em um único lugar afeta todas as aplicações
- **Transações**: Podem agrupar múltiplas operações em uma transação atômica

## Criação de Procedimentos Armazenados

A sintaxe varia um pouco entre diferentes SGBDs:

### MySQL

```sql
DELIMITER //
CREATE PROCEDURE criar_pedido(
    IN cliente_id INT,
    IN forma_pagamento VARCHAR(50),
    OUT pedido_id INT
)
BEGIN
    DECLARE data_atual DATETIME;
    SET data_atual = NOW();
    
    -- Inserir o pedido
    INSERT INTO pedidos (cliente_id, data_pedido, forma_pagamento, status)
    VALUES (cliente_id, data_atual, forma_pagamento, 'pendente');
    
    -- Obter o ID do pedido inserido
    SET pedido_id = LAST_INSERT_ID();
    
    -- Registrar log
    INSERT INTO logs_sistema (acao, tabela, registro_id, data_hora)
    VALUES ('criar_pedido', 'pedidos', pedido_id, data_atual);
END //
DELIMITER ;
```

### SQL Server

```sql
CREATE PROCEDURE criar_pedido
    @cliente_id INT,
    @forma_pagamento VARCHAR(50),
    @pedido_id INT OUTPUT
AS
BEGIN
    SET NOCOUNT ON;
    
    DECLARE @data_atual DATETIME = GETDATE();
    
    -- Inserir o pedido
    INSERT INTO pedidos (cliente_id, data_pedido, forma_pagamento, status)
    VALUES (@cliente_id, @data_atual, @forma_pagamento, 'pendente');
    
    -- Obter o ID do pedido inserido
    SET @pedido_id = SCOPE_IDENTITY();
    
    -- Registrar log
    INSERT INTO logs_sistema (acao, tabela, registro_id, data_hora)
    VALUES ('criar_pedido', 'pedidos', @pedido_id, @data_atual);
END;
```

### PostgreSQL

```sql
CREATE OR REPLACE PROCEDURE criar_pedido(
    cliente_id INT,
    forma_pagamento VARCHAR,
    INOUT pedido_id INT
)
LANGUAGE plpgsql
AS $$
DECLARE
    data_atual TIMESTAMP;
BEGIN
    data_atual := NOW();
    
    -- Inserir o pedido
    INSERT INTO pedidos (cliente_id, data_pedido, forma_pagamento, status)
    VALUES (cliente_id, data_atual, forma_pagamento, 'pendente')
    RETURNING id INTO pedido_id;
    
    -- Registrar log
    INSERT INTO logs_sistema (acao, tabela, registro_id, data_hora)
    VALUES ('criar_pedido', 'pedidos', pedido_id, data_atual);
END;
$$;
```

## Executando Procedimentos Armazenados

```sql
-- MySQL
CALL criar_pedido(101, 'cartão de crédito', @novo_pedido_id);
SELECT @novo_pedido_id;

-- SQL Server
DECLARE @novo_pedido_id INT;
EXEC criar_pedido 101, 'cartão de crédito', @novo_pedido_id OUTPUT;
SELECT @novo_pedido_id;

-- PostgreSQL
CALL criar_pedido(101, 'cartão de crédito', NULL);
```

## Exemplos Práticos de Procedimentos Armazenados

### 1. Procedimento para Transferência Bancária

```sql
-- MySQL
DELIMITER //
CREATE PROCEDURE transferir_fundos(
    IN conta_origem INT,
    IN conta_destino INT,
    IN valor DECIMAL(10,2),
    OUT mensagem VARCHAR(100)
)
BEGIN
    DECLARE saldo_origem DECIMAL(10,2);
    
    -- Iniciar transação
    START TRANSACTION;
    
    -- Verificar saldo da conta de origem
    SELECT saldo INTO saldo_origem FROM contas WHERE id = conta_origem FOR UPDATE;
    
    IF saldo_origem < valor THEN
        SET mensagem = 'Saldo insuficiente';
        ROLLBACK;
    ELSE
        -- Retirar da conta de origem
        UPDATE contas SET saldo = saldo - valor WHERE id = conta_origem;
        
        -- Adicionar à conta de destino
        UPDATE contas SET saldo = saldo + valor WHERE id = conta_destino;
        
        -- Registrar a transação
        INSERT INTO transacoes (conta_origem, conta_destino, valor, data_hora)
        VALUES (conta_origem, conta_destino, valor, NOW());
        
        COMMIT;
        SET mensagem = 'Transferência realizada com sucesso';
    END IF;
END //
DELIMITER ;
```

### 2. Procedimento para Processamento em Lote

```sql
-- SQL Server
CREATE PROCEDURE processar_descontos_inativos
AS
BEGIN
    DECLARE @data_limite DATE = DATEADD(month, -3, GETDATE());
    
    -- Atualizar preços com desconto para produtos sem vendas recentes
    UPDATE produtos
    SET preco_promocional = preco * 0.8,
        status_promocao = 'ativo'
    WHERE id IN (
        SELECT p.id
        FROM produtos p
        LEFT JOIN itens_pedido ip ON p.id = ip.produto_id
        LEFT JOIN pedidos pe ON ip.pedido_id = pe.id
        GROUP BY p.id
        HAVING MAX(pe.data_pedido) < @data_limite OR MAX(pe.data_pedido) IS NULL
    )
    AND status = 'ativo'
    AND preco_promocional IS NULL;
    
    -- Retornar o número de produtos atualizados
    SELECT @@ROWCOUNT AS produtos_atualizados;
END;
```

### 3. Procedimento com Cursor (Iteração)

```sql
-- MySQL
DELIMITER //
CREATE PROCEDURE calcular_frete_pedidos()
BEGIN
    DECLARE pedido_id INT;
    DECLARE cliente_id INT;
    DECLARE peso_total DECIMAL(10,2);
    DECLARE distancia INT;
    DECLARE valor_frete DECIMAL(10,2);
    DECLARE fim INT DEFAULT 0;
    
    -- Declarar cursor para pedidos sem frete calculado
    DECLARE cur_pedidos CURSOR FOR
        SELECT p.id, p.cliente_id
        FROM pedidos p
        WHERE p.valor_frete IS NULL AND p.status = 'aguardando_envio';
    
    -- Handler para fim do cursor
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET fim = 1;
    
    OPEN cur_pedidos;
    
    loop_pedidos: LOOP
        FETCH cur_pedidos INTO pedido_id, cliente_id;
        
        IF fim = 1 THEN
            LEAVE loop_pedidos;
        END IF;
        
        -- Calcular peso total do pedido
        SELECT SUM(ip.quantidade * pr.peso)
        INTO peso_total
        FROM itens_pedido ip
        JOIN produtos pr ON ip.produto_id = pr.id
        WHERE ip.pedido_id = pedido_id;
        
        -- Obter distância de entrega
        SELECT distancia_km INTO distancia
        FROM clientes c
        JOIN enderecos e ON c.id = e.cliente_id AND e.principal = 1
        JOIN regioes_entrega r ON e.cep BETWEEN r.cep_inicial AND r.cep_final
        WHERE c.id = cliente_id;
        
        -- Calcular valor do frete
        SET valor_frete = peso_total * 0.1 + distancia * 0.05;
        
        -- Atualizar pedido
        UPDATE pedidos
        SET valor_frete = valor_frete
        WHERE id = pedido_id;
    END LOOP;
    
    CLOSE cur_pedidos;
    
    SELECT 'Cálculo de frete finalizado' AS mensagem;
END //
DELIMITER ;
```

## Funções Armazenadas

Semelhantes aos procedimentos, mas retornam um único valor e podem ser usadas em consultas SQL.

```sql
-- MySQL
DELIMITER //
CREATE FUNCTION calcular_idade(data_nascimento DATE) 
RETURNS INT DETERMINISTIC
BEGIN
    DECLARE idade INT;
    SET idade = YEAR(CURDATE()) - YEAR(data_nascimento) - 
               (DATE_FORMAT(CURDATE(), '%m%d') < DATE_FORMAT(data_nascimento, '%m%d'));
    RETURN idade;
END //
DELIMITER ;

-- Usando a função
SELECT nome, calcular_idade(data_nascimento) AS idade FROM clientes;
```

## Triggers (Gatilhos)

Triggers são procedimentos armazenados especiais que são executados automaticamente em resposta a eventos no banco de dados.

```sql
-- MySQL
DELIMITER //
CREATE TRIGGER atualizar_estoque_apos_venda
AFTER INSERT ON itens_pedido
FOR EACH ROW
BEGIN
    -- Diminuir o estoque do produto
    UPDATE produtos
    SET estoque = estoque - NEW.quantidade
    WHERE id = NEW.produto_id;
    
    -- Verificar se o estoque ficou abaixo do mínimo
    INSERT INTO alertas_estoque (produto_id, estoque_atual, data_alerta)
    SELECT id, estoque, NOW()
    FROM produtos
    WHERE id = NEW.produto_id AND estoque < estoque_minimo;
END //
DELIMITER ;
```

# 🔄 Combinando Views e Procedimentos Armazenados

Views e procedimentos armazenados podem ser combinados para criar soluções poderosas.

## Exemplo: Sistema de Relatórios

```sql
-- 1. View para dados base
CREATE VIEW vendas_produto_detalhe AS
SELECT 
    p.id AS produto_id,
    p.nome AS produto,
    c.nome AS categoria,
    ip.quantidade,
    ip.preco_unitario,
    pe.data_pedido,
    EXTRACT(YEAR FROM pe.data_pedido) AS ano,
    EXTRACT(MONTH FROM pe.data_pedido) AS mes
FROM itens_pedido ip
JOIN produtos p ON ip.produto_id = p.id
JOIN categorias c ON p.categoria_id = c.id
JOIN pedidos pe ON ip.pedido_id = pe.id
WHERE pe.status = 'entregue';

-- 2. Procedimento para gerar relatório
DELIMITER //
CREATE PROCEDURE gerar_relatorio_vendas(
    IN ano_filtro INT,
    IN mes_filtro INT
)
BEGIN
    -- Relatório por categoria
    SELECT 
        categoria,
        SUM(quantidade) AS unidades_vendidas,
        SUM(quantidade * preco_unitario) AS valor_total,
        COUNT(DISTINCT produto_id) AS quantidade_produtos
    FROM vendas_produto_detalhe
    WHERE ano = ano_filtro AND (mes_filtro IS NULL OR mes = mes_filtro)
    GROUP BY categoria
    ORDER BY valor_total DESC;
    
    -- Relatório dos top 10 produtos
    SELECT 
        produto_id,
        produto,
        SUM(quantidade) AS unidades_vendidas,
        SUM(quantidade * preco_unitario) AS valor_total
    FROM vendas_produto_detalhe
    WHERE ano = ano_filtro AND (mes_filtro IS NULL OR mes = mes_filtro)
    GROUP BY produto_id, produto
    ORDER BY unidades_vendidas DESC
    LIMIT 10;
    
    -- Evolução mensal (se filtro de mês não informado)
    IF mes_filtro IS NULL THEN
        SELECT 
            mes,
            SUM(quantidade * preco_unitario) AS valor_total,
            COUNT(DISTINCT produto_id) AS produtos_vendidos
        FROM vendas_produto_detalhe
        WHERE ano = ano_filtro
        GROUP BY mes
        ORDER BY mes;
    END IF;
END //
DELIMITER ;
```

# 💡 Boas Práticas

## Para Views

1. **Nomes Descritivos**: Use nomes que indiquem claramente o propósito da view
2. **Comentários**: Documente o objetivo e as restrições da view
3. **Evite Cascata**: Evite criar views baseadas em outras views (limita-se a 1-2 níveis)
4. **Considere Performance**: Use views materializadas para queries complexas frequentes
5. **Evite SELECT ***: Especifique apenas as colunas necessárias

## Para Procedimentos Armazenados

1. **Tratamento de Erros**: Sempre inclua lógica para lidar com exceções
2. **Transações**: Use transações para garantir a integridade dos dados
3. **Validação de Entrada**: Verifique parâmetros de entrada para evitar erros
4. **Evite Lógica Complexa**: Mantenha procedimentos focados e específicos
5. **Segurança**: Evite injeção SQL usando parâmetros adequadamente
6. **Tamanho**: Divida procedimentos muito grandes em unidades menores e reutilizáveis

## Exemplo de Procedimento com Boas Práticas

```sql
-- MySQL
DELIMITER //
CREATE PROCEDURE atualizar_preco_produto(
    IN produto_id INT,
    IN novo_preco DECIMAL(10,2),
    OUT mensagem VARCHAR(100)
)
COMMENT 'Atualiza o preço de um produto com validações e registro de histórico'
BEGIN
    DECLARE produto_existe INT;
    DECLARE preco_atual DECIMAL(10,2);
    
    -- Tratamento de erros
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    BEGIN
        ROLLBACK;
        SET mensagem = 'Erro ao atualizar preço. Operação cancelada.';
    END;
    
    -- Validação de entrada
    IF produto_id IS NULL OR novo_preco IS NULL THEN
        SET mensagem = 'ID do produto e novo preço são obrigatórios';
        RETURN;
    END IF;
    
    IF novo_preco <= 0 THEN
        SET mensagem = 'Preço deve ser maior que zero';
        RETURN;
    END IF;
    
    -- Verificar se produto existe
    SELECT COUNT(*), preco INTO produto_existe, preco_atual
    FROM produtos WHERE id = produto_id;
    
    IF produto_existe = 0 THEN
        SET mensagem = CONCAT('Produto com ID ', produto_id, ' não encontrado');
        RETURN;
    END IF;
    
    -- Iniciar transação
    START TRANSACTION;
    
    -- Registrar histórico
    INSERT INTO historico_precos (produto_id, preco_antigo, preco_novo, data_alteracao, usuario)
    VALUES (produto_id, preco_atual, novo_preco, NOW(), CURRENT_USER());
    
    -- Atualizar preço
    UPDATE produtos
    SET preco = novo_preco,
        data_atualizacao = NOW()
    WHERE id = produto_id;
    
    -- Confirmar transação
    COMMIT;
    
    SET mensagem = CONCAT('Preço atualizado com sucesso. Anterior: ', preco_atual, ' / Novo: ', novo_preco);
END //
DELIMITER ;
```

# 🌐 Diferenças entre SGBDs

## MySQL/MariaDB

- Usa `DELIMITER` para alterar o delimitador durante criação de procedures
- Sintaxe `BEGIN...END` para blocos
- Suporta procedimentos armazenados e funções, mas não views materializadas nativamente

## PostgreSQL

- Usa `CREATE OR REPLACE PROCEDURE/FUNCTION`
- Tem linguagem própria (PL/pgSQL) para procedimentos
- Suporta views materializadas nativamente
- Permite herança de tabelas

## SQL Server

- Usa `CREATE PROCEDURE` sem necessidade de alterar delimitador
- Fornece T-SQL como linguagem procedural
- Bom suporte para cursores e tratamento de exceções
- Possui conceito de esquemas para organização

## Oracle

- Usa blocos `DECLARE...BEGIN...END`
- Tem PL/SQL como linguagem procedural robusta
- Suporte avançado para views materializadas e refreshing
- Pacotes permitem agrupar procedimentos relacionados

---

<!-- Rodapé -->
<div align="center">
  <p>✨ Desenvolvido com foco na clareza e aplicação prática ✨</p>
  <img src="https://img.shields.io/badge/SQL-00758F?style=for-the-badge&logo=mysql&logoColor=white" />
</div> 

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/> 