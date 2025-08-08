<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# 🔗 ORMs - Object-Relational Mapping

## O que é um ORM?

**ORM** (Object-Relational Mapping) é uma técnica de programação que permite mapear dados entre sistemas incompatíveis usando linguagens de programação orientadas a objetos. Em outras palavras, um ORM atua como uma ponte entre o mundo orientado a objetos da programação e o mundo relacional dos bancos de dados.

## Como funciona?

Um ORM traduz automaticamente entre:
- **Tabelas** do banco de dados ↔ **Classes** na linguagem de programação
- **Registros** (linhas) ↔ **Objetos/Instâncias**
- **Colunas** ↔ **Atributos/Propriedades**
- **Relacionamentos** ↔ **Associações entre objetos**

## Principais Vantagens dos ORMs

### 1. **Abstração do SQL**
```python
# Sem ORM (SQL puro)
query = "SELECT * FROM usuarios WHERE idade > 18 AND ativo = 1"
result = cursor.execute(query)

# Com ORM (exemplo Django)
usuarios = Usuario.objects.filter(idade__gt=18, ativo=True)
```

### 2. **Segurança contra SQL Injection**
ORMs automaticamente escapam valores, prevenindo ataques de injeção SQL.

### 3. **Portabilidade entre SGBDs**
O mesmo código pode funcionar com diferentes bancos de dados (MySQL, PostgreSQL, SQLite, etc.).

### 4. **Manutenibilidade**
Código mais limpo, legível e fácil de manter.

### 5. **Validação de Dados**
Validações automáticas baseadas no modelo de dados.

## Principais Desvantagens dos ORMs

### 1. **Performance**
- Consultas geradas podem não ser otimizadas
- Overhead adicional de processamento
- Problema N+1 queries

### 2. **Curva de Aprendizado**
- Necessário aprender a sintaxe específica do ORM
- Pode mascarar o entendimento de SQL

### 3. **Limitações**
- Consultas muito complexas podem ser difíceis de expressar
- Menos controle sobre o SQL gerado

### 4. **Dependência**
- Acoplamento com uma biblioteca específica
- Possíveis bugs ou limitações do ORM

## ORMs Populares por Linguagem

### Python
- **Django ORM**: Integrado ao framework Django
- **SQLAlchemy**: ORM mais poderoso e flexível
- **Peewee**: ORM leve e simples
- **Tortoise ORM**: ORM assíncrono inspirado no Django

### JavaScript/Node.js
- **Sequelize**: ORM maduro e completo
- **TypeORM**: ORM para TypeScript e JavaScript
- **Prisma**: ORM moderno com forte tipagem
- **Mongoose**: ODM para MongoDB

### Java
- **Hibernate**: ORM mais popular para Java
- **MyBatis**: Framework de mapeamento SQL
- **EclipseLink**: Implementação de referência JPA

### C#/.NET
- **Entity Framework**: ORM oficial da Microsoft
- **Dapper**: Micro ORM de alta performance
- **NHibernate**: Port do Hibernate para .NET

### PHP
- **Eloquent**: ORM do Laravel
- **Doctrine**: ORM robusto e completo
- **Propel**: ORM baseado em Active Record

### Ruby
- **Active Record**: ORM do Ruby on Rails
- **Sequel**: ORM flexível e poderoso
- **DataMapper**: ORM baseado no padrão Data Mapper

## Exemplo Prático: Django ORM

### Definindo Modelos
```python
from django.db import models

class Cliente(models.Model):
    nome = models.CharField(max_length=100)
    email = models.EmailField(unique=True)
    data_nascimento = models.DateField()
    ativo = models.BooleanField(default=True)
    
    def __str__(self):
        return self.nome

class Pedido(models.Model):
    cliente = models.ForeignKey(Cliente, on_delete=models.CASCADE)
    data_pedido = models.DateTimeField(auto_now_add=True)
    valor_total = models.DecimalField(max_digits=10, decimal_places=2)
    status = models.CharField(max_length=20, default='pendente')
```

