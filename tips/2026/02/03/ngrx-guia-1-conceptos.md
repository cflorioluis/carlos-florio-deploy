# 🗃️ NgRx (1/4): Conceptos básicos, para qué sirve y ventajas

Primer día de la guía NgRx: **qué es**, **para qué sirve** y **por qué usarlo** en aplicaciones Angular (por ejemplo en un sistema tipo POS o tienda).

---

## ¿Qué es NgRx?

**NgRx** es la implementación de **Redux** para Angular. Gestiona el **estado** de la aplicación de forma **predecible**: todo pasa por un flujo claro (acción → reducer → estado) y el estado vive en un **store** único (o por feature).

- **Estado centralizado**: una sola fuente de verdad.
- **Cambios predecibles**: las únicas formas de cambiar el estado son despachar **actions** y actualizar con **reducers**.
- **Herramientas**: DevTools para ver acciones y viajes en el tiempo.

---

## ¿Para qué sirve?

- Mantener el estado de la app (carrito, usuario, productos, UI) en un solo sitio.
- Evitar que cada componente tenga su propio estado duplicado y que los datos se desincronicen.
- Facilitar pruebas: reducers y selectors son funciones puras.
- Escalar: en apps grandes (dashboards, POS, CRMs) el flujo unidireccional evita bugs difíciles de rastrear.

---

## Ejemplo rápido: sistema POS

En una caja (POS) típica tienes:

- **Productos** en pantalla (lista desde el backend).
- **Carrito** de la venta actual (ítems, cantidades, descuentos).
- **Cliente** seleccionado (opcional).
- **Estado de la venta**: borrador, cobrando, cobrada, anulada.

Con NgRx:

- El **store** tiene (por ejemplo) `products`, `cart`, `customer`, `saleStatus`.
- **Actions**: `AddItemToCart`, `RemoveItemFromCart`, `SetCustomer`, `CheckoutRequest`, `CheckoutSuccess`.
- Los **reducers** actualizan el estado según la acción.
- Los **selectors** exponen datos derivados (total del carrito, ítems count, etc.).

Así, la pantalla de caja, el resumen del carrito y el componente de pago **leen del mismo estado** y **no se pasan eventos en cadena**; todos reaccionan al store.

---

## Ventajas en un sistema tipo POS

| Ventaja | Descripción |
|--------|-------------|
| **Una sola fuente de verdad** | Carrito y totales siempre coherentes en toda la app. |
| **Trazabilidad** | Con DevTools ves cada acción; útil para soporte y auditoría. |
| **Testing** | Reducers y selectors son fáciles de testear (entrada/salida). |
| **Escalabilidad** | Añadir pantallas (devoluciones, descuentos, múltiples pagos) es añadir acciones y estado, sin enredar componentes. |
| **Persistencia / rehidratación** | Puedes serializar el estado y recuperarlo (por ejemplo carrito en localStorage o tras recarga). |

---

## Resumen

NgRx sirve para **gestionar el estado** de forma **predecible y centralizada**. En un sistema POS (o cualquier app con bastante estado compartido), te da claridad, trazabilidad y una base sólida para crecer.

En los próximos días veremos **Actions**, **Reducers** y **Selectors** con ejemplos de código.

#angular #ngrx #redux #estado #pos
