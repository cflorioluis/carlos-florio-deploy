# 🧹 Tip del Día: Optional Catch Binding - Código más limpio en JS

A veces, al usar un bloque `try...catch`, sabemos que algo puede fallar pero **no necesitamos el objeto de error** para nada. Solo queremos manejar el fallo de forma silenciosa o ejecutar una acción alternativa.

Antes de ES2019 (ES10), estábamos obligados a declarar la variable de error aunque no la usáramos:

```javascript
try {
  // Algo que puede explotar 💥
  JSON.parse(data);
} catch (unusedError) { // 👈 Variable obligatoria pero inútil
  console.log('Error de parseo, usando default...');
}
```

## ✨ La Mejora: Optional Catch Binding

Ahora puedes omitir los paréntesis y la variable por completo:

```javascript
try {
  JSON.parse(data);
} catch { // 👈 ¡Limpio y directo!
  console.log('Error detectado, manejando...');
}
```

### 💡 ¿Cuándo usarlo?
*   Cuando el error es "esperado" y no aporta información útil para el reporte.
*   En limpiezas de almacenamiento (ej: un `localStorage.clear()` que podría fallar en modo incógnito).
*   Cuando simplemente quieres un valor por defecto si falla el cálculo principal.

¡Menos ruido visual, código más elegante! 🚀

#javascript #codingtips #clean-code #es2019 #webdev
