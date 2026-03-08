# ⚙️ NgRx (3/4): Reducers en detalle con ejemplos

Tercer día de la guía NgRx: **Reducers** — cómo definirlos, reglas inmutables y ejemplos de código.

---

## ¿Qué es un Reducer?

Un **reducer** es una **función pura** que recibe el **estado actual** y una **acción** y devuelve el **nuevo estado**. No muta el estado: devuelve uno nuevo. NgRx llama a los reducers cuando despachas una acción y usa el resultado para actualizar el store.

- **Entrada**: `(state, action)`
- **Salida**: nuevo estado (mismo tipo que el estado del slice).

---

## Creando un reducer (createReducer)

Con `createReducer` y el estado inicial evitas el típico `switch` y ganas tipado:

```typescript
import { createReducer, on } from '@ngrx/store';
import { addItemToCart, removeItemFromCart, clearCart } from '../actions/cart.actions';

export interface CartState {
  items: { productId: string; quantity: number }[];
}

const initialState: CartState = {
  items: []
};

export const cartReducer = createReducer(
  initialState,
  on(addItemToCart, (state, { productId, quantity }) => {
    const existing = state.items.find(i => i.productId === productId);
    const newItems = existing
      ? state.items.map(i =>
          i.productId === productId
            ? { ...i, quantity: i.quantity + quantity }
            : i
        )
      : [...state.items, { productId, quantity }];
    return { ...state, items: newItems };
  }),
  on(removeItemFromCart, (state, { productId }) => ({
    ...state,
    items: state.items.filter(i => i.productId !== productId)
  })),
  on(clearCart, () => initialState)
);
```

Cada `on(accion, (state, payload) => nuevoState)` maneja una acción y devuelve un **nuevo** objeto de estado.

---

## Inmutabilidad

- **No hagas** `state.items.push(...)` ni `state.xxx = yyy`.
- **Sí haz** `return { ...state, items: [...state.items, newItem] }` o copias equivalentes.
- Así NgRx y las herramientas detectan cambios y los componentes que usan selectors se re-renderizan bien.

---

## Registrar el reducer en el Store

En tu módulo (o en `provideStore` en standalone):

```typescript
import { StoreModule } from '@ngrx/store';
import { cartReducer } from './reducers/cart.reducer';

@NgModule({
  imports: [
    StoreModule.forRoot({ cart: cartReducer })
    // o StoreModule.forFeature('pos', { cart: cartReducer })
  ]
})
export class AppModule {}
```

El estado global tendrá `state.cart` con la forma de `CartState`.

---

## Resumen

Los **reducers** son funciones puras que, dado el estado actual y una acción, devuelven el nuevo estado. Con `createReducer` y `on` el código queda claro y tipado. Inmutabilidad obligatoria. Al día siguiente: **selectors** para leer y derivar datos del store.

#angular #ngrx #reducers #redux #inmutabilidad
