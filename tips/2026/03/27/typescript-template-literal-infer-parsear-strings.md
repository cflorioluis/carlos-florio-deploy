# TypeScript Template Literal Types y `infer` para Parsear Strings 🧬

Las Template Literal Types de TypeScript permiten crear tipos derivados de strings literales, mientras que `infer` en tipos condicionales extrae partes de esos strings. Juntas, son herramientas poderosas para tipos derivados, validación y parsing.

---

## Template Literal Types: Concepto básico 📝

```typescript
// Template literal type
type EventName = `on${Capitalize<string>}`;

// Strings literales que encajan con el patrón
type EventHandlers = {
  onClick: (event: MouseEvent) => void;
  onMouseEnter: (event: MouseEvent) => void;
  onMouseLeave: (event: MouseEvent) => void;
};
```

**Tip:** Usa backticks (\`) para crear tipos template literal.

---

## Tipos condicionales con `infer` 🔍

`infer` extrae tipos dentro de tipos condicionales:

```typescript
// Extraer el tipo de retorno de una función
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type Func = (a: number, b: string) => boolean;

// Resultado: boolean
type Result = ReturnType<Func>;
```

---

## Ejemplo 1: Parsear IDs con patrón `prefix-number-action` 🆔

```typescript
type UserId = `user-${number}`;
type PostId = `post-${number}`;

// Extraer el número del ID
type ExtractId<T extends string> = T extends `${string}-${infer Id}` ? Id : never;

type User1 = ExtractId<'user-123'>;   // '123'
type User2 = ExtractId<'post-456'>;  // '456'
type User3 = ExtractId<'invalid'>;   // never
```

---

## Ejemplo 2: Tipos derivados de rutas de API 🛣️

```typescript
// Patrón de ruta de API
type ApiRoute = `/api/${string}/${string}`;

// Extraer recurso e ID
type ExtractRoute<T extends ApiRoute> = T extends `/api/${infer Resource}/${infer Id}`
  ? { resource: Resource; id: Id }
  : never;

type UserRoute = ExtractRoute<'/api/users/123'>;
// Resultado: { resource: 'users'; id: '123' }

type PostRoute = ExtractRoute<'/api/posts/456'>;
// Resultado: { resource: 'posts'; id: '456' }
```

**Implementación práctica:**

```typescript
function parseApiRoute<T extends ApiRoute>(route: T): ExtractRoute<T> {
  const match = route.match(/^\/api\/([^/]+)\/([^/]+)$/);
  if (!match) throw new Error('Invalid route');

  return {
    resource: match[1],
    id: match[2]
  } as ExtractRoute<T>;
}

// Type-safe routing
const userRoute = parseApiRoute('/api/users/123');
console.log(userRoute.resource); // 'users'
console.log(userRoute.id);       // '123'
```

---

## Ejemplo 3: Sistema de eventos tipados con Template Literals 📡

```typescript
// Tipos de eventos
type EventType = 'click' | 'hover' | 'submit';

// Prefijo de eventos
type EventPrefix = 'on';

// Tipo de evento completo
type FullEventType = `${EventPrefix}${Capitalize<EventType>}`;
// Resultado: 'onClick' | 'onHover' | 'onSubmit'

// Sistema de eventos tipados
type EventHandler<T extends FullEventType> =
  T extends 'onClick' ? (event: MouseEvent) => void
  : T extends 'onHover' ? (event: MouseEvent) => void
  : T extends 'onSubmit' ? (event: SubmitEvent) => void
  : never;

// Objeto con handlers tipados
type EventHandlers = {
  [K in FullEventType]: EventHandler<K>;
};

// Implementación
const handlers: EventHandlers = {
  onClick: (event: MouseEvent) => console.log('Clicked!', event),
  onHover: (event: MouseEvent) => console.log('Hovered!', event),
  onSubmit: (event: SubmitEvent) => console.log('Submitted!', event)
};

// ✅ Type-safe
handlers.onClick(new MouseEvent('click'));

// ❌ Error: Tipo incorrecto
// handlers.onClick(new SubmitEvent('submit'));
```

---

## Ejemplo 4: Parsear versiones semánticas 🏷️

```typescript
// Patrón de versión semántica
type SemanticVersion = `${number}.${number}.${number}`;

// Extraer major, minor, patch
type ParseVersion<T extends SemanticVersion> = T extends `${infer Major}.${infer Minor}.${infer Patch}`
  ? { major: Major; minor: Minor; patch: Patch }
  : never;

type V1 = ParseVersion<'1.2.3'>;  // { major: '1'; minor: '2'; patch: '3' }
type V2 = ParseVersion<'2.0.0'>;  // { major: '2'; minor: '0'; patch: '0' }

// Implementación runtime
function parseVersion<T extends SemanticVersion>(version: T): ParseVersion<T> {
  const [major, minor, patch] = version.split('.');
  return {
    major,
    minor,
    patch
  } as ParseVersion<T>;
}

const version = parseVersion('1.2.3');
console.log(version.major); // '1'
console.log(version.minor); // '2'
console.log(version.patch); // '3'
```

---

## Ejemplo 5: Sistema de comandos CLI 🎯

```typescript
// Patrones de comandos
type Command<T extends string> = T extends `git ${infer Action}`
  ? Action
  : never;

// Tipos de acciones git
type GitAction =
  | 'clone'
  | 'commit'
  | 'push'
  | 'pull'
  | 'status'
  | 'branch'
  | 'checkout';

// Comando completo
type GitCommand = `git ${GitAction}`;
// Resultado: 'git clone' | 'git commit' | 'git push' | ...

// Handler de comandos
type CommandHandler<T extends GitCommand> =
  T extends `git ${infer Action}`
    ? Action extends 'clone'
      ? (url: string) => void
      : Action extends 'commit'
      ? (message: string) => void
      : Action extends 'push'
      ? (branch?: string) => void
      : () => void
    : never;

// Sistema de comandos
type CommandSystem = {
  [K in GitCommand]: CommandHandler<K>;
};

// Implementación
const gitCommands: CommandSystem = {
  'git clone': (url: string) => console.log(`Cloning ${url}`),
  'git commit': (message: string) => console.log(`Commit: ${message}`),
  'git push': (branch?: string) => console.log(`Pushing ${branch || 'current'}`),
  'git pull': () => console.log('Pulling'),
  'git status': () => console.log('Status'),
  'git branch': () => console.log('Branch'),
  'git checkout': () => console.log('Checkout')
};

// ✅ Type-safe commands
gitCommands['git clone']('https://github.com/user/repo.git');
gitCommands['git commit']('Initial commit');

// ❌ Error: Argumentos incorrectos
// gitCommands['git clone'](); // Espera un string
```

---

## Ejemplo 6: Template Literal Arrays 📦

Dividir strings en arrays de tipos:

```typescript
// Dividir un string por un delimitador
type Split<S extends string, Delimiter extends string> =
  S extends `${infer First}${Delimiter}${infer Rest}`
    ? [First, ...Split<Rest, Delimiter>]
    : [S];

type SplitPath = Split<'/users/123/posts/456', '/'>;
// Resultado: ['', 'users', '123', 'posts', '456']

// Implementación runtime
function split<S extends string, D extends string>(str: S, delimiter: D): Split<S, D> {
  return str.split(delimiter) as Split<S, D>;
}

const pathParts = split('/users/123/posts/456', '/');
console.log(pathParts); // ['', 'users', '123', 'posts', '456']
```

---

## Combinación avanzada: Routes + Handlers 🚀

```typescript
// Rutas de API
type ApiRoutes =
  | `/api/users/${number}`
  | `/api/users/${number}/posts`
  | `/api/posts/${number}`;

// Handler de rutas
type RouteHandler<T extends ApiRoutes> =
  T extends `/api/users/${number}`
    ? (id: number) => Promise<User>
    : T extends `/api/users/${number}/posts`
    ? (userId: number) => Promise<Post[]>
    : T extends `/api/posts/${number}`
    ? (id: number) => Promise<Post>
    : never;

// Sistema de rutas
type ApiSystem = {
  [K in ApiRoutes]: RouteHandler<K>;
};

// Implementación
const api: ApiSystem = {
  '/api/users/123': async (id: number) => ({ id, name: 'John' }),
  '/api/users/123/posts': async (userId: number) => [
    { id: 1, userId, title: 'Post 1' }
  ],
  '/api/posts/456': async (id: number) => ({ id, title: 'Post' })
};

// ✅ Type-safe API calls
const user = await api['/api/users/123'](123);
const posts = await api['/api/users/123/posts'](123);
const post = await api['/api/posts/456'](456);
```

---

## Resumen de patrones

| Patrón | Descripción | Ejemplo |
|--------|-------------|---------|
| `` `prefix-${infer Rest}` `` | Extraer después del prefijo | `ExtractId<'user-123'>` → `'123'` |
| `` `${infer A}.${infer B}` `` | Dividir en partes | `ParseVersion<'1.2.3'>` → `{major, minor, patch}` |
| `` `git ${infer Action}` `` | Extraer acción de comando | `Command<'git clone'>` → `'clone'` |
| `` `${A}${D}${Rest}` `` | Dividir string en array | `Split<'a,b,c', ','>` → `['a','b','c']` |
| `Capitalize<T>` | Capitalizar string | `Capitalize<'click'>` → `'Click'` |

---

## Conclusión

Las Template Literal Types combinadas con `infer` te permiten:
- ✅ Parsear strings y derivar tipos
- ✅ Crear sistemas de eventos tipados
- ✅ Implementar rutas de API type-safe
- ✅ Validar formatos en tiempo de compilación

Úsalas para transformar strings en tipos poderosos. 🧬

#typescript #types #template-literals #infer #type-safe #frontend
