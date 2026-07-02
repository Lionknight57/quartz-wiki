---
title: Angular
tags: [framework, frontend, typescript, javascript]
related: [TypeScript, Dependency Injection, RxJS, Unit Testing, CSharp]
sources: [Angular 2.md, Angular/Angular Core Module.md, Angular/Angular Best Practices.md, Angular/RxJS and Angular Signals Fundamentals.md, Angular/NgRx.md, Angular/Routing.md, AngularJS.md, Migrating Angjs to Angular2.md, Angular/Reactive Forms.md, Angular/RxJS.md, Angular/Angular Unit Testing.md, Angular Unit Testing.md, Angular/SSR.md, Angular/Templates.md, Angular/Directives.md, Angular/RxJs in Practice.md, Angular/Reactive Angular Course.md, Angular/Angular 2 2.md, Angular/AngularJS 2.md, Test Angular2+.md]
updated: 2026-04-12
---

# Angular

Google's component-based frontend framework. Written in TypeScript. Covers the full application stack — templating, DI, routing, HTTP, forms, and state management.

> **AngularJS (v1)** = the original 2010 framework (MVC, `$scope`, `ng-app`)
> **Angular (v2+)** = complete rewrite released 2016. Component-based, TypeScript-first.

---

## Why Angular

- **Expressive HTML** — `*ngIf`, `*ngFor` bring logic into templates
- **Powerful data binding** — one-way, two-way, event, property
- **Modularity by design** — NgModules or standalone components
- **Back-end integration** — `HttpClient`, interceptors, typed responses
- **Built for speed** — faster change detection (signals), server-side rendering

**Platform targets:** DOM (web), native mobile (Ionic/Capacitor), Web Workers, Angular Universal (SSR)

---

## Architecture

```
App
├── NgModules (or Standalone components)
│   ├── Components   — UI + logic unit
│   ├── Services     — shared business logic (injected via DI)
│   ├── Pipes        — transform display values
│   └── Directives   — modify DOM elements/behaviour
├── Routing          — maps URLs to components
└── RxJS / Signals   — reactive state
```

---

## Components

The core building block. A component = TypeScript class + HTML template + styles.

```typescript
@Component({
  selector: 'app-product',
  templateUrl: './product.component.html',
  styleUrls: ['./product.component.scss']
})
export class ProductComponent {
  title = 'My Product';
}
```

**Key capabilities:**
1. Angular creates custom tags: `<app-root>`, `<app-product>`
2. Component reuse across templates
3. Data separated from view
4. Bound data automatically refreshed in view
5. Tags in data are escaped for security

---

## Data binding

| Syntax | Direction | Example |
|---|---|---|
| `{{ value }}` | Component → View | `{{ product.name }}` |
| `[property]="expr"` | Component → DOM | `[src]="imageUrl"` |
| `(event)="handler()"` | DOM → Component | `(click)="onSave()"` |
| `[(ngModel)]="prop"` | Two-way | `[(ngModel)]="username"` |

---

## Structural directives

Modify the DOM structure (add/remove elements).

| Directive | Purpose |
|---|---|
| `*ngFor="let x of list"` | Iterate over a collection |
| `*ngIf="condition"` | Conditionally add/remove from DOM (not just hide) |
| `*ngIf="x; else tmpl"` | If-then-else using `ng-template` |
| `*ngSwitch` | Switch content based on a value |

```html
<div *ngFor="let product of products">{{ product.name }}</div>
<div *ngIf="isLoggedIn; else loginBlock">Welcome!</div>
<ng-template #loginBlock><p>Please log in.</p></ng-template>
```

---

## Attribute directives

Modify appearance or behaviour without changing structure.

| Directive | Purpose |
|---|---|
| `[ngClass]="{ 'active': isActive }"` | Apply CSS classes conditionally |
| `[ngStyle]="{ 'color': textColor }"` | Apply styles conditionally |

---

## Component communication

### Parent → Child: `@Input()`
```typescript
@Input() product: Product;
```
```html
<app-product [product]="selectedProduct"></app-product>
```

### Child → Parent: `@Output()` + `EventEmitter`
```typescript
@Output() productSelected = new EventEmitter<Product>();
onSelect() { this.productSelected.emit(this.product); }
```
```html
<app-product (productSelected)="onProductSelected($event)"></app-product>
```
> `@Output` events do **not** bubble up the DOM.

