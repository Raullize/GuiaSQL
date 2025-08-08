<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=header"/>

# 🏷️ Slugs em Bancos de Dados

## O que é um Slug?

**Slug** é uma versão simplificada e amigável para URLs de um texto, geralmente derivada de títulos ou nomes. O termo vem do jornalismo, onde "slug" era um nome curto usado para identificar uma história.

Em bancos de dados e desenvolvimento web, slugs são strings que:
- Contêm apenas letras minúsculas, números e hífens
- Não possuem espaços, acentos ou caracteres especiais
- São únicos dentro de seu contexto
- São legíveis e descritivos

## Exemplos de Slugs

| Título Original | Slug Gerado |
|----------------|-------------|
| "Como Aprender SQL?" | `como-aprender-sql` |
| "Melhores Práticas de Banco de Dados" | `melhores-praticas-de-banco-de-dados` |
| "Tutorial MySQL 2024" | `tutorial-mysql-2024` |
| "Índices & Performance" | `indices-performance` |

## Por que Usar Slugs?

### 1. **URLs Amigáveis (SEO-Friendly)**
```
❌ Ruim: https://site.com/artigo?id=123
✅ Bom: https://site.com/artigo/como-aprender-sql
```

### 2. **Melhor Experiência do Usuário**
- URLs mais legíveis e memoráveis
- Usuários podem entender o conteúdo pela URL
- Facilita compartilhamento

### 3. **SEO (Search Engine Optimization)**
- Motores de busca preferem URLs descritivas
- Palavras-chave na URL melhoram o ranking
- Melhor indexação do conteúdo

### 4. **Estabilidade de Links**
- Mesmo se o ID numérico mudar, o slug pode permanecer
- Links externos não quebram facilmente

## Implementação em Banco de Dados

### Estrutura da Tabela
```sql
CREATE TABLE artigos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    titulo VARCHAR(255) NOT NULL,
    slug VARCHAR(255) NOT NULL UNIQUE,
    conteudo TEXT,
    data_criacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    -- Índice para busca rápida por slug
    INDEX idx_slug (slug)
);
```

### Inserindo Dados com Slug
```sql
INSERT INTO artigos (titulo, slug, conteudo) VALUES 
('Como Aprender SQL', 'como-aprender-sql', 'Conteúdo do artigo...'),
('Melhores Práticas de BD', 'melhores-praticas-bd', 'Conteúdo do artigo...'),
('Tutorial MySQL Avançado', 'tutorial-mysql-avancado', 'Conteúdo do artigo...');
```

### Consultando por Slug
```sql
-- Buscar artigo por slug
SELECT * FROM artigos WHERE slug = 'como-aprender-sql';

-- Verificar se slug já existe (para evitar duplicatas)
SELECT COUNT(*) FROM artigos WHERE slug = 'novo-artigo';
```

## Geração Automática de Slugs

### Algoritmo Básico
1. Converter para minúsculas
2. Remover acentos e caracteres especiais
3. Substituir espaços por hífens
4. Remover hífens consecutivos
5. Remover hífens do início e fim
6. Verificar unicidade

### Implementação em Python
```python
import re
import unicodedata

def gerar_slug(texto):
    # Converter para minúsculas
    slug = texto.lower()
    
    # Remover acentos
    slug = unicodedata.normalize('NFKD', slug)
    slug = slug.encode('ascii', 'ignore').decode('ascii')
    
    # Substituir caracteres não alfanuméricos por hífen
    slug = re.sub(r'[^a-z0-9]+', '-', slug)
    
    # Remover hífens do início e fim
    slug = slug.strip('-')
    
    return slug

# Exemplos
print(gerar_slug("Como Aprender SQL?"))  # como-aprender-sql
print(gerar_slug("Índices & Performance"))  # indices-performance
print(gerar_slug("Tutorial MySQL 2024!"))  # tutorial-mysql-2024
```

### Implementação em JavaScript
```javascript
function gerarSlug(texto) {
    return texto
        .toLowerCase()
        .normalize('NFD')
        .replace(/[\u0300-\u036f]/g, '') // Remove acentos
        .replace(/[^a-z0-9\s-]/g, '') // Remove caracteres especiais
        .replace(/\s+/g, '-') // Substitui espaços por hífens
        .replace(/-+/g, '-') // Remove hífens consecutivos
        .replace(/^-|-$/g, ''); // Remove hífens do início e fim
}

// Exemplos
console.log(gerarSlug("Como Aprender SQL?")); // como-aprender-sql
console.log(gerarSlug("Índices & Performance")); // indices-performance
```

### Implementação em PHP
```php
function gerarSlug($texto) {
    // Converter para minúsculas
    $slug = strtolower($texto);
    
    // Remover acentos
    $slug = iconv('UTF-8', 'ASCII//TRANSLIT', $slug);
    
    // Substituir caracteres não alfanuméricos por hífen
    $slug = preg_replace('/[^a-z0-9]+/', '-', $slug);
    
    // Remover hífens do início e fim
    $slug = trim($slug, '-');
    
    return $slug;
}

// Exemplos
echo gerarSlug("Como Aprender SQL?"); // como-aprender-sql
echo gerarSlug("Índices & Performance"); // indices-performance
```

## Garantindo Unicidade

