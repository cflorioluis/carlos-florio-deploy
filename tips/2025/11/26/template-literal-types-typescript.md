# 💡 Tip del Día: Template Literal Types en TypeScript

## ¿Sabías que TypeScript puede manipular strings a nivel de tipos?

TypeScript tiene una característica increíble llamada **Template Literal Types** que te permite manipular y transformar strings directamente en el sistema de tipos. ¡Es como tener un mini lenguaje de programación dentro de los tipos!

---

## 🎯 ¿Qué son los Template Literal Types?

Los Template Literal Types son tipos que se construyen usando la sintaxis de template literals (backticks) pero a nivel de tipos. Permiten concatenar, extraer, y transformar strings en tiempo de compilación.

---

## 📝 Ejemplos Básicos

### Concatenación de tipos:

```typescript
type World = "world";
type Greeting = `hello ${World}`; // "hello world"

type Name = "Carlos";
type Welcome = `Bienvenido ${Name}`; // "Bienvenido Carlos"
```

### Combinación con Union Types:

```typescript
type Color = "red" | "blue" | "green";
type Size = "small" | "large";

type ButtonClass = `${Color}-${Size}-button`;
// Resultado: "red-small-button" | "red-large-button" | 
//            "blue-small-button" | "blue-large-button" | 
//            "green-small-button" | "green-large-button"
```

---

## 🚀 Casos de Uso Reales

### 1. Generar rutas de API tipadas:

```typescript
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";
type ApiEndpoint = "/users" | "/posts" | "/comments";

type ApiRoute = `${HttpMethod} ${ApiEndpoint}`;
// "GET /users" | "GET /posts" | "POST /users" | etc.

function fetchApi(route: ApiRoute) {
  // TypeScript te asegura que uses rutas válidas
}

fetchApi("GET /users"); // ✅ Válido
fetchApi("POST /invalid"); // ❌ Error de tipo
```

### 2. Extraer partes de strings:

```typescript
type ExtractMethod<T> = T extends `${infer Method} ${string}` 
  ? Method 
  : never;

type Route = "GET /users/123";
type Method = ExtractMethod<Route>; // "GET"
```

### 3. Capitalizar strings:

```typescript
type Capitalize<S extends string> = 
  S extends `${infer First}${infer Rest}` 
    ? `${Uppercase<First>}${Rest}` 
    : S;

type Name = Capitalize<"hello">; // "Hello"
type Title = Capitalize<"typescript">; // "Typescript"
```

### 4. Crear IDs únicos tipados:

```typescript
type EntityType = "user" | "post" | "comment";
type ID = string;

type EntityID<T extends EntityType> = `${T}_${ID}`;

type UserID = EntityID<"user">; // "user_..."
type PostID = EntityID<"post">; // "post_..."

function getUser(id: UserID) {
  // Solo acepta IDs de usuarios
}
```

---

## 🎨 Transformaciones Avanzadas

### Convertir a camelCase:

```typescript
type ToCamelCase<S extends string> = 
  S extends `${infer First}_${infer Rest}`
    ? `${First}${Capitalize<ToCamelCase<Rest>>}`
    : S;

type SnakeCase = "user_name";
type CamelCase = ToCamelCase<SnakeCase>; // "userName"
```

### Extraer parámetros de rutas:

```typescript
type ExtractParams<T> = 
  T extends `${string}:${infer Param}/${infer Rest}`
    ? Param | ExtractParams<`/${Rest}`>
    : T extends `${string}:${infer Param}`
    ? Param
    : never;

type Route = "/users/:userId/posts/:postId";
type Params = ExtractParams<Route>; // "userId" | "postId"
```

---

## 🔥 Ejemplo Completo: Router Tipado

```typescript
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";
type Route = "/users" | "/posts" | "/users/:id" | "/posts/:id";

type ApiCall = `${HttpMethod} ${Route}`;

type ExtractId<T> = T extends `${string}:id` ? string : never;

function router<M extends HttpMethod, R extends Route>(
  method: M,
  route: R
): ExtractId<R> extends never 
  ? () => void 
  : (id: ExtractId<R>) => void {
  // Implementación del router
  return (() => {}) as any;
}

// Uso:
const getUser = router("GET", "/users/:id");
getUser("123"); // ✅ Válido

const getUsers = router("GET", "/users");
getUsers(); // ✅ Válido
```

---

## ⚠️ Limitaciones

1. **Recursión limitada**: TypeScript tiene límites en la profundidad de recursión de tipos
2. **Performance**: Tipos muy complejos pueden ralentizar el compilador
3. **Legibilidad**: Pueden volverse difíciles de leer si son muy complejos

---

## 🎯 Puntos Clave

1. **Manipulación de strings a nivel de tipos**: Transforma strings en tiempo de compilación
2. **Type-safe APIs**: Crea APIs completamente tipadas sin runtime overhead
3. **Pattern matching**: Usa `infer` para extraer partes de strings
4. **Combinación poderosa**: Funciona genial con Union Types y Conditional Types

---

## 🔗 Recursos

- [TypeScript Handbook - Template Literal Types](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html)
- [Type Challenges](https://github.com/type-challenges/type-challenges) - Practica con ejercicios de tipos avanzados

---

**¿Te gustó este tip?** ¡Experimenta con Template Literal Types y crea APIs completamente tipadas! 🚀

