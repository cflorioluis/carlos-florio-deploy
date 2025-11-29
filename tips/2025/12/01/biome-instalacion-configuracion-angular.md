# 🚀 Biome: Instalación y Configuración en Angular

**Biome** es una herramienta todo-en-uno que reemplaza a ESLint y Prettier, ofreciendo linting, formateo y organización de imports en un solo paquete. Es hasta **25x más rápido** que las alternativas tradicionales.

---

## 📦 Instalación

```bash
npm install --save-dev --save-exact @biomejs/biome
```

**¿Qué hace este comando?**
- `--save-dev`: Instala como dependencia de desarrollo (no va a producción)
- `--save-exact`: Fija la versión exacta sin `^` o `~` para evitar actualizaciones inesperadas

---

## ⚙️ Inicialización

```bash
npx @biomejs/biome init
```

Este comando crea el archivo `biome.json` con una configuración por defecto y detecta automáticamente Git para habilitar la integración VCS.

---

## 🎯 Configuración Avanzada para Angular

Aquí tienes una configuración completa y optimizada para proyectos Angular con todas las mejores prácticas:

### Configuración Completa del `biome.json`

```json
{
  "$schema": "https://biomejs.dev/schemas/2.3.8/schema.json",
  "vcs": {
    "enabled": true,
    "clientKind": "git",
    "useIgnoreFile": true
  },
  "files": {
    "ignoreUnknown": false
  },
  "formatter": {
    "enabled": true,
    "indentStyle": "space",
    "indentWidth": 2,
    "lineWidth": 100,
    "lineEnding": "lf"
  },
  "linter": {
    "enabled": true,
    "rules": {
      "recommended": true,
      "style": {
        "useConst": "error",
        "noVar": "error",
        "useTemplate": "warn"
      },
      "suspicious": {
        "noExplicitAny": "warn",
        "noArrayIndexKey": "warn"
      },
      "correctness": {
        "noUnusedVariables": "error",
        "useExhaustiveDependencies": "warn"
      },
      "complexity": {
        "noForEach": "off",
        "useSimplifiedLogicExpression": "warn"
      },
      "performance": {
        "noDelete": "error"
      }
    }
  },
  "javascript": {
    "formatter": {
      "quoteStyle": "single",
      "semicolons": "always",
      "trailingCommas": "all",
      "arrowParentheses": "always"
    }
  },
  "json": {
    "formatter": {
      "indentWidth": 2,
      "trailingCommas": "none"
    }
  },
  "html": {
    "parser": {
      "interpolation": true
    },
    "formatter": {
      "indentWidth": 2,
      "indentStyle": "space",
      "lineWidth": 100,
      "attributePosition": "auto"
    }
  },
  "css": {
    "formatter": {
      "indentWidth": 2,
      "lineWidth": 100
    }
  },
  "assist": {
    "enabled": true,
    "actions": {
      "source": {
        "organizeImports": "on"
      }
    }
  }
}
```

---

## 📝 Explicación Detallada de Cada Sección

### `vcs` - Control de Versiones

```json
"vcs": {
  "enabled": true,
  "clientKind": "git",
  "useIgnoreFile": true
}
```

- **`enabled`**: Habilita la integración con Git para respetar `.gitignore`
- **`clientKind`**: Especifica que usas Git (también soporta SVN)
- **`useIgnoreFile`**: Biome respetará los archivos ignorados en `.gitignore`

### `formatter` - Configuración de Formateo

```json
"formatter": {
  "enabled": true,
  "indentStyle": "space",
  "indentWidth": 2,
  "lineWidth": 100,
  "lineEnding": "lf"
}
```

- **`indentStyle`**: Usa espacios en lugar de tabs (estándar Angular)
- **`indentWidth`**: 2 espacios de indentación
- **`lineWidth`**: Máximo 100 caracteres por línea
- **`lineEnding`**: Usa `lf` (Linux/Mac) para consistencia cross-platform

