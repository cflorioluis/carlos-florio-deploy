# 💡 Tip del Día: Navegación Rápida de Directorios en la Terminal

## 📁 Atajos para Navegar entre Directorios

_En colaboración con [Paco / Francisco Stefanov](mailto:francisco.andrey@fourvenues.com)_

---

## 🎯 El Problema Común

¿Sabías que para regresar de directorios en la terminal se hace `cd ..`? Y si necesitas ir para atrás varios niveles, muchos ejecutan `cd ..` tantas veces como directorios necesiten para ir hacia atrás.

Pero hay maneras de hacerlo **más rápido**.

---

## 🚀 Solución 1: Ir Varios Niveles Atrás de una Vez

### Método Tradicional (Lento)

```bash
# Si necesitas subir 3 niveles, haces esto:
cd ..
cd ..
cd ..
```

### Método Rápido (Recomendado)

```bash
# Puedes hacerlo todo en un solo comando:
cd ../../../
```

**Cómo funciona**: Cada `../` sube un nivel en la jerarquía de directorios.

### Ejemplos Prácticos

```bash
# Subir 1 nivel
cd ../

# Subir 2 niveles
cd ../../

# Subir 3 niveles
cd ../../../

# Subir 4 niveles
cd ../../../../

# Y así sucesivamente...
```

---

## ⚡ Solución 2: Abreviaciones en iTerm2

Si usas **iTerm2** (la terminal que usamos acá), tienes estas abreviaciones súper útiles:

### Abreviaciones Disponibles

| Abreviación | Equivale a | Niveles que sube |
|-------------|------------|------------------|
| `..` | `cd ../` | 1 nivel |
| `...` | `cd ../../` | 2 niveles |
| `....` | `cd ../../../` | 3 niveles |
| `.....` | `cd ../../../../` | 4 niveles |

### Ejemplos de Uso

```bash
# Estás en: /Users/usuario/proyecto/src/components
..      # Te lleva a: /Users/usuario/proyecto/src
...     # Te lleva a: /Users/usuario/proyecto
....    # Te lleva a: /Users/usuario
```

### ¿Cómo Activar en iTerm2?

Estas abreviaciones funcionan automáticamente en iTerm2 si tienes configurado el shell correctamente (Bash o Zsh). No necesitas configuración adicional.

---

## 🔄 Solución 3: `cd -` - El Truco del Directorio Anterior

### ¿Qué hace `cd -`?

El comando `cd -` te permite **volver al directorio en el que estabas antes** de hacer el último `cd`. Es como un "undo" de navegación.

### Ejemplo Práctico

```bash
# Estás en /algo
pwd
# Output: /algo

# Vas a otro directorio
cd /otra/cosa
pwd
# Output: /otra/cosa

# Vuelves al directorio anterior
cd -
pwd
# Output: /algo

# Puedes volver a usar cd - para volver a /otra/cosa
cd -
pwd
# Output: /otra/cosa
```

### Casos de Uso Reales

#### 1. **Alternar entre dos directorios**

```bash
# Trabajando en el frontend
cd ~/proyecto/frontend
# Haces cambios...

# Necesitas revisar algo en el backend
cd ~/proyecto/backend
# Revisas...

# Vuelves al frontend rápidamente
cd -
# ¡Estás de vuelta en frontend!
```

#### 2. **Navegación temporal**

```bash
# Estás en tu proyecto
cd ~/mi-proyecto

# Necesitas revisar algo en otro lugar
cd /tmp/algo-rapido

# Vuelves a tu proyecto
cd -
```

#### 3. **Scripts y automatización**

```bash
# En un script, puedes guardar el directorio actual
ORIGINAL_DIR=$(pwd)
cd /otro/lugar
# Haces algo...
cd "$ORIGINAL_DIR"  # O simplemente: cd -
```

---

## 📊 Comparación de Métodos

### Escenario: Subir 3 niveles

| Método | Comando | Velocidad | Compatibilidad |
|--------|---------|-----------|----------------|
| **Tradicional** | `cd ..` (3 veces) | ⭐⭐ Lento | ✅ Todos los shells |
| **Concatenado** | `cd ../../../` | ⭐⭐⭐⭐ Rápido | ✅ Todos los shells |
| **iTerm2** | `....` | ⭐⭐⭐⭐⭐ Muy rápido | ⚠️ Solo iTerm2/Bash/Zsh |

