<!-- Cabeçalho -->
<div align="center">
  <img src="https://img.shields.io/badge/SQL-00758F?style=for-the-badge&logo=mysql&logoColor=white" />
  <h1>Controle de Usuários e Permissões</h1>
</div>

---

# 🔐 Segurança em Bancos de Dados

A segurança é um aspecto crítico em qualquer sistema de banco de dados. O controle adequado de usuários e permissões ajuda a:

- Proteger dados confidenciais
- Prevenir modificações não autorizadas
- Garantir a integridade dos dados
- Cumprir requisitos regulatórios e de conformidade
- Estabelecer responsabilidade por ações no banco de dados

# 👤 Gerenciamento de Usuários

## Criação de Usuários

Cada SGBD tem sua própria sintaxe para criar usuários:

### MySQL

```sql
-- Criar usuário com senha
CREATE USER 'usuario1'@'localhost' IDENTIFIED BY 'senha_segura';

-- Criar usuário que pode se conectar de qualquer host
CREATE USER 'usuario2'@'%' IDENTIFIED BY 'senha_segura';

-- Criar usuário com autenticação de plugin (MySQL 8.0+)
CREATE USER 'usuario3'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'senha_segura';
```

### PostgreSQL

```sql
-- Criar usuário (role) com senha
CREATE USER usuario1 WITH PASSWORD 'senha_segura';

-- Criar usuário com atributos adicionais
CREATE USER usuario2 WITH 
    PASSWORD 'senha_segura'
    CREATEDB
    VALID UNTIL '2023-12-31';
```

### SQL Server

```sql
-- Criar login no nível do servidor
CREATE LOGIN usuario1 WITH PASSWORD = 'senha_segura';

-- Criar usuário em um banco de dados específico
USE banco_de_dados;
CREATE USER usuario1 FOR LOGIN usuario1;
```

### Oracle

```sql
-- Criar usuário
CREATE USER usuario1 IDENTIFIED BY senha_segura;

-- Criar usuário com configurações específicas
CREATE USER usuario2 IDENTIFIED BY senha_segura
    DEFAULT TABLESPACE users
    QUOTA 100M ON users
    PROFILE app_user;
```

## Alteração de Usuários

### MySQL

```sql
-- Alterar senha
ALTER USER 'usuario1'@'localhost' IDENTIFIED BY 'nova_senha';

-- Renomear usuário (MySQL 8.0+)
RENAME USER 'usuario1'@'localhost' TO 'novo_usuario'@'localhost';

-- Alterar política de expiração de senha
ALTER USER 'usuario1'@'localhost' PASSWORD EXPIRE INTERVAL 90 DAY;
```

### PostgreSQL

```sql
-- Alterar atributos do usuário
ALTER USER usuario1 WITH PASSWORD 'nova_senha';
ALTER USER usuario1 VALID UNTIL '2024-12-31';
ALTER USER usuario1 CREATEDB;
```

### SQL Server

```sql
-- Alterar login
ALTER LOGIN usuario1 WITH PASSWORD = 'nova_senha';

-- Habilitar/desabilitar login
ALTER LOGIN usuario1 DISABLE;
ALTER LOGIN usuario1 ENABLE;
```

### Oracle

```sql
-- Alterar senha
ALTER USER usuario1 IDENTIFIED BY nova_senha;

-- Alterar configurações
ALTER USER usuario1 
    DEFAULT TABLESPACE users
    QUOTA UNLIMITED ON users;
```

## Exclusão de Usuários

### MySQL

```sql
DROP USER 'usuario1'@'localhost';
```

### PostgreSQL

```sql
DROP USER IF EXISTS usuario1;
```

### SQL Server

```sql
-- Remover usuário do banco de dados
DROP USER usuario1;

-- Remover login do servidor
DROP LOGIN usuario1;
```

### Oracle

```sql
DROP USER usuario1 CASCADE;  -- CASCADE remove todos os objetos do usuário
```

# 🛡️ Gerenciamento de Privilégios

Os privilégios (ou permissões) controlam o que cada usuário pode fazer no banco de dados.

## Tipos de Privilégios

### Privilégios de Sistema

Controlam ações administrativas e de sistema.

Exemplos:
- CREATE USER
- SHUTDOWN
- CREATE DATABASE
- CREATE TABLESPACE

### Privilégios de Objeto

Controlam ações em objetos específicos (tabelas, views, procedimentos, etc.).

Exemplos:
- SELECT
- INSERT
- UPDATE
- DELETE
- EXECUTE
- ALTER
- INDEX

