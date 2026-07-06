# NestJS 11: `ParseDatePipe` para fechas en rutas

Patrones backend con NestJS 11 en apps reales.

---

## Contexto

NestJS unifica módulos, inyección de dependencias y adaptadores HTTP (Express/Fastify). En v11 conviene revisar la [guía de migración](https://docs.nestjs.com/migration-guide).

---

## Snippet orientativo

```typescript
@Module({
  imports: [ConfigModule.forRoot({ isGlobal: true })],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

---

## Buenas prácticas

- Valida DTOs con `ValidationPipe`.
- Centraliza configuración con `ConfigService`.
- Añade health checks antes de desplegar.