### Escenario: Volver al directorio anterior

| Método | Comando | Velocidad | Compatibilidad |
|--------|---------|-----------|----------------|
| **Manual** | `cd /ruta/completa/anterior` | ⭐⭐ Lento | ✅ Todos los shells |
| **cd -** | `cd -` | ⭐⭐⭐⭐⭐ Muy rápido | ✅ Todos los shells |

---

## 🎓 Más Trucos de Navegación

### 1. **Ir al directorio home**

```bash
# Todas estas opciones te llevan a tu home:
cd
cd ~
cd $HOME
```

### 2. **Ir al directorio anterior del historial**

```bash
# En algunos shells (Zsh con oh-my-zsh)
cd -1  # Directorio anterior
cd -2  # Dos directorios atrás
cd -3  # Tres directorios atrás
```

### 3. **Usar `pushd` y `popd` para una pila de directorios**

```bash
# Guarda el directorio actual y va a otro
pushd /otro/directorio

# Vuelve al directorio guardado
popd

# Puedes hacer múltiples pushd y luego popd en orden inverso
pushd /dir1
pushd /dir2
pushd /dir3
popd  # Vuelve a /dir2
popd  # Vuelve a /dir1
popd  # Vuelve al original
```

### 4. **Ver el directorio actual**

```bash
# Ver dónde estás
pwd

# O en algunos shells, el prompt ya lo muestra
```

### 5. **Navegación con autocompletado**

```bash
# Presiona Tab para autocompletar rutas
cd /usr/loc<Tab>  # Se completa a /usr/local
```

### 6. **`!!` - Repetir el Último Comando**

Un truco súper útil: `!!` repite el último comando que ejecutaste en esa terminal.

```bash
# Ejecutas un comando
npm install

# Si necesitas ejecutarlo de nuevo (quizás con sudo)
sudo !!
# Esto ejecuta: sudo npm install
```

**Cómo funciona**: `!!` se expande al último comando del historial.

#### Ejemplos Prácticos

```bash
# Ejemplo 1: Comando que necesita permisos
ls -la /usr/local
# Te das cuenta que necesitas sudo
sudo !!
# Ejecuta: sudo ls -la /usr/local

# Ejemplo 2: Comando largo que quieres repetir
npm run build -- --prod --source-map
# Si falla y quieres intentar de nuevo
!!
# Ejecuta el mismo comando completo

# Ejemplo 3: Ver qué ejecutará antes de hacerlo
echo !!
# Muestra el último comando sin ejecutarlo
```

### 7. **Flecha Hacia Arriba (↑) - Historial de Comandos**

La **flecha hacia arriba** (↑) invoca el último comando del historial. Es una de las formas más comunes de repetir comandos.

#### Características Importantes

- ✅ **Historial compartido**: El historial se comparte entre todas las terminales abiertas
- ✅ **Navegación**: Puedes usar ↑ y ↓ para navegar por el historial
- ✅ **Búsqueda**: En muchos shells puedes presionar `Ctrl+R` para buscar en el historial

#### Ejemplos

```bash
# Ejecutas un comando
cd ~/proyecto/src

# Presionas ↑ (flecha arriba)
# Aparece: cd ~/proyecto/src
# Presionas Enter para ejecutarlo de nuevo

# O puedes modificarlo antes de ejecutar
# ↑ para traer el comando, luego editas y Enter
```

#### Búsqueda en el Historial

```bash
# Presiona Ctrl+R y empieza a escribir
# El shell buscará comandos que coincidan

# Ejemplo:
# Ctrl+R, luego escribes "npm"
# Te muestra comandos recientes que contienen "npm"
```

#### Comandos Relacionados con el Historial

```bash
# Ver el historial completo
history

# Ver los últimos 10 comandos
history | tail -10

# Buscar en el historial
history | grep "npm"

# Ejecutar un comando del historial por número
!123  # Ejecuta el comando número 123 del historial

# Ejecutar el último comando que empiece con "cd"
!cd

# Ejecutar el último comando que contenga "npm"
!?npm
```

---

## 💻 Ejemplos por Sistema Operativo

### Linux / macOS (Bash/Zsh)

```bash
# Todas las opciones funcionan
cd ../../
cd -
..
...
....
```

### Windows (CMD)