## Concedendo Privilégios

### MySQL

```sql
-- Privilégios em tabela específica
GRANT SELECT, INSERT ON banco_dados.tabela TO 'usuario1'@'localhost';

-- Todos os privilégios em um banco
GRANT ALL PRIVILEGES ON banco_dados.* TO 'usuario1'@'localhost';

-- Privilégios em todas as tabelas de todos os bancos
GRANT SELECT ON *.* TO 'usuario1'@'localhost';

-- Conceder privilégio com opção de repasse
GRANT SELECT ON banco_dados.tabela TO 'usuario1'@'localhost' WITH GRANT OPTION;

-- Aplicar privilégios
FLUSH PRIVILEGES;
```

### PostgreSQL

```sql
-- Privilégios em tabela
GRANT SELECT, INSERT ON tabela TO usuario1;

-- Privilégios em todas as tabelas do esquema
GRANT SELECT ON ALL TABLES IN SCHEMA public TO usuario1;

-- Privilégios para criar objetos no esquema
GRANT CREATE ON SCHEMA public TO usuario1;

-- Privilégios em procedimento armazenado
GRANT EXECUTE ON FUNCTION calcular_total(integer) TO usuario1;
```

### SQL Server

```sql
-- Privilégios em tabela
GRANT SELECT, INSERT ON dbo.tabela TO usuario1;

-- Privilégios em esquema
GRANT SELECT ON SCHEMA::dbo TO usuario1;

-- Privilégios de banco de dados
GRANT CREATE TABLE TO usuario1;

-- Privilégios em procedimento armazenado
GRANT EXECUTE ON dbo.calcular_total TO usuario1;
```

### Oracle

```sql
-- Privilégios em tabela
GRANT SELECT, INSERT ON tabela TO usuario1;

-- Privilégios de sistema
GRANT CREATE SESSION, CREATE TABLE TO usuario1;

-- Privilégio com opção de repasse
GRANT SELECT ON tabela TO usuario1 WITH GRANT OPTION;

-- Privilégios em procedimento
GRANT EXECUTE ON calcular_total TO usuario1;
```

## Revogando Privilégios

### MySQL

```sql
-- Revogar privilégios específicos
REVOKE INSERT ON banco_dados.tabela FROM 'usuario1'@'localhost';

-- Revogar todos os privilégios
REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'usuario1'@'localhost';
```

### PostgreSQL

```sql
-- Revogar privilégios específicos
REVOKE INSERT ON tabela FROM usuario1;

-- Revogar todos os privilégios
REVOKE ALL PRIVILEGES ON tabela FROM usuario1;
```

### SQL Server

```sql
-- Revogar privilégios específicos
REVOKE INSERT ON dbo.tabela FROM usuario1;

-- Revogar com opção CASCADE
REVOKE SELECT ON dbo.tabela FROM usuario1 CASCADE;
```

### Oracle

```sql
-- Revogar privilégios específicos
REVOKE INSERT ON tabela FROM usuario1;

-- Revogar privilégios de sistema
REVOKE CREATE TABLE FROM usuario1;
```

# 🧩 Roles (Funções/Papéis)

Roles são conjuntos de privilégios que podem ser atribuídos a usuários, facilitando a gestão de permissões.

## Criação e Gerenciamento de Roles

### MySQL

```sql
-- Criar role
CREATE ROLE 'app_read', 'app_write';

-- Conceder privilégios à role
GRANT SELECT ON banco_dados.* TO 'app_read';
GRANT INSERT, UPDATE, DELETE ON banco_dados.* TO 'app_write';

-- Atribuir roles a usuários
GRANT 'app_read' TO 'usuario1'@'localhost';
GRANT 'app_read', 'app_write' TO 'usuario2'@'localhost';

-- Ativar roles para a sessão atual (MySQL 8.0+)
SET ROLE 'app_read';
SET ROLE ALL;  -- Ativa todas as roles do usuário
```

### PostgreSQL

```sql
-- Criar role
CREATE ROLE app_read;
CREATE ROLE app_write;

-- Conceder privilégios à role
GRANT SELECT ON ALL TABLES IN SCHEMA public TO app_read;
GRANT INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO app_write;

-- Atribuir roles a usuários
GRANT app_read TO usuario1;
GRANT app_read, app_write TO usuario2;
```

### SQL Server

