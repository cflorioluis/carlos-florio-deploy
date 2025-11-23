# 💡 Tip del Día: Introducción a TOON

## ¿Qué es TOON?

**TOON** es un formato de serialización de datos diseñado para ser más eficiente en tokens que JSON, especialmente útil cuando trabajas con modelos de lenguaje (LLMs) que cobran por tokens.

---

## 🎯 ¿Por qué usar TOON?

### Ventajas sobre JSON:

1. **Más eficiente en tokens**: Reduce significativamente el número de tokens necesarios para representar datos estructurados
2. **Formato tabular**: Para arrays uniformes de objetos, usa un formato compacto similar a CSV
3. **Legible**: Mantiene cierta legibilidad humana, aunque menos que JSON
4. **Optimizado para LLMs**: Diseñado específicamente para trabajar con modelos de lenguaje

---

## 📊 Comparación: JSON vs TOON

### Ejemplo con JSON:

```json
{
  "users": [
    { "id": 1, "name": "Alice", "role": "admin" },
    { "id": 2, "name": "Bob", "role": "user" },
    { "id": 3, "name": "Charlie", "role": "user" }
  ]
}
```

**Tokens aproximados**: ~45 tokens

### Mismo ejemplo en TOON:

```toon
users[3]{id,name,role}:
  1,Alice,admin
  2,Bob,user
  3,Charlie,user
```

**Tokens aproximados**: ~25 tokens

**Ahorro**: ~44% menos tokens! 🎉

---

## 🔧 Cómo usar TOON

### Instalación:

```bash
npm install @toon-format/toon
```

### Uso básico:

```typescript
import { encode, decode } from '@toon-format/toon'

// Convertir de JSON a TOON
const data = {
  users: [
    { id: 1, name: 'Alice', role: 'admin' },
    { id: 2, name: 'Bob', role: 'user' }
  ]
}

const toonString = encode(data)
console.log(toonString)
// Output: users[2]{id,name,role}:
//   1,Alice,admin
//   2,Bob,user

// Convertir de TOON a JSON
const jsonData = decode(toonString)
console.log(jsonData)
// Output: { users: [{ id: 1, name: 'Alice', role: 'admin' }, ...] }
```

---

## 📝 Formato TOON

### Formato tabular (para arrays uniformes):

```toon
nombreArray[tamaño]{campo1,campo2,campo3}:
  valor1,valor2,valor3
  valor4,valor5,valor6
```

### Formato de lista (para estructuras más complejas):

```toon
nombreArray[tamaño]:
  - campo1: valor1
    campo2: valor2
  - campo1: valor3
    campo2: valor4
```

---

## ⚠️ Limitaciones

1. **Arrays uniformes**: TOON funciona mejor con arrays de objetos que tienen exactamente la misma estructura
2. **Campos opcionales**: Si hay campos opcionales, TOON cambiará automáticamente al formato de lista (menos eficiente)
3. **Tipos de datos**: Soporta strings, números, booleanos, null, arrays y objetos anidados

---

## 🎯 Casos de uso ideales

- **APIs con LLMs**: Cuando envías datos estructurados a modelos de lenguaje
- **Almacenamiento eficiente**: Reducir el tamaño de datos serializados
- **Transferencia de datos**: Enviar menos tokens por red
- **Arrays uniformes**: Cuando todos los objetos tienen la misma estructura

---

## 🔗 Recursos

- [Documentación TOON](https://github.com/toon-format/toon)
- [Herramienta JSON ⇄ TOON Converter](/tools/json-toon-converter) - Prueba la conversión en tu navegador
- [Tip: Campos Opcionales en TOON](/tips/2025/11/28/campos-opcionales-toon) - Aprende sobre campos opcionales

---

## 🛠️ Prueba la Conversión

¿Quieres probar cómo funciona TOON? Usa nuestra herramienta interactiva:

**[🔗 Ir a JSON ⇄ TOON Converter](/tools/json-toon-converter)**

Puedes pegar tus JSON y ver cómo se convierten a formato TOON en tiempo real, comparando la eficiencia en tokens.

---

**¿Te gustó este tip?** ¡Empieza a usar TOON y ahorra tokens en tus proyectos! 🚀