### `linter.rules` - Reglas de Linting

#### `style.useConst`
Fuerza el uso de `const` en lugar de `let` cuando la variable no se reasigna:

```typescript
// ❌ Mal
let name = 'Carlos';
let age = 30;

// ✅ Bien
const name = 'Carlos';
const age = 30;
```

#### `style.noVar`
Prohíbe el uso de `var` (usa `let` o `const`):

```typescript
// ❌ Mal
var counter = 0;

// ✅ Bien
let counter = 0;
```

#### `suspicious.noExplicitAny`
Advierte sobre el uso de `any` (reduce la seguridad de tipos):

```typescript
// ⚠️ Advertencia
function process(data: any) {
  return data.value;
}

// ✅ Mejor
function process<T extends { value: unknown }>(data: T) {
  return data.value;
}
```

#### `correctness.noUnusedVariables`
Marca errores si hay variables declaradas pero no usadas:

```typescript
// ❌ Error
const unusedVariable = 'test';
console.log('Hello');

// ✅ Bien
const usedVariable = 'test';
console.log(usedVariable);
```

### `javascript.formatter` - Formateo de JavaScript/TypeScript

```json
"javascript": {
  "formatter": {
    "quoteStyle": "single",
    "semicolons": "always",
    "trailingCommas": "all",
    "arrowParentheses": "always"
  }
}
```

- **`quoteStyle: "single"`**: Usa comillas simples `'` en lugar de dobles
- **`semicolons: "always"`**: Siempre incluye punto y coma
- **`trailingCommas: "all"`**: Agrega comas finales (mejor para diffs en Git)
- **`arrowParentheses: "always"`**: Siempre incluye paréntesis en arrow functions

**Ejemplo de formateo:**

```typescript
// Biome formateará así:
const users = [
  { id: 1, name: 'Carlos' },
  { id: 2, name: 'Ana' },
];

const greet = (name: string): string => {
  return `Hello, ${name}`;
};
```

### `html.formatter` - Formateo de Templates Angular

```json
"html": {
  "parser": {
    "interpolation": true
  },
  "formatter": {
    "indentWidth": 2,
    "indentStyle": "space",
    "lineWidth": 100,
    "attributePosition": "auto"
  }
}
```

- **`interpolation: true`**: Habilita el parsing de interpolaciones Angular `{{ }}`
- **`attributePosition: "auto"`**: Coloca atributos en múltiples líneas si excede `lineWidth`

**Ejemplo de formateo de templates:**

Cuando un componente tiene muchos atributos y excede los 100 caracteres, Biome los coloca en múltiples líneas:

```html
<!-- Si la línea excede 100 caracteres -->
<ui-button
  size="medium"
  theme="primary"
  variant="outlined"
  icon="user"
  [disabled]="isLoading$ | async"
  (click)="handleSubmit()">
  {{ 'common.save' | translate }}
</ui-button>
```

**Auto-cierre de etiquetas vacías:**

Biome automáticamente cierra etiquetas vacías:

```html
<!-- Biome formateará así: -->
<img src="logo.png" alt="Logo" />
<app-icon name="user" />
<input type="text" [value]="name" />
```

### `assist` - Asistencia y Organización

```json
"assist": {
  "enabled": true,
  "actions": {
    "source": {
      "organizeImports": "on"
    }
  }
}
```

- **`organizeImports: "on"`**: Organiza automáticamente los imports, elimina los no usados y los ordena alfabéticamente

**Ejemplo:**

```typescript
// Antes
import { Component } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { catchError } from 'rxjs/operators';

// Después de organizar (Biome ordena y elimina no usados)
import { Component } from '@angular/core';
import { Observable } from 'rxjs';
```

---

## 🔧 Scripts NPM Recomendados

Agrega estos scripts a tu `package.json`:

```json
{
  "scripts": {
    "lint": "biome lint ./src",
    "lint:fix": "biome lint --write ./src",
    "format": "biome format --write ./src",
    "format:check": "biome format ./src",
    "check": "biome check --write ./src",
    "check:ci": "biome check ./src"
  }
}
```

**Explicación de comandos:**

| Comando | ¿Qué hace? | Cuándo usarlo |
|---------|-----------|---------------|
| `npm run lint` | Verifica errores de linting | Para revisar problemas |
| `npm run lint:fix` | Corrige errores automáticamente | Para arreglar issues |
| `npm run format` | Formatea el código | Antes de commit |
| `npm run format:check` | Verifica formato sin cambiar | En CI/CD |
| `npm run check` | **TODO**: lint + format + organize imports | **Comando principal** |
| `npm run check:ci` | Verifica sin modificar archivos | En pipelines CI/CD |

---

## 💡 Buenas Prácticas Adicionales

### 1. Evitar Returns Consecutivos

Biome puede detectar patrones donde un `return` sigue inmediatamente después de una llamada. Aunque no hay una regla específica para esto, puedes usar la regla `complexity.useSimplifiedLogicExpression`:

```typescript
// ⚠️ Patrón a evitar
function processData() {
  fetchData();
  return; // Return inmediato después de llamada
}

// ✅ Mejor
function processData() {
  const result = fetchData();
  if (!result) {
    return;
  }
  // Continuar procesamiento
}
```

### 2. Configuración del Editor (VSCode/Cursor)

Crea o actualiza `.vscode/settings.json`:

```json
{
  "editor.defaultFormatter": "biomejs.biome",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.organizeImports.biome": "explicit",
    "source.fixAll.biome": "explicit"
  },
  "[typescript]": {
    "editor.defaultFormatter": "biomejs.biome"
  },
  "[html]": {
    "editor.defaultFormatter": "biomejs.biome"
  },
  "[json]": {
    "editor.defaultFormatter": "biomejs.biome"
  }
}
```

### 3. Pre-commit Hook (Opcional)

Para ejecutar Biome antes de cada commit, puedes usar Husky:

```bash
npm install --save-dev husky lint-staged
```

En `package.json`:

```json
{
  "lint-staged": {
    "*.{ts,html,json}": [
      "biome check --write"
    ]
  }
}
```

---

## 🚀 Uso Diario

```bash
# Antes de hacer commit (recomendado)
npm run check

# Solo formatear
npm run format

# Solo lint con auto-fix
npm run lint:fix
```

---

## 📊 Comparación: Biome vs ESLint + Prettier

| Característica | Biome | ESLint + Prettier |
|----------------|-------|-------------------|
| **Velocidad** | ⚡ 25x más rápido | Normal |
| **Configuración** | 1 archivo (`biome.json`) | 2+ archivos |
| **Instalación** | 1 paquete | 10+ paquetes |
| **Linting** | ✅ Integrado | ✅ Requiere ESLint |
| **Formateo** | ✅ Integrado | ✅ Requiere Prettier |
| **Organizar imports** | ✅ Integrado | ❌ Requiere plugin |
| **Tamaño del bundle** | ~15MB | ~50MB+ |

---

## 🎯 Próximos Pasos

1. ✅ Instalar Biome: `npm install --save-dev --save-exact @biomejs/biome`
2. ✅ Inicializar: `npx @biomejs/biome init`
3. ✅ Configurar `biome.json` con la configuración completa
4. ✅ Agregar scripts npm
5. ✅ Instalar extensión de Biome en tu editor
6. ✅ Habilitar format on save
7. ✅ Ejecutar `npm run check` antes de cada commit

---

## 📚 Recursos

- [Documentación oficial de Biome](https://biomejs.dev)
- [Guía de migración desde ESLint](https://biomejs.dev/guides/migrate-eslint-prettier/)
- [Configuración de reglas](https://biomejs.dev/linter/rules/)
- [Formateo de código](https://biomejs.dev/formatter/)

