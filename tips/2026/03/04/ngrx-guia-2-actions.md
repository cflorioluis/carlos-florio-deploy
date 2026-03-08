# 🎬 NgRx (2/4): Actions en detalle con ejemplos

Segundo día de la guía NgRx: **Actions** — qué son, cómo definirlas y cómo usarlas con ejemplos de código.

---

## ¿Qué es una Action?

Una **action** es un objeto que **describe algo que pasó** en la aplicación. Tiene al menos una propiedad `type` (y suele ir en `payload` los datos necesarios). Los **reducers** leen ese tipo y el payload para decidir cómo actualizar el estado.

- No modifican el estado: solo **describen** el hecho.
- Son la **única** forma de "pedir" un cambio al store (junto con effects si usas efectos).

---

## Creando actions (createAction)

Con `createAction` de `@ngrx/store` defines el tipo y, si hace falta, el payload tipado:

```typescript
import { createAction, props } from '@ngrx/store';

// Sin payload
export const loadProducts = createAction('[POS] Load Products');
export const clearCart = createAction('[POS] Clear Cart');

// Con payload
export const addItemToCart = createAction(
  '[POS] Add Item To Cart',
  props<{ productId: string; quantity: number }>()
);

export const setCustomer = createAction(
  '[POS] Set Customer',
  props<{ customerId: string | null }>()
);

export const checkoutSuccess = createAction(
  '[POS] Checkout Success',
  props<{ saleId: string }>()
);
```

El prefijo `[POS]` ayuda en DevTools a agrupar por feature.

---

## Despachando actions (Store.dispatch)

Desde un componente o un **effect**, despachas la acción con `store.dispatch`:

```typescript
import { Store } from '@ngrx/store';
import { addItemToCart, clearCart } from './actions/cart.actions';

@Component({ /* ... */ })
export class ProductListComponent {
  constructor(private store: Store<AppState>) {}

  onAddToCart(productId: string, quantity: number) {
    this.store.dispatch(addItemToCart({ productId, quantity }));
  }

  onClearCart() {
    this.store.dispatch(clearCart());
  }
}
```

El reducer (que veremos al día siguiente) reacciona a `addItemToCart` y `clearCart` y actualiza el estado.

---

## Buenas prácticas

- **Nombres descriptivos**: verbo en pasado o hecho ("Item Added", "Cart Cleared").
- **Payload mínimo**: solo lo necesario para que el reducer actualice el estado.
- **Un archivo por feature**: por ejemplo `cart.actions.ts`, `products.actions.ts`.
- **Props tipadas**: usa `props<{ ... }>()` para tener TypeScript y autocompletado.

---

## Resumen

Las **actions** son el "qué pasó". Las defines con `createAction`, las despachas con `store.dispatch` y los **reducers** (día 3) las usan para calcular el nuevo estado. Sin actions no hay cambios en el store.

#angular #ngrx #actions #redux
