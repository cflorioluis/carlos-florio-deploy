# 💡 Tip del Día: Partial Types en TypeScript

## ¿Qué es `Partial<T>`?

`Partial<T>` es un **tipo utilitario** de TypeScript que convierte todas las propiedades de un tipo `T` en **opcionales**. Es extremadamente útil cuando necesitas crear objetos que pueden tener solo algunas de las propiedades del tipo original.

---

## 🎯 ¿Por qué usar `Partial`?

### Problema común sin `Partial`:

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

// ❌ Error: falta la propiedad 'age'
function updateUser(user: User, updates: User) {
  return { ...user, ...updates };
}

updateUser(
  { id: 1, name: 'Juan', email: 'juan@example.com', age: 25 },
  { name: 'Juan Pérez' } // Error: falta id, email, age
);
```

### Solución con `Partial`:

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

// ✅ Funciona perfectamente
function updateUser(user: User, updates: Partial<User>) {
  return { ...user, ...updates };
}

updateUser(
  { id: 1, name: 'Juan', email: 'juan@example.com', age: 25 },
  { name: 'Juan Pérez' } // ✅ Solo actualiza el nombre
);
```

---

## 📚 Cómo funciona `Partial`

`Partial<T>` es equivalente a:

```typescript
type Partial<T> = {
  [P in keyof T]?: T[P];
};
```

Esto significa que toma cada propiedad `P` del tipo `T` y la hace opcional agregando el operador `?`.

### Ejemplo práctico:

```typescript
interface Config {
  host: string;
  port: number;
  timeout: number;
  retries: number;
}

// Partial<Config> es equivalente a:
type PartialConfig = {
  host?: string;
  port?: number;
  timeout?: number;
  retries?: number;
};
```

---

## 🔧 Casos de uso comunes

### 1. Funciones de actualización (Update functions)

```typescript
interface Product {
  id: number;
  name: string;
  price: number;
  description: string;
  stock: number;
}

function updateProduct(id: number, updates: Partial<Product>): void {
  // Solo actualiza las propiedades proporcionadas
  const product = getProductById(id);
  Object.assign(product, updates);
}

// Uso:
updateProduct(1, { price: 29.99 }); // ✅ Solo actualiza el precio
updateProduct(2, { name: 'Nuevo nombre', stock: 100 }); // ✅ Actualiza múltiples campos
```

### 2. Configuraciones opcionales

```typescript
interface DatabaseConfig {
  host: string;
  port: number;
  username: string;
  password: string;
  ssl: boolean;
  timeout: number;
}

function createConnection(config: DatabaseConfig): Connection {
  // ...
}

// Con valores por defecto
function createConnectionWithDefaults(
  overrides: Partial<DatabaseConfig>
): Connection {
  const defaults: DatabaseConfig = {
    host: 'localhost',
    port: 5432,
    username: 'admin',
    password: '',
    ssl: false,
    timeout: 5000
  };
  
  return createConnection({ ...defaults, ...overrides });
}

// Uso:
createConnectionWithDefaults({ host: 'prod.db.com', ssl: true });
```

### 3. Formularios y validación

```typescript
interface FormData {
  firstName: string;
  lastName: string;
  email: string;
  phone: string;
}

function validateForm(data: Partial<FormData>): boolean {
  // Valida solo los campos que están presentes
  if (data.email && !isValidEmail(data.email)) {
    return false;
  }
  if (data.phone && !isValidPhone(data.phone)) {
    return false;
  }
  return true;
}
```

### 4. Construcción de objetos paso a paso

```typescript
interface QueryBuilder {
  select: string[];
  where: Record<string, any>;
  orderBy: string;
  limit: number;
}

class Query {
  private config: Partial<QueryBuilder> = {};
  
  select(fields: string[]): this {
    this.config.select = fields;
    return this;
  }
  
  where(conditions: Record<string, any>): this {
    this.config.where = conditions;
    return this;
  }
  
  build(): QueryBuilder {
    // Validar que los campos requeridos estén presentes
    if (!this.config.select || !this.config.where) {
      throw new Error('Missing required fields');
    }
    return this.config as QueryBuilder;
  }
}
```

---

## ⚠️ Limitaciones y consideraciones

### 1. `Partial` hace TODO opcional

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

