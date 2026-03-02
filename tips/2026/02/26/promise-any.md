# ⚡ Tip del Día: Promise.any() - La primera que gane

`Promise.any()` resuelve con el **primer resultado exitoso** y rechaza solo si **todas** las promesas fallan. Ideal para réplicas, fallbacks o "el que responda primero".

## Sintaxis

```javascript
const first = await Promise.any([promesaA(), promesaB(), promesaC()]);
// first = valor de la primera promesa que resuelva
```

## Ejemplo: varios endpoints de salud

```javascript
const endpoints = [
  'https://api-a.example.com/health',
  'https://api-b.example.com/health',
  'https://api-c.example.com/health'
];

const checkAny = () =>
  Promise.any(
    endpoints.map(url => fetch(url).then(r => r.json()))
  );

const result = await checkAny();
console.log('Al menos un servicio está bien:', result);
```

Si las tres fallan, obtienes un `AggregateError` con todas las razones:

```javascript
try {
  await Promise.any([...]);
} catch (e) {
  if (e instanceof AggregateError) {
    console.log('Todos fallaron:', e.errors);
  }
}
```

## vs Promise.race()

- **Promise.race**: resuelve o rechaza con la **primera** que termine (gane o falle).
- **Promise.any**: resuelve con la **primera que cumpla**; solo rechaza si **todas** rechazan.

## Resumen

| Método           | Resuelve cuando      | Rechaza cuando   |
|------------------|----------------------|------------------|
| Promise.all      | Todas cumplen        | Una rechaza      |
| Promise.any      | Una cumple           | Todas rechazan   |
| Promise.race     | Una termina (cumple) | Una termina (rechaza) |

Úsalo para "el que responda primero" sin fallar si algún servicio está caído. 🏁

#javascript #async #promises #frontend #backend