### Access child elements: `@ViewChild` / `@ViewChildren`
```typescript
@ViewChild('myInput') inputRef: ElementRef;
@ViewChildren(ProductComponent) productComponents: QueryList<ProductComponent>;
```

### Access projected content: `@ContentChild` / `@ContentChildren`
```typescript
@ContentChild(HeaderComponent) header: HeaderComponent;
```

---

## Templates

### Template reference variables
```html
<input #phoneInput type="text">
<button (click)="callPhone(phoneInput.value)">Call</button>
```

### `ng-template` — not rendered by default
```html
<ng-template #loading><p>Loading...</p></ng-template>
<div *ngIf="data; else loading">{{ data }}</div>
```

### `ng-container` — grouping without adding DOM element
```html
<ng-container *ngIf="isAdmin">
  <app-admin-panel></app-admin-panel>
</ng-container>
```

### Content projection (`ng-content`)
Slot external HTML into a component's template.
```html
<!-- parent -->
<app-card><h2>My Title</h2></app-card>

<!-- card.component.html -->
<div class="card"><ng-content></ng-content></div>
```

---

## Pipes

Transform values in templates without changing the source data.

| Pipe | Example | Output |
|---|---|---|
| `date` | `{{ date \| date:'shortDate' }}` | `4/12/26` |
| `currency` | `{{ price \| currency:'USD' }}` | `$9.99` |
| `uppercase` | `{{ name \| uppercase }}` | `KEN` |
| `number` | `{{ n \| number:'1.2-2' }}` | `1.23` |
| `percent` | `{{ 0.75 \| percent }}` | `75%` |
| `slice` | `{{ arr \| slice:0:3 }}` | first 3 items |
| `json` | `{{ obj \| json }}` | JSON string |
| `keyvalue` | `*ngFor="let kv of obj \| keyvalue"` | key-value pairs |
| `async` | `{{ obs$ \| async }}` | unwraps Observable/Promise |

**Custom pipe:**
```typescript
@Pipe({ name: 'truncate' })
export class TruncatePipe implements PipeTransform {
  transform(value: string, limit = 100): string {
    return value.length > limit ? value.substring(0, limit) + '...' : value;
  }
}
```

---

## Services and DI

Services hold shared business logic. Angular's DI system injects them via constructor.

```typescript
@Injectable({ providedIn: 'root' })   // singleton across the app
export class ProductService {
  constructor(private http: HttpClient) {}

  getProducts(): Observable<Product[]> {
    return this.http.get<Product[]>('/api/products');
  }
}

@Component({...})
export class ProductListComponent {
  constructor(private productService: ProductService) {}
}
```

See [[Dependency-Injection]] for lifetime scopes (root / module / component).

---

## RxJS and Observables

Angular uses RxJS throughout — HTTP, routing events, form value changes, etc.

**Observable lifecycle:**
- **Next** — emits a value, process it
- **Error** — error occurred, no more emissions
- **Complete** — stream finished, no more emissions

**Observer** responds to each notification:
```typescript
this.productService.getProducts().subscribe({
  next: products => this.products = products,
  error: err => this.errorMsg = err.message,
  complete: () => console.log('done')
});
```

**Creation functions:**
```typescript
of(1, 2, 3)           // emits each argument
from([1, 2, 3])       // emits each array item
fromEvent(btn, 'click') // DOM events
timer(1000)           // emits after delay
```

**Benefits for HTTP:** notifications, operators, error handling, retry, cancellation.

**`async` pipe** — subscribe/unsubscribe automatically in template:
```html
<div *ngFor="let p of products$ | async">{{ p.name }}</div>
```

---

## Angular Signals (v17+)

Fine-grained reactivity alternative to RxJS for component state.

```typescript
// Signal — like a reactive variable
count = signal(0);

// Computed — derived from signals
double = computed(() => this.count() * 2);

// Effect — side effect when signals change
effect(() => console.log('count is', this.count()));

// Update
this.count.set(5);
this.count.update(n => n + 1);
```

Signals give Angular more precise change detection without Zone.js.

---

## Routing

Maps URLs to components. Configured in `app.routes.ts` or `RouterModule`.

**Route lifecycle when URL changes:**
1. Match path
2. Process redirects
3. Process guards (`CanActivate`, `CanDeactivate`)
4. Resolve data
5. Activate component(s)
6. Display template(s)

```typescript
const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'products', component: ProductListComponent },
  { path: 'products/:id', component: ProductDetailComponent },
  { path: '**', component: NotFoundComponent }
];
```

