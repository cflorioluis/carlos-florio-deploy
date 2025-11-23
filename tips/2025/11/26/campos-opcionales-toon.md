# 💡 Tip del Día: Campos Opcionales en TOON

## ¿TOON puede convertir JSON con parámetros opcionales?

**Sí**, TOON puede convertir JSON con objetos que tienen parámetros opcionales, pero el formato resultante cambiará según la uniformidad de los datos.

---

## 🔍 Cómo TOON maneja campos opcionales

TOON utiliza un **formato tabular** para arrays de objetos uniformes, donde declara los campos una sola vez en el encabezado. Sin embargo, para usar este formato eficiente, todos los objetos deben tener **exactamente los mismos campos** con valores primitivos.

### ⚠️ Problema: Campos opcionales

Si tus objetos tienen campos opcionales (algunos objetos tienen un campo y otros no), TOON **no puede usar el formato tabular** y cambiará automáticamente al **formato de lista**.

---

## 📝 Ejemplo con campos opcionales

### JSON con campo opcional:

```json
{
  "users": [
    { "id": 1, "name": "Alice", "role": "admin" },
    { "id": 2, "name": "Bob", "role": "user", "extra": true }
  ]
}
```

### Conversión a TOON (formato de lista):

```toon
users[2]:
  - id: 1
    name: Alice
    role: admin
  - id: 2
    name: Bob
    role: user
    extra: true
```

> **Nota**: El formato de lista puede ser **menos eficiente en tokens** que JSON cuando hay campos opcionales.

---

## ✅ Solución: Normalizar los datos

Para aprovechar el **formato tabular más eficiente** de TOON, debes normalizar tus objetos para que todos tengan los mismos campos. Agrega el campo opcional con valor `null` a los objetos que no lo tienen:

### JSON normalizado:

```json
{
  "users": [
    { "id": 1, "name": "Alice", "role": "admin", "extra": null },
    { "id": 2, "name": "Bob", "role": "user", "extra": true }
  ]
}
```

### Conversión a TOON (formato tabular):

```toon
users[2]{id,name,role,extra}:
  1,Alice,admin,null
  2,Bob,user,true
```

---

## 💻 Uso de la API

Para convertir tu JSON a TOON, usa la función `encode()`:

```typescript
import { encode } from '@toon-format/toon'

const data = {
  users: [
    { id: 1, name: 'Alice', role: 'admin', extra: null },
    { id: 2, name: 'Bob', role: 'user', extra: true }
  ]
}

console.log(encode(data))
```

**Salida:**
```toon
users[2]{id,name,role,extra}:
  1,Alice,admin,null
  2,Bob,user,true
```

---

## 🎯 Puntos clave

1. **TOON está optimizado** para arrays uniformes de objetos con la misma estructura
2. **Campos opcionales** hacen que TOON use formato de lista en lugar de tabular
3. **Normalizar datos** (agregar `null` a campos faltantes) maximiza la eficiencia
4. **Formato tabular** es más eficiente en tokens que el formato de lista

---

## 🔗 Recursos

- [Documentación TOON](https://github.com/toon-format/toon)
- [Herramienta JSON ⇄ TOON Converter](/tools/json-toon-converter) - Prueba la conversión en tu navegador

---

## 🛠️ Prueba la Conversión

¿Quieres probar cómo funciona la conversión con campos opcionales? Usa nuestra herramienta interactiva:

**[🔗 Ir a JSON ⇄ TOON Converter](/tools/json-toon-converter)**

Puedes pegar tus JSON con campos opcionales y ver cómo se convierten a formato TOON en tiempo real.

---

**¿Te gustó este tip?** ¡Normaliza tus datos y aprovecha al máximo TOON! 🚀