function createUser(data: Partial<User>): User {
  // ⚠️ Problema: data podría no tener 'id' o 'email'
  return {
    id: data.id ?? 0, // Necesitas valores por defecto
    name: data.name ?? '',
    email: data.email ?? ''
  };
}
```

### 2. Alternativa: `Required<Partial<T>>` no funciona como esperas

Si quieres hacer solo algunas propiedades opcionales, necesitas crear un tipo personalizado:

```typescript
// Hacer solo 'email' opcional
type UserWithOptionalEmail = Omit<User, 'email'> & { email?: string };

// O hacer solo 'id' requerido
type UserWithRequiredId = Partial<User> & { id: number };
```

### 3. Validación en tiempo de ejecución

`Partial` solo ayuda con tipos en tiempo de compilación. Aún necesitas validar en tiempo de ejecución:

```typescript
function updateUser(id: number, updates: Partial<User>): void {
  // Validar que los datos sean válidos
  if (updates.email && !isValidEmail(updates.email)) {
    throw new Error('Invalid email');
  }
  // ...
}
```

---

## 🔄 Tipos relacionados

### `Required<T>`
Hace todas las propiedades requeridas (opuesto de `Partial`):

```typescript
interface Config {
  host?: string;
  port?: number;
}

type RequiredConfig = Required<Config>;
// { host: string; port: number; }
```

### `Pick<T, K>`
Selecciona propiedades específicas:

```typescript
type UserName = Pick<User, 'name'>;
// { name: string; }
```

### `Omit<T, K>`
Omite propiedades específicas:

```typescript
type UserWithoutId = Omit<User, 'id'>;
// { name: string; email: string; age: number; }
```

---

## 🌍 ¿Existe en Java?

**No**, `Partial` es específico de TypeScript. En Java no existe un equivalente directo, pero puedes lograr comportamientos similares con:

### 1. **Builder Pattern**
```java
public class User {
    private String name;
    private String email;
    
    public static class Builder {
        private String name;
        private String email;
        
        public Builder name(String name) {
            this.name = name;
            return this;
        }
        
        public Builder email(String email) {
            this.email = email;
            return this;
        }
        
        public User build() {
            return new User(this);
        }
    }
}

// Uso:
User user = new User.Builder()
    .name("Juan")
    .build(); // email es opcional
```

### 2. **Optional (para valores individuales)**
```java
Optional<String> email = Optional.of("juan@example.com");
```

### 3. **Maps o DTOs parciales**
```java
Map<String, Object> updates = new HashMap<>();
updates.put("name", "Juan Pérez");
// Solo actualiza los campos presentes en el map
```

---

## 🎯 Ejemplo completo: Sistema de actualización

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
  role: 'admin' | 'user' | 'guest';
}

class UserService {
  private users: User[] = [];
  
  // Crear usuario (todos los campos requeridos)
  createUser(user: User): User {
    this.users.push(user);
    return user;
  }
  
  // Actualizar usuario (solo campos proporcionados)
  updateUser(id: number, updates: Partial<Omit<User, 'id'>>): User | null {
    const user = this.users.find(u => u.id === id);
    if (!user) return null;
    
    // Validar actualizaciones
    if (updates.email && !isValidEmail(updates.email)) {
      throw new Error('Invalid email');
    }
    if (updates.role && !['admin', 'user', 'guest'].includes(updates.role)) {
      throw new Error('Invalid role');
    }
    
    // Aplicar actualizaciones
    Object.assign(user, updates);
    return user;
  }
  
  // Actualización parcial con validación
  patchUser(id: number, updates: Partial<User>): User | null {
    // No permitir actualizar el ID
    const { id: _, ...safeUpdates } = updates;
    return this.updateUser(id, safeUpdates);
  }
}

// Uso:
const service = new UserService();

service.createUser({
  id: 1,
  name: 'Juan',
  email: 'juan@example.com',
  age: 25,
  role: 'user'
});

// Actualizar solo el nombre
service.updateUser(1, { name: 'Juan Pérez' });

// Actualizar múltiples campos
service.updateUser(1, { 
  name: 'Juan Carlos Pérez',
  age: 26,
  role: 'admin'
});
```

---

## 📝 Resumen

- ✅ `Partial<T>` hace todas las propiedades de `T` opcionales
- ✅ Útil para funciones de actualización y configuraciones
- ✅ Solo funciona en tiempo de compilación (TypeScript)
- ⚠️ Aún necesitas validación en tiempo de ejecución
- ❌ No existe en Java (usa Builder Pattern o Maps)
- 🔄 Combina con `Omit`, `Pick`, `Required` para más flexibilidad

---

**¿Te gustó este tip?** ¡Empieza a usar `Partial` para hacer tus funciones de actualización más flexibles y type-safe! 🚀