### Operações CRUD
```python
# CREATE - Criar
cliente = Cliente.objects.create(
    nome="João Silva",
    email="joao@email.com",
    data_nascimento="1990-05-15"
)

# READ - Ler
clientes_ativos = Cliente.objects.filter(ativo=True)
cliente_especifico = Cliente.objects.get(email="joao@email.com")

# UPDATE - Atualizar
cliente.nome = "João Santos"
cliente.save()

# Ou atualização em lote
Cliente.objects.filter(ativo=False).update(ativo=True)

# DELETE - Deletar
cliente.delete()

# Ou deleção em lote
Cliente.objects.filter(data_nascimento__year__lt=1980).delete()
```

### Consultas Complexas
```python
# Joins automáticos
pedidos_com_cliente = Pedido.objects.select_related('cliente')

# Agregações
from django.db.models import Count, Sum, Avg

clientes_com_pedidos = Cliente.objects.annotate(
    total_pedidos=Count('pedido'),
    valor_total_gasto=Sum('pedido__valor_total')
).filter(total_pedidos__gt=0)

# Consultas complexas
clientes_vip = Cliente.objects.filter(
    pedido__valor_total__gte=1000,
    pedido__data_pedido__year=2023
).distinct()
```

## Boas Práticas com ORMs

### 1. **Use select_related() e prefetch_related()**
```python
# Evita o problema N+1
pedidos = Pedido.objects.select_related('cliente').all()
```

### 2. **Otimize consultas com only() e defer()**
```python
# Carrega apenas campos necessários
clientes = Cliente.objects.only('nome', 'email')
```

### 3. **Use bulk operations para grandes volumes**
```python
# Inserção em lote
Cliente.objects.bulk_create(lista_de_clientes)

# Atualização em lote
Cliente.objects.bulk_update(clientes, ['nome', 'email'])
```

### 4. **Monitore as consultas geradas**
```python
from django.db import connection

# Visualizar consultas SQL geradas
print(connection.queries)
```

### 5. **Use raw SQL quando necessário**
```python
# Para consultas muito complexas
clientes = Cliente.objects.raw(
    "SELECT * FROM app_cliente WHERE EXTRACT(YEAR FROM data_nascimento) = %s",
    [1990]
)
```

## Quando Usar ORMs?

### ✅ **Use ORMs quando:**
- Desenvolvimento rápido é prioridade
- Equipe tem pouco conhecimento em SQL
- Aplicação precisa suportar múltiplos SGBDs
- Segurança contra SQL injection é crítica
- Manutenibilidade do código é importante

### ❌ **Evite ORMs quando:**
- Performance é crítica
- Consultas são muito complexas
- Controle total sobre SQL é necessário
- Aplicação é muito simples
- Equipe tem expertise avançada em SQL

## Alternativas aos ORMs

### 1. **Query Builders**
- Mais controle que ORMs, mais abstração que SQL puro
- Exemplos: Knex.js, Laravel Query Builder

### 2. **Micro ORMs**
- Funcionalidades básicas de mapeamento
- Exemplos: Dapper (C#), MyBatis (Java)

### 3. **SQL Puro**
- Controle total sobre as consultas
- Máxima performance
- Maior complexidade de desenvolvimento

## Conclusão

ORMs são ferramentas poderosas que podem acelerar significativamente o desenvolvimento de aplicações. No entanto, é importante entender suas limitações e saber quando usar SQL puro para casos específicos.

A chave está em encontrar o equilíbrio entre produtividade de desenvolvimento e performance da aplicação, sempre considerando as necessidades específicas do projeto.

**💡 Dica:** Mesmo usando ORMs, é fundamental ter um bom conhecimento de SQL para entender o que está acontecendo "por baixo dos panos" e poder otimizar quando necessário.

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/>