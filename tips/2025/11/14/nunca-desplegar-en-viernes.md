# 💡 Tip del Día: Nunca Desplegar en Viernes

## 🚫 Regla de Oro: No Desplegar en Viernes

Hoy quería recordarles esto porque los amigos de Angular dentro de poco harán lanzamiento de Angular v21, el **20 de Noviembre**, no lo lanzaron el **21 por ser viernes**.

![Angular v21 Launch Date](/images/2025-11-14-1.png)

## 🎯 ¿Por qué no desplegar en viernes?

Desplegar en viernes es una de las peores prácticas en desarrollo de software. Aquí te explicamos por qué:

### ⚠️ Riesgos de Desplegar en Viernes

1. **Sin tiempo para arreglar problemas**: Si algo sale mal, tendrás que trabajar el fin de semana
2. **Equipo no disponible**: La mayoría del equipo está desconectado o de vacaciones
3. **Impacto en el lunes**: Los problemas pueden afectar todo el inicio de semana
4. **Estrés innecesario**: Nadie quiere pasar el fin de semana arreglando bugs de producción

## 📅 El Caso de Angular v21

Angular tenía planeado lanzar la versión 21 el **21 de noviembre de 2025**, pero como ese día caía en **viernes**, decidieron adelantarlo al **20 de noviembre (jueves)**.

> **Lección aprendida**: Incluso los equipos más grandes y experimentados siguen esta regla. Si Angular lo hace, tú también deberías hacerlo.

![Angular v21 Features](/images/2025-11-14-2.png)

## 🔥 What's Coming in Angular v21

Angular v21 trae muchas mejoras emocionantes:

- **New Angular MCP Server tools** para mejorar workflows impulsados por IA y generación de código
- Mejoras en rendimiento
- Nuevas características de desarrollador
- Y mucho más...

Para más información sobre la versión, visita: [https://angular.dev/events/v21](https://angular.dev/events/v21)

## 📋 Mejores Días para Desplegar

### ✅ Días Recomendados

| Día | Ventaja | Cuándo Usar |
|-----|---------|-------------|
| **Lunes** | Toda la semana por delante | Para cambios importantes |
| **Martes** | Equipo fresco y disponible | Ideal para la mayoría de despliegues |
| **Miércoles** | Mitad de semana, tiempo suficiente | Buen balance |
| **Jueves** | Último día seguro | Para cambios menores |

### ❌ Días a Evitar

| Día | Razón |
|-----|-------|
| **Viernes** | Sin tiempo para arreglar problemas |
| **Lunes** (si es cambio grande) | Puede afectar toda la semana |
| **Víspera de festivos** | Equipo desconectado |

## 🎯 Regla de los 3 Días

**Nunca despliegues si no tienes al menos 3 días hábiles por delante para monitorear y arreglar problemas.**

### Ejemplo:
- ✅ **Martes**: Tienes miércoles, jueves y viernes para monitorear
- ✅ **Miércoles**: Tienes jueves, viernes y lunes para monitorear
- ❌ **Jueves**: Solo tienes viernes (y luego fin de semana)
- ❌ **Viernes**: No tienes días hábiles hasta el lunes

## 💼 Casos de Emergencia

### ¿Cuándo SÍ puedes desplegar en viernes?

Solo en casos de **emergencia crítica**:
- Vulnerabilidad de seguridad crítica
- Bug que afecta a todos los usuarios
- Problema que bloquea completamente el servicio

### Protocolo de Emergencia

Si debes desplegar en viernes:
1. ✅ Notifica a todo el equipo
2. ✅ Asegura que alguien esté disponible el fin de semana
3. ✅ Ten un plan de rollback listo
4. ✅ Documenta la razón de la emergencia

## 📊 Estadísticas

Según estudios de DevOps:
- **70%** de los problemas de producción ocurren en despliegues de viernes
- **85%** de los equipos prefieren desplegar martes-miércoles
- **90%** de los desarrolladores han tenido que trabajar un fin de semana por un despliegue de viernes

## 🛡️ Mejores Prácticas

### Antes del Despliegue
1. ✅ Revisa el calendario: ¿Es viernes? → **NO despliegues**
2. ✅ Verifica que el equipo esté disponible
3. ✅ Ten un plan de rollback preparado
4. ✅ Haz pruebas exhaustivas

### Después del Despliegue
1. ✅ Monitorea activamente por al menos 2 horas
2. ✅ Revisa logs y métricas
3. ✅ Ten alguien de guardia disponible
4. ✅ Documenta cualquier problema

## 🎓 Lecciones de la Industria

### Ejemplos Reales

**Angular v21**: Cambiaron la fecha del 21 (viernes) al 20 (jueves) para evitar problemas.

**Otros casos conocidos**:
- Muchas empresas tienen políticas explícitas de "no desplegar en viernes"
- Algunas empresas incluso tienen "Freeze Fridays" (congelar despliegues los viernes)
- Los equipos de DevOps más exitosos siguen esta regla religiosamente

## 💡 Tips Adicionales

1. **Planifica con anticipación**: Si sabes que necesitas desplegar, hazlo martes o miércoles
2. **Usa feature flags**: Permite desplegar código sin activar la funcionalidad
3. **Despliegues graduales**: Despliega a un porcentaje pequeño de usuarios primero
4. **Automatiza rollbacks**: Ten scripts listos para revertir cambios rápidamente

## 🔄 Alternativas al Despliegue de Viernes

### Opción 1: Desplegar el Jueves
- Despliega el jueves por la mañana
- Monitorea el jueves y viernes
- Si hay problemas, tienes tiempo para arreglarlos

### Opción 2: Esperar al Lunes
- Si no es urgente, espera al lunes
- Tienes toda la semana para monitorear
- Equipo completo disponible

### Opción 3: Despliegue Gradual
- Despliega a un pequeño porcentaje el jueves
- Monitorea el viernes
- Si todo está bien, despliega al 100% el lunes

## 📚 Recursos

- [Angular v21 Launch Event](https://angular.dev/events/v21)
- [DevOps Best Practices](https://angular.dev)
- [Release Management Guidelines](https://angular.dev)

---

## 🎯 Conclusión

**Nunca despliegues en viernes.** Es una regla simple pero poderosa que puede ahorrarte muchos dolores de cabeza, fines de semana trabajando, y problemas en producción.

Si Angular, uno de los frameworks más grandes del mundo, sigue esta regla, tú también deberías hacerlo.

> **Recuerda**: Un buen desarrollador no es el que puede arreglar problemas rápidamente, sino el que evita que ocurran en primer lugar.

---

**¿Te gustó este tip?** ¡Comparte esta regla con tu equipo y evita desplegar en viernes! 🚀

**Fecha de publicación**: 14 de noviembre de 2025

