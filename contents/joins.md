<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# 🔗 JOINs em SQL

## O que são JOINs?

**JOINs** são operações que permitem combinar registros de duas ou mais tabelas em um banco de dados relacional. Eles são fundamentais para consultar dados distribuídos em múltiplas tabelas e formam a base da funcionalidade relacional do SQL.

Os JOINs nos permitem estabelecer conexões entre tabelas baseadas em colunas relacionadas, normalmente chaves primárias e estrangeiras.

## Tipos de JOINs

### INNER JOIN

O **INNER JOIN** retorna apenas os registros que têm correspondências em ambas as tabelas.

```sql
SELECT cliente.nome, pedido.numero
FROM cliente
INNER JOIN pedido ON cliente.id = pedido.cliente_id;
```

![Inner Join](https://upload.wikimedia.org/wikipedia/commons/thumb/1/18/SQL_Join_-_07_A_Inner_Join_B.svg/250px-SQL_Join_-_07_A_Inner_Join_B.svg.png)

### LEFT JOIN (ou LEFT OUTER JOIN)

O **LEFT JOIN** retorna todos os registros da tabela à esquerda e os registros correspondentes da tabela à direita. Se não houver correspondência, o resultado conterá NULL para as colunas da tabela à direita.

```sql
SELECT cliente.nome, pedido.numero
FROM cliente
LEFT JOIN pedido ON cliente.id = pedido.cliente_id;
```

![Left Join](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f6/SQL_Join_-_01_A_Left_Join_B.svg/250px-SQL_Join_-_01_A_Left_Join_B.svg.png)

### RIGHT JOIN (ou RIGHT OUTER JOIN)

O **RIGHT JOIN** retorna todos os registros da tabela à direita e os registros correspondentes da tabela à esquerda. Se não houver correspondência, o resultado conterá NULL para as colunas da tabela à esquerda.

```sql
SELECT cliente.nome, pedido.numero
FROM cliente
RIGHT JOIN pedido ON cliente.id = pedido.cliente_id;
```

![Right Join](https://upload.wikimedia.org/wikipedia/commons/thumb/5/5f/SQL_Join_-_03_A_Right_Join_B.svg/250px-SQL_Join_-_03_A_Right_Join_B.svg.png)

### FULL JOIN (ou FULL OUTER JOIN)

O **FULL JOIN** retorna todos os registros quando há uma correspondência em qualquer uma das tabelas. Se não houver correspondência, o resultado conterá NULL para as colunas da tabela sem correspondência.

```sql
SELECT cliente.nome, pedido.numero
FROM cliente
FULL JOIN pedido ON cliente.id = pedido.cliente_id;
```

![Full Join](https://upload.wikimedia.org/wikipedia/commons/thumb/3/3d/SQL_Join_-_05b_A_Full_Join_B.svg/250px-SQL_Join_-_05b_A_Full_Join_B.svg.png)

## Casos de Uso Práticos

### Encontrar clientes sem pedidos

```sql
SELECT cliente.nome
FROM cliente
LEFT JOIN pedido ON cliente.id = pedido.cliente_id
WHERE pedido.id IS NULL;
```

### Juntar dados de múltiplas tabelas

```sql
SELECT 
    c.nome AS cliente, 
    p.numero AS pedido,
    ip.quantidade,
    pr.nome AS produto
FROM cliente c
INNER JOIN pedido p ON c.id = p.cliente_id
INNER JOIN item_pedido ip ON p.id = ip.pedido_id
INNER JOIN produto pr ON ip.produto_id = pr.id;
```

## Dicas de Boas Práticas

1. **Use aliases** para tornar suas consultas mais legíveis
2. **Especifique as colunas** em vez de usar `SELECT *`
3. **Indique a tabela** para cada coluna quando trabalhar com múltiplas tabelas
4. **Priorize INNER JOIN** quando possível por questões de performance
5. **Crie índices** nas colunas usadas para junções para melhorar a performance

## Erros Comuns

- Esquecer de especificar a condição de junção (ON)
- Usar o tipo errado de JOIN para o caso de uso
- Juntar tabelas sem relacionamento significativo
- Criar cartesian products (junções sem condições)

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/> 