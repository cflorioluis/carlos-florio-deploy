# 🌍 Tip del Día: API Intl - Formatea fechas y monedas como un PRO

¿Sigues usando librerías pesadas como Moment.js o Numeral.js solo para formatear una fecha o una moneda? 🏋️‍♂️

JavaScript tiene una herramienta nativa súper potente: la **API Intl**. Es ligera, estándar y ya vive en tu navegador.

## 💰 1. Formatear Monedas
Olvídate de concatenar símbolos manualmente. `Intl.NumberFormat` lo hace por ti respetando las reglas de cada país:

```javascript
const monto = 1250.50;

const formatter = new Intl.NumberFormat('es-ES', {
  style: 'currency',
  currency: 'EUR',
});

console.log(formatter.format(monto)); // "1.250,50 €"
```

## 📅 2. Formatear Fechas
`Intl.DateTimeFormat` te permite mostrar fechas de forma humana sin complicaciones:

```javascript
const hoy = new Date();

const dtf = new Intl.DateTimeFormat('es-MX', {
  dateStyle: 'long', // 'full', 'long', 'medium', 'short'
});

console.log(dtf.format(hoy)); // "2 de febrero de 2026"
```

## 🕒 3. Tiempos Relativos
¿Quieres mostrar "hace 5 minutos" o "mañana"? Usa `Intl.RelativeTimeFormat`:

```javascript
const rtf = new Intl.RelativeTimeFormat('es', { numeric: 'auto' });

console.log(rtf.format(-1, 'day')); // "ayer"
console.log(rtf.format(2, 'hour')); // "dentro de 2 horas"
```

### 🏆 ¿Por qué usar Intl?
1.  **Bye bye Bundle Size**: Ahorras KBs valiosos al no importar librerías externas.
2.  **Rendimiento**: Está optimizado a nivel de motor de navegador.
3.  **Localización**: Soporta CIENTOS de idiomas y regiones automáticamente.

¡Usa lo nativo y mantén tu web rápida! 🚀

#javascript #frontend #webdev #performance #intl #i18n
