# ❓ Tip del Día: Nullish Coalescing (`??`)

## 💡 El problema del operador `||`

A menudo usamos el operador lógico OR (`||`) para asignar valores por defecto:

```javascript
const count = data.count || 10;
```

Pero hay un problema: el operador `||` comprueba si el valor es *falsy*. Esto significa que si `data.count` es `0` o una cadena vacía `""`, ¡se asignará `10`! Y a veces el `0` es un valor perfectamente válido.

### ✅ La Solución: Nullish Coalescing `??`

El operador `??` solo devuelve el operando de la derecha cuando el de la izquierda es estrictamente `null` o `undefined`.

```javascript
const count = data.count ?? 10;

// data.count = 0 -> count = 0 (Correcto!)
// data.count = null -> count = 10 (Correcto!)
```

---

## 🚀 ¿Cuándo usar cada uno?

-   **Usa `||`**: Si quieres atrapar cualquier valor *falsy* (`false`, `0`, `""`, `NaN`, `null`, `undefined`).
-   **Usa `??`**: Si solo quieres protegerte contra la ausencia real del valor (`null` o `undefined`).

---

## 🛠️ Ejemplo Pro: Combinado con Optional Chaining

```javascript
const userName = user?.profile?.name ?? 'Invitado';
```

Esta línea es potente: intenta obtener el nombre, y si no existe el usuario, el perfil o el nombre, devuelve 'Invitado'. ¡Cero errores!

**¡Controla tus valores nulos con precisión! 🎯**
