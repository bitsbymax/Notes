# Angular docs

## How an Angular App gets Loaded and Started

- **Entry point** це файл `index.html` в якому після збірки angular cli додав свої скрипти, які завантажуються першими і серед яких буде бандл з файлом `main.ts`, код якого і виконається спочатку.
- В ньому і стартує наш веб-застосунок в момент виклику `platformBrowserDynamic().bootstrapModule(AppModule)` з головним модулем _AppModule_, в якому в свою чергу вказано початковий компонент з селектором `<app-root></app-root>`, який і вказаний в `body` файлу `index.html`.

## Decorators

> - Декоратори модифікують поведінку класів в Angular
>
[Example of custom decorators usage](https://medium.com/@ddmytro787/practical-angular-decorators-2d516be1feb8)

Приклад декораторів:

```javascript
@NgModule({
  declarations: [],
  imports: [],
  exports: [],
  bootstrap: [],
  providers: []
})

@Component({
  selector: 'app-root',
  template: `<div class="root-wrapper" [ngClass]="layoutType">
    <router-outlet></router-outlet>
  </div>`,
//або
  templateUrl: '',
  styleUrls: [''],
//або
  style: ['']
})
```

---

## Interpolation

`{{ someValue }}` where _`someValue`_ is an expression which evaluates to **string**

```javascript
<h1>Welcome to the {{ hotelName }}</h1>
<p>Here will be some string: {{ returnString() }}</p>
```

---

## Property binding (One-way binding)

```html
<button type="button" [disabled]="!active">-</button>
<p [innerText]="hotelName"></p>
<image [src]="'assets/users/' + selectedUser.avatar"></image>
```

Через таку прив'язку ми можемо для будь-якої властивості елемента, через його атрибут, встановити значення будь-якої властивості нашого компонента

> Важлива деталь, при використанні _`property binding`_, значення змінюється саме властивості DOM об'єкта, а не атрибута. Проте за рахунок _`reflection`_, при зміні властивості, змінюється і атрибут, хоч і джерелом правди, тобто основою є все ж атрибут, а властивість віддзеркалює його значення. Це стандартна поведінка DOM. Ну і є певні виключення, детальніше тут: [attributes vs properties](https://jakearchibald.com/2024/attributes-vs-properties/)

---

## Event binding

```html
<button (click)="toggle($event)">Toggle</button>
```

Прив'язати можна всі доступні в JS для конкретного елемента методи. Береться назва метода без частинки **on**.

---

## Two-way data binding

Дає можливість слухати подію і оновлювати дані одночасно через одну властивість між батьківським і дочірнім компонентом. Поєднує в собі _`property binding`_ і _`event binding`_

For two-way data binding to work, the _`@Output()`_ property must use the **pattern** - **_inputChange_**, where input is the name of the _`@Input()`_ property. For example, if the _`@Input()`_ property is _size_, the _`@Output()`_ property must be _sizeChange_.

```javascript
@Component({
  selector: 'app-root',
  standalone: true,
  imports: [CommonModule],
  templateUrl: `<app-sizer [(size)]="fontSizePx"></app-sizer>
<div [style.font-size.px]="fontSizePx">Resizable Text</div>`,
  styleUrl: './root.component.scss',
})
export class RootComponent {
  fontSizePx = 16;
}
```

```javascript
export class SizerComponent {
  @Input() size!: number | string;
  @Output() sizeChange = new EventEmitter<number>();
  dec() {
    this.resize(-1);
  }
  inc() {
    this.resize(+1);
  }
  resize(delta: number) {
    this.size = Math.min(40, Math.max(8, +this.size + delta));
    this.sizeChange.emit(this.size);
  }
}
```

```html
<div>
  <button type="button" (click)="dec()" title="smaller">-</button>
  <button type="button" (click)="inc()" title="bigger">+</button>
  <span [style.font-size.px]="size">FontSize: {{size}}px</span>
</div>
```

---

## Directives

- Directives are use to change the behavior and appearance of DOM element
- Directives can implement all lifecycle hooks
- Directives can't have template

### Structural directive

> На одному і тому ж елементі можна мати лише одну **structural directive**

#### `*ngIf`

Умовний рендеринг, де при _`true`_ справа від _`*ngIf`_ буде рендеритись розмітка елементу, на якому директива додана, а при _`false`_ буде рендеритись та розмітка, яку ми додамо в _`ng-template`_

`*ngIf`

```html
<div *ngIf="isLoggedIn && !isNewUser">Welcome back, friend</div>
```

`*ngIf and Else`

```html
<div *ngIf="isLoggedIn; else loggedOut">Welcome back, friend</div>

<ng-template #loggedOut>Please friend, login</ng-template>
```

`*ngIf, Then and Else`

```html
<ng-container *ngIf="isLoggedIn; then loggedIn; else loggedOut"> </ng-container>

<ng-template #loggedIn>
  <div>Welcome back, friend.</div>
</ng-template>
<ng-template #loggedOut>
  <div>Please friend, login.</div>
</ng-template>
```

> New syntax

```javascript
//if
@Component({
  template: `
    @if (showHello) {
    <h2>Hello</h2>
    }
  `,
})
class Test {
  showHello: boolean = true;
}

//if -> else
@Component({
  template: `
    @if (showHello) {
    <h2>Hello</h2>
    } @else {
    <h2>Goodbye</h2>
    }
  `,
})
class Test {
  showHello: boolean = true;
}

//if-> else if-> else
@Component({
  template: `
    @if (showHello) {
    <h2>Hello</h2>
    } @else if (showGoodbye) {
    <h2>Goodbye</h2>
    } @else {
    <h2>See you later</h2>
    }
  `,
})
class Test {
  showHello: boolean = true;
  showGoodbye: boolean = false;
}
```

> - Notice that we don't have to import the @if, @for, @switch directives from @angular/common into our component templates anymore.
> - This is because the @if, @for, @switch syntax is part of the template engine itself, and it is not a directive.
> - The new @if, @for, @switch are built-in directly into the template engine, so it's automatically available everywhere.

---

#### `*ngFor`

`*ngFor="let lesson of lessons"` - Outputting Lists (цикл for для перебору елементів)

```html
<div *ngFor="let lesson of lessons">
  <div>{{ lesson | json }}</div>
</div>
```

`using index`

```html
<tr *ngFor="let hero of heroes; let i = index">
  <td>{{hero.name}}</td>
  <td>{{i}}</td>
</tr>
```

`even and odd`

```html
<tr *ngFor="let hero of heroes; let even = even; let odd = odd" [ngClass]="{ odd: odd, even: even }">
  <td>{{hero.name}}</td>
</tr>
```

`first and last`

```html
<tr *ngFor="let hero of heroes; let first = first; let last = last" [ngClass]="{ first: first, last: last }">
  <td>{{hero.name}}</td>
</tr>
```

`trackBy`

_`ngFor`_ already does a lot of optimizations out-of-the-box to try to reuse existing DOM elements as much as possible, but it's doing so based on _object identity_.

But we can provide our own mechanism for tracking items in a list by using _`trackBy`_. We need to pass a function to _`trackBy`_, and the function takes a couple of arguments, which are an _`index`_ and the _`current item`_:

```javascript
@Component({
  selector: 'heroes',
  template: `
    <table>
      <thead>
        <th>Name</th>
      </thead>
      <tbody>
        <tr *ngFor="let hero of heroes; trackBy: trackHero">
          <td>{{ hero.name }}</td>
        </tr>
      </tbody>
    </table>
  `,
})
export class Heroes {
  heroes = HEROES;

  trackHero(index, hero) {
    return hero ? hero.id : undefined;
  }
}
```

This implementation would do the tracking based on the id property.

> New syntax

```html
@for (room of rooms | slice : 0 : rooms.length; track room.roomNumber; let i = $index, even = $even, odd = $odd) {
<tr [ngClass]="even ? 'even' : 'odd'">
  <td>{{ i }}</td>
  <td>{{ room.roomNumber }}</td>
  <td>{{ room.checkInTime | date : "longDate" }}</td>
  <td>{{ room.checkOutTime | date : "longDate" }}</td>
  <td>{{ room.rating | number : "1.1-1" : "en-us" }}</td>
</tr>
}
```

`@for() {} @empty{}`

```javascript
@for (i of items ; track i.name) {
  <li>{{i.name}}</li>
}  @empty {
  <li>There are no items.</li>
}
```

---

#### `*ngSwitch`

`[ngSwitch]` - дає можливість умовного рендерингу через _`switch case`_.

```html
<div [ngSwitch]="value">
  //прив'язуємось до значення змінної value
  <p *ngSwitchCase="5">Value is 5</p>
  <p *ngSwitchCase="10">Value is 10</p>
  <p *ngSwitchCase="100">Value is 100</p>
  <p *ngSwitchDefault>Value is Default</p>
</div>
```

> New syntax

```JavaScript
@switch (color) {
    @case ("red") {
        <div>Red</div>
    }
    @case ("blue") {
        <div>Blue</div>
    }
    @default {
      <div>Default</div>
    }
}
```

---

#### `ng-content`

> Content Projection

`<ng-content></ng-content>` - директива, яка рендерить розмітку, яку ми вставляємо поміж тегів селектора компонента в тому місці, де ми цю директиву пропишемо:

```html
<app-server-element>
  <p>
    <strong>{{ serverElement.content }}</strong>
    <em> {{ serverElement.content }} </em>
  </p>
</app-server-element>
```

Потім верстку, яка поміж _`<app-server-element></>`_ ми можемо вставити всередину темплейта того компонента, у якого відповідно селектор _`app-server-element`_ додавши туди _`<ng-content></ng-content>`_. Тоді цей тег буде динамічно замінений на html код, який був переданий.

#### Multi-Slot Content Projection

За допомогою спеціального атрибута _`select`_ ми можемо вказати, який саме контент ми хочемо відобразити всередині директиви _`ng-content`_

`root-component.ts`

```html
<app-container>
  <h1>This is from app-container's ng-content</h1>
  <app-rooms></app-rooms>
  <app-employee></app-employee>
</app-container>
```

`app-container.html`

```html
<ng-content></ng-content>
<ng-content select="app-rooms"></ng-content>
<ng-content select="app-employee"></ng-content>
```

Порядок того, як елементи будуть додані в _`DOM`_ визначається саме порядком _`ng-content`_.
Тег без селектора буде рендерити розмітку за принципом виключення.

---

#### `ng-template`

Як і випливає з назви, ця директива представляє собою _Angular_ темплейт. Це означає, що контент всередині директиви буде частиною загального темплейту.
_Angular_ вже використовує _`ng-template`_ під капотом в багатьох структурних директивах, _`ngIf`_, _`ngFor`_ і _`ngSwitch`_.

Але потрібно розуміти, що сам по собі тег _`<ng-template></ng-template>`_ нічого не рендерить, тобто в результаті такої розмітки нічого на екран виведено не буде

```html
<ng-template>
  <div>content</div>
  //цей тег не відрендериться
</ng-template>
```

І це очікувана поведінка, бо використовуючи _`ng-template`_ ми лише визначаємо темплейт але ще не використовуємо ніде.

We use the _`<ng-template>`_ because much like it's HTML5 counterpart `<template>`, it's also considered “virtual”.

Being “virtual” means the _`<ng-template>`_ contents won't actually exist in the compiled DOM, until it's needed (you will never see it until Angular renders it).

When it's needed (for example the “else” expression kicks into play), Angular will grab the contents of the _`<ng-template>`_ tag, and replace the _`*ngIf`_ contents with it. That's it.

---

#### `ng-container`

Працює як **Fragment** в React, тобто його можна використовувати коли ми хочемо додати обгортку для якогось HTML елемента, але не хочемо додавати лишній HTML елемент на сторінку. На _`ng-container`_ також можна додавати структурні директиви.

There is another major use case for the _`ng-container`_ directive: it can also provide a placeholder for injecting a template dynamically into the page.

---

#### Dynamic Template Creation with the `ngTemplateOutlet` directive

Можливість створювати темплейти з додаванням на них _#template_reference_, як цей, що на прикладі нижче, і його використання потім, наприклад, в структурній директиві _`*ngIf`_ це лише початок.

```html
<ng-template #loggedIn>
  <div>Welcome back, friend.</div>
</ng-template>
```

За допомогою _`ngTemplateOutlet`_ ми можемо створити екземпляр доданого нами темплейту _`ng-template`_ з _#template reference_ і відрендерити його будь-де на сторінці за допомогою _`ng-container`_:

```html
<ng-container *ngTemplateOutlet="loggedIn"></ng-container>
```

#### Template Context

В межах нашого темплейта ми маємо доступ до локально створених змінних через префікс _`let`_ і також до змінних зовнішнього контексту, тобто контексту в якому наш темплейт буде використовуватись.

`app.component.ts`

```javascript
@Component({
  selector: 'app-root',
  standalone: true,
  imports: [RouterOutlet, RoomsComponent, CommonModule],
  templateUrl: './app.component.html',
  styleUrl: './app.component.scss',
})
export class AppComponent {
  totalCustomers = 10;
  ctx = { count: this.totalCustomers };

  data = {
    screen: true,
    label: 'Mobile',
  };
}
```

> Далі приклад того, як можна в контексті _`ng-template`_ отримати дані з нашого компонента

`app.component.html`

> використовуючи _`ng-container`_

```html
<ng-template #countTemplate let-customerCount="count">
  <div>Approximately {{ customerCount }} customers on the page...</div>
</ng-template>
<ng-container *ngTemplateOutlet="countTemplate; context: ctx"> </ng-container>
// Approximately 10 customers on the page...
```

> напряму в `ng-template`

```html
<ng-template
  #t
  [ngTemplateOutlet]="t"
  [ngTemplateOutletContext]="{
    defaultScreen: data.screen,
    label: data.label
  }"
  let-defaultScreen="defaultScreen"
  let-label="label"
>
  <div>default screen: {{ defaultScreen }} - label: {{ label }}</div>
</ng-template>
// default screen: true - label: Mobile
```

> А тут ми передаємо в темплейт локальні дані в скоупі циклу @for()

```html
@for (room of rooms; track room.roomNumber; let i = $index) {
<tr [ngClass]="even ? 'even' : 'odd'">
  <ng-container *ngTemplateOutlet="td; context: { i }"></ng-container>
  //передаємо контекст
  <td>{{ room.checkInTime | date : "longDate" }}</td>
  <td>{{ room.checkOutTime | date : "longDate" }}</td>
  <td>{{ room.rating | number : "1.1-1" : "en-us" }}</td>
</tr>
}

<ng-template #td let-index="i">
  <td>{{ index }}</td>
</ng-template>
```

---

### Attribute directives

> Unlike structural directives, attribute directives don't add or remove elements. They only change the element they were placed on

`[ngStyle]`

```javascript
[ngStyle] = '{ backgroundColor: getColor() }';
[ngStyle] = "{ color: rooms.availableRooms ? 'lightgreen' : 'red', fontSize: '20px', fontWeight: 'bold' }";
```

`[ngClass]`

```javascript
[ngClass]="{ online: serverStatus === 'online' }" or
[ngClass]="even ? 'even' : 'odd'"
```

---

## Decorators types

> Communication between components

- **`@Input()`** - дає можливість компоненту приймати і використовувати дані батьківського компонента

  ```typescript
  @Component({
    selector: 'app-customer-detail',
    templateUrl: './customer-detail.component.html',
    styleUrls: ['./customer-detail.component.css']
  })
  export class CustomerDetailComponent implements OnInit {
    @Input({ required: true}) customer!: Customer; // Input property
  }
  ```

  де `{ required: true }` - вказує про те, що ця властивість обов'язкова
  
  В батьківському компоненті:
  
  ```html
  <app-customer-detail [customer]="selectedCustomer"></app-customer-detail>
  ```

- **`@Output()`** - дає можливість компоненту передавати дані наверх батьківському компоненту
  
  ```typescript
  @Component({
    selector: 'app-customer-detail',
    templateUrl: './customer-detail.component.html',
    styleUrls: ['./customer-detail.component.css']
  })
  export class CustomerDetailComponent implements OnInit {
    @Output() customerChange: EventEmitter<Customer> = new EventEmitter<Customer>(); // Output property
  
    update() {
      this.customerChange.emit(this.customer); // Raise the event
    }
  }
  ```
  
  В батьківському компоненті:
  
  ```html
  <app-customer-detail
    [customer]="selectedCustomer"
    (customerChange)="update($event)">
  </app-customer-detail>
  
  ```

- **`@ViewChild()`** - дає доступ до подання компонента, де ми в свою чергу можемо отримати доступ до елементів темплейта через _`#template reference`_ або навіть дочірніх компонентів. Також через цей декоратор можна в класі компонента динамічно отримати екземпляр іншого компонента і вставити його в наш темплейт

  > Html element access
  
  Для доступу до елемента темплейта, додаємо на нього _`#template reference`_
  
  ```html
  <p #description></p>
  ```
  
  І далі в класі можемо його зчитати і модифікувати. Він буде доступний за посиланням: _`propertyName.nativeElement`_, посилання зберігатиме об'єкт _`ElementRef {nativeElement: p}`_, де будуть всі вбудовані властивості конкретного _`html`_ елемента.
  
  ```javascript
  @Component({
    selector: 'app-rooms',
    standalone: true,
    imports: [CommonModule],
    templateUrl: './rooms.component.html',
    styleUrl: './rooms.component.scss',
  })
  export class RoomsComponent implements OnInit
  {
    @ViewChild('description', { static: true }) description!: ElementRef;
  
    ngOnInit(): void {
      this.description.nativeElement.innerText = 'Our goal is to provide best service';
    }
  }
  ```
  
  ---
  
  > Child component access
  
  Якщо нам потрібно отримати доступ до дочірнього компонента який рендериться всередині нашого темплейта таким чином:
  
  ```html
  <h1>Welcome to the {{ hotelName }}</h1>
  <app-header></app-header> --> Дочірній компонент
  <p>Available rooms</p>
  {{ rooms.availableRooms ?? "No rooms available" }}
  ```
  
  це можна зробити за допомогою _`@ViewChild()`_ через використання _lifecycle hook_ _`ngAfterViewInit`_ або _`ngAfterViewChecked`_
  
  ```javascript
  export class RoomsComponent implements AfterViewInit, AfterViewChecked {
    @ViewChild(HeaderComponent) header!: HeaderComponent;
  
    constructor(private cdr: ChangeDetectorRef) {}
    ngAfterViewInit(): void {
      this.header.title = 'Hotel inventory'; //- тут це значення зміниться лише на наступному циклі change детектора
      //щоб відобразити зміни одразу
      this.cdr.detectChanges();
    }
    //або
    ngAfterViewChecked(): void {
      this.header.title = 'Hotel inventory'; //- тут це значення зміниться лише на наступному циклі change детектора
      //щоб відобразити зміни одразу
      this.cdr.detectChanges();
    }
  }
  ```
  
  Якщо ж нам потрібно отримати дані цього дочірнього компонента в _`ngOnInit`_, ми маємо вказати про це декоратору через спеціальну властивість _`static`_.
  По дефолту це значення _`false`_, щоб попередити можливі баги чи затримки у випадку, наприклад, коли у нашому вкладеному компоненті є асинхронні операції, які блокуватимуть _`execution flow`_ і відповідно ініціалізацію нашого батьківського компонента.
  
  ```javascript
  export class RoomsComponent implements OnInit, AfterViewInit, AfterViewChecked {
    @ViewChild(HeaderComponent, { static: true }) header!: HeaderComponent;
  
    ngOnInit(): void {
      this.header.title = 'Hotel inventory';// - ми можемо змінити значення властивості title компонента HeaderComponent з середини RoomsComponent в ngOnInit лише якщо вказано { static: true }.
    }
    ngAfterViewInit(): void {}
    // або
    ngAfterViewChecked(): void {}
  }
  ```
  
  ---
  
  Також ми можемо динамічно рендерити компонент всередині нашого темплейта за допомогою цього декоратора.
  Додамо в наш темплейт тег _`ng-template`_ з _`#template reference`_
  
  ```html
  <ng-template #bookRoom></ng-template>
  ```
  
  Далі через _`@ViewChild()`_ ми в нашому класі можемо екземпляр потрібного компонента отримати і вставити в наш темплейт:
  
  ```javascript
  import { BookButtonComponent } from '../book-button/book-button.component';
  
  export class RoomsComponent implements AfterViewInit, AfterViewChecked
  {
    @ViewChild('bookRoom', { read: ViewContainerRef }) vcr!: ViewContainerRef;
  
    ngAfterViewInit() {
      const componentRef = this.vcr.createComponent(BookButtonComponent); //тут ми отримуємо екземпляр нашого компонента який і буде відмальовано всередині ng-template
    }
  }
  ```
  
  Також за потреби ми можемо отримати доступ до властивостей класу компонента і модифікувати їх
  
  ```javascript
  ngAfterViewInit(): void {
    const componentRef = this.vcr.createComponent(BookButtonComponent);
    componentRef.instance.buttonLabel = 'Book new room'; //тут змінюємо значення потрібної властивості
  }
  ```
  
- **`@ViewChildren()`** - декоратор, який дає можливість отримати доступ до дочірніх компонентів темплейту, які ми використовуємо більше ніж один раз:

  ```html
  <h1>Welcome to the {{ hotelName }}</h1>
  <p #description></p>
  // як приклад
  <app-header></app-header>
  <app-header></app-header>
  ```
  
  Далі в класі створюємо властивість з цим декоратором:
  
  ```javascript
  export class RoomsComponent implements AfterViewInit, AfterViewChecked
  {
    @ViewChildren(HeaderComponent) headerChildren!: QueryList<HeaderComponent>;
  }
  ```
  
  У властивості _`headerChildren`_ буде ось таке посилання на об'єкт:
  
  ```javascript
  _QueryList
  dirty: false
  first: _HeaderComponent {title: 'Hotel inventory', __ngContext__: 3}
  last: _HeaderComponent {title: '', __ngContext__: 3}
  length: 3
  _changes: undefined
  _changesDetected: true
  _emitDistinctChangesOnly: true
  _onDirty: undefined
  _results: Array(3)
    0: _HeaderComponent {title: 'Hotel inventory', __ngContext__: 3}
    1: _HeaderComponent {title: '', __ngContext__: 3}
    2: _HeaderComponent {title: '', __ngContext__: 3}
    length: 3
    [[Prototype]]: Array(0)
    changes: (...)
  [[Prototype]]: Object
  ```
  
  І далі в _`ngAfterViewInit`_ ми вже маємо можливість працювати з кожним з цих компонентів:
  
  ```javascript
  ngAfterViewInit(): void {
    this.headerChildren.last.title = 'Hotel inventory 2';
  }
  ```
  
  > Для цього декоратора по дефолту _`{ static: false }`_ і змінити це значення не можна.
  
  - **`@ContentChild()`** - дає доступ до елементів з атрибутом _`#someName`_ в темплейті, але тих, що додані через _`<ng-content></ng-content>`_, тобто шляхом `content projection`
  
  ```html
  <app-rooms [hotelName]="hotelName">
    <!-- passing ng-content -->
    <h4>Employee data:</h4>
    <app-employee></app-employee>
  </app-rooms>
  ```
  
  `rooms.component.ts`
  
  ```javascript
  export class RoomsComponent implements AfterContentInit, AfterContentChecked
  {
    @ContentChild(EmployeeComponent) employee!: EmployeeComponent;
  
    ngAfterContentInit(): void {
      this.employee.empName = 'Rick';
      this.empName = this.employee.employeeName;
    }
  }
  ```
  
  `rooms.component.html`
  
  ```html
  <!-- ng-content displaying -->
  <ng-content select="[data]"></ng-content>
  <ng-content select="app-employee"></ng-content>
  ```
  
  `employee.component.ts`
  
  ```javascript
  export class EmployeeComponent {
    empName: string = 'John Doe';
  
    get employeeName(): string {
      return this.empName;
    }
  }
  ```
  
- **`ContentChildren()`** - використовується по аналогії з _`@ViewChildren`_, коли нам потрібен доступ до усього _`projected content`_.

- **`HostListener('any supported event')`** - дає можливість слухати будь-яку подію, яка підтримується JS і виконувати потрібну нам функцію, яка приймає _`eventData`_ в момент спрацювання події.

- **`HostBinding('property.subProperty')`** - в прикладі нижче ми говоримо Angular на елементі, де застосовується ця директива, звернутися до властивості _`style`_ під властивості _`backgroundColor`_ і встановити значення _`'red'`_. Пізніше через _`this.backgroundColor`_ можна встановити інше значення
  
  ```typescript
  @Directive({
    selector: '[appBetterHighlight]',
  })
  export class BetterHighlightDirective implements OnInit {
    @Input() defaultColor: string = 'transparent';
    @Input('appBetterHighlight') highlightColor: string = 'blue';
    @HostBinding('style.backgroundColor') backgroundColor: string;
  
    constructor(private elRef: ElementRef, private renderer: Renderer2) {
      console.log('appBetterHighlight directive created');
    }
  
    ngOnInit() {
      this.backgroundColor = this.defaultColor;
      // this.renderer.setStyle(this.elRef.nativeElement, 'background-color', 'blue');
    }
  
    @HostListener('mouseenter') mouseover(eventData: Event) {
      // this.renderer.setStyle(this.elRef.nativeElement, 'background-color', 'blue');
      this.backgroundColor = this.highlightColor;
    }
  
    @HostListener('mouseleave') mouseleave(eventData: Event) {
      // this.renderer.setStyle(this.elRef.nativeElement, 'background-color', 'transparent');
      this.backgroundColor = this.defaultColor;
    }
  }
  ```
  
  ```html
  <p [appBetterHighlight]="'red'" defaultColor="yellow">Style me with a better directive!</p>
  ```
  
  > Варто зауважити, що на прикладі вище ми поєднуємо селектор директиви з _`property-binding`_, тобто цей запис `[appBetterHighlight]="'red'"` означає, що ми до елементу додаємо директиву і одночасно прив'язуємось до відповідної властивості в директиві.
  
---

## Template Reference

**`#someMeaningfulName`** - is often a reference to a DOM element within a template. It can also be a reference to an Angular component or directive or a web component.

Щоб отримати доступ до елементу в коді, цей атрибут потрібно передати в метод, який викликається, наприклад на клік, або ж це можна зробити через _`@ViewChild()`_

---

## Lifecycle Hooks

> Component instance has lifecycle hooks which can help you to hook into different events on Components. Lifecycle ends when component is destroyed

- **`ngOnChanges()`**
  - викликається лише при наявності властивості з декоратором _`@Input()`_ при зміні значення цієї властивості, тобто в будь-якому разі, бо всі властивості по дефолту _`undefined`_, перед _`ngOnInit`_.
- **`ngOnInit()`**
  - хук, який викликатиметься лише **1 раз** після ініціалізації компонента. Викликається він наступним після _`ngOnChanges`_, якщо останній використовується, інакше першим. В цьому хуці як правило відбуваються підписки і робляться запити на отримання даних з бекенду.
- **`ngDoCheck()`**
  - викликається при кожному спрацюванні системи _`change detection`_ в скоупі всього застосунку. Тому майже не використовується, бо може спричиняти падіння продуктивності через дуже часте спрацювання, та і по суті дублює собою хук _`ngOnChanges`_.
- **`ngAfterContentInit()`**
  - викликається **1 раз** після метода _`ngDoCheck()`_ після додавання коду _`html`_ через _`<ng-content></ng-content>`_
- **`ngAfterContentChecked()`**
  - викликається кожного разу, коли контент, доданий через _`<ng-content></ng-content>`_ перевірено системою _`change detection`_
- **`ngAfterViewInit()`**
  - викликається Angular після ініціалізації уявлення (темплейта) компонента. Викликається лише **1 раз** одразу після першого виклику методу _`ngAfterContentChecked()`_
- **`ngAfterViewChecked()`**
  - викликається Angular після того, як стандартний детектор змін закінчив один повний цикл перевірки подання компонента. Викликається після першого виклику методу _`ngAfterViewInit()`_ та після кожного наступного виклику _`ngAfterContentChecked()`_
- **`ngOnDestroy()`**
  - викликається перед тим, як компонент буде прибрано з DOM дерева.

---

## Custom Directives

> Загалом директиви схожі на компоненти, в тому сенсі, що за допомогою них можна елементам в темплейті додавати потрібну нам кастомну логіку і вони (директиви) також можуть імплементувати _`life-cycle hooks`_. У них лише немає свого темплейта.

### Custom Attribute Directive

- Створити файл, де назва слідує такому паттерну `some-name.directive.ts`
- В файлі:

```javascript
  import { Directive } from '@angular/core'
  @Directive({
  selector: '[someName]'
  })
  export class SomeBasicDirective implements OnInit {
    constructor(private elementReference: ElementRef) {}
    ngOnInit() {
      this.elementReference.nativeElement.style.color = 'red';
    }
  }
```

- В _`NgModule`_ в _`declarations`_ додати `SomeBasicDirective`
- В потрібний елемент DOM додати атрибут _`someName`_, назва якого має відповідати тій, що у властивості `selector` нашої директиви - таким чином на прикладі вище ми задамо колір нашому елементу

> Цей спосіб(звернення до елемента напряму) не є найкращим варіантом його модифікації, так як Angular може рендерити темплейти без DOM дерева і тоді властивість, наприклад, `style`, буде недоступною.

- Ще один доступний `API` для роботи з DOM елементами це **`Renderer2`**:

```typescript
import { Renderer2 } from '@angular/core';
@Directive({
  selector: '[someName]',
})
export class SomeBasicDirective implements OnInit {
  constructor(private elementReference: ElementRef, private renderer: Renderer2) {}
  ngOnInit() {
    this.renderer.setStyle(this.elementReference.nativeElement, 'background-color', 'blue');
  }
}
```

> [https://angular.io/api/core/Renderer2](https://angular.io/api/core/Renderer2) - тут більше методів класу **Renderer2**

---

### Custom Structural Directive

`app.component.html`

```html
<div *appUnless="!onlyOdd">
  <li
    class="list-group-item"
    [ngClass]="{ odd: even % 2 !== 0 }"
    [ngStyle]="{
              backgroundColor: even % 2 !== 0 ? 'yellow' : 'transparent'
            }"
    *ngFor="let even of evenNumbers"
  >
    {{ even }}
  </li>
</div>
```

де _`*appUnless`_ - селектор структурної директиви, в яку ми передаємо значення змінної _`onlyOdd`_

_`unless.directive.ts`_

```typescript
@Directive({
  selector: '[appUnless]',
})
export class UnlessDirective {
  @Input() set appUnless(condition: boolean) {
    //appUnless() це властивість, хоч і є методом, до якої ми прив'язуємось, спрацює, коли зміниться параметр, який передається в цей метод. Ключове слово set це setter
    if (condition) {
      this.vcRef.createEmbeddedView(this.templateRef); //цей метод створює розмітку у ViewContainer до якого ми звертаємось
    } else {
      this.vcRef.clear(); //цей метод видаляє розмітку з ViewContainer
    }
  }

  constructor(private templateRef: TemplateRef<any>, private vcRef: ViewContainerRef) {}
  //в templateRef буде посилання на наш HTML код, тобто те, ЩО потрібно відобразити, в vcRef буде вказано ДЕ це треба відобразити
}
```

Не забути додати директиву в декоратор _`@NgModule({})`_, у властивість _`declaration`_ відповідного модуля.

---

## Services & Dependency injection

**Service** - клас, який, як правило, містить допоміжні методи або якийсь функціонал, який ми будемо використовувати в різних місцях програми для виконання якоїсь бізнес логіки. За рахунок перевикористання цей паттерн дає можливість зробити код чистішим, більш лінійним, централізованим, таким, який легше підтримувати.

_`Dependency Injection`_ (DI) is one of the most important mechanisms in Angular. This pattern allows for inversion of control by passing instances of requested dependencies to the class instead of creating them inside the class. This approach creates loose coupling and makes testing easier.
_`Dependency Injection`_ is not only a programming technique, but also an application design philosophy that promotes solutions that are modular, flexible and easy to test.
Using _`Dependency Injection`_ brings a number of benefits, including increased code readability, easier dependency management and flexibility in modifying applications.

---

### How to inject dependencies in Angular?

Angular allows the injection of necessary dependencies, such as classes, functions, or primitives, to classes decorated with `@Component`, `@Directive`, `@Pipe`, `@Injectable` and `@NgModule` by defining them as a constructor parameter:

```typescript
@Component({ … })
class UserComponent {
  constructor(private userService: UserService) {}
}
```

or using `inject` function:

#### `inject()`

```typescript
@Component({ … })
class UserComponent {
  private userService = inject(UserService);
}
```

The `inject` function in its current form was introduced in version 14. Aside from bringing a convenient and readable way of declaring dependencies, it also offers the following advantages:

- It allows the omission of explicit typing — let TypeScript do it for you.
- Class extension is made easier without the necessity of passing every argument to the base class _`constructor`_.
- Additionally, it lets the programmer move the logic to reusable functions — the downside here, however, is hiding dependencies inside the function.

```typescript
const getPageParam = (): Observable<string> =>
  inject(ActivatedRoute).queryParams.pipe(
    map((params) => params['page']),
    filter((pageParam) => pageParam !== null),
  );
```

It’s worth remembering that the inject function can only be used inside an injection context. This means

- Within a _`constructor`_,
- As a _`definition`_ of a class field,
- Inside the factory function, as `useFactory` in the _`Provider`_ interface, `@Injectable` decorator or a factory in the _`Injection token`_ definition,
- An API within the injection context, such as a router guard or a `runInInjectionContext` function callback.

---

### How does the Angular Injector work?

An abstraction called **`Injector`** is responsible for resolving dependencies. It can store an instance of a required dependency. If it already exists, it’s passed onto the consumer. Otherwise, a new instance is created and passed as a constructor parameter and stored in memory. Every dependency inside an `Injector` is a _`singleton`_ — which means there’s always only one instance.

To better demonstrate this process, let’s create a simple example. Let’s assume that we have a class representing some service:

```typescript
class SomeService {
  doSomething() {
    console.log('do something');
  }
}
```

and a class representing a component which uses the service:

```typescript
class Component {
  constructor(public service: SomeService) {}
}
```

The injector is responsible for storing and returning an instance of the dependency:

```typescript
class Injector {
  private container = new Map();

  constructor(private providers: any[] = []) {
    this.providers.forEach((service) => this.container.set(service, new service()));
  }

  get(service: any) {
    const serviceInstance = this.container.get(service);

    if (!serviceInstance) throw new Error('Service not provided');

    return serviceInstance;
  }
}
```

> _`private`_ - модифікатор доступу, який говорить, що екземпляр цього класу можна буде використати лише в самому класі

During the bootstrapping, Angular creates the _`Injector`_ and registers dependencies which will be passed to components:

```typescript
const injector = new Injector([SomeService]);
const component = new Component(injector.get(SomeService));
component.service.doSomething();
```

---

### `providedIn` property of `@Injectable({})`

In Angular, the _`providedIn`_ property is used in the _`@Injectable`_ decorator to specify where a service should be provided. Here are the possible values for providedIn:

- `'root'`: The service is available throughout the entire application as a singleton. This is the most common and recommended option for services that need to be accessible globally.

```typescript
@Injectable({
  providedIn: 'root',
})
export class MyService {}
```

- `'any'`: A new instance of the service is created for each lazy-loaded module that injects it. This can be useful for services that should not be shared across different modules.

```typescript
@Injectable({
  providedIn: 'any',
})
export class MyService {}
```

- `Specific Module`: You can specify a particular module where the service should be provided. This means the service will be available only within that module and its child modules.

```typescript
@Injectable({
  providedIn: SomeModule,
})
export class MyService {}
```

- `null`: If you set providedIn to null, the service will not be provided automatically. You will need to add it to the providers array of a component or module manually.

```typescript
@Injectable({
  providedIn: null,
})
export class MyService {}
```

These options allow you to control the scope and lifecycle of your services, optimizing your application’s performance and organization.

### Hierarchical Injectors in Angular

- `Element Injector` — registers dependencies defined in _`providers`_ inside the _`@Component`_ or _`@Directive`_ decorators. These dependencies are available for the component and its children.

```typescript
@Component({
  ...
  providers: [UserService]
})
export class UserComponent {}
```

- `Environment Injector` — child hierarchies of the environment injector are created whenever dynamically loaded components are created, such as with a router. In that case, the injector is available for components and its children. It is higher in the hierarchy than the element injector in the same component.

```typescript
const routes: Routes = [{ path: 'user', component: UserComponent, providers: [UserService] }];
```

- `Environment Root Injector` — contains globally available dependencies decorated with _`@Injectable`_ and having **providedIn** set to **"root"** or **"platform"**.

```typescript
@Injectable({ providedIn: 'root' })
export class UserService {
  name = 'John';
}
```

or defined in _`providers`_ of the _`ApplicationConfig`_ interface:

```typescript
bootstrapApplication(AppComponent, { providers: [UserService] });
```

> Decorator that marks a class as available to be provided and injected as a dependency:

```typescript
@Injectable(options?: ({ providedIn: Type<any> | "root" | "platform" | "any" | null }) & InjectableProvider)
```

> To achieve better optimization, it’s recommended to use the _`@Injectable`_ decorator. Such a definition makes dependencies _`tree-shakeable`_ — they are removed from bundled files if they haven’t been used.

- `Module Injector` — in module-based applications, this injector stores global dependencies decorated with _`@Injectable`_ and having **providedIn** set to **"root"** or **"platform"**. Additionally, it keeps track of dependencies defined in the _`providers`_ array within _`@NgModule`_. During compilation, Angular also recursively registers dependencies from eagerly loaded modules. Child hierarchies of _`Module Injector`_ are created by lazy loaded modules.

- `Platform Injector` — configured by Angular, this injector registers platform-specific dependencies such as _`DomSanitizer`_ or the _`PLATFORM_ID token`_. Additional dependencies can be defined by passing them to the _`extraProviders`_ array in the _`platformBrowserDynamic`_ function parameter.

- `Null Injector` — the highest injector in the hierarchy. Its job is to throw the error _`"NullInjectorError: No provider for …"`_ unless the _`@Optional`_ modifier was used.

#### Injectors Hierarchy

- If a component requires a dependency, Angular first looks for it in the `element injector` of the component.
- If it isn’t defined in the providers array, then the framework looks at the parent component. This process repeats for as long as Angular finds a dependency in an ancestor.
- If the dependency isn’t found, the next phase is searching in the `environment injector` (or the `module injector` in the case of module-based applications), and then the `environment root injector`.
- Finally, the `platform injector` is checked.
- If Angular reaches the `null injector` an error is thrown.

In this hierarchical order, if a dependency exists in more than injector, the instance defined on the lowest level, the one closest to the component is resolved.

**`Element Injector (Component)`** ---> **`Element Injector (Parent Component)`** ---> **`Element Injector (App Component)`** ---> **If not resolved** ---> **`Environment Injector`** ---> **`Environment Root Injector`** ---> **`Platform Injector`** ---> **`Null Injector`**

---

### Resolution Modifiers

- `@Self()`

Декоратор параметрів _`constructor() {}`_ який говорить _`DI framework`_ почати пошук залежностей з `Element injector`.
В такому разі сервіс потрібно додати в _`providers`_, інакше буде помилка `"Null Injector: NOT_FOUND"` .

```javascript
constructor(@Self() private roomsService: RoomsService) {}
```

- `@SkipSelf()`

Декоратор параметрів _`constructor() {}`_ який говорить _`DI framework`_ почати пошук залежностей з батьківського `Element Injector`. Локальний інжектор перевірятися на наявність провайдера не буде.

```javascript
constructor(@SkipSelf() private roomsService: RoomsService) {}
```

- `@Optional()`

Декоратор параметрів _`constructor() {}`_, який позначає параметр як опційну залежність. _`DI framework`_ повертає _`null`_, а не викидає помилку якщо залежність не буде знайдено.
Може бути використаний разом з іншими декораторами, які модифікують поведінку _`DI`_.

```javascript
constructor(@Optional() private loggerService: LoggerService) {}`
```

- `@Host()`

Декоратор параметрів _`constructor() {}`_ батьківського компонента або компонента хоста, якщо це наприклад компонент, через який відбувається _`content projection`_, який говорить _`DI framework`_ завершити створення подання _(view)_, перевіряючи інжектори дочірніх елементів і зупинятися при досягненні елемента хоста.

```javascript
constructor(@Host() private roomsService: RoomsService) {}
```

> Described decorators can be used for dependencies defined as `constructor` parameters. When we use the `inject` function, flags with names corresponding to decorators should be set in the options object:

```typescript
userService = inject(UserService, { optional: true, skipSelf: true });
```

---

### Dependency Injection Providers

- Class based provider
- Alias provider
- Value provider
- Factory provider

#### Class Provider

The class provider contains the option _`useClass`_ — its job is to create and resolve a new instance of the defined class. It replaces the class defined as a token by its extension, classes with different implementation, or its mock for testing purposes.

```typescript
@Injectable()
export class Logger {
  log(message: string) {
    console.log(message);
  }
}

@Injectable()
export class TimeLogger extends Logger {
  override log(message: string) {
    super.log(`${(new Date()).toLocaleTimeString()}: ${message}`}
  }
}

@Component({
  ...,
  providers: [ {provide: Logger, useClass: TimeLogger} ]
})
export class MyComponent {
  constructor(private readonly logger: Logger) {
    logger.log('Hello World');//5:17:35 PM: Hello World
  }
}
```

This example shows how to change implementation of dependency without making changes in the component itself.

#### Alias Provider

The alias provider maps one token to another, as defined in the _`useExisting`_ field. The first token is an _`alias`_ for the class associated with the second one. Angular doesn't create a new instance but instead resolves an existing one.

```typescript
@Component({
  ...,
  providers: [ TimeLogger, {provide: Logger, useExisting: TimeLogger} ]
})
export class MyComponent {
  constructor(private readonly logger: Logger) {
    logger.log('Hello World');//5:17:35 PM: Hello World
  }
}
```

This type of definition ensures that if the component depends on the _Logger_ or _TimeLogger_ classes, the existing instance of _TimeLogger_ is always resolved. It's worth noting the difference between _`useExisting`_ and _`useClass`_. If we were to use _`useClass`_ a new, independent instance of _TimeLogger_ would be created.

---

#### Value Provider

The value provider allows us to associate a static value defined within the `useValue` with a token. This technique is usually used for resolving configuration constants or mocking data in tests.

```typescript
@Component({
  ...,
  providers: [
    {
      provide: APP_CONFIG,
      useValue: { testEnv: !environment.production }
    }
  ]
})
export class MyComponent {
  readonly showTestEnvBanner = this.appConfig.testEnv;

  constructor(@Inject(APP_CONFIG) private readonly appConfig: AppConfig) {}
}
```

---

Також прикладом використання може бути сервіс у якому будуть зберігатися _`api endpoints`_. Для цього треба створити сервіс і інтерфейс:

`config.interface.ts`

```javascript
export interface AppConfig {
  apiEndpoint: string;
}
```

`config.service.ts`

```typescript
import { InjectionToken } from '@angular/core';
import { AppConfig } from './config.interface';
import { environment } from '../../environments/environment';

export const APP_SERVICE_CONFIG = new InjectionToken<AppConfig>('AppConfig');

export const APP_CONFIG: AppConfig = {
  apiEndpoint: environment.apiEndpoint,
};
```

> This is nothing more but just a unique identifier because all things that we inject must be **unique** for the injector.

І далі зареєструвати цей сервіс в _`providers`_ головного модуля _`app.module.ts`_ або в _`app.config.ts`_ для _`standalone application`_:

`app.config.ts`

```javascript
import { ApplicationConfig, provideZoneChangeDetection } from '@angular/core';
import { provideRouter } from '@angular/router';
import { routes } from './app.routes';
import { APP_CONFIG, APP_SERVICE_CONFIG } from './config/config.service';

export const appConfig: ApplicationConfig = {
  providers: [
    provideZoneChangeDetection({ eventCoalescing: true }),
    provideRouter(routes),
    //реєструємо сервіс і передаємо константу через useValue
    { provide: APP_SERVICE_CONFIG, useValue: APP_CONFIG },
  ],
};
```

Після цього цей сервіс вже можна використовувати:

```javascript
constructor(@Inject(APP_SERVICE_CONFIG) private config: AppConfig) {
  console.log(config.apiEndpoint);
}
```

`@Inject(token: any): any`

> Parameter decorator on a dependency parameter of a class constructor that specifies a custom provider of the dependency.
>
> When @Inject() is not present, the injector uses the type annotation of the parameter as the provider.

---

#### Factory Provider

Using the factory provider, let's create a dependency based on runtime values by calling a function defined in _`useFactory`_.

```typescript
@Injectable()
export class SecretMessageService {
  constructor(
    private readonly logger: Logger,
    private readonly isAuthorized: boolean
  ) {}

  private secretMessage = 'My secret message';

  getSecretMessage(): string | null {
    if (!this.isAuthorized) {
        this.logger.log('Authorize to get secret message!');
        return null;
    }

    return this.secretMessage;
  }
}

@Component({
  ...,
  providers: [
    {
      provide: SecretMessageService,
      useFactory: (logger: Logger, authService: AuthService) =>
        new SecretMessageService(logger, authService.isAuthorized),
      deps: [Logger, AuthService]
    }
  ]
})
export class MyComponent {
  constructor(private readonly secretMessageService: SecretMessageService) {
    const secretMessage = this.secretMessageService.getSecretMessage()
  }
}
```

This provider contains an additional field, _`deps`_, which is an array of tokens passed as arguments of the factory function. The **order** in which they are defined is **important**.

For functions with more arguments, it may be more convenient and flexible to replace them with a single _`Injector`_, which allows Angular to retrieve the needed dependencies inside the function. It would look something like this:

```typescript
{
  provide: SecretMessageService,
  useFactory: (injector: Injector) => {
    const logger = injector.get(Logger);
    const authService = injector.get(AuthService);
    return new SecretMessageService(logger, authService.isAuthorized)
  },
  deps: [Injector]
}
```

---

Another interesting use case is when we don't know in advance what dependency we want to use, and it is determined by some runtime condition. To use a simple example, we could have a service that connects to an external API, and we want to limit the number of requests sent so as not to generate additional costs:

```typescript
{
  provide: ThirdPartyService,
  useFactory: (appConfig: AppConfig, http: HttpClient) =>
    appConfig.testEnv ? new ThirdPartyMockService() : new ThirdPartyService(http),
  deps: [APP_CONFIG, HttpClient]
}
```

---

Як приклад використання _`factory`_ може бути використання _`Storage API`_, а саме _`localStorage`_.

`local-storage.token.ts`

```typescript
import { InjectionToken } from '@angular/core';

export const LocalStorageToken = new InjectionToken<Storage>('LocalStorage', {
  providedIn: 'root',
  factory: () => localStorage,
});
```

В цьому випадку непотрібно реєструвати сервіс, можна одразу використовувати _`localStorage`_

```typescript
constructor(@Inject(LocalStorageToken) private localStorage: Storage) {}
```

---

### Why do we need an Injection Token?

In the case of a value provider, an injection token is necessary. But why do we need it? Each dependency in an injector has to be described by a **unique identifier** — a _`token`_ — so that Angular knows what should be resolved.

For classes, as well as services, a token is a **reference** to the class itself. But what if the dependency is not a class, but an object, or even a primitive type? We cannot use an interface as a token, because such a construct does not exist in JavaScript — it will be removed during transpilation.

Theoretically, we can use a string as a token:

```typescript
{ provide: 'APP_CONFIG', useValue: {testEnv: !environment.production} }
```

However, this solution has a number of drawbacks. It's very easy to imagine making a typo or accidentally using the same value for different dependencies. This is where the _`InjectionToken`_ comes to the rescue:

```typescript
interface AppConfig {
  testEnv: boolean;
}
export const APP_CONFIG = new InjectionToken<AppConfig>('app config');
```

The value given as an argument to the constructor is not an identifier, but a description — the **identifier** created by _`InjectionToken`_ is always **unique**.

As you can see from the example above, we use the _`@Inject()`_ decorator by passing a reference to the token in question as an argument.

If we want the token to globally represent a value and be **tree-shakeable**, we can additionally use the option object:

```typescript
export const APP_CONFIG = new InjectionToken<AppConfig>('app config', {
  providedIn: 'root',
  factory: () => ({ testEnv: !environment.production }),
});
```

Another parameter we can configure in the provider is **`multi`**. Setting its value to _`true`_ allows us to bind multiple dependencies to a single token and return them as an array. This prevents the default behavior of overwriting dependencies.

To illustrate this, let’s create a token to which we will then assign two values. Here is the result we get:

```typescript
export const LOCALE = new InjectionToken<string>('locale');

@Component({
  ...,
  providers: [
    { provide: LOCALE, useValue: 'en'},
    { provide: LOCALE, useValue: 'pl'}
  ]
})
export class WithoutMultiComponent {
  constructor() {
    console.log(inject(LOCALE));  // ['pl']
  }
}

@Component({
  …,
  providers: [
    { provide: LOCALE, useValue: 'en' multi: true },
    { provide: LOCALE, useValue: 'pl' multi: true }
  ]
})
export class WithMultiComponent {
  constructor() {
    console.log(inject(LOCALE));  // ['en' 'pl']
  }
}
```

---

One of the most common use cases for this are _`interceptors`_. According to the **_Single Responsibility Principle_**, each interceptor is responsible for a different action, and a _`multi-provider`_ allows each interceptor to act even though they use the same token.

```typescript
providers: [
  { provide: HTTP_INTERCEPTORS, useClass: LoggingInterceptor, multi: true },
  { provide: HTTP_INTERCEPTORS, useClass: AuthInterceptor, multi: true },
];
```

### Forward Ref

The forwardRef function is used to create indirect references that are not resolved immediately. Since the order in which classes are defined matters, it is particularly useful when references are looped or when a component tries to use a reference to itself in its configuration:

```typescript
@Component({
  ...,
  providers: [
    {
      provide: NG_VALUE_ACCESSOR,
      multi: true,
      useExisting: forwardRef(() => CustomInputComponent)
    }
  ]
)}
export class CustomInputComponent { ... }
```

### Additional Benefits of Dependency Injection

In addition to code modularity and greater flexibility, creating loose dependencies makes testing easier. Replacing dependencies with their mock counterparts allows us to isolate the functionalities being tested and check their behavior in a controlled environment. Admittedly, testing frameworks take care of this for us, but in the case of complex services we can mock dependencies manually:

```typescript
class MyServiceMock {
  getData() {
    return of(...)
  }
}

describe(MyComponent, () => {
  beforeEach(() => {
    TestBed.configureTestingModule({
      provide: [{ provide: MyService, useClass: MyServiceMock }]
    })
  }
}
```

A design pattern that we can use using _`Dependency Injection`_ is `port-adapter`. It assumes that one module defines the shape of an abstraction and another provides its implementation. This allows us to decouple logic and loosen dependencies between modules, since the implementation can be swapped on-the-fly. This is where an abstract class that creates an interface and can also be used as a token works great:

```typescript
abstract class NotificationPort {
  abstract notify(message: string): void;
}

@Injectable()
class SnackbarNotificationAdapter extends NotificationPort {
  private readonly snackbarService = inject(SnackbarService);

  notify(message: string): void {
    this.snackbarService.open(message);
  }
}

@Injectable()
  class ToastNotificationAdapter extends NotificationPort {
    private readonly toastNotificationService = inject(ToastNotificationService);

    notify(message: string): void {
    this.toastNotificationService.push(message, Theme.INFO)
    }
}

{ provide: NotificationPort, useClass: SnackbarNotificationAdapter }
```

## HTTP_INTERCEPTORS and APP_INITIALIZER

`HTTP_INTERCEPTORS` - _`Dependency Injection Token`_, який використовується для додавання інтерсепторів, як зрозуміло з назви, це сервіс, який може перехоплювати запити на сервер і відповіді від нього.

> Всередині інтерсептора не можна модифікувати оригінальний запит. Потрібно склонувати його і тоді вже з ним працювати.

Приклад створення _`class-based`_ сервісу

```typescript
@Injectable()
export class RequestInterceptor implements HttpInterceptor {
  constructor() {}

  intercept(request: HttpRequest<unknown>, next: HttpHandler): Observable<HttpEvent<unknown>> {
    if (request.method === 'POST') {
      const newRequest = request.clone({
        headers: new HttpHeaders({ token: 'testTokenValue' }),
      });
      return next.handle(newRequest);
    }
    return next.handle(request);
  }
}
```

_`app.module.ts`_

```typescript
@Module({
  declarations: [],
  imports: [],
  providers: [{
    provide: HTTP_INTERCEPTORS,
    useClass: RequestInterceptor,
    multi: true
  }],
})
```

---

`APP_INITIALIZER` - _`Dependency Injection Token`_, який використовується для додавання сервісу, який використовується для виконання якоїсь логіки перед стартом застосунку.

- user specific data
- translations
- some external configurations
- redirections
- authentication.

_`init.service.ts`_

```typescript
@Injectable({
  providedIn: 'root',
})
export class InitService {
  config: any;
  constructor(private http: HttpClient) {}

  init() {
    return this.http.get('assets/config.json').pipe(tap((config) => (this.config = config)));
  }
}
```

Реєструємо як сервіс (варіант з використанням модулів)

_`app.module.ts`_

```typescript
function initFactory(initService: InitService) {
  return () => initService.init();
}

@Module({
  declarations: [],
  imports: [],
  providers: [{
    provide: APP_INITIALIZER,
    useFactory: initFactory,
    deps: [InitService],
    multi: true
  }],
})
```

> Як _`HTTP_INTERCEPTORS`_ так і _`APP_INITIALIZER`_ можна додати більше ніж один, лише треба пам'ятати, що важливий порядок їх додавання, бо виконуватись вони будуть в тому ж порядку.

---

## Routing

`app-routing.module.ts`

- Підключення роутінгу: _`import { RouterModule, Routes } from "@angular/router";`_
- Далі передати в _`imports: [RouterModule.forRoot(appRoutes)]`_, де _`appRoutes`_ - змінна, куди ми прописали наші шляхи ось в такому вигляді:

```typescript
const appRoutes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'users', component: UsersComponent },
  { path: 'servers', component: ServersComponent },
];
```

- В самих лінках використовується `routerLink='/somePath'` або `[routerLink]="['/tags', tagName]"` замість _`href`_. Тоді при переході сторінка не буде перезавантажуватися, як з використанням _`href`_. `routerLink` завжди знає, для якого компонента він застосовується, так як для нього прописаний конкретний шлях.
- Для рендерингу темплейту, в розмітку додається _`<router-outlet></router-outlet>`_, після якого Angular додасть в DOM компонент, який відповідатиме поточному шляху, відповідно до того, що було налаштовано в `routes: Routes = []`.
- **Абсолютний шлях**: `/somePath` - завжди додається до кореневого домену
- **Відносний шлях**: `somePath` - додається до поточного шляху
- Для додавання стилів темплейту, шлях якого зараз обраний, використовується атрибут `routerLinkActive="cssClass"` і атрибут `[routerLinkActiveOptions]="{ exact: true }"` - якщо потрібно, щоб перевірявся і порівнювався весь шлях, бо по дефолту для шляху `/somePath`, при тому, що просто `/` це кореневий шлях, стилі будуть застосовуватися і для кореневого шляху і для `/somePath`.

---

### Navigating Programmatically

Для цього підключається сервіс **`Router`** через конструктор і викликається метод **`navigate`**, куди передається **абсолютний** шлях

```typescript
constructor(private router: Router) {}
onLoadServers() {
  this.router.navigate(['/servers'])
}
```

При використанні **відносного** шляху в методі **`navigate`**, потрібно передати другий аргумент, так як цей метод не знає, на якому компоненті він застосовується і який його шлях. Аргумент, який вказує, відносно якого шляху робити перехід, тобто до якого додавати шлях з першого аргумента.
Для цього підключається модуль **`ActivatedRoute`** і як другий аргумент передаємо `{ relativeTo: this.route }`

```typescript
constructor(private route: ActivatedRoute) {}
onReload() {
  this.router.navigate(['servers'], { relativeTo: this.route })
}
```

---

#### Fetching Route Parameters

`ActivatedRoute` service:

- Allow to read the router data
- Allows access to snapshot data
- Allows to access data from Route Config

Також можна отримати дані з адресного рядка з відповідних параметрів, які ми додаємо сюди: `{ path: "users/:id/:name", component: UserComponent }` при першій ініціалізації компонента.

Якщо наприклад в адресному рядку буде така адреса `'localhost:4200/users/10/Max'`, то, підключивши модуль **`ActivatedRoute`**, передавши його в `constructor(private route: ActivatedRoute) {}` і використавши метод _`this.route.snapshot.params["id"]`_ - ми повернемо значення 10. Але якщо потім ми програмно передамо нові параметри запиту з середини цього компонента, наприклад додамо в темплейт лінку:

```html
<a [routerLink]="['/users', 5, 'Ira']"></a>
```

Angular не буде оновлювати компонент. Тому, якщо ми використовували дані з параметрів запиту на сторінці, вони не будуть оновлені.

---

#### Fetching Route Parameters Reactively

Для того, щоб асинхронно оновлювати дані з параметрів запиту, можна використовувати _`Observables`_.

```typescript
import { ActivatedRoute, Params } from '@angular/router';
import { Subscription } from 'rxjs';

в компоненті
paramsSubscription: Subscription;

this.paramsSubscription = this.route.params.subscribe( // цей метод може приймати 3 функції як аргументи
(params: Params) => {
  this.user.id = params['id'];
  this.user.name = params['name'];
})
```

**`Params`** - належить до _`Observables`_. Це функціонал, який доданий сторонньою бібліотекою _`rxjs`_, але широко використовується в Angular.

Він реалізує паттерн "асинхронного наглядача", що в даному випадку і відбувається. _`Observable`_ це легкий спосіб підписатись на якусь асинхронну подію, яка може відбутися колись в певний момент.

> Весь код всередині _`subscribe()`_ буде виконано лише тоді, коли відбудуться якісь зміни в _`params`_

Також, окрім створення підписки, можна напряму отримати і, якщо потрібно, використати в темплейті дані з параметрів рядка, використовуючи стрім.

```typescript
id$ = this.route.params.pipe(map((params) => params['id']));
```

і далі через `async` pipe відобразити.

- **`paramMap()`** - ще один зручний метод сервісу `ActivatedRoute`, за допомогою якого можна більш гнучко працювати з параметрами адреси. Має такі методи як `get()`, `getAll()`, `has()`, `keys()`.

```typescript
id$ = this.route.paramMap.pipe(map((params) => params.get('id)))
```

---

#### Passing Query parameters and Fragments

Наприклад наша адреса має такий вигляд:
`<http://localhost:4200/servers/5/edit?allowEdit=1#loading>`:

Сконструювати посилання можна таким чином: _`[routerLink]="['/servers', 5, 'edit']"`_

За додаткові параметри і фрагменти відповідають ці атрибути:

- _`[queryParams]="{ allowEdit: '1' }"`_
- _`[fragment]="'loading'`_ або _`"fragment="loading"`_

Також ці дані можна передати через метод _`navigate()`_ модуля **`Router`**

```typescript
this.router.navigate(['/servers', id, 'edit'], {
  queryParams: { allowEdit: '1' },
  fragment: 'loading',
});
```

- _`navigate()`_ створює `URL` динамічно з переданих даних.

- _`navigateByUrl('path')`_ просто отримує вже готову адресу, по якій буде здійснено перехід

---

#### Retrieving Query parameters and Fragments

Отримати їх можна через модуль **`ActivatedRoute`**

```typescript
constructor(
  private serversService: ServersService,
  private route: ActivatedRoute) { }

//OnInit
this.route.queryParams.subscribe();
this.route.fragment.subscribe();
```

---

#### Query params handling

**`queryParamsHandling`** in Angular is a _**routing strategy**_ that allows developers to control how `query parameters` are handled when the URL changes

By default, Angular resets the `query parameters` when the URL changes. However, with `queryParamsHandling`, you can choose to:

1. **Merge**: Merge new parameters with the current parameters.
2. **Preserve**: Preserve the current parameters.
3. **Replace** (default): Replace the current parameters with new parameters.

This feature is particularly useful when navigating between different views in your Angular app and managing query parameters. 😊🚀

---

### Child Routes

Вкладені або дочірні маршрути додаються через відповідну властивість у модулі

```typescript
const routes: Routes = [
  { path: "", component: HomeComponent },
  {
    path: "users",
    component: UsersComponent,
    children: [{ path: ":id/:name", component: UserComponent }],
  }
```

> Потрібно пам'ятати, що порядок `{path}` важливий, динамічні параметри повинні бути в кінці масиву, щоб шлях правильно парсився

І потім додати _`<router-outlet></router-outlet>`_ в потрібному місці батьківського темплейта

---

#### queryParamsHandling

Для того, щоб передати _`queryParams`_ далі по маршруту, наприклад якщо ми на клік викликаємо метод _`navigate()`_ потрібно другим параметром в цей метод передати `{ queryParamsHandling: 'preserve/merge' }`
`merge` - щоб об'єднати параметри і `preserve` - щоб залишити поточні. По дефолту значення `replace`, тобто поточні будуть замінені новими.

---

#### Redirecting and Wildcard Routes

Для обробки маршрутів, які не прописані в основному модулі, використовується `Wildcard` маршрут `**` і властивість _`redirectTo`_, куди записується шлях, на який перенаправляти при переході на не існуючий маршрут.

```typescript
{ path: 'page-not-found', component: PageNotFoundComponent },
{ path: '**', redirectTo: '/page-not-found' }
```

---

### Router Events

Кожного разу, коли відбувається навігація, тобто адреса змінюється, _`Router`_ сервіс емітить ланцюг подій, які можна перехопити і при потребі використати.

```typescript
ngOnInit() {
  this.router.events.subscribe((event) => console.log(event));
}
```

```javascript
NavigationStart {id: 2, url: '/recipes', navigationTrigger: 'imperative', restoredState: null}
RoutesRecognized {id: 2, url: '/recipes', urlAfterRedirects: '/recipes', state: RouterStateSnapshot}
GuardsCheckStart {id: 2, url: '/recipes', urlAfterRedirects: '/recipes', state: RouterStateSnapshot}
ChildActivationStart {snapshot: ActivatedRouteSnapshot}
ActivationStart {snapshot: ActivatedRouteSnapshot}
ChildActivationStart {snapshot: ActivatedRouteSnapshot}
ActivationStart {snapshot: ActivatedRouteSnapshot}
ChildActivationStart {snapshot: ActivatedRouteSnapshot}
ActivationStart {snapshot: ActivatedRouteSnapshot}
GuardsCheckEnd {id: 2, url: '/recipes', urlAfterRedirects: '/recipes', state: RouterStateSnapshot, shouldActivate: true}
ResolveStart {id: 2, url: '/recipes', urlAfterRedirects: '/recipes', state: RouterStateSnapshot}
ResolveEnd {id: 2, url: '/recipes', urlAfterRedirects: '/recipes', state: RouterStateSnapshot}
ActivationEnd {snapshot: ActivatedRouteSnapshot}
ChildActivationEnd {snapshot: ActivatedRouteSnapshot}
ActivationEnd {snapshot: ActivatedRouteSnapshot}
ChildActivationEnd {snapshot: ActivatedRouteSnapshot}
ActivationEnd {snapshot: ActivatedRouteSnapshot}
ChildActivationEnd {snapshot: ActivatedRouteSnapshot}
Scroll {routerEvent: NavigationEnd, position: null, anchor: null}
NavigationEnd {id: 2, url: '/recipes', urlAfterRedirects: '/recipes'}
```

Кожна окрема серія таких подій матиме властивість `id`, де значення це число, яке з кожним новим переходом інкрементується.

---

## Route Guards

- `canActivate`
- `canActivateChild`
- `canDeactivate`
- `canLoad`
- `Resolve`

> Один і той же сервіс, які ми використовуємо як `Guard`, можна застосовувати для декількох `lazy loaded` шляхів і навіть для різних _guards_ в межах одного шляху.

```typescript
const routes: Routes = [
  { path: 'login', component: LoginComponent, canActivate: [LoginGuard] },
  { path: 'employee', component: EmployeeComponent, canActivate: [LoginGuard] },
  {
    path: 'rooms',
    loadChildren: () => import('./rooms/rooms.module').then((m) => m.RoomsModule),
    canActivate: [LoginGuard],
    canLoad: [LoginGuard],
  },
  {
    path: 'booking',
    loadChildren: () => import('./booking/booking.module').then((m) => m.BookingModule),
    canActivate: [LoginGuard],
  },
  { path: '**', component: NotFoundComponent },
];
```

### `canActivate`

Захист реалізовується за допомогою вбудованого в пакет `@angular/router` інтерфейсу **`СanActivate`**. Для цього потрібно створити сервіс **(all Guards are Service)**, який буде імплементувати цей інтерфейс і прописати потрібну логіку:

`auth.guard.ts`

```typescript
export class AuthGuard implements CanActivate {
  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot,
  ): Observable<boolean> | Promise<boolean> | boolean {
    return this.authService.user$.pipe(
      take(1), //Так як нам не потрібно, щоб наш захист слухав нові дані цього Subject'a постійно, а лише щоб значення user$ бралось лиш раз і все, аж до поки захист не буде викликано повторно
      map((user) => {
        const isAuth = !!user; //converting to boolean
        if (isAuth) {
          return isAuth;
        }
        return this.router.createUrlTree(['/auth']); //редіректимось на сторінку авторизації якщо ми не були залогінені попередньо
      }),
    );
  }
}
```

`feature-routing.module.ts`

```typescript
const routes: Routes = [
  {
    path: '',
    component: RecipesComponent,
    canActivate: [AuthGuard], //передаємо Guard
    children: [
      {
        path: '',
        component: RecipeStartComponent,
      },
      {
        path: 'new',
        component: RecipeEditComponent,
      },
      {
        path: ':id',
        component: RecipeDetailsComponent,
        resolve: [RecipesResolverService],
      },
      {
        path: ':id/edit',
        component: RecipeEditComponent,
        resolve: [RecipesResolverService],
      },
    ],
  },
];

@NgModule({
  imports: [RouterModule.forChild(routes)], //forChild() використовується для модулів, які ми потім будемо імпортувати в основний модуль
  exports: [RouterModule],
})
export class RecipesRoutingModule {}
```

### `canActivateChild`

Для захисту дочірніх маршрутів використовується інтерфейс **`canActivateChild`**, який імплементується таким же способом.

**АЛЕ!!!** в _`Routes`_ ми маємо передати замість _`canActivate`_ ---> _`canActivateChild`_. В такому разі захист працюватиме для всіх вкладених маршрутів.

### **`canLoad`** and **`canActivate`**

- `canActivate` checks after the data of a lazy loaded module have been loaded whether the loaded stuff is allowed to be shown,

- `canLoad` checks first whether it is necessary to load the data at all

- `canActivate`- click button/load data - call guard - display page (or don't)

- `canLoad`: click button - call guard - load data/display page (or don't)

> `canLoad` can only be applied to lazy loaded routes

### Controlling Navigation with **`canDeactivate`**

Функціонал, який додається для зручності користувача, де можна задати логіку, яка буде, наприклад, перепитувати користувача, чи точно він бажає покинути сторінку, чи, наприклад, нагадати про щось, що клієнт міг забути натиснути і тд.

```typescript
export class CanDeactivateGuard implements canDeactivate<interFaceName> {}
```

Angular ініціалізує виконання коду з `CanDeactivateGuard` як тільки ми залишимо шлях або компонент, який рендериться при переході по цьому шляху

### Resolving Dynamic Data with the `Resolve` Guard

Такий підхід дає можливість виконати якийсь код до того, як відбудеться перехід по якомусь шляху і відбудеться рендер компонента. Таким чином, наприклад, можна отримати якісь дані, які будуть потім потрібні компоненту. Це така собі альтернатива до хуку _`OnInit`_, але все ж код виконується ще до завантаження компонента.

Імплементація відбувається через створення класу, який імплементує клас `Resolve`:

```typescript
export class ServerResolver implements Resolve<genericType> {
  resolve(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Server | Observable<genericType> | Promise<genericType> {
    //виконуємо якусь логіку, наприклад пріфетч даних з бекенду
  }
}

//потім додаємо до наших шляхів відповідний параметр:
{
  path: ":id",
  component: ServerComponent,
  resolve: { serverData: ServerResolver }, //server - довільна назва, ServerResolver - написаний нами СЕРВІС
}

//отримуємо доступ до цих даних в компоненті
export class DummyComponent implements NgOnInit {
  //другий спосіб через stream і async pipe
  data$ = this.route.data.pipe(pluck('serverData'))

  constructor(private route: ActivatedRoute) {}

  ngOnIit() {
    //перший спосіб
    this.route.data.subscribe((data) => data['serverData']) //наші дані
  }
}
```

---

## RxJS and Observables

Концепція **RxJS** полягає в тому, щоб працювати з даними через (_`stream`_), тобто потік даних. В цій концепції є **producer**, той, хто створює якісь дані і надає їх у вигляді _`Observable`_, і **consumer** або _`Observer`_, той хто через _`subscription`_, тобто підписку, їх споживає, отримує.

Також в **RxJS** використовується _`push-based`_ архітектура. На відміну від _`pull-based`_ архітектури, де _`consumer`_ має запитувати кожне значення, яке _`producer`_ створив, вручну і, скоріше за все, це відбудеться через певний проміжок часу, в _`push-based`_ архітектурі _`consumer`_ отримує ці значення одразу ж як тільки _`producer`_ їх створив через зареєстрований _`next handler`_.

> pull-based architecture -->
>
> getData -> addData -> getData
>
> push-based architecture -->
>
> getData -> continuous stream of data

Це означає, що якщо _`producer`_ згенерував нові дані, вони надсилаються напряму в потік (_`stream`_) і коли він оновиться, всі, хто має _`subscription`_ на цей _`stream`_, отримають ці дані.

---

### Key concepts

- **`Observable`** --> is an object that emits a sequence of items over time, either asynchronously or synchronously. Represents a _`stream`_, or source of data that can arrive over time.

- **`Observer`** --> you write the code which gets executed(Handle Data, Handle Error, Handle Completion). Is a collection of callbacks that knows how to listen to values delivered by the Observable.

- **`Subscription`** --> represents the execution of an Observable, is primarily useful for cancelling the execution.

- **`Operators`** --> are pure functions that enable a functional programming style of dealing with collections with operations like map, filter, concat, reduce, etc.

- **`Subject`** --> is equivalent to an EventEmitter, and the only way of multicasting a value or event to multiple Observers.

- **`Schedulers`** --> are centralized dispatchers to control concurrency, allowing us to coordinate when computation happens on e.g. setTimeout or requestAnimationFrame or others.

---

### Observable

An _`Observable`_ is the main tool provided by the _`RxJS`_ library whose _`Angular`_ uses extensively. As with a regular _`JavaScript Promise`_, the goal of an Observable is to handle asynchronous events.

It can be used to represent data from a server, a UI event, or any other kind of data stream.

An observable can have multiple observers subscribed to it and will notify them whenever new values are emitted.

_`Creating Observable manually`_

```typescript
observable = new Observable<number>((observer) => {
  observer.next(1);
  observer.error('Error');
  observer.complete();
});
```

You can create an _`Observable`_ from nearly anything, but the most common use case in RxJS is from _`events`_. This can be anything from mouse moves, button clicks, input into a text field, or even route changes.

The easiest way to create an observable is through the built in _`creation`_ functions. For example, we can use the _`fromEvent`_ helper function to create an observable of mouse click events:

```typescript
// import the fromEvent operator
import { fromEvent } from 'rxjs';

// grab button reference
const button = document.getElementById('myButton');

// create an observable of button clicks
const myObservable = fromEvent(button, 'click');
```

At this point we have an observable but it's not doing anything.
**This is because observables are cold, or do not activate a producer (like wiring up an event listener), until there is a...**

---

### Subscription

_`Subscriptions`_ are what set everything in motion. You can think of this like a faucet, you have a stream of water ready to be tapped (observable), someone just needs to turn the handle. In the case of observables, that role belongs to the subscriber.

To create a _`Subscription`_, you call the **`subscribe`** method, supplying a function (or object) - also known as an **`observer`**. This is where you can decide how to react(-ive programming) to each event. Let's walk through what happens in the previous scenario when a subscription is created:

```typescript
// import the fromEvent operator
import { fromEvent } from 'rxjs';

// grab button reference
const button = document.getElementById('myButton');

// create an observable of button clicks
const myObservable = fromEvent(button, 'click');

// for now, let's just log the event on each click
const subscription = myObservable.subscribe((event) => console.log(event));
```

In the example above, calling _`myObservable.subscribe()`_ will:

1. Set up an event listener on our button for click events.

2. Call the function we passed to the subscribe method (observer) on each click event.

3. Return a subscription object with an unsubscribe which contains clean up logic, like removing appropriate event listeners.

---

It's important to note that each _`Subscription`_ will create a **`new execution context`**. This means calling `subscribe` a second time will create a new event listener:

```typescript
// addEventListener called
const subscription = myObservable.subscribe((event) => console.log(event));

// addEventListener called again!
const secondSubscription = myObservable.subscribe((event) => console.log(event));

// clean up with unsubscribe
subscription.unsubscribe();
secondSubscription.unsubscribe();
```

By default, a subscription creates a one on one, one-sided conversation between the observable and observer. This is also known as **`unicasting`**.

If we have one observable, many observers - you will take a different approach which includes **`multicasting`** with _`Subjects`_ (either explicitly or behind the scenes).

---

`Subscribing to data with Happy path callback`

```typescript
ngOnInit(): void {
  this.roomsService.getRooms().subscribe((rooms) => {
    this.roomList = rooms;
  });
}
```

> Ми маємо змогу підписатися на результат виклику _`getRooms()`_ по тій причині, що цей метод повертатиме _`Observable`_.

`Subscribing to data with full notation`

```typescript
ngOnInit(): void {
  this.observable.subscribe({
    next: (value) => console.log(value),
    error: (error) => console.log(error),
    complete: () => console.log('Completed'),
  });
}
```

---

### Promise vs Observable

The key difference between an _`Observable`_ and a _`Promise`_ is that Observables are **lazy**. You can declare how your data should be handled once received, but you will then need to explicitly _`subscribe`_ to trigger the asynchronous call.

In other words, **making the call and handling the results are separated operations**.

Whereas with a _`Promise`_, when you call the _`then`_ function, you are actually doing both operations at once. **It triggers the call and handles the result**.

1. По-перше, після створення нашого стріма (через об'єкт _`Observable`_ або через оператори створення `of`, `from`) потрібно обов'язково підписатись через _`subscribe`_, без підписки стрім не буде працювати і ми опинимось у вічному очікуванні, на відміну від _`Promise`_, якому підписка не потрібна.

2. По-друге, _`stream`_ — це довільний набір даних, який можна доповнювати, перезаписувати, трансформувати, фільтрувати, об'єднувати (об'єднувати з іншими стрімами), переривати, що не властиво _`Promise`_, він просто створюється -> і викликається.

3. Ну і по-третє, стрім можна уявити як щось з нашого життя, підписка на наш Medium канал, як тільки вийде нова стаття, ви обов'язково про неї дізнаєтесь, звичайно, якщо підпишетесь

> > Якщо `Promise` — це константа, то `Observable(stream)` — це **Array<змінних>**
>
> Загалом використовувати _`Promise`_ в Angular це _`anti-pattern`_

Як висновок:

- _`Promise`_ поверне значення лише раз і завершиться
- _`Promise`_ не можна відмінити на відміну від _`Observable`_
- У _`Promise`_ немає допоміжних методів, які додають зручності для роботи з даними. Вся логіка пишеться в _`then`_
- _`Promise`_ не є lazy, тобто він виконається, навіть якщо ми не викликатимемо _`then`_ для нього
- _`Promise`_ помилку ми можемо обробити лише в самому _`Promise`_

---

### Operators

_`Operators`_ offer a way to manipulate values from a source, returning an observable of the transformed values.

Такі собі посередники, які можна застосовувати до даних, які ми отримуємо від _`Observable`_ перед тим, як ці дані будуть передані через _`Subscription`_ до _`Observer`_ для подальшої робити з ними.

Їх можна застосовувати до будь-яких _`Observable`_ викликаючи метод **_`pipe()`_**, який є у кожного _`Observable`_.

Всі оператори можна поділити на **8** категорій:

1. Creation operators
2. Combination operators
3. Conditional operators
4. Error handling operators
5. Filtering operators
6. Multicasting operators
7. Transformation operators
8. Utility operators

#### Map operators difference

```typescript
const example = (operator: any) => () => {
  from([0, 1, 2, 3, 4]) //? first is outer Observable
    .pipe(
      operator(
        (x: any) => of(x).pipe(delay(1000)), //? second is inner Observable
      ),
    )
    .subscribe({
      next: console.log,
      error: console.error,
      complete: () => {
        console.log(`${operator.name} completed`);
      },
    });
};
```

> from() ітерує значення в масиві і для кожного значення повертає новий Observable

- **`mergeMap()`**

  - Map to Observable, emit values. Внутрішній і зовнішній Observables не блокують виконання один одного, тобто значення з зовнішнього попадають у внутрішній одразу по мірі їх створення і внутрішній починає працювати одразу ж як отримав перше значення з зовнішнього стріма. Тобто по суті вони відпрацьовують паралельно.
  - `0, 1, 2, 3, 4, mergeMap completed` - з затримкою в 3 секунди отримаємо одразу всі значення

- **`concatMap()`**

  - Map values to inner observable, subscribe and emit in order. Оператор чекає на завершення кожного зі створених Observables. Тобто контролюватиме, щоб внутрішній Observable чекав на завершення виконання зовнішнього і навпаки. Тобто по суті observables відпрацьовують в порядку створення, один за одним.
  - Приклад використання це 2 апі запита де для другого запита (inner observable) нам потрібні дані з першого (outer observable)
  - `0, 1, 2, 3, 4, concatMap completed` - з затримкою в 3 секунди між кожним значенням отримаємо всі значення

- **`switchMap()`**

  - Map to observable, complete previous inner observable, emit values. Оператор чекає поки повністю завершиться зовнішній Observable і лише тоді його останнє значення передається у внутрішній Observable
  - `4, switchMap completed` - з затримкою в 3 секунди отримаємо останнє значення

- **`exhaustMap()`**
  - Map to inner observable, ignore other values until that observable completes. All next observables are ignored until observable will not be completed. Як тільки зовнішній observable згенерував значення, внутрішній одразу ж почне виконуватися і поки він не завершиться, навіть якщо зовнішній продовжить емітити нові значення, вони будуть просто ігноруватися.
  - Приклад використання це ігнорування кліків на кнопку, яка робить якийсь запит на сервер, доки цей запит не завершиться
  - `0, exhaustMap completed` - з затримкою в 3 секунди отримаємо перше значення

#### Pipe

The _`pipe`_ function is the assembly line from your observable data source through your operators. Just like raw material in a factory goes through a series of stops before it becomes a finished product, source data can pass through a _`pipe-line`_ of operators where you can manipulate, filter, and transform the data to fit your use case. It's not uncommon to use 5 (or more) operators within an observable chain, contained within the pipe function.

For instance, a typeahead solution built with observables may use a group of operators to optimize both the request and display process:

```typescript
// observable of values from a text box, pipe chains operators together
inputValue
  .pipe(
    // wait for a 200ms pause
    debounceTime(200),
    // if the value is the same, ignore
    distinctUntilChanged(),
    // if an updated value comes through while request is still active cancel previous request and 'switch' to new observable
    switchMap((searchTerm) => typeaheadApi.search(searchTerm)),
  )
  // create a subscription
  .subscribe((results) => {
    // update the dom
  });
```

> Кількість операторів, які можна передати - необмежена, вказуються через кому.

---

### Subjects

**`new Subject()`** - is a special type of Observable that allows values to be multicasted to many Observers.

_`Subjects`_ are like _`EventEmitters`_.

- Every Subject is an Observable and an Observer.
- You can subscribe to a Subject, and you can call next to feed values as well as error and complete.

_`Subject`_ дуже схожий на _`Observable`_, його можна створити через оператор `new`, на нього можна підписатись `subscribe`, і його стрім можна змінювати за допомогою `pipe`.
Відмінність в тому, що його підписник водночас може бути і джерелом стріма.

Інше порівняння, це YouTube.

`Observable` -> звичайне відео на ютубі.
`Subject` -> онлайн трансляція (ютуб стрім).

Якщо ви відкриваєте нове ютуб відео, ви починає дивитись його з самого початку, ваш друг\сусід\колега який знаходиться біля вас також може увімкнути те ж саме відео, як тільки він це зробить, він теж зможе дивитись відео з самого початку, не важливо на якій хвилині відео находитесь ви на своєму девайсі.

Тобто, кожен новий підписник Observable буде отримувати дані з самого початку, ті які є в Observable.
Такий підхід називають `cold observable`.

Якщо ви відкриваєте онлайн трансляцію на ютубі (ютуб стрім), ви являєтесь новим підписником, і те, що ви будете бачити, може бути початком\серединою\кінцем стріма, якщо стрім почався годину тому, гарантовано ви будете його спостерігати не з самого початку, а відповідно на годину пізніше вій його початку.

Відповідно, якщо ваш друг\сусід\колега відкриє цей самий стрім, він буде спостерігати відео на тому ж моменті, на якому й ви, але втратить початок цього стріма, так як підписався на нього значно пізніше.

Такий підхід називають `hot observable`.
`Subject`, на відміну від `Observable`, по замовчуванню є `hot observable`.

> Для `Subjects` також потрібно робити _`unsubscribe()`_.

```typescript
const sub = new Subject();

sub.subscribe((value) => {
  console.log(value);
  //після того, як виконається команда sub.next('вода'), в консолі ми побачимо 'new emitted value'
});

sub.next('new emitted value');
```

**!ВАЖЛИВО!** - коли нам потрібно використати, наприклад, _`@Output`_ - і кастомну подію, використовуємо _`EventEmitter`_ а не _`Subject`_

#### BehaviorSubject and ReplaySubject

`BehaviorSubject` — клас, який наслідує методи і властивості класу `Subject`, але з різницею в тому, що він може мати початкові дані\початковий стейт.

Також може надавати початкові дані підписникам незалежно від того, коли вони підписались на нього.

І отримати це значення можна напряму через _`getValue()`_

```typescript
export class SubjectComponent {
  users: User[] = [
    { id: '1', name: 'John', age: 30, isActive: true },
    { id: '2', name: 'Jack', age: 35, isActive: false },
    { id: '3', name: 'Mike', age: 25, isActive: true },
    { id: '3', name: 'Monika', age: 25, isActive: true },
  ];
  behaviorSubject$ = new BehaviorSubject<User[]>([]);

  constructor() {
    this.behaviorSubject$.subscribe((value) => console.log(value)); // спочатку тут буде [], потім вже відповідні дані надані в next()
    this.behaviorSubject$.next(this.users);
    const value = this.behaviorSubject$.getValue();
    console.log(value); //отримаємо наших юзерів
  }
}
```

`ReplaySubject` — клас, який наслідує методи і властивості класу `Subject`.

Зберігає в буфер значення потоку. Розмір буферу задається в функції конструкторі. Нова підписка миттєво отримає вміст буферу.

#### AsyncSubject

`AsyncSubject` — клас, який наслідує методи і властивості класу `Subject`. Передає в потік тільки останнє значення і тільки після завершення.

Схожий на Promise.

```typescript
const source1$ = new BehaviorSubject('init value');
const source2$ = new ReplaySubject(2);
const source3$ = new AsyncSubject();
```

### When to use `Observable` or when `Subject`

#### Observables

- Use an Observable when you want to subscribe to a stream of values emitted over time.
- Observables are great for scenarios where you need to handle data streams from HTTP requests, WebSocket connections, or other asynchronous operations.
- You can subscribe to an Observable using the .subscribe() method.
- Observables are more suitable when you only need to consume data and don't need to emit values yourself.

#### Subject

- Use a Subject when you need to control the values emitted by the stream.
- A Subject is both an observable and an observer. You can subscribe to it, but you can also emit values into it.
- It's useful when you want to manually trigger updates or push new data to subscribers.

### Unsubscribing

- `Subscription` with `OnDestroy()`

```typescript
export class PostsComponent implements OnDestroy {
  interval$ = interval(1000);
  intervalSubscription: Subscription;

  constructor() {
    this.intervalSubscription = this.interval$.subscribe((i) => {
      console.log(i);
    });
  }

  ngOnDestroy() {
    this.intervalSubscription.unsubscribe();
  }
}
```

- `async pipe`

```html
<div>{{ interval$ | async }}</div>
```

> Відписка відбудеться автоматично.

- `take(count: number)` RxJS operator

```typescript
constructor() {
  this.intervalSubscription = this.interval$.pipe(take(1)).subscribe((i) => {
    console.log(i);
  });
}
```

> Відписка відбудеться автоматично.

- `takeWhile(predicate)` RxJS operator

```typescript
constructor() {
  this.intervalSubscription = this.interval$.pipe(takeWhile((i) => i < 5>)).subscribe((i) => {
    console.log(i);
  });
}
```

> Відписка відбудеться автоматично.

- `takeUntil()` with `Subject` and `OnDestroy` RxJS operator

```typescript
export class PostsComponent implements OnDestroy {
  interval$ = interval(1000);
  unsubscribe$ = new Subject<void>();

  constructor() {
    this.interval$.pipe(takeUntil(this.unsubscribe$)).subscribe((i) => console.log(i)); //1 підписка
    this.interval$.pipe(takeUntil(this.unsubscribe$)).subscribe((i) => console.log(i)); //2 підписка
  }

  ngOnDestroy() {
    this.unsubscribe$.next();
    this.unsubscribe$.complete();
  }
}
```

> В даному способі ми спираємось на те, чи наш _`Subject`_ вже завершився, а завершиться він коли компонент буде розмонтовано.
>
> Також цей спосіб зручний тим, що ми можемо використати _`Subject`_ для всіх наших підписників в компоненті. Тоді не потрібно буде створювати для кожної підписки змінну з типом _`Subscription`_ і робити _`unsubscribe()`_.
>
> Відписка відбудеться автоматично.

Цей спосіб можна покращити, використавши клас, який буде шерити загальну логіку:

```typescript
export abstract class Unsubscribe implements OnDestroy {
  unsubscribe$ = new Subject<void>();

  ngOnDestroy() {
    this.unsubscribe$.next();
    this.unsubscribe$.complete();
  }
}
```

І далі розширити наш основний клас:

```typescript
export class PostsComponent extends Unsubscribe {
  interval$ = interval(1000);

  constructor() {
    super(); //для використання власного конструктора
    this.interval$.pipe(takeUntil(this.unsubscribe$)).subscribe((i) => console.log(i)); //1 підписка
    this.interval$.pipe(takeUntil(this.unsubscribe$)).subscribe((i) => console.log(i)); //2 підписка
  }
}
```

- `takeUntilDestroyed` from @angular/core

```typescript
export class PostsComponent {
  interval$ = interval(1000).pipe(takeUntilDestroyed());

  constructor() {
    this.interval$.subscribe((i) => console.log(i));
  }
}
```

> Відписка буде зроблена автоматично, коли компонент буде розмонтовано.

---

## Handling Forms

В Angular є 2 підходи до роботи з формами - _`Template-Driven`_ і _`Reactive`_.

Перший більш простий, де нам треба самостійно створити форму в темплейті і Angular "прочитає" її за допомогою спеціальної директиви `ngForm`.

Другий спосіб складніше, коли ми самостійно визначаємо структуру форми в TypeScript, потім в HTML коді і потім вручну їх з'єднуємо.
Робота зі стилями форм і контролів відбувається для обох підходів однаково, за рахунок класів, які Angular автоматично додає на кожен тег.

### Template-Driven Approach

- Спочатку в `imports[]` додаємо _`FormsModule`_
- To register child controls with the form, use `NgModel` with a `name` attribute
- Для відправки даних з форми потрібно додати на тег _`form`_ атрибут `(ngSubmit)="someMethod()"` і передати в нього метод, яким ми будемо виконувати потрібну нам логіку

#### Form structure or input as an object

- Для того, щоб отримати структуру форми як `{}`, потрібно додати в тег _`form`_ template reference **`#f="ngForm"`**, передати його в метод ось так `(ngSubmit)="someMethod(f)"` і в компоненті, у цьому методі отримати форму `someMethod(form: NgForm) {}`. Але при даному варіанті ми отримуємо доступ лише при сабміті форми.
- Щоб отримати доступ до форми без сабміта, можна використати `@ViewChild('localRefName') propName: NgForm;`. Передавати в метод нічого не потрібно при цьому: `(ngSubmit)="onSubmit()"`
- Для того, щоб отримати структуру конкретного _`input`_ як `{}`, достатньо додати `template reference` ось так: `#someName="ngModel"`

#### Default input value

- Для встановлення дефолтного значення поля форми, створюємо властивість з потрібним значенням в компоненті, і далі робимо _`property binding`_ на самому _`input`_: `[ngModel]="propName"`

#### Two-way binding

- Для того, щоб одразу значення, яке ми вводимо в _`input`_, можна було використовувати, потрібно використати _`two-way data binding`_. Додаємо властивість в компонент з потрібним значенням і робимо прив'язку: `[(ngModel)]="propName"`

#### Input groups

- Також можна згрупувати декілька _`inputs`_ в одну групу, тобто вони будуть додані всередину окремої властивості в середині властивості `value`. Робиться це через атрибут `ngModelGroup="anyName"`. Якщо потрібно отримати доступ до значень _`inputs`_ і інших властивостей, використовуємо template reference ось так: `#anyName="ngModelGroup"`

`app.component.html`

```html
<form (ngSubmit)="onSubmit()" #f="ngForm">
  <div id="user-data" ngModelGroup="userData" #userData="ngModelGroup">
    <div class="form-group">
      <label for="username">Username</label>
      <input type="text" id="username" class="form-control" name="username" required [ngModel]="defaultUserName" />
    </div>
    <button class="btn btn-default" type="button" (click)="suggestUserName()">Suggest an Username</button>
    <div class="form-group">
      <label for="email">Mail</label>
      <input type="email" id="email" class="form-control" required email ngModel name="email" #email="ngModel" />
      <span class="help-block" *ngIf="!email.valid && email.touched">Please enter a valid email</span>
    </div>
  </div>
  <p *ngIf="!userData.valid && userData.touched">User data is invalid!</p>
  <div class="form-group">
    <label for="secret">Secret Questions</label>
    <select id="secret" class="form-control" [ngModel]="defaultQuestion" name="secret">
      <option value="pet">Your first Pet?</option>
      <option value="teacher">Your first teacher?</option>
    </select>
  </div>
  <div class="form-group">
    <textarea class="form-control" name="questionAnswer" rows="3" [(ngModel)]="answer"></textarea>
  </div>
  <p>Your reply: {{ answer }}</p>
  <div class="radio" *ngFor="let gender of genders">
    <label for="gender">
      <input type="radio" id="gender" name="gender" required ngModel [value]="gender" />
      {{ gender }}
    </label>
  </div>
  <hr />
  <button class="btn btn-primary" type="submit" [disabled]="f.invalid">Submit</button>
</form>
```

`app.component.ts`

```typescript
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
})
export class AppComponent {
  @ViewChild('f') signUpForm: NgForm;
  @ViewChild('email') emailInput: NgForm;

  defaultQuestion = 'pet';
  defaultUserName = '';
  answer = '';
  genders = ['male', 'female'];
  submitted = false;
  user = {
    username: '',
    email: '',
    secretQuestion: 'pet',
    answer: '',
    gender: '',
  };

  suggestUserName() {
    const suggestedName = 'Superuser';
    //patchValue() і setValue() доступні лише на формі, яка обгорнута в ngForm
    /* this.signUpForm.setValue({ //setValue() перезапише всі дані
      userData: {
        username: suggestedName,
        email: "admin@gmail.com"
      },
      secret: "pet",
      questionAnswer: "",
      gender: "male"
    }) */
    //це не найкращий спосіб задати потрібні значення полям форми, так як якщо там вже були введені якісь значення раніше, вони будуть перезаписані
    this.signUpForm.form.patchValue({
      //patchValue() дає можливість перезаписати дані, які ми хочемо
      userData: {
        username: suggestedName,
        email: 'test@gmail.com',
      },
      secretQuestion: 'pet',
      questionAnswer: 'Funny',
      gender: 'male',
    });
  }

  /* onSubmit(form: NgForm) {
    console.log(form)
  } */

  onSubmit() {
    console.log(this.signUpForm);
    // console.log(this.emailInput);
    this.submitted = true;
    this.user.username = this.signUpForm.value.userData.username;
    this.user.email = this.signUpForm.value.userData.email;
    this.user.secretQuestion = this.signUpForm.value.secret;
    this.user.answer = this.signUpForm.value.questionAnswer;
    this.user.gender = this.signUpForm.value.gender;

    this.signUpForm.reset(); //цей метод не тільки очищає поля форми, а і також всі вбудовані властивості форми, як от valid, touched і так далі
  }
}
```

#### Setting and patching input values

- Для того, або, наприклад, по кліку на кнопку перезаписати всі дані форми або лише деякі, використовуємо методи _`patchValue()`_ і _`setValue()`_. Вони доступні лише для форми, на яку додано `local ref` зі значенням `ngForm`.`

> `setValue()` - потребує передачі об'єкту зі всіма властивостями, які ми ми додали, використовуючи `patchValue()` ми можемо передати лише властивості, значення яких хочемо змінити.

#### Resetting input values

- Для очистки полів форми і також всі вбудовані властивості форми, як от `valid`, `invalid`, `touched`, `pristine`, `dirty`, і так далі можна використовувати методи _`reset()`_ і `resetForm()`. В останній також можна передати `{}` з полями форми і з дефолтними значеннями для них.
- Також для `Template-driven` форм доречно використовувати вбудовані валідатори HTML5, такі як: `email`, `required`, `pattern`, `min`, `max`, `minLength`, `maxLength`

#### Update on options

- Також за допомогою `[ngModelOptions]` можна налаштувати на яку подію буде відбуватися оновлення форми чи окремого інпута:

```html
<input
  type="text"
  id="username"
  class="form-control"
  name="username"
  required
  [ngModel]="defaultUserName"
  [ngModelOptions]="{updateOn: blur}"
/>
```

#### Custom validation

`email-validator.directive.ts`

```typescript
@Directive({
  selector: '[appEmailValidator]',
  providers: [
    {
      provide: NG_VALIDATORS, //Injection token
      useExisting: EmailValidatorDirective,
      multi: true,
    },
  ],
})
export class EmailValidatorDirective implements Validator {
  validate(control: AbstractControl): ValidationErrors | null {
    const email = control.value;

    if (String(email).includes('test')) {
      return { invalidEmail: true };
    }

    return null;
  }
}
```

```html
<div class="form-group">
  <label for="email">Mail</label>
  <input
    type="email"
    id="email"
    class="form-control"
    required
    name="email"
    email
    ngModel
    #email="ngModel"
    appEmailValidator
  />
  <div class="help-block" *ngIf="!email.valid && email.touched">
    <span>Email is invalid</span>
    <span class="help-block" *ngIf="email.errors?.invalidEmail">It should not have "test" inside</span>
  </div>
</div>
```

### Reactive Approach

- Спочатку в `imports[]` of **`main.module.ts`** додаємо _`ReactiveFormsModule`_
- Для ініціалізації форми в компоненті використовуємо хук `ngOnInit()`, створивши перед цим властивість з довільною назвою, але з типом **`FormGroup`**
- Далі за рахунок ініціалізації об'єкту класу `FormGroup`, записуємо в цю властивість нову форму, яку цей клас створить для нас: `this.signUpForm = new FormGroup({})`; - такий запис створить порожню форму.
- Для конфігурації форми в `constructor` передається `{}` з потрібними нам властивостями, наприклад: `"username" : new FormControl(null)`, а в цю властивість записуємо ініціалізацію об'єкту класу `FormControl`, в конструктор якого можна передати, наприклад, якесь дефолтне значення для інпута, або _`null`_, якщо залишаємо його порожнім
- Для того, щоб сказати Angular, що ми хочемо використовувати власну конфігурації форми, потрібно в темплейті на тег _`form`_ додати через _`Property binding`_ атрибут _`formGroup`_ і передати як аргумент нашу властивість з типом **FormGroup**: `<form [formGroup]="signUpForm"></form>`

> Загалом в темплейті може бути лише одна форма з атрибутом `[formGroup]`.

- Далі для інпутів, які ми хочемо контролювати, додаємо атрибут _`formControlName`_, в який передаємо назву створеної нами властивості всередині `new FormGroup({})`, наприклад `formControlName="username"`. Також тут можна використовувати _`Property binding`_ з таким синтаксисом: `[formControlName]="'username'"`
- Для відправки даних з форми потрібно додати на тег _`form`_ атрибут `(ngSubmit)="someMethod()"` і передати в нього метод, яким ми будемо виконувати потрібну нам логіку

> Використовувати `template reference` нам вже непотрібно для доступу до форми як `{}`

- Валідація полів форми додається через вбудований клас **`Validators`**, з якого береться потрібна властивість і передається в `new FormControl()`, якщо їх декілька, можна передати `[]`

```typescript
"username" : new FormControl(null, Validators.required),
"email" : new FormControl(null, [Validators.required, Validators.email])
```

- Доступ до властивостей полів форми в темплейті, таких як, наприклад, `valid`, `touch` і тд. можна отримати двома способами:

```typescript
*ngIf="!signUpForm.get('username').valid && signUpForm.get('username').touched"
```

2 варіант працює лише якщо у нас немає вкладених контролів тобто групування

```typescript
*ngIf="!signUpForm.valid && signUpForm.touched"
```

де `signUpForm` - це назва властивості з типом _`FormGroup`_ в нашому компоненті.

- Для групування полів форми використовується атрибут _`formGroupName`_, який додається до тегу, яким ми обгортаємо згруповані поля форми. В атрибут передається як рядок назва властивості `formGroupName="userData"` в яку ми вклали наші поля в TS коді

```typescript
"userData": new FormGroup({
  "username" : new FormControl(null, Validators.required),
  "email" : new FormControl(null, [Validators.required, Validators.email]),
}),
```

- Для доступу до значень полів форми в коді можна використати такий запис:
  `this.recipeForm.value.name`, де `name` - це одна з властивостей, до якої прив'язаний інпут. Для отримання об'єкту зі всіма інпутами і їх значень, використовуємо `this.recipeForm.value`.

- У випадку, якщо у формі є вимкнені інпути (_disabled_), можна використати метод `getRawValue()` який поверне об'єкт зі всіма інпутами, включно з вимкненими.

- Якщо нам потрібно декілька полів додати, використовуючи лише одну властивість всередині форми, можна використовувати **`FormArray`** --> `'anyName': new FormArray([])`. Далі туди можна додавати потрібні нам дані, ітерувати їх і через _`Property binding`_ і індекс в масиві, використовувати їх в темплейті. Можна навіть додавати поля форми в цей масив ось так: `(<FormArray>this.signUpForm.get('anyName')).push(new FormControl());`

- Якщо нам в темплейті потрібно відобразити помилку для інпута, який знаходиться всередині `FormArray`, використовується такий запис:

```html
<div formArrayName="hobbies">
  <h4>Your Hobbies</h4>
  <button class="btn btn-default" type="button" (click)="onAddHobby()">Add Hobby</button>
  <div class="form-group" *ngFor="let hobbyControl of hobbyControls; let i = index">
    <input type="text" title="hobbies" name="hobbies" class="form-control" [formControlName]="i" />
    <mat-error *ngIf="signUpForm.get(['hobbies', i,])"></mat-error>
    //[formArrayName, formGroupName, formControlName] в такому порядку
  </div>
</div>
```

- Для того, аби підписатися на зміни значення або статусу всієї форми або конкретних її полів, використовуються вбудовані **observables** `valueChanges()` і `statusChanges()`.

> `valueChanges()` викликатиметься на кожне спрацювання події `onchange`.

Приклади в коді `forms-reactive`

- _`patchValue()`_,_`setValue()`_ і _`reset()`_ також доступні для _`Reactive`_ підходу

- **`updateOn`** - властивість, за допомогою якої можна налаштувати на яку події значення інпутів будуть оновлюватися. По дефолту це `change`, інші це `blur` і `submit`. Можна задати як окремому інпуту так і всій формі загалом.

#### Custom validation for Reactive forms

> Валідатори можна додати як на всю форму, так і на масив інпутів чи окремий інпут

##### Валідатори в окремому класі

```typescript
export class CustomValidators {
  static invalidProjectName(control: FormControl): { [s: string]: boolean } {
    if (control.value === 'Test') {
      return { invalidProjectName: true };
    }
    return null;
  }

  static asyncInvalidProjectName(control: FormControl): Promise<any> | Observable<any> {
    const promise = new Promise((resolve, reject) => {
      setTimeout(() => {
        if (control.value === 'TestProject') {
          resolve({ invalidProjectName: true });
        } else {
          resolve(null);
        }
      }, 2000);
    });
    return promise;
  }

  static validateSpecialChar(char) {
    return (control: AbstractControl) => {
      if (String(value).includes(char)) {
        return { invalidSpecialChar: true };
      }
      return null;
    };
  }
}
```

`app.component.ts`

```typescript
export class AppComponent implements OnInit {
  projectForm: FormGroup;

  ngOnInit() {
    this.projectForm = new FormGroup({
      projectName: new FormControl(
        null,
        [Validators.required, CustomValidators.invalidProjectName],
        CustomValidators.asyncInvalidProjectName,
      ),
      email: new FormControl(null, [Validators.required, Validators.email]),
      projectStatus: new FormControl('critical'),
    });
  }
}
```

> Якщо ми додаємо валідатор на всю форму, в самому валідаторі можна додати еррорку на окрему властивість у формі через метод `setErrors({})`.

##### Валідатори в класі компонента

`app.component.ts`

```typescript
export class AppComponent implements OnInit {
  genders = ["male", "female"];
  signUpForm: FormGroup;
  forbiddenUsernames = ["Chris", "Anna"];

  ngOnInit() {
    this.signUpForm = new FormGroup({
      userData: new FormGroup({
        username: new FormControl(null, [
          Validators.required,
          this.forbiddenNames.bind(this),
        ]),
        email: new FormControl(
          null,
          [Validators.required, Validators.email],
          this.forbiddenEmails
        ),
      }),
      gender: new FormControl("male"),
      hobbies: new FormArray([]),
    });

  //!КАСТОМНИЙ ВАЛІДАТОР
  forbiddenNames(control: FormControl): { [s: string]: boolean } {
    //Він перевірятиме, чи введені користувачем дані == одному з імен в масиві forbiddenUsernames. Ця функція буде викликатись автоматично в той момент, коли Angular перевіряє валідність поля форми
    if (this.forbiddenUsernames.indexOf(control.value) !== -1) {
      //робимо перевірку того, чи введене в поле форми значення відповідає одному зі значень в нашому масиві forbiddenUsernames
      return { nameIsForbidden: true };
    }
    return null; //щоб дати зрозуміти Angular, що поле валідне, повертаємо null або взагалі нічого не повертаємо
  } // функція приймає на вхід поле форми і повертає key: value пару, де ключ - рядок, значення - true/false
  //!КАСТОМНИЙ АСИНХРОННИЙ ВАЛІДАТОР
  forbiddenEmails(control: FormControl): Promise<any> | Observable<any> {
    const promise = new Promise<any>((resolve, reject) => {
      setTimeout(() => {
        if (control.value === "test@test.com") {
          resolve({ emailIsForbidden: true });
        } else {
          resolve(null);
        }
      }, 1500);
    });
    return promise;
  }
}
```

---

## Pipes

- Pipes використовується для зміни вихідних даних. Працюють як синхронно так і асинхронно. Вони не модифікують сам об'єкт з яким працюють.

- Бувають двох типів: `pure` (не допускають змін) і `impure` (допускають зміни). Різниця між цими двома типами полягає в реагуванні на зміни значень, які передаються в _`pipe`_.

**По замовчуванню** всі _`pipes`_ є типу _`pure`_.

- Такі об'єкти відстежують зміни в значеннях примітивних типів (String, Number, Boolean, Symbol). В інших об'єктах — типів Date, Array, Function, Object зміни відстежуються, коли змінюється посилання, а не значення по посиланню.

- `Impure pipes` відстежують всі зміни.

### Some built-in pipes

- DatePipe
- UpperCasePipe
- LoweCasePipe
- CurrencyPipe
- DecimalPipe
- PercentPipe
- JsonPipe
- SlicePipe
- AsyncPipe

---

Наприклад, є властивість _`username: 'Max'`_; яку ми показуємо на сторінці `<p>{{ username }}</p>`, і якщо нам по якійсь причині потрібно вивести ім'я великими літерами, можна використати вбудований _pipe_ _`uppercase`_:

```html
<p>{{ username | uppercase }}</p>
```

для трансформації виводу

### Застосування _`date pipe`_ з параметризацією

```html
<td>{{ room.checkOutTime | date : 'longDate' }}</td>
```

Також пайпи можна поєднувати в ланцюг:

```html
<p>{{ server.started | date: 'fullDate' | uppercase }}</p>
```

> Angular парсить їх зліва направо

### `Async` pipe

The async pipe is one of the simplest ways to handle subscriptions in Angular. It allows you to bind an observable or promise directly to your template without having to manually subscribe and unsubscribe from it.

The pipe automatically subscribes when the component is initialized and unsubscribes when the component is destroyed, making it a great way to quickly set up subscriptions with minimal code.

```html
@if (rooms$ | async; as rooms ) {
<app-rooms-list *ngIf="!hideRooms" [rooms]="rooms" [title]="title" (selectedRoom)="selectRoom($event)" />
}
```

де _`rooms$`_ - це наш _`observable`_

#### It works well with OnPush change detection

The async pipe will trigger change detection when a new value is emitted, even with the OnPush strategy enabled. You would have to do it manually when you are using subscribe. The OnPush strategy is great for the performance of Angular applications. Its use alone is enough of an argument to always consider the use of the async pipe.

### Custom Pipes

Можна створювати **кастомні** пайпи, до яких, в тому числі, можна додавати _параметризацію_:
`{{ server.name | shorten: 15 }}` - **shorten** - назва пайпу, **15** - параметр, який ми передаємо.  
Імплементація:

```javascript
@Pipe({
  name: 'someName',
})
export class PipeName implements PipeTransform {
  transform(value: any, ...args: any[]) {
    return null;
  }
}
```

_`PipeName`_ додається в _`declarations`_ of _`@NgModule`_.

---

## Dynamic Components

Динамічний компонент - це компонент, який ми за певної умови додаємо в DOM під час виконання коду. Наприклад ми хочемо показати попередження чи модальне/діалогове вікно яке повинно відобразитись лише за певної дії чи умови.
Як правило, ми будемо використовувати `*ngIf=""` для цього:

```html
<div class="row auth-wrapper">
  <div class="col-xs-12">
    <app-alert *ngIf="error"></app-alert> <--Динамічно використовуємо компонент
    <form
      #authForm="ngForm"
      (ngSubmit)="onSubmit(authForm)"
      *ngIf="!isLoading;
          else loadingSpinner"
    >
      //-------some code--------//
    </form>
    <ng-template #loadingSpinner style="text-align: center;">
      <app-spinner></app-spinner>
    </ng-template>
  </div>
</div>
```

### Інший спосіб це `Dynamic Component Loader`

Загальний концепт, за яким ми створюємо компонент і вручну додаємо його в DOM зсередини коду. Ми самі в коді будемо контролювати за яких умов цей компонент ініціалізуватиметься, які дані в нього будуть передані і коли він буде видалений.

Цей підхід може бути корисним, так як по суті ми можемо все контролювати з коду, нам не потрібно чіпати темплейт.
Як це робить можна подивитися в `course_project --> auth.component --> showErrorAlert()`

---

## Angular Modules

- Angular modules - це будівельні блоки застосунку. Angular аналізує всі _`@NgModules`_ в проекті, щоб зрозуміти його і всі його деталі і можливості. _`@NgModule()`_ декоратор трансформує звичайний TypeScript клас в модуль Angular
- В Angular modules визначаються всі **`компоненти`**, **`директиви`**, **`пайпи`**. Вони додаються в масив `declarations: []`. В застосунку має бути хоча б один модуль.
- Всі основні можливості Angular додані як _`@NgModules`_, наприклад `FormsModule`, щоб можна було швидко і просто їх додати, коли вони нам потрібні.
- Також потрібно пам'ятати, що кожен модуль в Angular працює самостійно, вони не комунікують один з одним напряму.
- Тому якщо нам потрібен функціонал для роботи з формами в декількох компонентах, які в свою чергу в окремих модулях, нам потрібно додавати відповідний модуль, наприклад, `FormsModule`, в _`imports[]`_ кожного з цих модулів. _`BrowserModule`_, який відповідає за різні базові фічі, як _`ngFor/ngIf`_, додаємо в основний модуль, і якщо він потрібен в інших модулях, то додавати треба _`CommonModule`_.
- Потрібно пам'ятати, що вказувати в _`declarations[]`_ чи то компонент, чи директива, чи пайп можна лише раз в якомусь одному модулі. Далі їх просто потрібно експортувати і імпортувати де нам потрібно.
- Стосовно **`сервісів`** є 5 варіантів їх додавання з різними особливостями.
  - Перший і рекомендований, це використовувати _`@Injectable({providedIn: 'root'})`_ що робить його доступним **`application wide`** надаючи доступ до одного і того ж екземпляру класу. В такому разі його не потрібно додавати в _`providers[]`_ в _`AppModule`_, що і є ще одним варіантом, який є аналогом першому.
  - Те ж саме буде відбуватись, якщо ми додаватимемо сервіс в будь-який інший модуль, який є жадібно завантажений, тобто одразу ж, а не на вимогу. Але в такому разі краще додавати сервіс в `AppModule`.
  - При додаванні сервісу в _`Lazy loaded`_ модуль, такий модуль матиме свій власний екземпляр класу сервісу, використовуючи _`child injector`_
  - Також можливий варіант, коли єдиний екземпляр класу сервісу буде доступний лише в компоненті або в дереві компонентів.
- Сервіси не потрібно експортувати щоби потім імпортувати.

## Lazy Loading

Суть полягає в тому, щоб завантажувати певні модулі(і весь їх вміст) лише тоді, коли ми переходитимо по певним маршрутам, які відповідатимуть цим модулям. Тобто при використанні цього підходу ми на початку завантажуватимемо лише модуль кореневого, тобто початкового маршруту.

- _`preloadingStrategy`_ - частина модуля _`RouterModule`_, властивість, яка дає можливість попереднього завантаження `lazy-loaded` коду для того, щоб прибрати можливу затримку, яка може виникнути при його завантаженні(цього коду).

Може мати 2 значення: `NoPreloading` - стандартне значення і `PreloadAllModules` - provides a preloading strategy that preloads all modules as quickly as possible.

_`PreloadAllModules`_ - говорить ангуляру про те, що потрібно завантажити всі бандли, створені _`loadChildren`_, одразу ж при ініціалізації кореневого/початкового модуля. При цьому сам початковий модуль, який теж завантажується як окремий бандл, залишається малим по розміру.

- _`loadChildren`_ - спец. властивість яка говорить Ангуляру завантажувати модуль/компонент лише тоді, коли відбувається перехід за адресою в path. Ефект від використання цього методу полягає в тому, що код буде на цьому моменті поділений і все, що знаходиться за вказаним в _`loadChildren`_ шляхом(модуль і весь вміст в _`declarations[]`_) буде складено в окремий бандл який буде завантажений браузером і зпарсений тоді, коли юзер перейде за адресою з _`path`_ і не раніше

Загалом суть в тому, що такий модуль має бути ізольований, він не має мати зв'язків чи залежностей від інших модулів. Тобто такий модуль знає лише свою імплементацію і при завантажені має виконувати весь свій функціонал.

```typescript
const routes: Routes = [
  { path: '', redirectTo: '/recipes', pathMatch: 'full' }, //стандартний pathMatch: 'prefix' - не працюватиме для  path: '', бо такий шлях є частиною будь-якого шляху
  {
    path: 'recipes',
    loadChildren: () => import('./components/recipes/recipes.module').then((module) => module.RecipesModule),
  },
  {
    path: 'shopping-list',
    loadChildren: () =>
      import('./components/shopping-list/shopping-list.module').then((module) => module.ShoppingListModule),
  },
  {
    path: 'auth',
    loadChildren: () => import('./auth/auth.module').then((module) => module.AuthModule),
  },
]; //Також для маршрутів які ми оптимізуємо, у разі якщо у них є дочірні маршрути

@NgModule({
  //NgModule трансформує звичайний TypeScript клас в модуль Angular
  imports: [
    RouterModule.forRoot(routes, {
      preloadingStrategy: PreloadAllModules,
    }),
  ], //в вбудований модуль RouterModule ми передаємо свою конфігурацію шляхів. forRoot() конфігурується лише раз для вказання кореневих шляхів
  exports: [RouterModule], //і тут ми цей сконфігурований модуль експортуємо, щоб потім імпортувати і використати в основному модулі AppModule
})
export class AppRoutingModule {} //!Модуль в якому прописані шляхи для компонентів з Lazy Loading
```

> Важливо пам'ятати.
>
> Коли використовується **`@Injectable({ providedIn: 'any' })`** для якогось сервісу і _`lazy-loading`_ модуль, буде створено один екземпляр класу цього сервісу для використання _application-wide_ і для кожного такого модуля окремо, якщо звичайно в такому модулі цей сервіс використовується.
>
> Також у випадку, якщо сервіс ми інжектимо через `injection token`, то для кожного окремого `lazy-loaded` модуля в `providers` можна передавати різне значення в `useValue`.

### Image lazy loading

- Суть даного методу полягає у відкладеному завантажені зображень, яке наразі не відображається у вікні перегляду. Таке зображення завантажується лише тоді, коли користувач докручує до нього і зображення стає видимим. Використовуючи цю техніку, ми можемо отримати кращі показники роботи та час завантаження.

- В серпні 2020 року сучасні браузери додали вбудовану підтримку для лінивих завантажень зображень, і ми можемо скористатися нею, додавши один простий атрибут до нашого елемента `<img>`:

```html
<img src="src.png" alt="Angular" loading="lazy" />
```

Атрибут завантаження підтримує три варіанти — `auto`, `eager`, і `lazy`.
Давайте створимо директиву, яка додає цей атрибут, якщо браузер його підтримує:

```typescript
import { Directive, ElementRef } from '@angular/core';
@Directive({ selector: 'img' })
export class LazyImgDirective {
  constructor({ nativeElement }: ElementRef<HTMLImageElement>) {
    const supports = 'loading' in HTMLImageElement.prototype;
    if (supports) {
      nativeElement.setAttribute('loading', 'lazy');
    }
  }
}
```

---

## Standalone capabilities

### Standalone Components

- Standalone components are regular Angular components with one important twist: They don't require a **`@NgModule`** as a wrapper.
- Instead, you can use them, well, "standalone". They define their own dependencies and can be imported and used anywhere.
- It's also not just "standalone components", but also _`standalone directives`_ and _`standalone pipes`_.
- Також самостійні компоненти можна використовувати разом із стандартним підходом з декоратором `@NgModule`

```javascript
@Component({
  standalone: true, //ця властивість зі значенням true говорить Angular про те, що це самостійний компонент. Його не потрібно передавати в _declarations[]_ модулю
  selector: 'app-test',
  templateUrl: './test.component.html',
  styleUrls: ['./test.component.css'],
})
```

- Якщо ми хочемо використовувати такий компонент в інших компонентах якогось модуля, потрібно передати його в `imports[]` цього модуля

### Standalone directives

Стосовно директив, якщо ми хочемо використати директиву в самостійному компоненті, є **два** підходи.

1. Перший, зробити директиву теж standalone і передати її в `imports[]`.
2. Другий, проміжний, в нашому компоненті в `imports[]` додати модуль, де додана потрібна нам директива.

- Якщо ми хочемо зробити самостійним наш кореневий компонент, потрібно в `main.ts` використати `bootstrapApplication(AppComponent)` де _`AppComponent`_ - кореневий компонент.

### Services and Standalone Components

- По дефолту, щоб сервіс був доступний всюди, використовуємо `@Injectable({ providedIn: 'root' })`. Це вирішує всі проблеми.
- Також, можна передати сервіс в `bootstrapApplication(AppComponent, { providers: SomeService })`. В обох випадках використовуватиметься єдиний екземпляр класу цього сервісу.
- Можна додати сервіс напряму в _`standalone`_ компонент в _`providers[]`_. Тоді у цьому компоненті буде власний екземпляр класу цього сервісу.

> Реалізацію Routing і lazy loading для самостійних компонентів можна глянути в **standalone-components** додатку.

---

## Offline Capabilities with Service Workers

У широкому сенсі воркер - це скрипт, що працює в потоці, окремому від потоку браузера. Звичайний Javascript файл, що додається в HTML документ через тег `<script>`, виконується в основному потоці. Якщо в основному потоці буде виконуватися багато операцій, це може уповільнити сайт, роблячи взаємодію смиканою і повільною. Веб-воркери, сервіс-воркери та ворклети - все це скрипти, які виконуються в окремому потоці. Також потрібно пам'ятати, що всі воркери не мають доступу до DOM

`Сервіс-воркери` - це тип воркерів, які служать певній меті - бути проксі(проміжною ланкою) між браузером та мережею та/або кешем. На відміну від звичайних веб-воркерів, сервіс-воркери мають додаткові можливості, які дозволяють виконувати свої функції проксі-сервера. Після встановлення та активації сервіс-воркери можуть перехоплювати будь-які запити з основного документа. Після перехоплення сервіс-воркери можуть, наприклад, відповісти, повернувши документ із кешу замість того, щоб йти за ним у мережу, тим самим дозволяючи веб-додатку працювати автономно!

_`Service Worker`_ працює у окремому потоці. Це означає, що ви не можете отримати доступ до багатьох об'єктів, доступних в основному потоці `JavaScript`, включаючи `DOM` та деякі `API`, такі як `XHR` або файли `cookie`.
Треба мати на увазі, що сервіс-воркери повинні обслуговуватися через `HTTPS`, інакше їх не зареєструє браузер.

**`@angular/pwa`** бібліотека, яка дає можливість використовувати service workers в angular app

**`ngsw-config.json`** - файл, в якому конфігурується service-worker

`npm run build:serve` для скрипта `"build:serve": "ng build --configuration production && http-server dist/app-name"`

`npm i -g http-server/http-server-spa` - легкий `node-based` сервер, за допомогою якого ми можемо запустити нашу програму і перевірити роботу 'service worker'. Для цього треба перейти в папку `dist/app-name` після того, як ми збілдили застосунок.

Команда для запуску сервера на вказаному порті: `http-server -p 8081`
Перевірити роботу воркера можна в devtools браузера: `Application-->Service Workers`

Наприклад, якщо ми увімкнемо `offline mode`, на сторінці буде відображатись весь наш статичний контент, або якщо такого немає, просто порожня сторінка. Але не помилка про відсутнє з'єднання з інтернетом.

Налаштування роботи воркера в файлі _`ngsw-config.json`_
`"assetGroups": []` - масив зі статичними даними
`"dataGroups": []` - масив з динамічними даними
`"installMode": "prefetch"/"lazy"`, - відповідає за те, як буде закешовано контент об'єкта _`resources`_ під час фази ініціалізації.
`"updateMode": "prefetch"/"lazy"`, - відповідає за те, як буде закешовано контент об'єкта _`resources`_ під час фази оновлення.
В об'єкт _`resources`_ можна передати масив `"urls" : []`, куди можна передати лінки на якісь зовнішні ресурси.

---

## Angular Elements

- Ця фіча дає можливість перетворити звичайний **angular component** в нативний **web component** (custom html element so to say), які є частиною **DOM** і які можна використовувати в JS чи інших фреймворках.

> Реалізація описана в ng-elements застосунку.
> npm install @angular/elements - для інсталяції пакета

---

## Angular Change Detection

> Оригінальна стаття з діаграмами [тут](https://justangular.com/blog/a-change-detection-zone-js-zoneless-local-change-detection-and-signals-story)
>
> Ще на цю тему статті [тут](https://dev.to/jzolnowski/exploring-angulars-change-detection-in-depth-analysis-h3j) і [тут](https://blog.angular-university.io/how-does-angular-2-change-detection-really-work/)

- Виявлення змін в **Angular** є ключовим процесом, який забезпечує синхронізацію стану програми та інтерфейсу користувача. Саме так **Angular** забезпечує оновлення інтерфейсу користувача, коли змінюються основні дані.
- Без цього будь-які зміни у програмі не відображатимуться в інтерфейсі користувача автоматично, що робить програму непослідовною та ненадійною.
- Виявлення змін важливе, оскільки воно забезпечує точність інтерфейсу користувача, забезпечуючи коректне відображення кожної дії користувача, отримання даних або події, що робить програму чуйною та зручною для користувача.
- Як **Angular** знає, коли потрібно оновити `view`? Як він дізнається, що якісь дані в компоненті змінились? Як він дізнається, коли запускати перевірку змін?

- Для синхронних оновлень це не проблема, але для комплексних програм нам все одно прийдеться використовувати якісь `APIs`, де будуть потрібні асинхронні дії, на які потрібно реагувати і відповідно запускати механізм перевірки змін.

**За це і відповідає `Zone.js`**

---

### Zone.js

**`Zone.js`** існує з перших днів **Angular** 2.0. Це бібліотека, яка `monkey patches` (динамічно змінює поведінку коду в `run-time`) **APIs** браузера та дозволяє нам вклинюватися в `event loop`.

Що це означає? Це означає, що ми можемо запускати наш код до того, як якийсь виклик буде передано з `call stack` до `WEB API` і після того, який цей виклик покине чергу (`Microtask queue`).

```javascript
setTimeout(() => {
  console.log('Hello world');
}, 1000);
```

Код вище надрукує _'Hello world'_ через 1 секунду. Але що, якщо ми хочемо запустити якийсь код до або після зворотного виклику `setTimeout`?

`Zone.js` дозволяє нам це робити. Ми можемо створити зону спостереження (**Angular також створює свою власну зону спостереження - `NgZone`**) і підключитися до зворотного виклику `setTimeout`.

```typescript
const zone = Zone.current.fork({
  onInvokeTask: (delegate, current, target, task, applyThis, applyArgs) => {
    console.log('Before setTimeout');
    delegate.invokeTask(target, task, applyThis, applyArgs);
    console.log('After setTimeout');
  },
});
```

Щоб запустити наш `setTimeout` всередині зони, нам потрібно використати метод `zone.run()`.

```typescript
zone.run(() => {
  setTimeout(() => {
    console.log('Hello world');
  }, 1000);
});
///Before setTimeout
//Hello world
//After setTimeout
```

---

### Zone.js + Angular

**Angular** за замовчуванням завантажує `zone.js` у кожній програмі та створює зону під назвою **`NgZone`**.

**NgZone** містить `observable` під назвою **`onMicrotaskEmpty`**.

Цей **observable** видає значення, коли в черзі більше немає мікрозавдань **(microtasks or _promise based tasks_)** . І це те, що **Angular** використовує, щоб знати, коли весь асинхронний код завершено і він може безпечно запустити `change detection`.

```typescript
// ng_zone_scheduling.ts NgZoneChangeDetectionScheduler
this._onMicrotaskEmptySubscription = this.zone.onMicrotaskEmpty.subscribe({
  next: () => this.zone.run(() => this.applicationRef.tick()),
});
```

У наведеному коді ми бачимо, що **Angular** викличе метод `applicationRef.tick()`, коли `observable` - `onMicrotaskEmpty` згенерує значення.

- Що це за метод `tick()` 🤔? Саме цей метод запускає механізм виявлення змін для всього дерева компонентів синхронно (**SYNCHRONOUSLY**).

І тепер _Angular_ знає, що весь асинхронний код завершено, і він може безпечно запускати `change detection`.

```javascript
tick(): void {
  // code removed for brevity
  for (let view of this._views) {
    // runs the change detection for a single component
    view.detectChanges();
  }
}
```

Метод `tick()` пройдеться по всім представленням компонентів починаючи з кореневого **(здебільшого у нас є лише одне кореневе представлення/компонент, яким є `AppComponent`)** і синхронно запустить `detectChanges()`.

>Each Angular component has an associated change detector, which is created at application startup time
>
- Також потрібно пам'ятати, що якщо під час розробки використовується `production mode`, `cd` механізм буде спрацьовувати лише раз при кожному виклику `tick()`, що не рекомендується, так як є шанс пропустити якусь помилку, наприклад при `change detection loop`.
- Тому always use `development mode` during the development phase, as that will avoid the problem because Angular always running change detection **twice** in development mode.

---

### Change detection process in Angular

The change detection process in Angular involves **two** main stages:

- **Marking the Component as Dirty**
- **Refreshing the View**

---

#### Component Dirty marking

Ще одна річ, яку робить **Angular**, полягає в тому, що він позначає компонент як змінений (**dirty**), коли знає, що щось усередині компонента змінилося.

**Події, у випадку настання яких компонент буде позначено як змінений**:

- **`Events`** (click, mouseover, etc.)

  - Кожного разу, коли ми натискаємо кнопку із слухачем в темплейті, **Angular** обгортатиме коллбек функцією `wrapListenerIn_markDirtyAndPreventDefault()`. І як ми бачимо з назви функції 😅, вона позначатиме компонент як змінений.

    ```javascript
    function wrapListener(): EventListener {
      return function wrapListenerIn_markDirtyAndPreventDefault(e: any) {
        // ... code removed for brevity
        markViewDirty(startView); // mark the component as dirty
      };
    }
    ```

- **`Changed inputs`** (properties with `@Input()` decorator)

  - Також, в процесі роботи механізму `cd`, **Angular** перевірить, чи змінилося вхідне значення компонента.
    - It's comparing values by using a method called
  `looseNotIdentical()`, which is really just a **===** comparison with special logic for the `NaN` case:

    ```typescript
    export function looseIdentical(a, b): boolean {
      return a === b || typeof a === "number" && typeof b === "number"
        && isNaN(a) && isNaN(b);
    }
    ```

    - Якщо воно змінилося, це позначить компонент як змінений (**dirty**).

    ```javascript
    setInput(name: string, value: unknown): void {
    // Do not set the input if it is the same as the last value
      if (Object.is(this.previousInputValues.get(name), value)) {
        return;
      }
      // code removed for brevity
      setInputsForProperty(lView[TVIEW], lView, dataValue, name, value);
      markViewDirty(childComponentLView); // mark the component as dirty
    }
    ```

> Також по дефолту, Angular Change Detection в процесі перевірки, наприклад якщо ми передаємо в `@Input` об'єкт, порівнюватиме лише ті властивості об'єкта, які використовуються в темплейті компонента.

- **`Output emissions`** (properties with `@Output()` decorator)
  - Щоб слухати вихідні зміни в **Angular**, ми реєструємо подію в темплейті. Відповідно, функція, яку ми назначили обробником цієї події, буде загорнута у `wrapListenerIn_markDirtyAndPreventDefault()`, і коли подія відбудеться, компонент буде позначено як змінений.

- **Async** pipe
- **`markForCheck()`**

---

- Поглянемо, що робить функція `markViewDirty()`

```typescript
/**
 * Marks current view and all ancestors dirty.
 */
export function markViewDirty(lView: LView): LView | null {
  while (lView) {
    lView[FLAGS] |= LViewFlags.Dirty;
    const parent = getLViewParent(lView);
    // Stop traversing up as soon as you find a root view that wasn't attached to any container
    if (isRootView(lView) && !parent) {
      return lView;
    }
    // continue otherwise
    lView = parent!;
  }
  return null;
}
```

Як ми можемо прочитати з коментаря, функція `markViewDirty()` позначить поточний компонент і всіх предків **(аж до корінного компонента)** зміненими.

- Отже, коли ми натискаємо кнопку, **Angular** викличе функцію-обробник події, і оскільки вона обернута функцією `wrapListenerIn_markDirtyAndPreventDefault()`, остання позначить компонент як змінений викликом `markViewDirty()`.
- Як сказано раніше, **Angular** використовує `zone.js` і загортає в нього всю аплікацію.
- І після того, як `markViewDirty()` доходить до корінного компонента і позначає його брудним, `wrapListenerIn_markDirtyAndPreventDefault()` завершує виконання та тригерить `zone.js`.

- Оскільки **Angular** підписаний на `observable` - `onMicrotaskEmpty`, вище описаний ланцюг подій призведе до того, що **`NgZone`** згенерує нове значення в стрім `onMicrotaskEmpty`, на який в свою чергу підписаний **Angular**, і як тільки останній отримає це значення, він запустить **`cd`**.

---

#### Component binding refresh

- Під час роботи механізму **`cd`**, він перевіряє кожен компонент зверху вниз по дереву (від кореневого).
- Він перевірить усі компоненти (змінені та незмінені) і перевірить їх властивості. Якщо властивість змінилася, **Angular** оновить темплейт викликавши `refreshView()` method.

Але чому **Angular** перевіряє всі компоненти 🤔? Чому він не перевіряє лише компоненти, які були позначені як брудні 🤔?

Ну, тому що так працює стандартна стратегія виявлення змін (**default change detection strategy**).

---

#### turning on/off change detection, and triggering it manually

- **`detach()`**
  - There could be special occasions where we do want to **turn off** change detection. Imagine a situation where a lot of data arrives from the backend via a _websocket_. We might want to update a certain part of the UI only once every 5 seconds. To do so, we start by injecting the change detector into the component:
  
    ```typescript
    constructor(private ref: ChangeDetectorRef) {
      ref.detach();
      setInterval(() => {
        this.ref.detectChanges();
      }, 5000);
    }
    ```

  - As we can see, we just **detach** the change detector with `detach()` method , which effectively turns off change detection. Then we simply trigger it manually every 5 seconds by calling `detectChanges()`.

- **`reattach()`**
  - Відповідно робить зворотну дію, тобто включає компонент назад в загальне дерево компонентів, яке буде перевіряти механізмом `cd`.

---

### OnPush Change Detection

**Angular** має також стратегію виявлення змін під назвою **`OnPush`**.

- Коли ми використовуємо цю стратегію, **Angular** запускатиме виявлення змін лише для компонента, який позначено як змінений (**dirty**).

По-перше, давайте змінимо стратегію виявлення змін на `OnPush`:

```typescript
@Component({
  // ...
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class UserCard {}
```

Принцип роботи цієї стратегії полягає в наступному:

- **OnPush + Non-Dirty -> Skip**
- **OnPush + Dirty -> Check bindings -> Refresh bindings -> Check children**

Тобто за допомогою `OnPush`, `cd` механізм не перевірятиме компонент, який не зазнав жодних змін, як і не перевірятимуться його дочірні компоненти.

---

### OnPush + Observables + async pipe

- Коли ми працюємо з **Angular**, `observables` є нашим інструментом номер один для керування даними та змінами стану.
- Для підтримки `observables`, **Angular** надає `async` пайп.
- `Async pipe` підписується на `observable` і повертає останнє значення.
- Щоб повідомити **Angular**, що значення змінилося, він викличе метод `markForCheck()`, який надходить із класу `ChangeDetectorRef` **(ChangeDetectorRef компонента)**.

```typescript
@Pipe()
export class AsyncPipe implements OnDestroy, PipeTransform {
  constructor(ref: ChangeDetectorRef) {}

  transform<T>(obj: Observable<T>): T | null {
    // code removed for brevity
  }

  private _updateLatestValue(async: any, value: Object): void {
    // code removed for brevity
    this._ref!.markForCheck(); // <-- marks component for check
  }
}
```

А метод `markForCheck()` просто викличе функцію `markViewDirty()`, яку ми бачили раніше.

```typescript
// view_ref.ts
markForCheck(): void {
  markViewDirty(this._cdRefInjectingView || this._lView);
}
```

Отже, як і раніше, якщо ми використаємо `observables` з `async pipe` в темплейті, це працюватиме так само, як якщо б ми використовували клік івент. Він позначить компонент як змінений, а Angular запустить `cd`.

---

### OnPush + Observables + Who is triggering zone.js?

Що якщо дані нашого компонента змінюються без нашої взаємодії (click, mouseover тощо), ймовірно, десь під капотом виконується `setTimeout` або `setInterval` або виклик `HTTP`, який тригерить `zone.js`.

```typescript
@Component({
  selector: 'todos',
  standalone: true,
  imports: [AsyncPipe, JsonPipe],
  template: `{{ todos$ | async | json }}`,
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class TodosComponent {
  private http = inject(HttpClient);
  private ngZone = inject(NgZone);

  todos$ = of([] as any[]);

  ngOnInit() {
    this.ngZone.runOutsideAngular(() => {
      setTimeout(() => {
        // this will be updated, but there's nothing triggering zone.js
        this.todos$ = this.getTodos();
      });
    });
  }

  getTodos() {
    return this.http.get<any>('https://jsonplaceholder.typicode.com/todos/1').pipe(shareReplay(1));
  }
}
```

Що відбувається в коді вище:

- У `ngOnInit` ми використали `API` - `ngZone.runOutsideAngular()`, який дозволяє нам запускати код поза **Angular** `zone`.
- Ми використовуємо `setTimeout` (щоб пропустити перший таск, яке виконується, а також тому, що **Angular запускає виявлення змін принаймні один раз за замовчуванням**), а всередині `setTimeout` ми присвоюємо значення для `observable` (тобто змінюємо стан).
- Оскільки `setTimeout` не запускатиметься всередині зони, виклик `API` також здійснюватиметься за межами зони, оскільки код виконується всередині `runOutsideAngular()`, нічого не сповіщатиме `zone.js` про те, що щось змінилося.
- В результаті виконання коду у браузері буде показано лише `[]`.
- Стан додатку зламано 🧨!

Не супер 😄! Але тут постає ще одне запитання:

#### **_Навіщо нам позначати всіх предків як змінені?_**

Причина цього проста: якщо ми не позначатимемо всіх предків як змінені, ми можемо отримати зламаний стан ще швидше. Як?

- Отже, уявімо, ми позначаємо для перевірки лише компонент, де відбувся клік, і його дочірні компоненти. А для батьківського компонента задано **ChangeDetectionStrategy.OnPush**.
- У той момент, коли запуститься `cd` механізм, і він дійде по дереву компонентів до нашого батьківського компонента, до якого застосовано `OnPush` стратегію, він побачить, що він не позначений як змінений, а це означає: **OnPush + Non-Dirty -> Skip**.
- Відповідно, компонент де відбувся клік, оновлено не буде.

Таким чином стан додатку знову буде зламано 🧨!

#### Why can’t we just run the change detection for the component that is marked as dirty?

- Ми можемо це зробити за допомогою методу `detectChanges()` у класі `ChangeDetectorRef`.

  - Але це має свої недоліки. Оскільки цей метод запускає виявлення змін синхронно, це може спричинити проблеми з продуктивністю. Оскільки постійно створюватимуться однакові таски, які попадатимуть в `macrotask queue`, це може заблокувати основний потік і спричинити неплавну роботу програми. Уявіть виявлення змін для списку зі 100 елементів кожні 1 або 2 секунди. Це дуже багато роботи для браузера.

#### `markForCheck()` проти `detectChanges()` (coalesced run vs sync runs)

- **`markForCheck()`**

  - Коли ми використовуємо `markForCheck()`, ми просто повідомляємо **Angular**, що компонент змінений, і більше нічого не відбувається, тому навіть якщо ми викличемо `markForCheck()` 1000 разів, це не буде проблемою.

- **`detectChanges()`**

  - Але коли ми використовуємо `detectChanges()`, **Angular** виконає фактичну роботу, як-от перевірка прив'язок і оновлення темплейта, якщо це необхідно, **для компонента, де відбувся виклик і його дочірніх компонентів** (окрім `OnPush Not Dirty`).

Ось чому ми повинні використовувати `markForCheck()` замість `detectChanges()`.

#### Can’t we schedule `detectChanges()` in the next browser task?

- Можемо, це те, що робить `push pipe` або директива `rxLet` з `rx-angular`. Вона планує запуск `cd` механізму на наступному завданні браузера.
- Але це погана ідея робити це для кожного компонента. Тому що, якщо у нас є список зі 100 елементів і ми заплануємо виявлення змін для кожного елемента, у нас буде створено 100 завдань для браузера. І це також не добре для продуктивності.

---

### Brief overview of Signals

Світ фронтенду рухається до використання `signals`. _`Solid.js`_, _`Svelte`_, _`Vue`_ і _`Angular`_ створюють свої реалізації сигналів. І це тому, що сигнали є кращим способом керування станом і змінами стану.

- Сигнали в Angular принесли багато переваг. Ми можемо легко створювати та шерити стан, а також виконувати побічні дії, коли стан змінюється, за допомогою ефектів (`effects`).
- Нам не потрібно підписуватися на них, нам не потрібно скасовувати підписку на них і нам не потрібно турбуватися про витік пам'яті 🧯.
- Ми можемо просто викликати їх, і вони повернуть своє поточне значення.

```typescript
const name = signal('John'); // create a signal with initial value
const upperCaseName = computed(() => name().toUpperCase()); // create a computed signal

effect(() => {
  console.log(name() + ' ' + upperCaseName()); // run side effect when name or upperCaseName changes
});

setTimeout(() => {
  name('Jane'); // change the name after 1 second
}, 1000);

// Output:
// John JOHN
// Jane JANE
```

- Ми також можемо використовувати сигнали в темплейті, як і звичайні виклики функцій.

```typescript
@Component({
  template: `
    <button (click)="name.set('Jane')">Change name</button>
    <p>{{ name() }}</p>
  `,
})
export class AppComponent {
  name = signal('John');
}
```

- Якщо виникне запитання, чи гарною ідеєю є виклик функції в темплейті, я б сказав, що це гарна ідея, якщо виклик функції дешевий - а виклик сигналу дешевий. Це просто виклик функції, яка повертає значення (без обчислення).

> Почитати можна [тут](https://justangular.com/blog/its-ok-to-use-function-calls-in-angular-templates)

#### Signals and Change Detection

- В версії **Angular** **v17** `signals` отримали оновлення 🚀! Тепер темплейти сприймають сигнали дещо по іншому ніж просто виклики функцій.

- Коли використовується `async pipe`, він викликає `markForCheck()`, а зараз, використовуючи сигнали, нам достатньо їх просто викликати.
- Після цього **Angular** реєструватиме ефект (споживача), який слухатиме цей сигнал і позначатиме темплейт для перевірки кожного разу, коли сигнал змінюватиметься.

Це тепер працює завдяки новому доповненню до механізму `cd` - `Glo-cal change detection`(_Global + Local change detection_).

#### Local Change Detection (Targeted mode)

З новими змінами було додано 2 нових прапора в **Angular**:

- `RefreshView`
- `HAS_CHILD_VIEWS_TO_REFRESH`

Як вони працюють?

- Під час запуску ефекту в темплейті, **Angular** запускає функцію під назвою `markViewForRefresh()`, яка встановлює `RefreshView` прапор для поточного компонента, а потім викликає `markAncestorsForTraversal()`, яка позначає всіх предків за допомогою `HAS_CHILD_VIEWS_TO_REFRESH`.

```typescript
/**
 * Adds the `RefreshView` flag from the lView and updates HAS_CHILD_VIEWS_TO_REFRESH flag of
 * parents.
 */
export function markViewForRefresh(lView: LView) {
  if (lView[FLAGS] & LViewFlags.RefreshView) {
    return;
  }
  lView[FLAGS] |= LViewFlags.RefreshView;
  if (viewAttachedToChangeDetector(lView)) {
    markAncestorsForTraversal(lView);
  }
}
```

> We still depend on zone.js to trigger change detection.

У той момент, коли `zone.js` вступає в роботу (ті самі причини, що й раніше), він викличе `appRef.tick()`, запуститься `cd` механізм, але з деякими відмінностями та новими правилами!

#### Targeted Mode Rules

- `NgZone` запускає виявлення змін в `GlobalMode` (перевірятиме зверху вниз і оновлюватиме всі компоненти)

- В `GlobalMode` ми перевіряємо компоненти з прапорцем `CheckAlways` (звичайний компонент без налаштованої стратегії виявлення змін, тобто дефолтна поведінка) і компоненти `Dirty OnPush`

##### What triggers `TargetedMode`?

- Коли в `GlobalMode` `cd` механізм доходить до `Non-Dirty OnPush` компонента з прапорцем `HAS_CHILD_VIEWS_TO_REFRESH`, відбувається перехід до `TargetedMode`!

##### In `TargetedMode`

- Оновлювати темплейт, лише якщо для нього встановлено прапор `RefreshView`
- **НЕ** оновлювати темплейти, для яких встановлено прапори `CheckAlways` або він просто `Dirty`
- Якщо `cd` механізм по дереву компонентів доходить до компонента з прапорцем `RefreshView`, оновити його і далі всі дочірні компоненти перевіряти в `GlobalMode`

В `GlobalMode` всі компоненти з `CheckAlways` прапорцем оновляться як зазвичай.

> Що ми маємо в результаті:

Ці нововведення дають можливість механізму виявлення змін пропустити перевірку більшої кількості
компонентів ніж раніше.

У випадку, коли є `Non-Dirty OnPush` компонент, у якого є дві або більше груп дочірніх
компонентів, перевірятиметься гілка, у якої буде прапорець `HAS_CHILD_VIEWS_TO_REFRESH` і в ній оновиться компонент з прапором `RefreshView`.

Далі відбудеться перехід в `GlobalMode`, що означає, що всі дочірні
компоненти з `CheckAlways` прапорцем оновляться теж.

> Погратися з всіма правила механізму виявлення змін можна [тут](https://jeanmeche.github.io/angular-change-detection/)

---

### Zoneless Angular — Let’s remove zone.js from Angular

---

## Signals

Сигнали дають нам нові шляхи оновлення даних і темплейта.
