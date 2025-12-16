# 🔌 NestJS: Custom Decorators para un Código Limpio

Si usas NestJS, seguramente te encanta cómo los decoradores (`@Get`, `@Body`, `@Controller`) hacen tu código declarativo y limpio. Pero, ¿sabías que puedes crear los tuyos propios?

El caso de uso más común: **Obtener el usuario autenticado**.

---

## ❌ La forma "sucia"

Sin decoradores personalizados, tus controladores se llenan de `request`:

```typescript
@Get('profile')
getProfile(@Req() req: Request) {
  // Tienes que saber que el user está en req.user
  // Y además req.user suele ser 'any' o 'unknown'
  const user = req.user; 
  return user;
}
```

Esto acopla tu controlador al objeto `Request` de Express/Fastify y es difícil de tipar.

---

## ✅ La forma "NestJS"

Vamos a crear un decorador `@User()`.

**1. Crear el decorador (`user.decorator.ts`):**

```typescript
import { createParamDecorator, ExecutionContext } from '@nestjs/common';

export const User = createParamDecorator(
  (data: string | undefined, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    const user = request.user;

    // Si pasamos data (ej: @User('email')), devolvemos solo esa propiedad
    return data ? user?.[data] : user;
  },
);
```

**2. Usarlo en el controlador:**

```typescript
@Get('profile')
getProfile(@User() user: UserEntity) {
  // ¡Limpio, directo y tipado!
  console.log(user.email);
  return user;
}

@Get('email')
getEmail(@User('email') email: string) {
  // Accediendo a una propiedad específica
  return email;
}
```

---

## 🚀 Ventajas

1.  **Desacoplamiento**: Tu controlador no necesita saber qué es `req`. Esto facilita los tests unitarios (puedes pasar un objeto usuario directo sin mockear todo el objeto request).
2.  **Legibilidad**: `@User() user` se explica solo.
3.  **Reusabilidad**: Puedes poner lógica compleja en el decorador (ej: validar roles, transformar datos) y reutilizarla en todos tus endpoints.

¡Extiende NestJS para que trabaje para ti!
