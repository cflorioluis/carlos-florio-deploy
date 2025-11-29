# 💡 Tip del Día: Moment.js - Formatos de Fechas e Internacionalización

_En colaboración con [JuanJo Calderón](mailto:juanjose.calderon@fourvenues.com)_

---

## 🌍 ¿Por qué unificar el formato de fechas?

Cuando trabajas en proyectos internacionales o con equipos multilingües, es crucial tener un formato de visualización de fechas consistente y localizado. **Moment.js** es una biblioteca poderosa que permite formatear fechas en distintos idiomas y estándares de manera sencilla.

---

## 📚 ¿Qué es Moment.js?

**Moment.js** es una biblioteca de JavaScript para parsear, validar, manipular y formatear fechas. Una de sus características más útiles es la capacidad de formatear fechas según diferentes locales (idiomas) y estándares.

**🔗 Documentación oficial**: [momentjs.com](https://momentjs.com)

---

## 📦 Instalación

```bash
npm install moment
```

Para TypeScript:

```bash
npm install moment
npm install --save-dev @types/moment
```

---

## 🌐 Configuración de Locales

Moment.js soporta múltiples idiomas. Para usar un idioma específico, necesitas importar el locale correspondiente:

```typescript
import moment from 'moment';
import 'moment/locale/es'; // Para español
import 'moment/locale/en-gb'; // Para inglés británico
import 'moment/locale/fr'; // Para francés

// Configurar el locale
moment.locale('es'); // Español
// o
moment.locale('en'); // Inglés (por defecto)
```

---

## 📅 Formatos Predefinidos Comunes

Moment.js incluye varios formatos predefinidos que se adaptan automáticamente al locale configurado. Estos son algunos de los más utilizados:

### Formato `ll` - Fecha larga localizada

El formato `ll` muestra la fecha en un formato legible y localizado:

```typescript
moment.locale('en'); // Inglés (en-US)
moment().format('ll');   // "Nov 12, 2025"

moment.locale('es'); // Español
moment().format('ll');   // "12 de nov. de 2025"
```

**Ejemplos en diferentes idiomas:**

| Locale | Formato `ll` | Ejemplo |
|--------|--------------|---------|
| `en` (en-US) | `MMM D, YYYY` | Nov 12, 2025 |
| `es` | `D [de] MMM [de] YYYY` | 12 de nov. de 2025 |
| `fr` | `D MMM YYYY` | 12 nov. 2025 |
| `de` | `D. MMM YYYY` | 12. Nov. 2025 |
| `it` | `D MMM YYYY` | 12 nov 2025 |

### Formato `LT` - Hora localizada

El formato `LT` muestra la hora en formato localizado:

```typescript
moment.locale('en'); // Inglés (en-US)
moment().format('LT');   // "12:17 PM"

moment.locale('es'); // Español
moment().format('LT');   // "12:18"
```

**Ejemplos en diferentes idiomas:**

| Locale | Formato `LT` | Ejemplo |
|--------|--------------|---------|
| `en` (en-US) | `h:mm A` | 12:17 PM |
| `es` | `H:mm` | 12:18 |
| `fr` | `HH:mm` | 12:18 |
| `de` | `HH:mm` | 12:18 |

### Formato `L` - Fecha corta localizada

El formato `L` muestra la fecha en formato corto según el estándar del locale:

```typescript
moment.locale('en'); // Inglés (en-US)
moment().format('L');   // "11/12/2025" (MM/DD/YYYY)

moment.locale('es'); // Español
moment().format('L');   // "12/11/2025" (DD/MM/YYYY)
```

**Ejemplos en diferentes idiomas:**

| Locale | Formato `L` | Ejemplo |
|--------|--------------|---------|
| `en` (en-US) | `MM/DD/YYYY` | 11/12/2025 |
| `es` | `DD/MM/YYYY` | 12/11/2025 |
| `fr` | `DD/MM/YYYY` | 12/11/2025 |
| `de` | `DD.MM.YYYY` | 12.11.2025 |

### Formato `ddd ll` - Día de la semana + Fecha

El formato `ddd` representa las 3 primeras letras del día de la semana, y puedes combinarlo con otros formatos:

```typescript
moment.locale('en'); // Inglés (en-US)
moment().format('ddd ll');   // "Wed Nov 12, 2025"

moment.locale('es'); // Español
moment().format('ddd ll');   // "mie 12 de nov. de 2025"
```

**Ejemplos en diferentes idiomas:**

| Locale | Formato `ddd ll` | Ejemplo |
|--------|------------------|---------|
| `en` (en-US) | `ddd MMM D, YYYY` | Wed Nov 12, 2025 |
| `es` | `ddd D [de] MMM [de] YYYY` | mie 12 de nov. de 2025 |
| `fr` | `ddd D MMM YYYY` | mer. 12 nov. 2025 |
| `de` | `ddd D. MMM YYYY` | Mi. 12. Nov. 2025 |

---

## 🎯 Formatos Más Usados en Producción

Según las mejores prácticas, estos son los formatos más comunes en aplicaciones profesionales:

### 1. `ll` - Fecha larga legible
```typescript
moment().format('ll'); // "12 de nov. de 2025" (es) o "Nov 12, 2025" (en)
```

**Cuándo usarlo:**
- Tarjetas de noticias
- Encabezados de artículos
- Perfiles de usuario
- Historial de actividades

### 2. `L` - Fecha corta estándar
```typescript
moment().format('L'); // "12/11/2025" (es) o "11/12/2025" (en)
```

**Cuándo usarlo:**
- Formularios
- Tablas de datos
- Filtros de fecha
- Campos de entrada

### 3. `LT` - Hora localizada
```typescript
moment().format('LT'); // "12:18" (es) o "12:18 PM" (en)
```

**Cuándo usarlo:**
- Timestamps de mensajes
- Horarios de eventos
- Logs de actividad
- Notificaciones

### 4. `ddd ll` - Día + Fecha
```typescript
moment().format('ddd ll'); // "mie 12 de nov. de 2025" (es) o "Wed Nov 12, 2025" (en)
```

**Cuándo usarlo:**
- Calendarios
- Agendas
- Programación de eventos
- Recordatorios

---

## 🔧 Uso en Angular

### Instalación y Configuración

```typescript
// app.module.ts o en un servicio
import * as moment from 'moment';
import 'moment/locale/es';

// Configurar el locale globalmente
moment.locale('es');
```

### Servicio de Utilidades

```typescript
import { Injectable } from '@angular/core';
import * as moment from 'moment';
import 'moment/locale/es';

@Injectable({
  providedIn: 'root'
})
export class DateFormatService {
  private locale = 'es';

  constructor() {
    moment.locale(this.locale);
  }

  formatDateLong(date: Date | string): string {
    return moment(date).format('ll');
  }

  formatDateShort(date: Date | string): string {
    return moment(date).format('L');
  }

  formatTime(date: Date | string): string {
    return moment(date).format('LT');
  }

  formatDateWithDay(date: Date | string): string {
    return moment(date).format('ddd ll');
  }

  setLocale(locale: string): void {
    this.locale = locale;
    moment.locale(locale);
  }
}
```

### Uso en Componentes

```typescript
import { Component } from '@angular/core';
import { DateFormatService } from './date-format.service';

@Component({
  selector: 'app-example',
  template: `
    <div>
      <p>Fecha larga: {{ formatDateLong() }}</p>
      <p>Fecha corta: {{ formatDateShort() }}</p>
      <p>Hora: {{ formatTime() }}</p>
      <p>Día + Fecha: {{ formatDateWithDay() }}</p>
    </div>
  `
})
export class ExampleComponent {
  constructor(private dateFormatService: DateFormatService) {}

  formatDateLong(): string {
    return this.dateFormatService.formatDateLong(new Date());
  }

  formatDateShort(): string {
    return this.dateFormatService.formatDateShort(new Date());
  }

  formatTime(): string {
    return this.dateFormatService.formatTime(new Date());
  }

  formatDateWithDay(): string {
    return this.dateFormatService.formatDateWithDay(new Date());
  }
}
```

### Pipe Personalizado

```typescript
import { Pipe, PipeTransform } from '@angular/core';
import * as moment from 'moment';
import 'moment/locale/es';

@Pipe({
  name: 'momentFormat'
})
export class MomentFormatPipe implements PipeTransform {
  transform(value: Date | string, format: string = 'll'): string {
    return moment(value).format(format);
  }
}
```

**Uso en templates:**

```html
<p>{{ fecha | momentFormat:'ll' }}</p>
<p>{{ fecha | momentFormat:'L' }}</p>
<p>{{ fecha | momentFormat:'ddd ll' }}</p>
```

---

## 📋 Tabla de Referencia de Tokens

Moment.js usa tokens para construir formatos personalizados. Aquí tienes los más comunes:

| Token | Salida | Descripción |
|-------|--------|-------------|
| `YYYY` | 2025 | Año con 4 dígitos |
| `YY` | 25 | Año con 2 dígitos |
| `MMMM` | noviembre | Mes completo |
| `MMM` | nov. | Mes abreviado (3 letras) |
| `MM` | 11 | Mes con 2 dígitos |
| `M` | 11 | Mes sin cero inicial |
| `DD` | 12 | Día con 2 dígitos |
| `D` | 12 | Día sin cero inicial |
| `dddd` | miércoles | Día de la semana completo |
| `ddd` | mie | Día de la semana abreviado (3 letras) |
| `dd` | mi | Día de la semana (2 letras) |
| `HH` | 14 | Hora 24h con 2 dígitos |
| `H` | 14 | Hora 24h sin cero inicial |
| `hh` | 02 | Hora 12h con 2 dígitos |
| `h` | 2 | Hora 12h sin cero inicial |
| `mm` | 18 | Minutos con 2 dígitos |
| `m` | 18 | Minutos sin cero inicial |
| `ss` | 30 | Segundos con 2 dígitos |
| `A` | PM | AM/PM |
| `a` | pm | am/pm |

---

## 🎨 Formatos Personalizados Comunes

```typescript
// Fecha y hora completa
moment().format('lll'); // "12 de nov. de 2025 12:18" (es)
moment().format('lll'); // "Nov 12, 2025 12:18 PM" (en)

// Fecha y hora con día de la semana
moment().format('dddd, ll'); // "miércoles, 12 de nov. de 2025" (es)
moment().format('dddd, ll'); // "Wednesday, Nov 12, 2025" (en)

// Formato ISO
moment().format('YYYY-MM-DD'); // "2025-11-12"

// Formato completo con hora
moment().format('YYYY-MM-DD HH:mm:ss'); // "2025-11-12 14:18:30"

// Formato relativo
moment().fromNow(); // "hace 2 horas" (es) o "2 hours ago" (en)
moment().calendar(); // "hoy a las 14:18" (es) o "Today at 2:18 PM" (en)
```

---

## ⚠️ Consideraciones Importantes

### 1. Moment.js está en modo mantenimiento

⚠️ **Importante**: Moment.js está en modo mantenimiento. Los desarrolladores recomiendan migrar a alternativas modernas como:

- **Day.js** (API similar, más ligero)
- **date-fns** (Funcional, modular)
- **Luxon** (Creado por el equipo de Moment.js)

### 2. Alternativas Modernas

#### Day.js (Recomendado)

```bash
npm install dayjs
```

```typescript
import dayjs from 'dayjs';
import 'dayjs/locale/es';

dayjs.locale('es');
dayjs().format('DD/MM/YYYY'); // "12/11/2025"
```

#### date-fns

```bash
npm install date-fns
```

```typescript
import { format } from 'date-fns';
import { es } from 'date-fns/locale';

format(new Date(), 'dd/MM/yyyy', { locale: es }); // "12/11/2025"
```

### 3. Tamaño del Bundle

- **Moment.js**: ~67KB (minificado + gzipped)
- **Day.js**: ~2KB (minificado + gzipped)
- **date-fns**: ~13KB (solo funciones usadas, tree-shakeable)

---

## 🔄 Migración desde Moment.js

Si ya estás usando Moment.js y quieres migrar, aquí tienes una guía rápida:

### Moment.js → Day.js

```typescript
// Antes (Moment.js)
moment(date).format('ll');

// Después (Day.js)
dayjs(date).format('D MMM YYYY');
```

### Moment.js → date-fns

```typescript
// Antes (Moment.js)
moment(date).format('ll');

// Después (date-fns)
format(date, 'd MMM yyyy', { locale: es });
```

---

## 📝 Resumen

- ✅ **Moment.js** permite formatear fechas según diferentes locales
- ✅ Los formatos predefinidos (`ll`, `L`, `LT`, `ddd`) se adaptan automáticamente al idioma
- ✅ Los formatos más usados en producción son: `ll`, `L`, `LT`, y `ddd ll`
- ⚠️ Moment.js está en modo mantenimiento - considera migrar a Day.js o date-fns
- 📦 Para proyectos nuevos, considera usar alternativas más modernas y ligeras

---

## 🔗 Recursos

- [Moment.js Documentation](https://momentjs.com/docs/)
- [Moment.js Format Tokens](https://momentjs.com/docs/#/displaying/format/)
- [Moment.js Locales](https://momentjs.com/docs/#/i18n/)
- [Day.js Documentation](https://day.js.org/)
- [date-fns Documentation](https://date-fns.org/)

---

**En colaboración con**: [JuanJo Calderón](mailto:juanjose.calderon@fourvenues.com)

---

**¿Te gustó este tip?** ¡Unifica el formato de fechas en tus proyectos y mejora la experiencia de usuario internacional! 🌍🚀

