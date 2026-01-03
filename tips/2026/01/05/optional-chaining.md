# 🔗 Tip del Día: Optional Chaining (`?.`)

## 💡 ¿Código lleno de comprobaciones `if`?

Seguro que te ha pasado: quieres acceder a una propiedad anidada y terminas escribiendo algo como esto:

```javascript
if (user && user.address && user.address.street) {
  const street = user.address.street;
}
```

¡Es tedioso y propenso a errores!

### ✅ La Solución: Optional Chaining

Con el operador `?.`, puedes intentar acceder a la propiedad y, si algún eslabón de la cadena es `null` o `undefined`, la expresión simplemente devuelve `undefined` en lugar de lanzar un error (`Uncaught TypeError: Cannot read property '...' of undefined`).

```javascript
// Mucho más limpio:
const street = user?.address?.street;

// También funciona con llamadas a funciones:
user.saveData?.();
```

---

## 🚀 Beneficios

1.  **Código más corto y legible**: Menos azúcar sintáctico, más claridad.
2.  **Robustez**: Evita que toda la aplicación se rompa por un dato inesperado.
3.  **Encadenamiento con funciones**: Ideal para callbacks opcionales.

---

## ⚠️ Un pequeño recordatorio

No lo uses para TODO. Si esperas que una propiedad SIEMPRE esté ahí, no uses `?.`. Úsalo solo cuando la ausencia del dato sea una posibilidad válida en tu lógica.

**¡Simplifica tu código hoy mismo! 🚀**
