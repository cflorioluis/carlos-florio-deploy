# Novedades (marzo 2026): TypeScript 6 RC y Node.js 24 🚀

Dos movimientos importantes en el ecosistema **TypeScript / Node** que conviene tener en el radar si haces backend, tooling o frontend con runtime Node.

## TypeScript 6.0 Release Candidate

Microsoft publicó el **RC de TypeScript 6.0** como paso previo a la siguiente gran fase del lenguaje. Entre las líneas generales:

- Sigue evolucionando el tipado y la compatibilidad con propuestas ECMAScript avanzadas.
- Es un buen momento para **probar en ramas de CI** y reportar regressions antes del release final.

**Fuente:** [Announcing TypeScript 6.0 RC](https://devblogs.microsoft.com/typescript/announcing-typescript-6-0-rc/) · [The New Stack](https://thenewstack.io/typescript-6-0-rc-arrives-as-a-bridge-to-a-faster-future/)

## Node.js 24 (Current)

Node **24** entra como rama *Current* con motor V8 actualizado y ecosistema npm al día. Punto que más ruido está generando: experimentos alrededor de **ejecutar TypeScript sin paso de compilación completo** en algunos flujos (strip de tipos / flags experimentales — revisa siempre la doc oficial para tu versión exacta).

**Fuente:** [Node.js v24.0.0 release](https://nodejs.org/blog/release/v24.0.0/)

## Qué hacer en tu equipo

1. **Pin** de versiones en CI (`engines`, Docker base image).
2. Probar **TS 6 RC** solo en proyectos no críticos o con flag de nightly.
3. Leer **breaking changes** y deprecations en las notas oficiales antes de subir major en producción.

## Resumen

| Tema | Idea clave |
|------|------------|
| TS 6 RC | Probar antes, preparar migraciones |
| Node 24 | Actualizar entorno y revisar features/flags nuevos |
