# 🔄 Tip del Día: Aliases en Destructuración

## 💡 ¿Nombres de variables que no te convencen?

Cuando destructuras un objeto, JS usa por defecto el nombre de la propiedad como nombre de la variable:

```javascript
const user = { name: 'Carlos', uid: '12345' };
const { name, uid } = user;
```

Pero, ¿qué pasa si quieres un nombre más descriptivo o si ya tienes una variable llamada `uid` en tu scope?

### ✅ La Solución: Destructuración con Aliases

Puedes renombrar la variable sobre la marcha usando los dos puntos `:`:

```javascript
const { name, uid: userId } = user;

console.log(userId); // '12345'
```

---

## 🚀 Casos de Uso Útiles

1.  **Evitar colisiones de nombres**: Cuando traes datos de diferentes fuentes con propiedades que se llaman igual.
2.  **Legibilidad**: Convertir abreviaturas raras de una base de datos antigua en nombres legibles.
3.  **Contexto**: `const { date: creationDate } = post;` deja mucho más claro qué fecha es.

---

## 🧠 Bonus Tip: También con Valores por Defecto

Puedes combinarlo con valores por defecto si la propiedad pudiera no existir:

```javascript
const { role: userRole = 'viewer' } = user;
```

**¡Código más limpio y variables con sentido! 🧹✨**
