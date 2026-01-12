# Git Stash: Guardar Cambios Temporalmente 💾

¿Alguna vez has estado trabajando en algo y de repente necesitas cambiar de rama urgentemente? No quieres hacer commit de código a medio terminar, pero tampoco quieres perder tus cambios. **Git Stash** es la solución perfecta.

## ¿Qué es Git Stash?

Git Stash guarda temporalmente tus cambios (staged y unstaged) sin hacer commit, dejando tu working directory limpio. Es como guardar un borrador que puedes recuperar después.

## Comandos Básicos

### Guardar cambios
```bash
# Guarda todos los cambios (staged y unstaged)
git stash

# Guarda con un mensaje descriptivo
git stash save "WIP: implementando login"

# Incluye archivos untracked también
git stash -u

# Incluye TODO (incluso archivos ignorados)
git stash -a
```

### Ver stashes guardados
```bash
# Lista todos los stashes
git stash list

# Salida:
# stash@{0}: WIP on main: 5c3d2a1 Add user profile
# stash@{1}: WIP on feature: 8f9e1b2 Update API
# stash@{2}: On develop: 3a7c4d5 Fix bug
```

### Recuperar cambios
```bash
# Aplica el stash más reciente y lo elimina
git stash pop

# Aplica un stash específico
git stash pop stash@{1}

# Aplica el stash pero NO lo elimina
git stash apply

# Aplica un stash específico sin eliminarlo
git stash apply stash@{2}
```

### Eliminar stashes
```bash
# Elimina el stash más reciente
git stash drop

# Elimina un stash específico
git stash drop stash@{1}

# Elimina TODOS los stashes
git stash clear
```

## Casos de Uso Prácticos

### 1. Cambio de Rama Urgente
```bash
# Estás en feature/login trabajando
git stash save "Login form validation"

# Cambias a otra rama para un hotfix
git checkout main
git checkout -b hotfix/critical-bug

# Haces el fix y vuelves
git checkout feature/login
git stash pop
```

### 2. Probar Algo Rápido
```bash
# Guardas tus cambios actuales
git stash

# Pruebas algo diferente
# ... haces cambios experimentales ...

# Si no funciona, descartas todo
git reset --hard

# Recuperas tu trabajo original
git stash pop
```

### 3. Aplicar Cambios en Otra Rama
```bash
# Estás en la rama equivocada
git stash

# Cambias a la rama correcta
git checkout correct-branch

# Aplicas los cambios
git stash pop
```

## Comandos Avanzados

### Ver contenido de un stash
```bash
# Muestra los cambios en el stash más reciente
git stash show

# Muestra el diff completo
git stash show -p

# Muestra un stash específico
git stash show -p stash@{1}
```

### Crear rama desde un stash
```bash
# Crea una nueva rama y aplica el stash
git stash branch nueva-feature

# Útil cuando el stash tiene conflictos
git stash branch fix-conflicts stash@{1}
```

### Stash parcial (solo algunos archivos)
```bash
# Modo interactivo para elegir qué guardar
git stash -p

# Te pregunta por cada cambio:
# Stash this hunk [y,n,q,a,d,e,?]?
# y = yes, n = no, q = quit, a = all, d = don't stash
```

## Tip Pro 💡

Puedes usar `git stash` para limpiar rápidamente tu working directory y ver si los tests pasan sin tus cambios:

```bash
# Guarda todo
git stash

# Ejecuta los tests
npm test

# Si pasan, tus cambios no rompieron nada
# Si fallan, el problema ya existía

# Recupera tus cambios
git stash pop
```

## Workflow Recomendado

```bash
# 1. Guarda con mensaje descriptivo
git stash save "WIP: refactoring user service"

# 2. Haz lo que necesites hacer
git checkout other-branch
# ... trabajo urgente ...

# 3. Vuelve a tu rama
git checkout original-branch

# 4. Revisa qué guardaste
git stash show -p

# 5. Recupera tus cambios
git stash pop

# 6. Si hay conflictos, resuélvelos
# Git te mostrará los conflictos como en un merge
```

## Errores Comunes a Evitar ⚠️

```bash
# ❌ NO hagas stash de commits
# Stash es para cambios NO commiteados

# ❌ NO uses stash como almacenamiento permanente
# Usa branches para trabajo a largo plazo

# ✅ Usa stash para cambios temporales
# ✅ Dale nombres descriptivos a tus stashes
# ✅ Limpia stashes viejos regularmente
```

---

**Resumen rápido:**
- `git stash` = guarda cambios temporalmente
- `git stash pop` = recupera y elimina el stash
- `git stash apply` = recupera sin eliminar
- `git stash list` = ve todos los stashes guardados
- Perfecto para cambios de rama urgentes 🚀
