# 🐘 PostgreSQL: JSONB (Lo mejor de dos mundos)

¿SQL o NoSQL? ¿Por qué elegir? PostgreSQL con columnas `JSONB` te da la estructura relacional sólida de SQL con la flexibilidad de documentos de NoSQL.

---

## 📦 ¿Cuándo usar JSONB?

No uses JSONB para todo (para eso usa Mongo). Úsalo cuando:
-   Tienes datos con estructura variable (ej: configuración de plugins, metadatos de usuario).
-   No quieres hacer 5 `JOINs` para traer datos simples que siempre se consultan juntos.
-   Estás prototipando rápido y el esquema aún no está fijo.

---

## 🛠️ Ejemplo Práctico

Imagina una tabla de `products` donde cada categoría tiene atributos muy diferentes (ropa tiene talla/color, electrónica tiene voltaje/peso).

```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    attributes JSONB -- Aquí guardamos la magia
);

-- Insertamos datos variados
INSERT INTO products (name, attributes) VALUES 
('Camiseta', '{"size": "M", "color": "blue", "material": "cotton"}'),
('Laptop', '{"ram": "16GB", "brand": "Apple", "ports": ["usb-c", "hdmi"]}');
```

---

## 🔍 Consultas Poderosas

Lo genial de Postgres es que puedes indexar y consultar DENTRO del JSON.

**1. Buscar por una propiedad del JSON:**
```sql
-- Encuentra productos de color azul
SELECT * FROM products WHERE attributes->>'color' = 'blue';
```

**2. Buscar si el JSON contiene un sub-documento (Containment):**
```sql
-- Encuentra productos de marca Apple (muy rápido con índices GIN)
SELECT * FROM products WHERE attributes @> '{"brand": "Apple"}';
```

**3. Indexar para velocidad:**
Si haces muchas consultas sobre el JSON, crea un índice GIN:
```sql
CREATE INDEX idx_products_attributes ON products USING GIN (attributes);
```

---

## 🆚 JSON vs JSONB

En Postgres existen ambos tipos:
-   **JSON**: Guarda el texto exacto (incluyendo espacios). Es más rápido de insertar pero lento de consultar.
-   **JSONB** (Binary): Se guarda en formato binario descompuesto. Es un poco más lento de insertar, pero **muchísimo más rápido de consultar** y soporta índices. **Usa siempre JSONB** a menos que tengas una razón muy específica.

¡Dale flexibilidad a tu esquema relacional!
