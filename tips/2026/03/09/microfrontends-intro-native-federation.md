# 🧩 Introducción a microfrontends: por qué Native Federation (Angular + React + Vue)

Primer día de la guía: **qué son los microfrontends**, **por qué elegir Native Federation** cuando mezclas frameworks (Angular, React, Vue) y **qué vamos a construir** en los próximos días: un **host en Angular** que carga **remotes en React y Vue**.

---

## ¿Qué es un microfrontend?

Un **microfrontend** es una forma de dividir una aplicación web en **varias aplicaciones independientes** (cada una con su propio equipo, framework o ciclo de despliegue) que se integran en **una sola shell** que el usuario ve como una única app.

- **Host (shell):** la aplicación contenedora que muestra la barra de navegación, el layout y **carga** los fragmentos.
- **Remotes:** aplicaciones separadas que **exponen** módulos o componentes (rutas, widgets, pantallas) para que el host los cargue en tiempo de ejecución.

Cada remote puede estar desarrollado con **otro framework** (React, Vue, Angular) y desplegado por separado. El usuario navega en una sola URL y ve todo integrado.

---

## Module Federation vs Native Federation

Hay dos enfoques muy usados para “federar” módulos entre host y remotes:

| | Module Federation (Webpack) | Native Federation |
|--|-----------------------------|--------------------|
| **Base** | Webpack 5, protocolo propio | ESM nativo + import maps |
| **Bundler** | Todos deben usar Webpack | Vite, esbuild, Rspack… cada uno el suyo |
| **Contrato** | Contenedores Webpack | Módulos JavaScript estándar |
| **Multi‑framework** | Sí (React + Vue + Angular), pero todos construyen con Webpack | Sí; cada equipo puede usar su stack (Vite+React, Vite+Vue, Angular+esbuild) |
| **Futuro** | Webpack en mantenimiento | Alineado con Vite y el ecosistema actual |

**Conclusión:** Para una app donde **cada parte use un framework distinto** (por ejemplo host en **Angular** y remotes en **React** y **Vue**), **Native Federation** encaja mejor: contrato ESM, sin atar a todo el mundo a Webpack, y cada app puede usar su bundler moderno.

---

## Por qué Native Federation para Angular + React + Vue

1. **Un solo host, varios remotes:** El **host** será una app **Angular**; los **remotes** serán una app **React** y una app **Vue**. Cada una se construye y despliega por su cuenta; el host las carga en tiempo de ejecución.

2. **Contrato estándar:** Lo que se “federado” son **módulos ESM**. Angular carga esos módulos con `loadRemoteModule`; no importa si el código viene de un build de React (Vite) o de Vue (Vite).

3. **Dependencias compartidas:** Se puede definir qué librerías son **shared** (por ejemplo una sola versión de React para el host y el remote React) para no duplicar runtimes y evitar conflictos.

4. **Comunicación:** Host y remotes pueden comunicarse por **CustomEvent**, callbacks o un pequeño bus, sin acoplar implementaciones.

En los próximos días veremos **paso a paso** cómo montar ese host Angular y los remotes React y Vue con Native Federation, con ejemplos de código detallados para cada parte (**Angular**, **React**, **Vue**) y un ejemplo que iremos ampliando día a día.

---

## Qué vamos a construir (resumen)

- **Día 10:** Conceptos de Native Federation (host, remote, ESM, shared). Crear el workspace con **tres proyectos**: app **Angular** (host), app **React** (remote), app **Vue** (remote). Configuración mínima de Native Federation en cada uno.
- **Día 11:** Exponer componentes desde **React** y **Vue** y cargarlos en el **host Angular**. Ver el primer “hola mundo” de cada remote dentro del shell.
- **Día 12:** **Shared** dependencies y **comunicación** entre host y remotes (eventos, props).
- **Día 13:** **Builds de producción**, despliegue y buenas prácticas.

Cada día indicaremos claramente qué fragmento de código corresponde a **Angular**, **React** o **Vue** para que puedas seguir el ejemplo en tu propio repo.

---

## Resumen

Los **microfrontends** permiten tener una sola aplicación vista por el usuario aunque esté compuesta por varias apps (host + remotes). Para mezclar **Angular, React y Vue**, **Native Federation** es una opción sólida: basada en ESM, independiente del bundler y adecuada para que cada equipo use su framework. En esta guía usaremos **Angular como host** y **React y Vue como remotes**; a partir del siguiente tip entraremos en la implementación con código detallado.

#microfrontends #native-federation #angular #react #vue
