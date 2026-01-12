# Console.table() para Debugging 🐛

¿Sigues usando `console.log()` para ver arrays y objetos? Hay una forma mucho mejor: `console.table()`. Esta función muestra tus datos en una tabla bonita y fácil de leer directamente en la consola del navegador.

## La Diferencia

**Antes (con console.log):**
```javascript
const users = [
  { id: 1, name: 'Ana', role: 'Admin', active: true },
  { id: 2, name: 'Carlos', role: 'User', active: false },
  { id: 3, name: 'María', role: 'Editor', active: true }
];

console.log(users);
// Salida: Array(3) [ {…}, {…}, {…} ]
// Tienes que expandir cada objeto para ver los datos
```

**Ahora (con console.table):**
```javascript
console.table(users);
```

Esto muestra una tabla perfectamente formateada:
```
┌─────────┬────┬──────────┬──────────┬────────┐
│ (index) │ id │   name   │   role   │ active │
├─────────┼────┼──────────┼──────────┼────────┤
│    0    │ 1  │  'Ana'   │ 'Admin'  │  true  │
│    1    │ 2  │ 'Carlos' │  'User'  │ false  │
│    2    │ 3  │  'María' │ 'Editor' │  true  │
└─────────┴────┴──────────┴──────────┴────────┘
```

## Casos de Uso Prácticos

### 1. Comparar Objetos
```javascript
const before = { name: 'Juan', age: 25, city: 'Madrid' };
const after = { name: 'Juan', age: 26, city: 'Barcelona' };

console.table([before, after]);
// Perfecto para ver diferencias entre estados
```

### 2. Mostrar Solo Columnas Específicas
```javascript
const products = [
  { id: 1, name: 'Laptop', price: 999, stock: 5, category: 'Electronics' },
  { id: 2, name: 'Mouse', price: 25, stock: 50, category: 'Electronics' },
  { id: 3, name: 'Desk', price: 299, stock: 3, category: 'Furniture' }
];

// Solo muestra las columnas que te interesan
console.table(products, ['name', 'price', 'stock']);
```

### 3. Debugging de Objetos Anidados
```javascript
const apiResponse = {
  user: { id: 1, name: 'Ana' },
  settings: { theme: 'dark', lang: 'es' },
  permissions: { read: true, write: false }
};

console.table(apiResponse);
// Muestra cada propiedad de primer nivel en una fila
```

### 4. Analizar Datos de Performance
```javascript
const metrics = [
  { route: '/home', loadTime: 1.2, requests: 15 },
  { route: '/about', loadTime: 0.8, requests: 8 },
  { route: '/products', loadTime: 2.5, requests: 32 }
];

console.table(metrics);
// Perfecto para comparar métricas rápidamente
```

## Otros Console Methods Útiles 🎯

Ya que estamos hablando de debugging, aquí hay otros métodos de console que quizás no conoces:

```javascript
// Agrupa logs relacionados
console.group('User Actions');
console.log('Login successful');
console.log('Profile loaded');
console.groupEnd();

// Muestra un warning
console.warn('API key is missing!');

// Muestra un error
console.error('Failed to fetch data');

// Mide tiempo de ejecución
console.time('API Call');
await fetch('/api/data');
console.timeEnd('API Call');
// Salida: API Call: 234.56ms

// Cuenta cuántas veces se ejecuta algo
for (let i = 0; i < 5; i++) {
  console.count('Loop iteration');
}
// Salida: Loop iteration: 1, 2, 3, 4, 5
```

## Tip Pro 💡

Combina `console.table()` con `Array.map()` para transformar datos antes de visualizarlos:

```javascript
const users = [
  { firstName: 'Ana', lastName: 'García', age: 28 },
  { firstName: 'Carlos', lastName: 'López', age: 35 }
];

console.table(
  users.map(u => ({
    'Nombre Completo': `${u.firstName} ${u.lastName}`,
    'Edad': u.age,
    'Mayor de edad': u.age >= 18 ? '✅' : '❌'
  }))
);
```

---

**Resumen rápido:**
- `console.table()` muestra datos en formato tabla
- Funciona con arrays y objetos
- Puedes especificar qué columnas mostrar
- Mucho más legible que `console.log()` para datos estructurados 📊