```html
<a routerLink="/products">Products</a>
<router-outlet></router-outlet>
```

---

## NgRx — State Management

Redux-pattern state management for Angular. Use when:
- Preventing repeated API fetches for shared data
- Component lifecycle doesn't match data lifecycle
- Quick optimistic UI updates on save
- Avoiding complex data modification edge cases

**Packages:**
| Package | Purpose |
|---|---|
| `@ngrx/store` | Centralised immutable state |
| `@ngrx/effects` | Side effects (API calls) |
| `@ngrx/entity` | Cache and manage entity collections |
| `@ngrx/router-store` | Sync router state to store |
| `@ngrx/store-devtools` | Redux DevTools integration |

```bash
ng add @ngrx/store
ng add @ngrx/store-devtools
```

---

## Angular CLI

```bash
npm install -g @angular/cli
ng new my-app                     # create new app
ng serve                          # dev server
ng build --prod                   # production build
ng generate component product     # create component
ng generate service product       # create service
ng generate pipe truncate         # create pipe
ng test                           # run unit tests
ng e2e                            # run E2E tests
```

---

## Best practices

**File & folder structure:**
- Locate code quickly, identify at a glance
- Flattest structure possible
- One item per file (one component, one service)
- DRY — shared code in a `shared/` module

**Naming conventions:**
- Components: `product-list.component.ts`
- Services: `product.service.ts`
- Pipes: `truncate.pipe.ts`
- Modules: `product.module.ts`

**Performance:**
- Use `OnPush` change detection on leaf components
- Use `async` pipe (auto-unsubscribes)
- Lazy-load feature modules via routing
- Use signals for fine-grained reactivity (Angular 17+)

---

## AngularJS → Angular migration

Migration happens in two phases:

**Phase 1: Preparation**
- Add TypeScript
- Follow Angular style guide in existing AngularJS code
- Use component directives (closer to Angular components)
- Avoid `$scope` — use controller-as syntax

**Phase 2: Migration**
Using `@angular/upgrade` (ngUpgrade) to run both frameworks simultaneously:
- Migrate components one at a time
- Migrate pipes → pipes
- Migrate services → injectable services
- Migrate transclusion → content projection
- Eventually remove AngularJS dependency

**Vocabulary:**
| AngularJS | Angular |
|---|---|
| `$scope` | Component class properties |
| Controller | Component |
| `ng-repeat` | `*ngFor` |
| `ng-if` | `*ngIf` |
| Filter | Pipe |
| Factory / Service | `@Injectable()` service |
| `$http` | `HttpClient` |
| `ui-router` | `RouterModule` |

---

## Forms

Angular has two form strategies:

| | Template-driven | Reactive (Model-driven) |
|---|---|---|
| Setup | Easy, similar to Angular 1 | More flexible, more complex |
| Data binding | Two-way (`ngModel`) | Immutable model |
| Validation | Template | Component class |
| Dynamic fields | Harder | Easy |
| Unit testing | Harder | Easier |
| Use when | Simple forms | Complex, dynamic forms |

### Reactive Forms

Built from `FormControl`, `FormGroup`, and `FormArray` objects in the component class.

```typescript
import { FormBuilder, FormGroup, Validators } from '@angular/forms';

@Component({...})
export class SignupComponent {
  form: FormGroup;

  constructor(private fb: FormBuilder) {
    this.form = this.fb.group({
      name: ['', Validators.required],
      email: ['', [Validators.required, Validators.email]],
      address: this.fb.group({
        street: [''],
        city: ['']
      })
    });
  }

  onSubmit() {
    if (this.form.valid) { console.log(this.form.value); }
  }
}
```

```html
<form [formGroup]="form" (ngSubmit)="onSubmit()">
  <input formControlName="name">
  <div formGroupName="address">
    <input formControlName="street">
  </div>
  <button type="submit">Submit</button>
</form>
```

**`valueChanges`** — Observable that emits every time a form value changes. Use with RxJS operators:
```typescript
this.form.get('email').valueChanges
  .pipe(debounceTime(400), distinctUntilChanged())
  .subscribe(val => this.checkEmail(val));
```

**`FormArray`** — for dynamic repeated fields:
```typescript
get items() { return this.form.get('items') as FormArray; }
addItem() { this.items.push(this.fb.control('')); }
```

---

## RxJS operators reference

RxJS solves callback hell and timing problems with streams. Key operator categories:

