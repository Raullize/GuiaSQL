<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# O que são Dados e Informação?

## Conceitos Fundamentais

### O que são Dados?
**Dados** são fatos brutos, não processados, que por si só não possuem significado claro ou contexto. São a matéria-prima a partir da qual a informação é derivada.

Exemplos de dados:
- Números (25, 1000, 37.5)
- Palavras isoladas ("São Paulo", "João", "Produto")
- Datas (15/03/2023)
- Valores booleanos (Verdadeiro/Falso)

### O que é Informação?
**Informação** é o resultado do processamento, organização e interpretação dos dados de uma forma que tenha significado e valor para quem a recebe.

Exemplos de transformação de dados em informação:
- Dado: 37.5
- Informação: "A temperatura do paciente é 37.5°C, indicando febre leve"

## Ciclo de Vida: Dados → Informação → Conhecimento

1. **Dados**: Fatos brutos coletados
2. **Informação**: Dados processados e contextualizados
3. **Conhecimento**: Informação aplicada e compreendida
4. **Sabedoria**: Conhecimento aplicado com discernimento

## Importância no Contexto de Banco de Dados

No contexto de SQL e bancos de dados relacionais:

- **Dados** são armazenados em tabelas, como valores individuais em células
- **Informação** é obtida através de consultas SQL que relacionam, filtram e agregam esses dados

```sql
-- Exemplo: Transformando dados em informação
-- Dados: Valores em tabelas separadas
SELECT c.nome, p.descricao, v.data, v.valor
FROM clientes c
JOIN vendas v ON c.id = v.cliente_id
JOIN produtos p ON p.id = v.produto_id
WHERE v.data BETWEEN '2023-01-01' AND '2023-12-31'
-- Resultado: Informação sobre quais clientes compraram quais produtos,
-- quando e por quanto, durante o ano de 2023
```

## A Importância da Qualidade dos Dados

Para que os bancos de dados gerem informações confiáveis, é essencial:

- Integridade dos dados
- Precisão dos dados
- Consistência dos dados
- Completude dos dados

O SQL fornece mecanismos como constraints, tipos de dados e relacionamentos que ajudam a manter a qualidade dos dados armazenados.

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/>