```sql
-- Criar role no banco de dados
CREATE ROLE app_read;
CREATE ROLE app_write;

-- Conceder privilégios à role
GRANT SELECT ON SCHEMA::dbo TO app_read;
GRANT INSERT, UPDATE, DELETE ON SCHEMA::dbo TO app_write;

-- Atribuir roles a usuários
ALTER ROLE app_read ADD MEMBER usuario1;
ALTER ROLE app_write ADD MEMBER usuario2;
```

### Oracle

```sql
-- Criar role
CREATE ROLE app_read;
CREATE ROLE app_write;

-- Conceder privilégios à role
GRANT SELECT ANY TABLE TO app_read;
GRANT INSERT, UPDATE, DELETE ANY TABLE TO app_write;

-- Atribuir roles a usuários
GRANT app_read TO usuario1;
GRANT app_read, app_write TO usuario2;
```

## Roles Pré-definidas

Muitos SGBDs têm roles pré-definidas com conjuntos específicos de permissões:

### MySQL

- `DBA`: Privilégios administrativos completos
- `ADMIN`: Gerenciamento de usuários e permissões

### PostgreSQL

- `pg_read_all_data`: Leitura em todas as tabelas (PG 14+)
- `pg_write_all_data`: Escrita em todas as tabelas (PG 14+)
- `pg_signal_backend`: Pode enviar sinais para outros backends
- `pg_monitor`: Monitoramento do sistema

### SQL Server

- `db_owner`: Possui todos os privilégios no banco
- `db_datareader`: Pode ler todos os dados
- `db_datawriter`: Pode modificar todos os dados
- `db_ddladmin`: Pode executar comandos DDL (CREATE, ALTER, DROP)

### Oracle

- `CONNECT`: Privilégios básicos de conexão
- `RESOURCE`: Pode criar objetos no próprio esquema
- `DBA`: Acesso administrativo completo

# 🔍 Visualizando Permissões

## Verificando privilégios concedidos

### MySQL

```sql
-- Verificar privilégios de um usuário
SHOW GRANTS FOR 'usuario1'@'localhost';

-- Visualizar privilégios do usuário atual
SHOW GRANTS;

-- Visualizar privilégios de tabela
SELECT * FROM information_schema.table_privileges 
WHERE grantee LIKE '%usuario1%';
```

### PostgreSQL

```sql
-- Verificar privilégios em tabelas
SELECT grantee, table_name, privilege_type
FROM information_schema.table_privileges
WHERE grantee = 'usuario1';

-- Verificar roles do usuário
SELECT r.rolname
FROM pg_roles r
JOIN pg_auth_members m ON m.roleid = r.oid
JOIN pg_roles u ON m.member = u.oid
WHERE u.rolname = 'usuario1';
```

### SQL Server

```sql
-- Verificar permissões no nível do servidor
SELECT * FROM sys.server_permissions
WHERE grantee_principal_id = (SELECT principal_id FROM sys.server_principals WHERE name = 'usuario1');

-- Verificar permissões no nível do banco de dados
SELECT * FROM sys.database_permissions
WHERE grantee_principal_id = (SELECT principal_id FROM sys.database_principals WHERE name = 'usuario1');
```

### Oracle

```sql
-- Verificar privilégios de sistema
SELECT * FROM dba_sys_privs WHERE grantee = 'USUARIO1';

-- Verificar privilégios de objeto
SELECT * FROM dba_tab_privs WHERE grantee = 'USUARIO1';

-- Verificar roles concedidas
SELECT * FROM dba_role_privs WHERE grantee = 'USUARIO1';
```

# 📊 Estratégias e Boas Práticas

## 1. Princípio do Privilégio Mínimo

Conceda apenas as permissões necessárias para um usuário realizar suas tarefas - nada mais.

```sql
-- Ao invés de
GRANT ALL PRIVILEGES ON banco.* TO 'usuario'@'localhost';

-- Prefira
GRANT SELECT ON banco.tabela1 TO 'usuario'@'localhost';
GRANT SELECT, INSERT, UPDATE ON banco.tabela2 TO 'usuario'@'localhost';
```

## 2. Utilize Roles para Gerenciamento

Agrupe permissões em roles baseadas em funções organizacionais ou requisitos de aplicação.

```sql
-- Criar roles funcionais
CREATE ROLE vendas_leitura;
CREATE ROLE vendas_escrita;
CREATE ROLE relatorios;

-- Atribuir permissões apropriadas
GRANT SELECT ON banco.clientes TO vendas_leitura;
GRANT SELECT ON banco.pedidos TO vendas_leitura;

GRANT INSERT, UPDATE ON banco.pedidos TO vendas_escrita;
GRANT SELECT, INSERT, UPDATE ON banco.itens_pedido TO vendas_escrita;

GRANT SELECT ON banco.view_relatorio_vendas TO relatorios;

-- Atribuir roles a usuários conforme função
GRANT vendas_leitura TO 'atendente'@'localhost';
GRANT vendas_leitura, vendas_escrita TO 'vendedor'@'localhost';
GRANT relatorios TO 'gerente'@'localhost';
```

