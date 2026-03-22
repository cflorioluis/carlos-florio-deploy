# UX: en botones e iconos, **acción** ≠ **estado** 🎯

**[Demo en vivo →](/tips/2026/3/17/ux-iconos-accion-no-estado/demo)** — compara contraseña con icono “estado” vs “acción” y un mini ejemplo play/pause.

Un error muy común en interfaces es que el **icono del control** describa el *estado actual* en lugar de la *acción que va a ocurrir* al pulsar. Eso aumenta la carga cognitiva: el usuario tiene que “traducir” qué pasará.

## Ejemplo clásico: mostrar / ocultar contraseña

### ❌ Icono = estado (“lo que está pasando”)

- Contraseña **oculta** → icono de **ojo tachado** (indica “está oculto”).
- Contraseña **visible** → icono de **ojo abierto** (indica “se ve”).

El usuario debe inferir: *“si está tachado, al pulsar… ¿se muestra?”*

### ✅ Icono = acción (“lo que hará el clic”)

- Contraseña **oculta** → icono de **ojo abierto** (“pulsa para **ver**”).
- Contraseña **visible** → icono de **ojo tachado** (“pulsa para **ocultar**”).

Así el icono anticipa el **efecto** del clic, como un botón normal.

## Regla práctica

- **Controles que disparan una acción** (botón, icono dentro de un campo, FAB): el icono debe sugerir **qué va a pasar**, no un diagnóstico del sistema.
- **Indicadores puros de estado** (badges “Activo”, LEDs, pills de sincronización): ahí sí tiene sentido mostrar **el estado actual**, y si hace falta acción, separar en **otro control** explícito.

## Más ejemplos donde aplica

| Situación | Evitar (solo estado) | Mejor (acción clara) |
|-----------|----------------------|----------------------|
| Reproducir audio | Icono “pausado” fijo | Play / Pause según lo que hará el clic |
| Favorito | Solo corazón relleno | Corazón vacío = “añadir”, relleno = “quitar” (o texto + icono) |
| Expandir panel | Icono de “ya expandido” | Chevron que indica “se abrirá” / “se cerrará” |

## Lectura recomendada

Principio alineado con buenas prácticas de **affordance** y **predictibilidad** en diseño de producto; el ejemplo de contraseña es uno de los que más se repite en auditorías UX.

> Inspiración visual: publicación de diseño de producto sobre *no confundir estado con acción* (ojo / ojo tachado en campos de contraseña).
