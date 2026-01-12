# Object.groupBy() - Nueva API de JavaScript 🎯

¡JavaScript tiene un nuevo método nativo para agrupar arrays! `Object.groupBy()` llegó en ES2024 y hace que agrupar elementos sea súper fácil, sin necesidad de librerías externas como Lodash.

## El Problema que Resuelve

Antes, para agrupar un array de objetos por una propiedad, tenías que hacer algo así:

```javascript
const products = [
  { name: 'Laptop', category: 'Electronics', price: 999 },
  { name: 'Shirt', category: 'Clothing', price: 29 },
  { name: 'Phone', category: 'Electronics', price: 699 },
  { name: 'Jeans', category: 'Clothing', price: 59 }
];

// Forma antigua (con reduce)
const grouped = products.reduce((acc, product) => {
  const category = product.category;
  if (!acc[category]) {
    acc[category] = [];
  }
  acc[category].push(product);
  return acc;
}, {});
```

## La Nueva Forma (Mucho Más Simple)

```javascript
const grouped = Object.groupBy(products, product => product.category);

console.log(grouped);
// {
//   Electronics: [
//     { name: 'Laptop', category: 'Electronics', price: 999 },
//     { name: 'Phone', category: 'Electronics', price: 699 }
//   ],
//   Clothing: [
//     { name: 'Shirt', category: 'Clothing', price: 29 },
//     { name: 'Jeans', category: 'Clothing', price: 59 }
//   ]
// }
```

## Sintaxis

```javascript
Object.groupBy(array, callbackFn)
```

- **array**: El array que quieres agrupar
- **callbackFn**: Función que retorna la clave de agrupación para cada elemento

## Ejemplos Prácticos

### 1. Agrupar por Rango de Precios
```javascript
const products = [
  { name: 'Laptop', price: 999 },
  { name: 'Mouse', price: 25 },
  { name: 'Keyboard', price: 75 },
  { name: 'Monitor', price: 299 }
];

const byPriceRange = Object.groupBy(products, product => {
  if (product.price < 50) return 'Económico';
  if (product.price < 300) return 'Medio';
  return 'Premium';
});

console.log(byPriceRange);
// {
//   Económico: [{ name: 'Mouse', price: 25 }],
//   Medio: [
//     { name: 'Keyboard', price: 75 },
//     { name: 'Monitor', price: 299 }
//   ],
//   Premium: [{ name: 'Laptop', price: 999 }]
// }
```

### 2. Agrupar por Fecha
```javascript
const events = [
  { name: 'Meeting', date: '2026-01-12' },
  { name: 'Lunch', date: '2026-01-12' },
  { name: 'Workshop', date: '2026-01-13' },
  { name: 'Review', date: '2026-01-13' }
];

const byDate = Object.groupBy(events, event => event.date);

console.log(byDate);
// {
//   '2026-01-12': [
//     { name: 'Meeting', date: '2026-01-12' },
//     { name: 'Lunch', date: '2026-01-12' }
//   ],
//   '2026-01-13': [
//     { name: 'Workshop', date: '2026-01-13' },
//     { name: 'Review', date: '2026-01-13' }
//   ]
// }
```

### 3. Agrupar por Primera Letra
```javascript
const names = ['Ana', 'Carlos', 'Alberto', 'Beatriz', 'Carmen'];

const byFirstLetter = Object.groupBy(names, name => name[0]);

console.log(byFirstLetter);
// {
//   A: ['Ana', 'Alberto'],
//   C: ['Carlos', 'Carmen'],
//   B: ['Beatriz']
// }
```

### 4. Agrupar por Múltiples Condiciones
```javascript
const users = [
  { name: 'Ana', age: 25, active: true },
  { name: 'Carlos', age: 17, active: true },
  { name: 'María', age: 30, active: false },
  { name: 'Juan', age: 16, active: true }
];

const byAgeAndStatus = Object.groupBy(users, user => {
  const ageGroup = user.age >= 18 ? 'adult' : 'minor';
  const status = user.active ? 'active' : 'inactive';
  return `${ageGroup}-${status}`;
});

console.log(byAgeAndStatus);
// {
//   'adult-active': [{ name: 'Ana', age: 25, active: true }],
//   'minor-active': [
//     { name: 'Carlos', age: 17, active: true },
//     { name: 'Juan', age: 16, active: true }
//   ],
//   'adult-inactive': [{ name: 'María', age: 30, active: false }]
// }
```

## Map.groupBy() - La Variante con Map

También existe `Map.groupBy()` que retorna un `Map` en lugar de un objeto. Útil cuando las claves no son strings:

```javascript
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

const byEvenOdd = Map.groupBy(numbers, num => num % 2 === 0);

console.log(byEvenOdd);
// Map(2) {
//   false => [1, 3, 5, 7, 9],
//   true => [2, 4, 6, 8, 10]
// }

// Acceder a los valores
console.log(byEvenOdd.get(true));  // [2, 4, 6, 8, 10]
console.log(byEvenOdd.get(false)); // [1, 3, 5, 7, 9]
```

## Compatibilidad de Navegadores

`Object.groupBy()` es muy nuevo (ES2024). Verifica la compatibilidad:

```javascript
// Polyfill simple si no está disponible
if (!Object.groupBy) {
  Object.groupBy = function(array, keyFn) {
    return array.reduce((result, item) => {
      const key = keyFn(item);
      if (!result[key]) {
        result[key] = [];
      }
      result[key].push(item);
      return result;
    }, {});
  };
}
```

**Soporte actual:**
- ✅ Chrome 117+
- ✅ Firefox 119+
- ✅ Safari 17.4+
- ✅ Node.js 21+

## Comparación con Lodash

```javascript
// Lodash
import { groupBy } from 'lodash';
const grouped = groupBy(products, 'category');

// Nativo
const grouped = Object.groupBy(products, p => p.category);
```

¡Mismo resultado, sin dependencias! 🎉

## Tip Pro 💡

Combina `Object.groupBy()` con `Object.entries()` para transformar los grupos:

```javascript
const products = [
  { name: 'Laptop', category: 'Electronics', price: 999 },
  { name: 'Phone', category: 'Electronics', price: 699 },
  { name: 'Shirt', category: 'Clothing', price: 29 }
];

const grouped = Object.groupBy(products, p => p.category);

// Calcula el total por categoría
const totals = Object.entries(grouped).map(([category, items]) => ({
  category,
  total: items.reduce((sum, item) => sum + item.price, 0),
  count: items.length
}));

console.log(totals);
// [
//   { category: 'Electronics', total: 1698, count: 2 },
//   { category: 'Clothing', total: 29, count: 1 }
// ]
```

---

**Resumen rápido:**
- `Object.groupBy(array, fn)` agrupa arrays por una clave
- Mucho más simple que usar `reduce()`
- `Map.groupBy()` para claves no-string
- Disponible en navegadores modernos (2024+)
- Adiós Lodash para este caso de uso 👋