## 3. Crie Usuários Específicos para Aplicações

Evite usar o mesmo usuário para múltiplas aplicações ou propósitos.

```sql
-- Separar por aplicação
CREATE USER 'app_web'@'%' IDENTIFIED BY 'senha123';
CREATE USER 'app_relatorios'@'%' IDENTIFIED BY 'senha456';
CREATE USER 'batch_noturno'@'localhost' IDENTIFIED BY 'senha789';
```

## 4. Use Views para Controle de Acesso a Nível de Linha/Coluna

Views podem restringir quais colunas e linhas um usuário pode acessar.

```sql
-- View que limita acesso por região
CREATE VIEW vendas_regiao_sul AS
SELECT id_venda, cliente, produto, valor
FROM vendas
WHERE regiao = 'Sul';

-- Conceder acesso à view e não à tabela original
GRANT SELECT ON vendas_regiao_sul TO 'gerente_sul'@'localhost';
```

## 5. Implemente Row-Level Security (RLS)

Para controle de acesso mais granular, use recursos de segurança em nível de linha.

```sql
-- PostgreSQL RLS exemplo
CREATE TABLE pedidos (
    id SERIAL PRIMARY KEY,
    cliente_id INT,
    valor DECIMAL(10,2),
    data DATE,
    regiao VARCHAR(50)
);

-- Habilitar RLS
ALTER TABLE pedidos ENABLE ROW LEVEL SECURITY;

-- Criar políticas por região
CREATE POLICY vendas_sul ON pedidos 
    FOR SELECT 
    USING (regiao = 'Sul');

CREATE POLICY vendas_norte ON pedidos 
    FOR SELECT 
    USING (regiao = 'Norte');

-- Atribuir políticas a roles
GRANT SELECT ON pedidos TO gerentes;
```

## 6. Audite Atividades de Usuários

Implemente auditoria para monitorar e registrar ações importantes no banco de dados.

```sql
-- MySQL: Habilitar log geral de consultas
SET GLOBAL general_log = 'ON';
SET GLOBAL general_log_file = '/var/log/mysql/mysql-queries.log';

-- PostgreSQL: Configurar auditoria
-- No postgresql.conf:
-- log_statement = 'mod'       # Registra comandos modificadores (INSERT, UPDATE, DELETE)
-- log_min_duration_statement = 1000  # Registra consultas que demoram mais de 1 segundo
```

## 7. Gerencie Senhas com Segurança

Implemente políticas de senha fortes e rotação regular.

```sql
-- MySQL 8.0+ política de validação de senha
INSTALL COMPONENT 'file://component_validate_password';

-- Configurar política de senha
SET PERSIST validate_password.policy = 'STRONG';
SET PERSIST validate_password.length = 10;

-- Expiração de senha
ALTER USER 'usuario1'@'localhost' PASSWORD EXPIRE INTERVAL 90 DAY;
```

## 8. Limite Conexões e Recursos

Evite que um único usuário consuma todos os recursos do servidor.

```sql
-- MySQL: limitar conexões
ALTER USER 'usuario1'@'localhost' WITH 
    MAX_CONNECTIONS_PER_HOUR 20
    MAX_QUERIES_PER_HOUR 1000
    MAX_UPDATES_PER_HOUR 500;

-- PostgreSQL: limitar recursos
ALTER ROLE usuario1 SET statement_timeout = '30s';  -- Limite de 30 segundos por consulta
```

# 🔄 Exemplo de Implementação Completa

O exemplo a seguir demonstra uma estratégia de gerenciamento de usuários e permissões para um sistema de e-commerce:

```sql
-- Criar roles funcionais
CREATE ROLE vendas_somente_leitura;
CREATE ROLE vendas_operador;
CREATE ROLE vendas_supervisor;
CREATE ROLE estoque_operador;
CREATE ROLE relatorios;
CREATE ROLE administrador;

-- Permissões para vendas (leitura)
GRANT SELECT ON ecommerce.produtos TO vendas_somente_leitura;
GRANT SELECT ON ecommerce.clientes TO vendas_somente_leitura;
GRANT SELECT ON ecommerce.pedidos TO vendas_somente_leitura;
GRANT SELECT ON ecommerce.itens_pedido TO vendas_somente_leitura;

-- Permissões para vendas (operador)
GRANT vendas_somente_leitura TO vendas_operador;
GRANT INSERT ON ecommerce.pedidos TO vendas_operador;
GRANT INSERT ON ecommerce.itens_pedido TO vendas_operador;
GRANT SELECT, UPDATE ON ecommerce.estoque TO vendas_operador;

-- Permissões para vendas (supervisor)
GRANT vendas_operador TO vendas_supervisor;
GRANT UPDATE, DELETE ON ecommerce.pedidos TO vendas_supervisor;
GRANT UPDATE, DELETE ON ecommerce.itens_pedido TO vendas_supervisor;
GRANT SELECT ON ecommerce.view_desempenho_vendedores TO vendas_supervisor;

-- Permissões para estoque
GRANT SELECT ON ecommerce.produtos TO estoque_operador;
GRANT SELECT, UPDATE ON ecommerce.estoque TO estoque_operador;
GRANT SELECT ON ecommerce.pedidos TO estoque_operador;
GRANT SELECT ON ecommerce.itens_pedido TO estoque_operador;

-- Permissões para relatórios
GRANT SELECT ON ecommerce.view_vendas_diarias TO relatorios;
GRANT SELECT ON ecommerce.view_estoque_critico TO relatorios;
GRANT SELECT ON ecommerce.view_desempenho_vendedores TO relatorios;
GRANT SELECT ON ecommerce.view_clientes_vip TO relatorios;

-- Permissões para administrador
GRANT ALL PRIVILEGES ON ecommerce.* TO administrador;

-- Criar usuários
CREATE USER 'atendente1'@'%' IDENTIFIED BY 'senha123';
CREATE USER 'vendedor1'@'%' IDENTIFIED BY 'senha456';
CREATE USER 'supervisor'@'%' IDENTIFIED BY 'senha789';
CREATE USER 'estoquista1'@'%' IDENTIFIED BY 'senha101';
CREATE USER 'gerente'@'%' IDENTIFIED BY 'senha112';
CREATE USER 'admin'@'localhost' IDENTIFIED BY 'senhaSegura!123';

-- Atribuir roles a usuários
GRANT vendas_somente_leitura TO 'atendente1'@'%';
GRANT vendas_operador TO 'vendedor1'@'%';
GRANT vendas_supervisor TO 'supervisor'@'%';
GRANT estoque_operador TO 'estoquista1'@'%';
GRANT relatorios TO 'gerente'@'%';
GRANT administrador TO 'admin'@'localhost';

-- Configurar limites de recursos
ALTER USER 'vendedor1'@'%' WITH 
    MAX_QUERIES_PER_HOUR 1000 
    MAX_UPDATES_PER_HOUR 200;

ALTER USER 'atendente1'@'%' WITH 
    MAX_QUERIES_PER_HOUR 500 
    MAX_UPDATES_PER_HOUR 100;

-- Políticas de senha
ALTER USER 'admin'@'localhost' PASSWORD EXPIRE INTERVAL 60 DAY;
ALTER USER 'supervisor'@'%' PASSWORD EXPIRE INTERVAL 90 DAY;
```

# 🌐 Segurança em Diferentes Ambientes

## Desenvolvimento vs. Produção

| Ambiente | Abordagem |
|----------|-----------|
| **Desenvolvimento** | Permissões mais amplas, senhas simples, acesso direto a dados |
| **Teste/QA** | Similar à produção, mas com dados de teste, usuários de teste |
| **Produção** | Privilégios mínimos, senhas complexas, acesso restrito, auditoria completa |

## Banco de Dados na Nuvem

Ao trabalhar com bancos de dados hospedados em nuvem, considere:

1. **Autenticação integrada**: Use serviços de identidade cloud (AWS IAM, Azure AD)
2. **Rotação automática de credenciais**: Implemente serviços de gerenciamento de segredos
3. **Segurança de rede**: Restrinja IPs/sub-redes que podem acessar o banco
4. **Criptografia**: Ative criptografia em trânsito e em repouso

```sql
-- AWS RDS MySQL exemplo (via AWS CLI)
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --vpc-security-groups sg-0123456789abcdef \
    --backup-retention-period 7 \
    --storage-encrypted \
    --apply-immediately
```

---

<!-- Rodapé -->
<div align="center">
  <p>✨ Desenvolvido com foco na clareza e aplicação prática ✨</p>
  <img src="https://img.shields.io/badge/SQL-00758F?style=for-the-badge&logo=mysql&logoColor=white" />
</div> 