### Método 1: Adicionar Sufixo Numérico
```sql
-- Função para gerar slug único
DELIMITER //
CREATE FUNCTION gerar_slug_unico(titulo_original VARCHAR(255))
RETURNS VARCHAR(255)
READS SQL DATA
DETERMINISTIC
BEGIN
    DECLARE slug_base VARCHAR(255);
    DECLARE slug_final VARCHAR(255);
    DECLARE contador INT DEFAULT 1;
    DECLARE existe INT DEFAULT 1;
    
    -- Gerar slug base (implementar lógica de limpeza)
    SET slug_base = LOWER(REPLACE(titulo_original, ' ', '-'));
    SET slug_final = slug_base;
    
    -- Verificar se já existe
    WHILE existe > 0 DO
        SELECT COUNT(*) INTO existe FROM artigos WHERE slug = slug_final;
        
        IF existe > 0 THEN
            SET slug_final = CONCAT(slug_base, '-', contador);
            SET contador = contador + 1;
        END IF;
    END WHILE;
    
    RETURN slug_final;
END//
DELIMITER ;
```

### Método 2: Usar UUID Curto
```python
import uuid

def gerar_slug_com_uuid(titulo):
    slug_base = gerar_slug(titulo)
    uuid_curto = str(uuid.uuid4())[:8]
    return f"{slug_base}-{uuid_curto}"

# Exemplo
print(gerar_slug_com_uuid("Como Aprender SQL"))  
# como-aprender-sql-a1b2c3d4
```

## Boas Práticas para Slugs

### 1. **Comprimento Adequado**
```sql
-- Limitar tamanho do slug
ALTER TABLE artigos ADD CONSTRAINT chk_slug_length 
CHECK (CHAR_LENGTH(slug) BETWEEN 3 AND 100);
```

### 2. **Validação de Formato**
```sql
-- Garantir formato correto
ALTER TABLE artigos ADD CONSTRAINT chk_slug_format 
CHECK (slug REGEXP '^[a-z0-9]+(-[a-z0-9]+)*$');
```

### 3. **Índices para Performance**
```sql
-- Índice único para slugs
CREATE UNIQUE INDEX idx_slug_unique ON artigos(slug);

-- Índice composto se slug for por categoria
CREATE UNIQUE INDEX idx_categoria_slug ON artigos(categoria_id, slug);
```

### 4. **Histórico de Slugs**
```sql
-- Tabela para manter histórico de slugs antigos
CREATE TABLE slug_redirects (
    id INT PRIMARY KEY AUTO_INCREMENT,
    slug_antigo VARCHAR(255) NOT NULL,
    artigo_id INT NOT NULL,
    data_criacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (artigo_id) REFERENCES artigos(id),
    UNIQUE KEY (slug_antigo)
);
```

## Casos de Uso Comuns

### 1. **Blog/CMS**
```sql
CREATE TABLE posts (
    id INT PRIMARY KEY AUTO_INCREMENT,
    titulo VARCHAR(255),
    slug VARCHAR(255) UNIQUE,
    conteudo TEXT,
    autor_id INT,
    data_publicacao TIMESTAMP
);
```

### 2. **E-commerce (Produtos)**
```sql
CREATE TABLE produtos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(255),
    slug VARCHAR(255) UNIQUE,
    descricao TEXT,
    preco DECIMAL(10,2),
    categoria_id INT
);
```

### 3. **Categorias Hierárquicas**
```sql
CREATE TABLE categorias (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(255),
    slug VARCHAR(255),
    parent_id INT,
    slug_completo VARCHAR(500), -- Ex: tecnologia/programacao/sql
    
    UNIQUE KEY (parent_id, slug)
);
```

## Considerações de Performance

### 1. **Índices Apropriados**
```sql
-- Para busca por slug
CREATE INDEX idx_slug ON tabela(slug);

-- Para busca por slug + status
CREATE INDEX idx_slug_status ON tabela(slug, status);
```

### 2. **Cache de Slugs**
```python
# Exemplo com Redis
import redis

r = redis.Redis()

def buscar_por_slug(slug):
    # Tentar cache primeiro
    cached = r.get(f"slug:{slug}")
    if cached:
        return json.loads(cached)
    
    # Buscar no banco
    result = db.query("SELECT * FROM artigos WHERE slug = %s", [slug])
    
    # Cachear resultado
    r.setex(f"slug:{slug}", 3600, json.dumps(result))
    
    return result
```

## Problemas Comuns e Soluções

### 1. **Slugs Duplicados**
```python
def resolver_slug_duplicado(slug_base, tabela):
    contador = 1
    slug_final = slug_base
    
    while slug_existe(slug_final, tabela):
        slug_final = f"{slug_base}-{contador}"
        contador += 1
    
    return slug_final
```

### 2. **Mudança de Slug**
```python
def atualizar_slug(artigo_id, novo_slug):
    # Salvar slug antigo para redirect
    slug_antigo = get_slug_atual(artigo_id)
    
    # Atualizar slug
    update_slug(artigo_id, novo_slug)
    
    # Criar redirect
    criar_redirect(slug_antigo, artigo_id)
```

### 3. **Caracteres Especiais**
```python
# Mapeamento de caracteres especiais
CARACTERES_ESPECIAIS = {
    'ç': 'c', 'ñ': 'n', 'ß': 'ss',
    '&': 'and', '@': 'at', '%': 'percent'
}

def limpar_caracteres_especiais(texto):
    for especial, substituto in CARACTERES_ESPECIAIS.items():
        texto = texto.replace(especial, substituto)
    return texto
```

## Conclusão

Slugs são uma ferramenta essencial para criar URLs amigáveis e melhorar a experiência do usuário. Quando implementados corretamente em bancos de dados, eles oferecem:

- **URLs mais legíveis**: Melhor para usuários e SEO
- **Estabilidade**: Links que não quebram facilmente
- **Performance**: Com índices adequados, buscas rápidas
- **Flexibilidade**: Fácil de implementar e manter

**💡 Dica:** Sempre considere a unicidade, performance e manutenibilidade ao implementar slugs em seus projetos.

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00758F&height=120&section=footer"/>