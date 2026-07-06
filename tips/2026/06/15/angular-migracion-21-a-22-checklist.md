# Migración Angular 21→22 (4/4): checklist producción

Serie de migración Angular paso a paso. Consulta la guía interactiva en [update.angular.io](https://update.angular.io/?v=21.0-22.0).

---

## Objetivo del día

Corre e2e/unit, despliega a staging y documenta rollback.

---

## Checklist rápido

1. Revisa `package.json` y versiones de Node/TypeScript compatibles.
2. Ejecuta las pruebas y el linter en una rama dedicada.
3. Aplica `ng update` solo cuando el entorno local esté limpio.
4. Documenta breaking changes que afecten a tu equipo.

---

## Comando de referencia

```bash
ng update @angular/core@<target> @angular/cli@<target>
```

---

## Siguiente paso

Continúa la serie al día siguiente para el siguiente bloque de la migración.
