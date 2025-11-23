# 💡 Tip del Día: Separadores Numéricos en TypeScript

## ¿Sabías que puedes hacer tus números más legibles?

En TypeScript (y JavaScript desde ES2021), puedes usar el guion bajo (`_`) como separador visual en números para mejorar su legibilidad. ¡Es completamente ignorado por el motor de JavaScript!

## 📝 Ejemplos Prácticos

### Separadores de Miles
```typescript
// ❌ Difícil de leer
const precio = 1000000;
const distancia = 384400;

// ✅ Mucho más legible
const precio = 1_000_000;
const distancia = 384_400;
```

### Números Binarios
```typescript
// Separar grupos de 4 bits
const mascara = 0b1010_0001_1000_0101;
```

### Números Hexadecimales
```typescript
// Separar bytes
const color = 0xFF_EC_00;
const maxValue = 0x7FFF_FFFF;
```

### Números Octales
```typescript
const permiso = 0o755;
const otroPermiso = 0o7_5_5; // También funciona
```

## 🎯 Casos de Uso Reales

### Constantes de Configuración
```typescript
const MAX_FILE_SIZE = 10_000_000; // 10 MB en bytes
const TIMEOUT_MS = 30_000; // 30 segundos
const MAX_RETRIES = 3;
```

### Valores Financieros
```typescript
const SALARIO_ANUAL = 50_000;
const PRESUPUESTO = 1_500_000;
```

### IDs y Códigos
```typescript
const USER_ID = 1_234_567_890;
const TRANSACTION_ID = 987_654_321;
```

## ⚠️ Reglas Importantes

1. **No puedes usar `_` al inicio o al final del número:**
   ```typescript
   // ❌ Error
   const num1 = _1000;
   const num2 = 1000_;
   ```

2. **No puedes usar `_` consecutivos:**
   ```typescript
   // ❌ Error
   const num = 1__000;
   ```

3. **No puedes usar `_` después de un punto decimal:**
   ```typescript
   // ❌ Error
   const num = 1.5_0;
   ```

4. **Sí puedes usar `_` antes de un punto decimal:**
   ```typescript
   // ✅ Válido
   const num = 1_000.5;
   ```

## 🔍 ¿Cómo Funciona?

El separador `_` es puramente visual y es eliminado durante el parsing del número. Estos dos valores son **exactamente iguales**:

```typescript
const a = 1000000;
const b = 1_000_000;

console.log(a === b); // true
console.log(a); // 1000000
console.log(b); // 1000000
```

## 💻 Compatibilidad

- ✅ TypeScript: Todas las versiones modernas
- ✅ JavaScript: ES2021 (ES12) en adelante
- ✅ Navegadores: Chrome 75+, Firefox 70+, Safari 13.1+

## 🎨 Mejores Prácticas

1. **Usa separadores consistentes:** Decide si usarás `_` cada 3 dígitos (miles) o según el contexto
2. **Sé consistente en tu código:** Si usas separadores en un lugar, úsalos en lugares similares
3. **No abuses:** Para números pequeños (menos de 4 dígitos), generalmente no es necesario

## 📊 Comparación Visual

```typescript
// Sin separadores - ¿Cuánto es esto?
const valor1 = 1234567890;

// Con separadores - ¡Ah, es 1.234 millones!
const valor2 = 1_234_567_890;
```

---

**¿Te gustó este tip?** ¡Compártelo con tu equipo y mejora la legibilidad de tu código! 🚀