**Transformation:**
| Operator | What it does |
|---|---|
| `map(fn)` | Transform each emitted value |
| `switchMap(fn)` | Map to inner observable, cancel previous |
| `mergeMap(fn)` | Map to inner observable, keep all |
| `concatMap(fn)` | Map to inner observable, queue in order |
| `scan(fn, seed)` | Accumulate values like `reduce` |

**Filtering:**
| Operator | What it does |
|---|---|
| `filter(fn)` | Only pass values matching predicate |
| `debounceTime(ms)` | Wait N ms after last emission |
| `distinctUntilChanged()` | Skip if same as previous value |
| `take(n)` | Complete after N emissions |
| `takeUntil(obs$)` | Complete when another obs emits |

**Combination:**
| Operator | What it does |
|---|---|
| `combineLatest([a$, b$])` | Emit when any source emits (with latest from all) |
| `forkJoin([a$, b$])` | Emit once when all sources complete |
| `merge(a$, b$)` | Emit from all sources as they arrive |
| `zip(a$, b$)` | Pair emissions by index |

**Error handling:**
```typescript
obs$.pipe(
  catchError(err => of(defaultValue)),
  retry(3)
)
```

**Unsubscribe pattern** (prevent memory leaks):
```typescript
private destroy$ = new Subject<void>();

ngOnInit() {
  this.data$.pipe(takeUntil(this.destroy$)).subscribe(...);
}

ngOnDestroy() {
  this.destroy$.next();
  this.destroy$.complete();
}
```

---

## Unit Testing

Angular uses **Jasmine** (test framework) + **Karma** (test runner).

### Test types

| Type | What it tests | Uses TestBed? |
|---|---|---|
| Isolated | Pure class logic only | No |
| Shallow integration | Component + template (stub children) | Yes |
| Deep integration | Component + all child components | Yes |

### TestBed

Creates a mini Angular module for testing:

```typescript
beforeEach(() => {
  TestBed.configureTestingModule({
    declarations: [HeroComponent],
    schemas: [NO_ERRORS_SCHEMA]   // ignore unknown child elements
  });
  fixture = TestBed.createComponent(HeroComponent);
});
```

### Fixture and queries

```typescript
// DOM via nativeElement
expect(fixture.nativeElement.querySelector('a').textContent).toContain('SuperDude');

// DOM via debugElement (more Angular-aware)
let link = fixture.debugElement.query(By.css('a'));
expect(link.nativeElement.textContent).toContain('SuperDude');

// Detect changes after data update
fixture.detectChanges();
```

### Mocking services

```typescript
let mockHeroService: jasmine.SpyObj<HeroService>;

beforeEach(() => {
  mockHeroService = jasmine.createSpyObj(['getHeroes', 'addHero', 'deleteHero']);
  mockHeroService.getHeroes.and.returnValue(of(HEROES));
  mockHeroService.deleteHero.and.returnValue(of(true));

  TestBed.configureTestingModule({
    providers: [{ provide: HeroService, useValue: mockHeroService }]
  });
});

it('should call deleteHero', () => {
  component.delete(HEROES[2]);
  expect(mockHeroService.deleteHero).toHaveBeenCalledWith(HEROES[2]);
});
```

### HTTP testing

```typescript
let httpController: HttpTestingController;

beforeEach(() => {
  TestBed.configureTestingModule({
    imports: [HttpClientTestingModule]
  });
  httpController = TestBed.inject(HttpTestingController);
});

it('should fetch heroes', () => {
  service.getHeroes().subscribe(heroes => expect(heroes.length).toBe(3));
  const req = httpController.expectOne('/api/heroes');
  req.flush(HEROES);
  httpController.verify();
});
```

---

## Server-Side Rendering (SSR)

SSR renders the Angular app on the server before sending HTML to the browser.

**Benefits:**
- Better Core Web Vitals: FCP (First Contentful Paint) and LCP (Largest Contentful Paint) improve
- Better SEO — search engines see rendered HTML
- Faster perceived load time

**Setup (Angular 17+):**
```bash
ng add @angular/ssr
```

Adds a Node.js Express server. Check SSR is working by viewing Page Source — content should be present in the HTML (not just `<app-root></app-root>`).

---

## See also
- [[TypeScript-JavaScript]] — Angular is TypeScript-first
- [[Dependency-Injection]] — Angular's DI system
- [[Testing]] — Jasmine + Karma + Cypress for Angular testing
- [[Async-Programming]] — RxJS parallels async/await patterns
- [[Design-Patterns]] — Observer pattern underlies RxJS
