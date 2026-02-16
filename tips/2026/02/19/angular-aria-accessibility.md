# Angular 21: Angular Aria - Accesibilidad de Primera Clase ♿

Angular 21 introduce **Angular Aria** en Developer Preview: un paquete dedicado para hacer tus aplicaciones más accesibles. ¡La accesibilidad ya no es opcional!

## ¿Qué es Angular Aria? 🤔

Un conjunto de directivas, componentes y utilidades que facilitan la implementación de **ARIA** (Accessible Rich Internet Applications) en tus aplicaciones Angular.

## Instalación 📦

```bash
npm install @angular/cdk-experimental-aria
```

## Directivas ARIA Automáticas 🎯

### aria-label Dinámico

```typescript
import { AriaLabelDirective } from '@angular/cdk-experimental-aria';

@Component({
  selector: 'app-user-card',
  standalone: true,
  imports: [AriaLabelDirective],
  template: `
    <button 
      [ariaLabel]="'Eliminar usuario ' + user.name"
      (click)="deleteUser()"
    >
      🗑️
    </button>
  `
})
export class UserCardComponent {
  user = { name: 'Carlos Florio' };
}
```

### Live Regions para Notificaciones 📢

```typescript
import { LiveAnnouncer } from '@angular/cdk-experimental-aria';

@Component({
  selector: 'app-notification',
  standalone: true
})
export class NotificationComponent {
  private liveAnnouncer = inject(LiveAnnouncer);

  showSuccess(message: string) {
    // Anuncia el mensaje a lectores de pantalla
    this.liveAnnouncer.announce(message, 'polite');
  }

  showError(message: string) {
    // Anuncia inmediatamente (interrumpe)
    this.liveAnnouncer.announce(message, 'assertive');
  }
}
```

```html
<!-- El LiveAnnouncer crea automáticamente: -->
<div aria-live="polite" aria-atomic="true" class="cdk-visually-hidden">
  Usuario guardado correctamente
</div>
```

## Focus Management 🎯

### FocusTrap para Modales

```typescript
import { FocusTrap } from '@angular/cdk-experimental-aria';

@Component({
  selector: 'app-modal',
  standalone: true,
  template: `
    <div class="modal-overlay" (click)="close()">
      <div 
        class="modal-content" 
        cdkTrapFocus
        [cdkTrapFocusAutoCapture]="true"
        (click)="$event.stopPropagation()"
      >
        <h2>Confirmar Acción</h2>
        <p>¿Estás seguro de que quieres continuar?</p>
        
        <button (click)="confirm()">Confirmar</button>
        <button (click)="close()">Cancelar</button>
      </div>
    </div>
  `
})
export class ModalComponent {
  // El foco queda atrapado dentro del modal
  // Tab solo navega entre los botones
}
```

### Focus Monitor

```typescript
import { FocusMonitor } from '@angular/cdk-experimental-aria';

@Component({
  selector: 'app-custom-input',
  standalone: true
})
export class CustomInputComponent implements OnInit, OnDestroy {
  private focusMonitor = inject(FocusMonitor);
  private elementRef = inject(ElementRef);

  ngOnInit() {
    this.focusMonitor.monitor(this.elementRef, true)
      .subscribe(origin => {
        // origin: 'keyboard' | 'mouse' | 'touch' | 'program' | null
        if (origin === 'keyboard') {
          console.log('Navegación por teclado detectada');
        }
      });
  }

  ngOnDestroy() {
    this.focusMonitor.stopMonitoring(this.elementRef);
  }
}
```

## Roles y Estados ARIA 🏷️

```typescript
@Component({
  selector: 'app-tabs',
  standalone: true,
  template: `
    <div role="tablist" aria-label="Configuración">
      @for (tab of tabs; track tab.id) {
        <button
          role="tab"
          [attr.aria-selected]="selectedTab === tab.id"
          [attr.aria-controls]="'panel-' + tab.id"
          [tabindex]="selectedTab === tab.id ? 0 : -1"
          (click)="selectTab(tab.id)"
        >
          {{ tab.label }}
        </button>
      }
    </div>

    @for (tab of tabs; track tab.id) {
      <div
        role="tabpanel"
        [id]="'panel-' + tab.id"
        [attr.aria-labelledby]="'tab-' + tab.id"
        [hidden]="selectedTab !== tab.id"
      >
        {{ tab.content }}
      </div>
    }
  `
})
export class TabsComponent {
  tabs = [
    { id: 'general', label: 'General', content: '...' },
    { id: 'security', label: 'Seguridad', content: '...' },
    { id: 'privacy', label: 'Privacidad', content: '...' }
  ];
  
  selectedTab = 'general';
}
```

## Utilidades de Accesibilidad 🛠️

### Clase Visually Hidden

```typescript
import { A11yModule } from '@angular/cdk-experimental-aria';

@Component({
  template: `
    <button>
      <span class="cdk-visually-hidden">Cerrar modal</span>
      ✕
    </button>
  `
})
export class CloseButtonComponent {}
```

```css
/* Generado automáticamente por Angular Aria */
.cdk-visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
```

## Navegación por Teclado ⌨️

```typescript
import { ListKeyManager } from '@angular/cdk-experimental-aria';

@Component({
  selector: 'app-menu',
  standalone: true
})
export class MenuComponent implements AfterViewInit {
  @ViewChildren(MenuItemComponent) items!: QueryList<MenuItemComponent>;
  private keyManager!: ListKeyManager<MenuItemComponent>;

  ngAfterViewInit() {
    this.keyManager = new ListKeyManager(this.items)
      .withWrap() // Vuelve al inicio al llegar al final
      .withTypeAhead() // Búsqueda por tecleo
      .withHomeAndEnd(); // Soporte para Home/End

    this.keyManager.setFirstItemActive();
  }

  onKeydown(event: KeyboardEvent) {
    this.keyManager.onKeydown(event);
  }
}
```

## Testing de Accesibilidad 🧪

```typescript
import { TestBed } from '@angular/core/testing';
import { axe, toHaveNoViolations } from 'jest-axe';

expect.extend(toHaveNoViolations);

describe('ButtonComponent Accessibility', () => {
  it('should have no accessibility violations', async () => {
    const fixture = TestBed.createComponent(ButtonComponent);
    const results = await axe(fixture.nativeElement);
    
    expect(results).toHaveNoViolations();
  });
});
```

## Mejores Prácticas 💡

1. **Siempre usa labels**: Cada input debe tener un label asociado
2. **Roles semánticos**: Usa elementos HTML nativos cuando sea posible
3. **Contraste de colores**: Mínimo 4.5:1 para texto normal
4. **Navegación por teclado**: Todo debe ser accesible sin mouse
5. **Textos alternativos**: Todas las imágenes deben tener alt text

¡Haz que tu aplicación Angular sea accesible para todos! 🌍♿
