<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# 📖 Glossário SQL e Banco de Dados

Este glossário contém definições de termos técnicos utilizados em SQL e bancos de dados relacionais, organizados alfabeticamente para facilitar a consulta.

---

## A

**ACID** - Acrônimo que representa as quatro propriedades fundamentais de uma transação: Atomicidade, Consistência, Isolamento e Durabilidade.

**Agregação** - Operação que combina múltiplos valores em um único resultado, como SUM(), COUNT(), AVG().

**Alias** - Nome alternativo (apelido) dado a uma tabela ou coluna em uma consulta SQL para facilitar a leitura ou evitar ambiguidade.

**ALTER** - Comando DDL usado para modificar a estrutura de objetos existentes no banco de dados.

**Atomicidade** - Propriedade ACID que garante que uma transação seja executada completamente ou não seja executada.

**Atributo** - Característica ou propriedade de uma entidade, representada como coluna em uma tabela.

**AUTO_INCREMENT** - Propriedade que faz com que o valor de uma coluna seja incrementado automaticamente a cada novo registro.

## B

**Backup** - Cópia de segurança dos dados do banco para proteção contra perda de informações.

**BCNF (Boyce-Codd Normal Form)** - Forma normal mais restritiva que a 3FN, eliminando certas anomalias de dependência.

**BEGIN** - Comando que inicia uma transação explícita.

**BETWEEN** - Operador usado para filtrar valores dentro de um intervalo específico.

**BIGINT** - Tipo de dados para números inteiros grandes (64 bits).

**BLOB** - Tipo de dados para armazenar dados binários grandes (Binary Large Object).

**BOOLEAN** - Tipo de dados que aceita apenas valores verdadeiro (TRUE) ou falso (FALSE).

## C

**Cardinalidade** - Número de registros únicos em uma coluna ou relacionamento entre tabelas.

**CASCADE** - Ação que propaga operações (UPDATE/DELETE) para registros relacionados.

**CHAR** - Tipo de dados para strings de tamanho fixo.

**CHECK** - Constraint que valida se os dados atendem a uma condição específica.

**Chave Candidata** - Conjunto de atributos que podem servir como chave primária.

**Chave Composta** - Chave primária formada por duas ou mais colunas.

**Chave Estrangeira (Foreign Key)** - Campo que estabelece relação com a chave primária de outra tabela.

**Chave Primária (Primary Key)** - Campo ou conjunto de campos que identifica unicamente cada registro.

**CLOB** - Tipo de dados para armazenar texto grande (Character Large Object).

**Coluna** - Campo vertical em uma tabela que representa um atributo específico.

**COMMIT** - Comando que confirma e torna permanentes as alterações de uma transação.

**Concorrência** - Capacidade de múltiplos usuários acessarem o banco simultaneamente.

**Consistência** - Propriedade ACID que garante que o banco permaneça em estado válido após transações.

**Constraint** - Regra aplicada a colunas ou tabelas para manter a integridade dos dados.

**COUNT()** - Função de agregação que conta o número de registros.

**CREATE** - Comando DDL usado para criar objetos no banco de dados.

**CRUD** - Acrônimo para Create, Read, Update, Delete - operações básicas em dados.

**CROSS JOIN** - Tipo de junção que retorna o produto cartesiano entre duas tabelas.

## D

**Dados** - Fatos brutos e não processados armazenados no banco.

**Data Warehouse** - Sistema otimizado para análise de grandes volumes de dados históricos.

**DATABASE** - Coleção organizada de dados relacionados.

**DATE** - Tipo de dados para armazenar datas.

**DATETIME** - Tipo de dados para armazenar data e hora.

**DCL (Data Control Language)** - Categoria de comandos SQL para controle de acesso (GRANT, REVOKE).

**DDL (Data Definition Language)** - Categoria de comandos SQL para definir estruturas (CREATE, ALTER, DROP).

**DECIMAL** - Tipo de dados para números com precisão decimal fixa.

**DEFAULT** - Valor padrão atribuído a uma coluna quando nenhum valor é especificado.

**DELETE** - Comando DML para remover registros de uma tabela.

**Desnormalização** - Processo de introduzir redundância controlada para melhorar performance.

**DISTINCT** - Palavra-chave que elimina registros duplicados do resultado.

**DML (Data Manipulation Language)** - Categoria de comandos SQL para manipular dados (INSERT, UPDATE, DELETE, SELECT).

**DROP** - Comando DDL para remover objetos do banco de dados.

**Durabilidade** - Propriedade ACID que garante persistência das transações confirmadas.

## E

**Entidade** - Objeto do mundo real representado no banco de dados.

**ER (Entity-Relationship)** - Modelo conceitual para representar entidades e relacionamentos.

**EXISTS** - Operador que verifica a existência de registros em uma subconsulta.

**EXPLAIN** - Comando que mostra o plano de execução de uma consulta.

## F

**FLOAT** - Tipo de dados para números de ponto flutuante.

**FOREIGN KEY** - Ver Chave Estrangeira.

**FROM** - Cláusula que especifica a tabela de origem dos dados.

**FULL JOIN** - Junção que retorna todos os registros de ambas as tabelas.

**Função de Agregação** - Função que opera em conjuntos de valores (SUM, AVG, COUNT, etc.).

## G

**GRANT** - Comando DCL para conceder permissões a usuários.

**GROUP BY** - Cláusula que agrupa registros com valores similares.

## H

**HAVING** - Cláusula que filtra grupos criados por GROUP BY.

**Hash Index** - Tipo de índice otimizado para buscas de igualdade.

