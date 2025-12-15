# 🛡️ Validación de Variables de Entorno con Zod

¿Alguna vez se te ha caído el servidor en producción porque faltaba una variable de entorno que olvidaste configurar? Es un error clásico, pero tiene fácil solución.

---

## ❌ El Problema

Lo típico es acceder a `process.env.DB_HOST` directamente en tu código. Si esa variable no existe, puede que te des cuenta:
1.  Cuando la app intenta conectarse a la DB y falla (runtime error).
2.  Peor aún, cuando la app arranca pero funciona mal silenciosamente.

---

## ✅ La Solución: Zod

[Zod](https://zod.dev/) es una librería de validación de esquemas para TypeScript. Podemos usarla para validar `process.env` **antes** de que la aplicación arranque.

### 1. Define tu esquema

Crea un archivo `env.ts` (o `config.ts`):

```typescript
import { z } from 'zod';

const envSchema = z.object({
  PORT: z.string().transform(Number).default('3000'),
  DATABASE_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
  NODE_ENV: z.enum(['development', 'production', 'test']).default('development'),
  // Opcionales
  REDIS_URL: z.string().url().optional(),
});

// Validamos process.env
const _env = envSchema.safeParse(process.env);

if (!_env.success) {
  console.error('❌ Invalid environment variables:', _env.error.format());
  process.exit(1); // Matamos el proceso si la config está mal
}

export const env = _env.data;
```

### 2. Úsalo en tu app

Ahora, en lugar de usar `process.env`, importas tu objeto `env` validado:

```typescript
import { env } from './config/env';

// ✅ Tienes autocompletado y tipos garantizados
app.listen(env.PORT, () => {
  console.log(`Server running in ${env.NODE_ENV} mode`);
});
```

---

## 🚀 Ventajas

1.  **Fail Fast**: La aplicación ni siquiera arranca si la configuración está mal. Te enteras al instante, no cuando un usuario reporta un error.
2.  **Type Safety**: TypeScript sabe que `env.PORT` es un `number` (gracias al transform) y que `env.DATABASE_URL` existe.
3.  **Autocompletado**: Olvídate de escribir mal el nombre de las variables.
4.  **Valores por defecto**: Centralizas la lógica de defaults en un solo sitio.

¡Deja de confiar en la suerte y valida tu entorno!
