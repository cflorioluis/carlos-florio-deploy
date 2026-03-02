# 🌳 Tip del Día: git worktree - Varias ramas, varias carpetas

**git worktree** permite tener **varias ramas en carpetas distintas** del mismo repositorio. Sin stasheos, sin clonar de nuevo: una carpeta por rama.

## Crear un worktree

```bash
# Crear carpeta feature-ui con la rama feature/ui
git worktree add ../mi-proyecto-feature-ui feature/ui

# O crear rama nueva y worktree a la vez
git worktree add -b hotfix/urgente ../mi-proyecto-hotfix main
```

Ahora tienes:

- `./` → rama actual (ej. `main`)
- `../mi-proyecto-feature-ui` → rama `feature/ui`
- `../mi-proyecto-hotfix` → rama `hotfix/urgente`

## Listar worktrees

```bash
git worktree list
```

## Eliminar un worktree

```bash
# Primero sal de esa carpeta y luego:
git worktree remove ../mi-proyecto-feature-ui

# Si la carpeta ya no existe pero Git la sigue listando:
git worktree prune
```

## Casos de uso

- Revisar un PR en una carpeta mientras sigues trabajando en otra rama.
- Comparar dos ramas abriendo dos instancias del IDE.
- Hacer un hotfix sin tocar tu rama de desarrollo.

Un solo repo, varias "copias de trabajo" independientes. 🎯

#git #workflow #productividad #terminal