## I

**Índice** - Estrutura que acelera consultas criando atalhos para os dados.

**Informação** - Dados processados e contextualizados que possuem significado.

**INNER JOIN** - Junção que retorna apenas registros com correspondência em ambas as tabelas.

**INSERT** - Comando DML para adicionar novos registros a uma tabela.

**INT/INTEGER** - Tipo de dados para números inteiros.

**Integridade Referencial** - Garantia de que relacionamentos entre tabelas sejam válidos.

**Isolamento** - Propriedade ACID que separa transações concorrentes.

## J

**JOIN** - Operação que combina registros de duas ou mais tabelas.

**JSON** - Tipo de dados para armazenar documentos no formato JavaScript Object Notation.

## K

**KEY** - Ver Chave.

## L

**LEFT JOIN** - Junção que retorna todos os registros da tabela à esquerda.

**LIKE** - Operador para busca de padrões em texto usando wildcards.

**LIMIT** - Cláusula que restringe o número de registros retornados.

**Lock** - Mecanismo que controla acesso concorrente aos dados.

**LONGTEXT** - Tipo de dados para textos muito longos.

## M

**MAX()** - Função de agregação que retorna o maior valor.

**MIN()** - Função de agregação que retorna o menor valor.

**Modelo Relacional** - Paradigma que organiza dados em tabelas relacionadas.

**MySQL** - Sistema de gerenciamento de banco de dados relacional popular.

## N

**NoSQL** - Categoria de bancos de dados não relacionais.

**Normalização** - Processo de organizar dados para eliminar redundâncias.

**NOT NULL** - Constraint que impede valores nulos em uma coluna.

**NULL** - Valor especial que representa ausência de dados.

## O

**OFFSET** - Especifica quantos registros pular antes de retornar resultados.

**ON DELETE CASCADE** - Ação que remove registros relacionados automaticamente.

**ORDER BY** - Cláusula que ordena os resultados de uma consulta.

**ORM (Object-Relational Mapping)** - Técnica que mapeia objetos para tabelas relacionais.

**OUTER JOIN** - Junção que inclui registros sem correspondência.

## P

**Performance** - Medida de eficiência e velocidade das operações do banco.

**Plano de Execução** - Estratégia que o SGBD usa para executar uma consulta.

**PostgreSQL** - Sistema de gerenciamento de banco de dados objeto-relacional.

**PRIMARY KEY** - Ver Chave Primária.

**Procedure** - Ver Stored Procedure.

## Q

**Query** - Consulta ou comando SQL para recuperar dados.

**Query Optimizer** - Componente do SGBD que escolhe a melhor forma de executar consultas.

## R

**Registro** - Linha em uma tabela que representa uma instância de entidade.

**Relacionamento** - Associação entre entidades em um banco de dados.

**REVOKE** - Comando DCL para remover permissões de usuários.

**RIGHT JOIN** - Junção que retorna todos os registros da tabela à direita.

**ROLLBACK** - Comando que desfaz alterações de uma transação.

## S

**Schema** - Estrutura lógica que define a organização do banco de dados.

**SELECT** - Comando DML para consultar dados.

**SGBD (Sistema de Gerenciamento de Banco de Dados)** - Software que gerencia bancos de dados.

**Slug** - Versão simplificada de texto para URLs amigáveis.

**SQL (Structured Query Language)** - Linguagem padrão para bancos relacionais.

**SQLite** - Sistema de banco de dados leve e embarcado.

**Stored Procedure** - Conjunto de comandos SQL armazenados no banco.

**Subquery** - Consulta aninhada dentro de outra consulta.

**SUM()** - Função de agregação que calcula a soma de valores.

## T

**Tabela** - Estrutura bidimensional que armazena dados em linhas e colunas.

**TCL (Transaction Control Language)** - Categoria de comandos para controle de transações.

**TEXT** - Tipo de dados para armazenar texto de tamanho variável.

**TIME** - Tipo de dados para armazenar horários.

**TIMESTAMP** - Tipo de dados para data e hora com precisão.

**Transação** - Sequência de operações tratadas como uma unidade.

**Trigger** - Código que executa automaticamente em resposta a eventos.

**TRUNCATE** - Comando que remove todos os registros de uma tabela rapidamente.

**Tupla** - Termo formal para registro ou linha em uma tabela.

## U

**UNION** - Operador que combina resultados de duas ou mais consultas.

**UNIQUE** - Constraint que garante valores únicos em uma coluna.

**UPDATE** - Comando DML para modificar registros existentes.

## V

**VARCHAR** - Tipo de dados para strings de tamanho variável.

**VIEW** - Tabela virtual baseada em uma consulta SQL.

## W

**WHERE** - Cláusula que especifica condições para filtrar registros.

**Wildcard** - Caracteres especiais (% e _) usados com LIKE para busca de padrões.

## X

**XML** - Tipo de dados para armazenar documentos XML.

---

## 📚 Como Usar Este Glossário

- **Busca rápida**: Use Ctrl+F para encontrar termos específicos
- **Referência cruzada**: Termos relacionados são mencionados nas definições
- **Contexto**: Cada definição inclui o contexto de uso em SQL/BD
- **Iniciantes**: Linguagem simples e exemplos quando necessário

## 💡 Dicas de Estudo

1. **Revise regularmente**: Consulte o glossário durante o aprendizado
2. **Pratique os termos**: Use os conceitos em exercícios práticos
3. **Conecte conceitos**: Entenda como os termos se relacionam
4. **Expanda conhecimento**: Use as definições como ponto de partida para estudos mais profundos

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/>