```cmd
REM cd - NO funciona en CMD
cd ..\..\..\

REM Para volver, necesitas recordar la ruta o usar pushd/popd
pushd C:\otro\directorio
popd
```

### Windows (PowerShell)

```powershell
# cd - funciona en PowerShell
cd ../../
cd -

# También puedes usar
Set-Location -Stack
Push-Location C:\otro\directorio
Pop-Location
```

### Windows (Git Bash / WSL)

```bash
# Funciona igual que Linux/Mac
cd ../../
cd -
..
...
....
```

---

## 🎯 Casos de Uso Reales

### Caso 1: Desarrollo Full-Stack

```bash
# Trabajando en el frontend
cd ~/proyecto/frontend/src/components
# Haces cambios...

# Necesitas revisar el backend
cd ~/proyecto/backend/api
# Revisas...

# Vuelves al frontend
cd -  # ¡Rápido y fácil!
```

### Caso 2: Navegación Profunda

```bash
# Estás muy profundo en una estructura
cd ~/proyecto/src/app/components/forms/inputs

# Necesitas ir a la raíz del proyecto
cd ../../../../  # O simplemente: ....

# O si usas iTerm2:
....
```

### Caso 3: Alternar entre Proyectos

```bash
# Proyecto A
cd ~/proyecto-a/src

# Cambias a proyecto B
cd ~/proyecto-b/src

# Vuelves a proyecto A
cd -

# Vuelves a proyecto B
cd -
```

---

## 🔧 Configuración Avanzada

### Alias Útiles

Puedes crear aliases para hacer la navegación aún más rápida:

```bash
# Agregar a ~/.bashrc o ~/.zshrc

# Subir 2 niveles
alias ..='cd ../..'

# Subir 3 niveles
alias ...='cd ../../..'

# Subir 4 niveles
alias ....='cd ../../../..'

# Ir al directorio anterior
alias back='cd -'
```

### Función Personalizada para Navegación

```bash
# Agregar a ~/.bashrc o ~/.zshrc
up() {
  local levels=${1:-1}
  local path=""
  for ((i=0; i<levels; i++)); do
    path="../$path"
  done
  cd "$path"
}

# Uso:
up      # Sube 1 nivel
up 2    # Sube 2 niveles
up 3    # Sube 3 niveles
```

---

## 📝 Resumen de Comandos

### Navegación Básica

```bash
cd ..              # Subir 1 nivel
cd ../..           # Subir 2 niveles
cd ../../..        # Subir 3 niveles
cd ../../../..     # Subir 4 niveles
```

### Abreviaciones iTerm2 (Bash/Zsh)

```bash
..                 # Subir 1 nivel
...                # Subir 2 niveles
....               # Subir 3 niveles
.....              # Subir 4 niveles
```

### Directorio Anterior

```bash
cd -               # Volver al directorio anterior
```

### Historial de Comandos

```bash
!!                 # Repetir el último comando
↑ (flecha arriba) # Navegar por el historial
Ctrl+R            # Buscar en el historial
history           # Ver historial completo
!123              # Ejecutar comando #123 del historial
!cd               # Ejecutar último comando que empiece con "cd"
```

### Otros Útiles

```bash
cd                 # Ir al home
cd ~               # Ir al home
pwd                # Ver directorio actual
pushd <dir>        # Guardar y cambiar
popd               # Volver al guardado
```

---

## ✅ Conclusión

Estos trucos de navegación te permiten:

- ✅ **Ahorrar tiempo** navegando más rápido entre directorios
- ✅ **Ser más eficiente** con menos comandos
- ✅ **Alternar fácilmente** entre directorios con `cd -`
- ✅ **Navegar múltiples niveles** de una vez

**Los más útiles del día a día**:
1. `cd -` - Para volver al directorio anterior (funciona en todos lados)
2. `cd ../../` - Para subir varios niveles de una vez
3. `..`, `...`, `....` - Si usas iTerm2, son súper rápidos
4. `!!` - Repetir el último comando (¡muy útil!)
5. `↑` (flecha arriba) - Navegar por el historial (el historial se comparte entre terminales)

---

**¿Conocías estos trucos?** ¡Pruébalos y mejora tu productividad en la terminal! 🚀

**En colaboración con**: [Paco / Francisco Stefanov](mailto:francisco.andrey@fourvenues.com)

**Fecha de publicación**: 18 de noviembre de 2025

