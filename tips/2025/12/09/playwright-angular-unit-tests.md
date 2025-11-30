# 💡 Tip del Día: Playwright en Angular para Test Unitarios

---

## 🎯 ¿Por qué Playwright para Componentes?

¿Sabías que Playwright ahora tiene soporte experimental para testear componentes de Angular? Sí, ya no es solo para tests E2E. Esto es genial porque los tests de Playwright son **increíblemente rápidos** y se ejecutan en navegadores reales, lo que te da mucha más confianza que un entorno simulado como jsdom.

---

## 🚀 Ventajas de Playwright Component Testing

### 1. ⚡ Velocidad

Al no depender de toda la infraestructura de Angular TestBed para cada test, la ejecución es mucho más ligera y rápida.

### 2. 🌐 Navegadores Reales

Testeas en Chromium, Firefox y WebKit reales, no en simulaciones. Esto significa que capturas bugs que solo aparecen en navegadores específicos.

### 3. 🔒 Aislamiento

Los componentes se montan de forma aislada, lo que evita efectos secundarios entre tests y hace que sean más predecibles.

### 4. 🛠️ Herramientas Poderosas

Tienes acceso al Trace Viewer, grabación de video y screenshots automáticos cuando fallan los tests. Esto facilita muchísimo el debugging.

---

## 📦 Instalación

Primero, instala Playwright si no lo tienes:

```bash
npm init playwright@latest
```

Luego, instala el paquete experimental de componentes para Angular:

```bash
npm install --save-dev @playwright/experimental-ct-angular
```

---

## 🧪 Tu Primer Test de Componente

Crea un archivo `src/app/app.component.spec.ts` (o donde quieras tus tests) y prueba esto:

```typescript
import { test, expect } from '@playwright/experimental-ct-angular';
import { AppComponent } from './app.component';

test.use({ viewport: { width: 500, height: 500 } });

test('debería renderizar el título', async ({ mount }) => {
  const component = await mount(AppComponent);
  
  await expect(component).toContainText('Bienvenido a mi App');
});
```

### Explicación del Código

- **`test.use`**: Configura el viewport para el test
- **`mount`**: Monta el componente en un navegador real
- **`expect`**: Usa las aserciones de Playwright para verificar el comportamiento

---

## 🎨 Tests Más Avanzados

### Test con Inputs

```typescript
import { test, expect } from '@playwright/experimental-ct-angular';
import { UserCardComponent } from './user-card.component';

test('debería mostrar el nombre del usuario', async ({ mount }) => {
  const component = await mount(UserCardComponent, {
    props: {
      userName: 'Carlos Florio',
      userEmail: 'carlos@example.com'
    }
  });
  
  await expect(component).toContainText('Carlos Florio');
  await expect(component).toContainText('carlos@example.com');
});
```

### Test con Interacciones

```typescript
import { test, expect } from '@playwright/experimental-ct-angular';
import { CounterComponent } from './counter.component';

test('debería incrementar el contador al hacer click', async ({ mount }) => {
  const component = await mount(CounterComponent);
  
  // Verificar estado inicial
  await expect(component.locator('.count')).toHaveText('0');
  
  // Hacer click en el botón
  await component.locator('button.increment').click();
  
  // Verificar que el contador aumentó
  await expect(component.locator('.count')).toHaveText('1');
});
```

---

## 📝 Organizando Tests con `test.step`

Playwright permite organizar tests complejos en pasos usando `test.step`:

```typescript
import { test, expect } from '@playwright/experimental-ct-angular';
import { LoginFormComponent } from './login-form.component';

test('flujo completo de login', async ({ mount }) => {
  const component = await mount(LoginFormComponent);
  
  await test.step('Rellenar formulario', async () => {
    await component.locator('input[name="email"]').fill('user@example.com');
    await component.locator('input[name="password"]').fill('password123');
  });
  
  await test.step('Enviar formulario', async () => {
    await component.locator('button[type="submit"]').click();
  });
  
  await test.step('Verificar mensaje de éxito', async () => {
    await expect(component.locator('.success-message')).toBeVisible();
  });
});
```

**Ventajas de `test.step`:**
- 📊 Mejor organización visual en los reportes
- 🐛 Más fácil identificar en qué paso falló el test
- 📖 Tests más legibles y mantenibles

---

## 🆚 Comparación: TestBed vs Playwright

| Característica | Angular TestBed | Playwright CT |\n|----------------|-----------------|---------------|\n| **Velocidad** | Lento | Rápido |\n| **Navegadores** | jsdom (simulado) | Reales (Chromium, Firefox, WebKit) |\n| **Aislamiento** | Requiere configuración | Automático |\n| **Debugging** | Básico | Trace Viewer, videos, screenshots |\n| **Curva de aprendizaje** | Alta | Media |\n| **Madurez** | Estable | Experimental |\n\n---

## ⚠️ Consideraciones

### Estado Experimental

⚠️ **Importante**: El soporte para componentes de Angular en Playwright está en fase experimental. Esto significa:

- Puede haber cambios en la API
- No todas las features de Angular están soportadas
- Úsalo en proyectos nuevos o para experimentar

### Cuándo Usar Playwright CT

✅ **Úsalo cuando:**
- Necesitas tests rápidos de componentes
- Quieres probar en navegadores reales
- Necesitas debugging visual (screenshots, videos)
- Estás probando componentes aislados

❌ **No lo uses cuando:**
- Necesitas testear integración compleja con servicios
- El componente depende mucho del contexto de Angular
- Requieres estabilidad absoluta (usa TestBed)

---

## 🔗 Recursos Adicionales

- [Documentación Oficial de Playwright para Componentes](https://playwright.dev/docs/test-components)
- [Repositorio de Playwright](https://github.com/microsoft/playwright)
- [Ejemplos de Angular con Playwright](https://github.com/microsoft/playwright/tree/main/examples/components-angular)
- [Guía de Migración desde TestBed](https://playwright.dev/docs/test-components#migrating-from-other-frameworks)

---

## 📝 Resumen

- ✅ **Playwright Component Testing** permite testear componentes de Angular en navegadores reales
- ✅ Es **más rápido** que TestBed tradicional
- ✅ Proporciona **herramientas de debugging** superiores (Trace Viewer, videos, screenshots)
- ✅ Usa `test.step` para organizar tests complejos
- ⚠️ Está en fase **experimental** - úsalo con precaución en producción
- 🚀 Ideal para proyectos nuevos y componentes aislados

---

**¿Te gustó este tip?** ¡Dale una oportunidad a Playwright y dile adiós a la lentitud de los tests tradicionales! 🚀🧪
