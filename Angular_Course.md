# Angular  docs

## How an Angular App gets Loaded and Started

- **Entry point** це файл `index.html` в якому після збірки angular cli додав свої скрипти, які завантажуються першими і серед яких буде бандл з файлом `main.ts`, код якого і виконається спочатку.
- В ньому і стартує наш веб-застосунок в момент виклику `platformBrowserDynamic().bootstrapModule(AppModule)` з головним модулем _AppModule_, в якому в свою чергу вказано початковий компонент з селектором `<app-root></app-root>`, який і вказаний в `body` файлу `index.html`.

## Decorators

> - Декоратори модифікують поведінку класів в Angular

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

We use the _`<ng-template>`_ because much like it’s HTML5 counterpart `<template>`, it’s also considered “virtual”.

Being “virtual” means the _`<ng-template>`_ contents won’t actually exist in the compiled DOM, until it’s needed (you will never see it until Angular renders it).

When it’s needed (for example the “else” expression kicks into play), Angular will grab the contents of the _`<ng-template>`_ tag, and replace the _`*ngIf`_ contents with it. That’s it.

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
- **`@Output()`** - дає можливість компоненту передавати дані наверх батьківському компоненту
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

>Варто зауважити, що на прикладі вище ми поєднуємо селектор директиви з _`property-binding`_, тобто цей запис `[appBetterHighlight]="'red'"` означає, що ми до елементу додаємо директиву і одночасно прив'язуємось до відповідної властивості в директиві.
---

## Template Reference

**`#someMeaningfulName`** - is often a reference to a DOM element within a template. It can also be a reference to an Angular component or directive or a web component.

Щоб отримати доступ до елементу в коді, цей атрибут потрібно передати в метод, який викликається, наприклад на клік, або ж це можна зробити через _`@ViewChild()`_

---

## Lifecycle Hooks

> Component instance has lifecycle hooks which can help you to hook into different events on Components. Lifecycle ends when component is destroyed

- **ngOnChanges()** - викликається лише при наявності властивості з декоратором _`@Input()`_ при зміні значення цієї властивості, тобто в будь-якому разі, бо всі властивості по дефолту _`undefined`_, перед _`ngOnInit`_.
- **ngOnInit()** - хук, який викликатиметься лише **1 раз** після ініціалізації компонента. Викликається він наступним після _`ngOnChanges`_, якщо останній використовується, інакше першим. В цьому хуці як правило відбуваються підписки і робляться запити на отримання даних з бекенду.
- **ngDoCheck()** - викликається при кожному спрацюванні системи _`change detection`_ в скоупі всього застосунку. Тому майже не використовується, бо може спричиняти падіння продуктивності через дуже часте спрацювання, та і по суті дублює собою хук _`ngOnChanges`_.
- **ngAfterContentInit()** - викликається **1 раз** після метода _`ngDoCheck()`_ після додавання коду _`html`_ через _`<ng-content></ng-content>`_
- **ngAfterContentChecked()** - викликається кожного разу, коли контент, доданий через _`<ng-content></ng-content>`_ перевірено системою _`change detection`_
- **ngAfterViewInit()** - викликається Angular після ініціалізації уявлення (темплейта) компонента. Викликається лише **1 раз** одразу після першого виклику методу _`ngAfterContentChecked()`_
- **ngAfterViewChecked()** - викликається Angular після того, як стандартний детектор змін закінчив один повний цикл перевірки подання компонента. Викликається після першого виклику методу _`ngAfterViewInit()`_ та після кожного наступного виклику _`ngAfterContentChecked()`_
- **ngOnDestroy()** - викликається перед тим, як компонент буде прибрано з DOM дерева.

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

### DI Providers

- Class based providers
- Value Providers
- Factory

### Class based providers

- В окремому файлі створюється клас, в який додається потрібний метод.
- Далі за рахунок **Dependency injection** ми можемо використовувати цей сервіс в потрібному компоненті. Для цього в _`constructor`_ передаємо аргумент, який буде властивістю нашого класу і в якому буде зберігатися екземпляр класу з методами, які ми туди додали.

```javascript
constructor(private cdr: ChangeDetectorRef, private roomsService: RoomsService) {}
```

> `@Injectable(options?: ({ providedIn: Type<any> | "root" | "platform" | "any" | null }) & InjectableProvider)`
>
> Decorator that marks a class as available to be provided and injected as a dependency.

_`private`_ - модифікатор доступу, який говорить, що екземпляр цього класу можна убуде використати лише в самому класі

- Для того, щоб використати цей сервіс, потрібно або передати його в _`AppModule`_ у властивість _`providers`_: _`[serviceName]`_, або ж використати _`@Injectable({providedIn:'root'})`_. В обох випадках він буде доступним **application wide**.

> Рекомендовано використовувати другий спосіб, _`@Injectable({providedIn:'root'})`_, так як по-перше, якщо такий сервіс таки не буде використовуватися, фреймворк не додаватиме його в продакшн бандл, по-друге, фреймворк створить єдиний екземпляр такого сервісу для всього застосунку.
>
> Якщо ж нам таки потрібен локальний екземпляр класу, потрібно передати його в _`providers`_: _`[serviceName]`_ нашого компонента.

#### Resolution modifiers

- `@Self()`

Декоратор параметрів _`constructor() {}`_ який говорить _`DI framework`_ почати пошук залежностей з локального інжектора.
В такому разі сервіс потрібно додати в _`providers: []`_, інакше буде помилка.

```javascript
constructor(@Self() private roomsService: RoomsService) {}
```

- `@SkipSelf()`

Декоратор параметрів _`constructor() {}`_ який говорить _`DI framework`_ почати пошук залежностей з батьківського інжектора. Локальний інжектор перевірятися на наявність провайдера не буде.

```javascript
constructor(@SkipSelf() private roomsService: RoomsService) {}
```

- `@Optional()`

Декоратор параметрів _`constructor() {}`_, який позначає параметр як опційну залежність. _`DI framework`_ повертає _`null`_, якщо залежність не буде знайдено.
Може бути використаний разом з іншими декораторами, які модифікують поведінку _DI_.

```javascript
constructor(@Optional() private loggerService: LoggerService) {}`
```

- `@Host()`

Декоратор параметрів конструктора класу батьківського компонента або компонента хоста, якщо це наприклад компонент, через який відбувається _`content projection`_, який говорить _`DI framework`_ завершити створення подання _(view)_, перевіряючи інжектори дочірніх елементів і зупинятися при досягненні елемента хоста.

```javascript
constructor(@Host() private roomsService: RoomsService) {}
```

### Value providers

Прикладом використання може бути сервіс у якому будуть зберігатися _`api endpoints`_. Для цього треба створити сервіс і інтерфейс:

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

> This is nothing more but just a unique identifier because all things that we inject must be unique for the injector.

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

### Factory

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

## Http Interceptors and APP_INITIALIZER

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

- В самих лінках використовується `routerLink='/somePath'` замість _`href`_. Тоді при переході сторінка не буде перезавантажуватися, як з використанням _`href`_. `routerLink` завжди знає, для якого компонента він застосовується, так як для нього прописаний конкретний шлях.
- Для рендерингу темплейту, в розмітку додається _`<router-outlet></router-outlet>`_, після якого Angular додасть в DOM компонент, який відповідатиме поточному шляху, відповідно до того, що було налаштовано в `routes: Routes = []`.
- **Абсолютний шлях**: "/somePath" - завжди додається до кореневого домену
- **Відносний шлях**: "somePath" - додається до поточного шляху
- Для додавання стилів темплейту, шлях якого зараз обраний, використовується атрибут `routerLinkActive="cssClass"` і атрибут `[routerLinkActiveOptions]="{ exact: true }"` - якщо потрібно, щоб перевірявся і порівнювався весь шлях, бо по дефолту для шляху "/somePath", при тому, що просто "/" це кореневий шлях, стилі будуть застосовуватися і для кореневого шляху і для "/somePath".

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

**`paramMap()`** - ще один зручний метод сервісу `ActivatedRoute`, за допомогою якого можна більш гнучко працювати з параметрами адреси. Має такі методи як `get()`, `getAll()`, `has()`, `keys()`.

```typescript
id$ = this.route.paramMap.pipe(map((params) => params.get('id)))
```

#### Passing Query parameters and Fragments

`<http://localhost:4200/servers/5/edit?allowEdit=1#loading>`:

_`[routerLink]="['/servers', 5, 'edit']"`_
За додаткові параметри і фрагменти відповідають ці атрибути:
_`[queryParams]="{ allowEdit: '1' }"`_
_`[fragment]="'loading'"/fragment="loading"`_

Також ці дані можна передати через метод _`navigate()`_ модуля **`Router`**

```typescript
this.router.navigate(['/servers', id, 'edit'], {
  queryParams: { allowEdit: '1' },
  fragment: 'loading',
});
```

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

#### queryParamsHandling

Для того, щоб передати _`queryParams`_ далі по маршруту, наприклад якщо ми на клік викликаємо метод _`navigate()`_ потрібно другим параметром в цей метод передати `{ queryParamsHandling: 'preserve/merge' }`
`merge` - щоб об'єднати параметри і `preserve` - щоб перезаписати старі на нові.

#### Redirecting and Wildcard Routes

Для обробки маршрутів, які не прописані в основному модулі, використовується `Wildcard` маршрут `**` і властивість _`redirectTo`_, куди записується шлях, на який перенаправляти при переході на не існуючий маршрут.

```typescript
{ path: 'page-not-found', component: PageNotFoundComponent },
{ path: '**', redirectTo: '/page-not-found' }
```

---

## Guards

- Захист реалізовується за допомогою вбудованого в пакет @angular/router інтерфейсу **СanActivate**. Для цього потрібно створити сервіс (all GUARDS are SERVICE), який буде імплементувати цей інтерфейс і прописати потрібну логіку:
  export class AuthGuard implements CanActivate {
  canActivate(
  route: ActivatedRouteSnapshot,
  state: RouterStateSnapshot
  ): Observable<boolean> | Promise<boolean> | boolean {
  return ....is needed
  }
  }
  І потім
  const appRoutes: Routes = [
  {
  path: "servers",
  _canActivate_: [AuthGuard], //ПЕРЕДАЄМ САМ СЕРВІС
  component: ServersComponent,
  children: [
  { path: ":id", component: ServerComponent },
  { path: ":id/edit", component: EditServerComponent },
  ],
  }

- Для захисту дочірніх маршрутів викор. інтерфейс **canActivateChild**, який імплементується таким же способом. **АЛЕ!!!** в _Routes_ ми маємо передати замість _canActivate_ ---> _canActivateChild_ В такому разі захист працюватиме для всіх вкладених маршрутів.

- **canLoad** and **canActivate**
  ● canActivate checks after the data of a lazy loaded module have been loaded whether the loaded stuff is allowed to be shown,

● canLoad checks first whether it is necessary to load the data at all:

canActivate: click button/load data - call guard - display page (or don't)
canLoad: click button - call guard - load data/display page (or don't)

- _Controlling Navigation with **canDeactivate**_

Функціонал, який додається для зручності користувача, де можна задати логіку, яка буде, наприклад, перепитувати користувача, чи точно він бажає покинути сторінку, чи, наприклад, нагадати про щось, що клієнт міг забути натиснути і тд.
export class CanDeactivateGuard implements _canDeactivate_<interFaceName>
{ }
Angular ініціалізує виконання коду з CanDeactivateGuard як тільки ми залишимо шлях або компонент, який рендериться при переході по цьому шляху

- _Resolving Dynamic Data with the resolve Guard_

Такий підхід дає можливість виконати якийсь код до того, як відбудеться перехід по якомусь шляху і відбудеться рендер компонента. Таким чином, наприклад, можна отримати якісь дані, які будть потім потрібні компоненту. Це така собі альтернатива до хука _OnInit_, але все ж код виконується ще до завантаження компонента.
Імплементація відбувається через створення класу, який імплементує клас Resolve:
export class ServerResolver implements Resolve<genericType> {
resolve(route: ActivatedRouteSnapshot, state: RouterStateSnapshot):
Server | Observable<genericType> | Promise<genericType> {
тут описуємо логіку
}
}
І потім додаємо до наших шляхів відповідний параметр:
{
path: ":id",
component: ServerComponent,
resolve: { server: ServerResolver }, server - довільна назва, ServerResolver - написаний нами СЕРВІС
}

---

## RxJS and Observables

> An Observable is the main tool provided by the RxJS library whose Angular uses extensively. As with a regular JavaScript Promise, the goal of an Observable is to handle asynchronous events.
>
> The key difference between an Observable and a Promise is that Observables are lazy. You can declare how your data should be handled once received, but you will then need to explicitly subscribe to trigger the asynchronous call. In other words, making the call and handling the results are separated operations. Whereas with a Promise, when you call the then function, you are actually doing both operations at once. It triggers the call and handles the result.

**`Observable`** --> is an object that emits a sequence of items over time, either asynchronously or synchronously. It can be used to represent data from a server, a UI event, or any other kind of data stream. An observable can have multiple observers subscribed to it and will notify them whenever new values are emitted.
Represents the idea of an invokable collection of future values or events.

_`producing data manually`_

```typescript
observable = new Observable<number>((observer) => {
  observer.next(1);
  observer.error('Error');
  observer.complete();
});
```

_`subscribing to data`_

```typescript
ngOnInit(): void {
  // 1 спосіб
  this.observable.subscribe({
    next: (value) => console.log(value),
    error: (error) => console.log(error),
    complete: () => console.log('Completed'),
  });
  // 2 спосіб
  this.observable.subscribe((data) => console.log(data));
}
```

**`Observer`** --> You write the code which gets executed(Handle Data, Handle Error, Handle Completion). Is a collection of callbacks that knows how to listen to values delivered by the Observable.

**`Subscription`** --> represents the execution of an Observable, is primarily useful for cancelling the execution.

**`Operators`** --> are pure functions that enable a functional programming style of dealing with collections with operations like map, filter, concat, reduce, etc.

**`Subject`** --> is equivalent to an EventEmitter, and the only way of multicasting a value or event to multiple Observers.

**`Schedulers`** --> are centralized dispatchers to control concurrency, allowing us to coordinate when computation happens on e.g. setTimeout or requestAnimationFrame or others.

---

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

```typescript
ngOnInit(): void {
  this.roomsService.getRooms().subscribe((rooms) => {
    this.roomList = rooms;
  });
}
```

Ми маємо змогу підписатися на результат виклику _`getRooms()`_ по тій причині, що цей метод повертатиме _`Observable`_.

### Operators

Такі собі посередники, які можна застосовувати до даних, які ми отримуємо від _`Observable`_ перед тим, як ці дані будуть передані через _`Subscription`_ до _`Observer`_ для подальшої робити з ними.

Так як дані в потоці після того, як вони попадають в підписку, модифікувати не можна, нам і потрібні оператори, щоб ці дані модифікувати.

Їх можна застосовувати до будь-яких _`Observable`_ викликаючи метод _`pipe()`_ з rxjs, який є у кожного _`Observable`_. Цей метод якраз і використовує чи приймає один з операторів, наприклад _`map()`_, _`filter()`_, _`select()`_, _`merge()`_, _`takeUntil()`_, _`of()`_, _`from()`_, _`shareReplay()`_ - кешує дані, _`tap()`_, _`mergeMap()`_, _`switchMap()`_, _`concatMap()`_, _`exhaustMap()`_ - дають можливість виконати якийсь код не модифікуючи при цьому дані, які приходять нам в _`subscribe()`_. Кількість операторів, які можна передати - необмежена, вказуються через кому

### Subjects (_BehaviorSubjects_, _ReplaySubjects_, _AsyncSubjects_)

**`new Subject()`** - is a special type of Observable that allows values to be multicasted to many Observers.

Subjects are like EventEmitters.
Every Subject is an Observable and an Observer. You can subscribe to a Subject, and you can call next to feed values as well as error and complete.

Спеціальний вид _`Observable`_, який є активним варіантом, коли звичайний _`Observable`_ пасивний, бо, наприклад, викликати метод _`next()`_ для **_`new Observable()`_** можна тільки з середини, а от для _`Subject`_ можна і ззовні.

Їх доречно використовувати замість _`EventEmitter`_ для крос-компонентної комунікації, вони більше ефективні і рекомендовані до використання в таких випадках. Для них також потрібно робити _`unsubscribe()`_.

**!ВАЖЛИВО!** - коли нам потрібно використати, наприклад, _`@Output`_ - і кастомну подію, викор. _`EventEmitter`_ а не _`Subject`_

#### When to use `Observable` or when `Subject`

#### Observable

- Use an Observable when you want to subscribe to a stream of values emitted over time.
- Observables are great for scenarios where you need to handle data streams from HTTP requests, WebSocket connections, or other asynchronous operations.
- You can subscribe to an Observable using the .subscribe() method.
- Observables are more suitable when you only need to consume data and don’t need to emit values yourself.

#### Subject

- Use a Subject when you need to control the values emitted by the stream.
- A Subject is both an observable and an observer. You can subscribe to it, but you can also emit values into it.
- It’s useful when you want to manually trigger updates or push new data to subscribers.

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
- Для того, щоб отримати структуру форми як `{}`, потрібно додати в тег _`form`_ template reference **`#f="ngForm"`**, передати його в метод ось так `(ngSubmit)="someMethod(f)"` і в компоненті, у цьому методі отримати форму `someMethod(form: NgForm) {}`. Але при даному варіанті ми отримуємо доступ лише при сабміті форми.
- Щоб отримати доступ до форми без сабміта, можна використати `@ViewChild('localRefName') propName: NgForm;`. Передавати в метод нічого не потрібно при цьому: `(ngSubmit)="onSubmit()"`
- Для того, щоб отримати структуру конкретного _`input`_ як `{}`, достатньо додати `template reference` ось так: `#someName="ngModel"`
- Для встановлення дефолтного значення поля форми, створюємо властивість з потрібним значенням в компоненті, і далі робимо _`property binding`_ на самому input: `[ngModel]="propName"`
- Для того, щоб одразу значення, яке ми вводимо в input, можна було використовувати, потрібно використати _`two-way data binding`_. Додаємо властивість в компонент з потрібним значенням і робимо прив'язку: `[(ngModel)]="propName"`
- Також можна згрупувати декілька inputs в одну групу, тобто вони будуть додані всередину окремої властивості в середині властивості `value`. Робиться це через атрибут `ngModelGroup="anyName"`. Якщо потрібно отримати доступ до значень inputs і інших властивостей, використовуємо template reference ось так: `#anyName="ngModelGroup"`
- Для того, або, наприклад, по кліку на кнопку перезаписати всі дані форми або лише деякі, використовуємо методи _`patchValue()`_ і _`setValue()`_. Вони доступні лише на формі, яка обгорнута в `ngForm`. Приклади в коді `forms-td`
- Для очистки полів форми і також всі вбудовані властивості форми, як от `valid`, `invalid`, `touched`, `pristine`, `dirty`, і так далі можна використовувати методи _`reset()`_ і `resetForm()`. В останній також можна передати `{}` з полями форми і з дефолтними значеннями для них.
- Також для `Template-driven` форм доречно використовувати вбудовані валідатори HTML5, такі як: `email`, `required`, `pattern`, `min`, `max`, `minLength`, `maxLength`

`app.component.html`

```html
<form (ngSubmit)="onSubmit()" #f="ngForm">
  <div
    id="user-data"
    ngModelGroup="userData"
    #userData="ngModelGroup"
  >
    <div class="form-group">
      <label for="username">Username</label>
      <input
        type="text"
        id="username"
        class="form-control"
        name="username"
        required
        [ngModel]="defaultUserName"
      />
    </div>
    <button class="btn btn-default" type="button" (click)="suggestUserName()">Suggest an Username</button>
    <div class="form-group">
      <label for="email">Mail</label>
      <input
        type="email"
        id="email"
        class="form-control"
        required
        email
        ngModel name="email"
        #email="ngModel"
      />
      <span class="help-block" *ngIf="!email.valid && email.touched">Please enter a valid email</span>
    </div>
  </div>
  <p *ngIf="!userData.valid && userData.touched">User data is invalid!</p>
  <div class="form-group">
    <label for="secret">Secret Questions</label>
    <select
      id="secret"
      class="form-control"
      [ngModel]="defaultQuestion"
      name="secret"
    >
      <option value="pet">Your first Pet?</option>
      <option value="teacher">Your first teacher?</option>
    </select>
  </div>
  <div class="form-group">
    <textarea
      class="form-control"
      name="questionAnswer"
      rows="3"
      [(ngModel)]="answer">
    </textarea>
  </div>
  <p>Your reply: {{ answer }}</p>
  <div class="radio" *ngFor="let gender of genders">
    <label for="gender">
      <input
        type="radio"
        id="gender"
        name="gender"
        required 
        ngModel
        [value]="gender"
      />
      {{ gender }}
    </label>
  </div>
  <hr />
  <button class="btn btn-primary" type="submit" [disabled]="f.invalid">Submit</button>
</form>
```

```typescript
@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"],
})
export class AppComponent {
  @ViewChild("f") signUpForm: NgForm;
  @ViewChild("email") emailInput: NgForm;

  defaultQuestion = "pet";
  defaultUserName = "";
  answer = "";
  genders = ["male", "female"];
  submitted = false;
  user = {
    username: "",
    email: "",
    secretQuestion: "pet",
    answer: "",
    gender: "",
  };

  suggestUserName() {
    const suggestedName = "Superuser";
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
        email: "test@gmail.com",
      },
      secretQuestion: "pet",
      questionAnswer: "Funny",
      gender: "male",
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

#### T-d form custom validation


### Reactive Approach

- Спочатку в imports[] of **main.module.ts** додаємо _ReactiveFormsModule_
- Для ініціалізації форми викор хук ngOninit(), створивши перед цим властивість з довільною назвою, але з типом **FormGroup**
- Далі за рахунок ініціалізації об'єкту класу FormGroup, записуємо в цю властивість нову форму, яку цей клас створить для нас: this.signUpForm = new FormGroup({}); - такий запис стоврить порожню форму.
- Для конфігурації форми в constructor() передається {} з потрібними нам властивостями, наприклад: "username" : new **FormControl**(null), а в цю властивість записуємо ініціалізацію об'єкту класу FormControl, в конструктор якого можна передати, наприклад, якесь дефолтне значення для інпута, або _null_, якщо залишаємо його порожнім
- Для того, щоб сказати Angular, що ми хочемо використовувати власну конфігурації форми, потрібно в темплейті на тег _form_ додати через _Property binding_ атрибут _formGroup_ і передати як аргумент нашу властивість з типом **FormGroup**: <form [formGroup]="propName">
- Далі для інпутів, які ми хочемо контролювати. додаємо атрибут _formControlName_, в який передаємо як рядок назву створеної нами властивості всередині new FormGroup({}), наприклад formControlName="username". Також тут можна викор. _Property binding_ з таким синтаксисом: [formControlName]="'username'"
- Для відправки даних з форми потрібно додати на тег _form_ атрибут _(ngSubmit)_="someMethod()" і передати в нього метод, яким ми будемо виконувати потрібну нам логіку
- Використовувати template reference нам вже непотрібно для доступу до форми як {}
- Валідація полів форми додається через вбудований клас **Validators**, з якого береться потрібна властивість і передається в new **FormControl**(), якщо їх декілька, можна передати [] ->
  "username" : new FormControl(null, Validators.required),
  "email" : new FormControl(null, [Validators.required, Validators.email])
- Доступ до властивойстей полів форми, таких як, наприклад, valid, touch і тд. можна отримати двома способами:
  *ngIf="
  !signUpForm.*get*('username').valid &&
  signUpForm.*get*('username').touched
  "
  //2 варіант працює лише якщо у нас немає вкладених контролів тобто групування
  *ngIf="
  !signUpForm.valid &&
  signUpForm.touched
  "
  де signUpForm - це назва властивості з типом _FormGroup_ в нашому компоненті.
- Для групування полів форми викор. атрибут _formGroupName_, який додається до тегу, яким ми обгортаємо згруповані поля форми. В атрибут передається як рядок назва властивості в яку ми вклали наші поля в TS коді formGroupName="userData":
  "userData": new FormGroup({
  "username" : new FormControl(null, Validators.required),
  "email" : new FormControl(null, [Validators.required, Validators.email]),
  }),
- Якщо нам потрібно декілька полів додати, використовуючи лише одну властивість всередені форми, можна юзати **FormArray** --> 'anyName': new FormArray([]). Далі туди можна додавати потрібні нам дані, ітерувати їх і через _Property binding_ і індекс в масиві, використовувати їх в темплейті. Можна навіть додавати поля форми в цей масив ось так: (<FormArray>this.signUpForm.get('anyName')).push(new FormControl());
- Для того, аби підписатися на зміни значення або статуса всієї форми або конкретних її полів, викор. вбудовані observables valueChanges() і statusChanges(). Приклади в коді forms-reactive
- _patchValue()_,_setValue()_ і _reset()_ також доступні для _Reactive_ підходу

---

## Pipes

> Pipes використовується для зміни вихідних даних. Працюють як синхронно так і асинхронно. Вони не модифікують сам об'єкт з яким працюють.

Some built-in pipes:

- DatePipe
- UpperCasePipe
- LoweCasePipe
- CurrencyPipe
- DecimalPipe
- PercentPipe
- JsonPipe
- SlicePipe
- AsyncPipe

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

Angular парсить їх зліва направо.

### Async pipe

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

Потрібно пам'ятати, що при використанні пайпу під час ітерації, він не викликається повторно при оновленні масиву чи об'єкту _by default_. Щоб змінити цю поведінку, потрібно в декоратор додати властивість `pure : false`.

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
- Всі основні можливості Angular додані як _`@NgModules`_,наприклад `FormsModule`, щоб можна було швидко і просто їх додати, коли вони нам потрібні.
- Також потрібно пам'ятати, що кожен модуль в Angular працює самостійно, вони не комунікують один з одним напряму.
- Тому якщо нам потрібен функціонал для роботи з формами в декількох компонентах, які в свою чергу в окремих модулях, нам потрібно додавати відповідний модуль, наприклад, `FormsModule`, в _`imports[]`_ кожного з цих модулів. _`BrowserModule`_, який відповідає за різні базові фічі, як _`ngFor/ngIf`_, додаємо в основний модуль, і якщо він потрібен в інших модулях, то додавати треба _`CommonModule`_.
- Потрібно пам'ятати, що вказувати в _`declarations[]`_ чи то компонент, чи директива, чи пайп можна лише раз в якомусь одному модулі. Далі їх просто потрібно експортувати і імпортувати де нам потрібно.
- Стосовно **`сервісів`** є 5 варіантів їх додавання з різними особливостями.
- Перший і рекомендований, це використовувати _`@Injectable({providedIn: 'root'})`_ що робить його доступним **`application wide`** надаючи доступ до одного і того ж екземпляру класу. В такому разі його не потрібно додавати в _`providers[]`_ в _`AppModule`_, що і є ще одним варіантом, який є аналогом першому.
- Те ж саме буде відбуватись, якщо ми додаватимемо сервіс в будь-який інший модуль, який є жадібно завантажений, тобто одразу ж, а не на вимогу. Але в такому разі краще додавати сервіс в `AppModule`.
- При додаванні сервісу в _`Lazy loaded`_ модуль, такий модуль матиме свій власний екземпляр класу сервісу, використовуючи _`child injector`_
- Також можливий варіант, коли єдиний екземпляр класу сервісу буде доступний лише в компоненті або в дереві компонентів.
- Сервіси не потрібно експортувати щоби потім імпортувати.

### **Lazy Loading**

Суть полягає в тому, щоб завантажувати певні модулі(і весь їх вміст) лише тоді, коли ми переходитимо по певним маршрутам, які відповідатимуть цим модулям. Тобто при використанні цього підходу ми на початку завантажуватимемо лише модуль кореневого, тобто початкового маршруту.
Приклад імплементації є в `app-routing.module.ts`.

Також там є приклад використання _`preloadingStrategy`_ - інструмент, класу _`RouterModule`_ який дає можливість попереднього завантаження `lazy-loaded` коду для того, щоб прибрати можливу затримку, яка може виникнути при його завантаженні(цього коду).

---

## Standalone Components

- Standalone components are regular Angular components with one important twist: They don't require a **@NgModule** as a wrapper.
- Instead, you can use them, well, "standalone". They define their own dependencies and can be imported and used anywhere.
- It's also not just "standalone components", but also "_standalone directives_" and "_standalone pipes_".
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

Стосовно директив, якщо ми хочемо викор. директиву в самостійному компоненті, є _два_ підходи.

1. Перший, зробити директиву теж standalone і передати її в `imports[]`.
2. Другий, проміжний, в нашому компоненті в `imports[]` додати модуль, де додана потрібна нам директива.

- Якщо ми хочемо зробити самостійним наш кореневий компонент, потрібно в `main.ts` викор. `bootstrapApplication(AppComponent)` де _AppComponent_ - кореневий компонент.

### Services and Standalone Components

- По дефолту, щоб сервіс був доступний всюди, викор. `@Injectable({ providedIn: 'root' })`. Це вирішує всі проблеми.
- Також, можна передати сервіс в `bootstrapApplication(AppComponent, {providers: SomeService})`. В обох випадках використовуватиметься єдиний екземпляр класу цього сервісу.
  Можна додати сервіс напряму в standalone компонент в _providers[]_. тоді у цьому компоненті буде власний екземпляр класу цього сервісу.

> Реалізацію Routing і lazy loading для самостійних компонентів можна глянути в standalone-components додатку.

---

## Offline Capabilities with Service Workers

У широкому сенсі воркер - це скрипт, що працює в потоці, окремому від потоку браузера. Звичайний Javascript файл, що додається в HTML документ через тег `<script>`, виконується в основному потоці. Якщо в основному потоці буде виконуватися багато операцій, це може уповільнити сайт, роблячи взаємодію смиканою і повільною. Веб-воркери, сервіс-воркери та ворклети - все це скрипти, які виконуються в окремому потоці. Також потрібно пам'ятати, що всі воркери не мають доступу до DOM

`Сервіс-воркери` - це тип воркерів, які служать певній меті - бути проксі(проміжною ланкою) між браузером та мережею та/або кешем. На відміну від звичайних веб-воркерів, сервіс-воркери мають додаткові можливості, які дозволяють виконувати свої функції проксі-сервера. Після встановлення та активації сервіс-воркери можуть перехоплювати будь-які запити з основного документа. Після перехоплення сервіс-воркери можуть, наприклад, відповісти, повернувши документ із кешу замість того, щоб йти за ним у мережу, тим самим дозволяючи веб-додатку працювати автономно!

_`Service Worker`_ працює у окремому потоці. Це означає, що ви не можете отримати доступ до багатьох об'єктів, доступних в основному потоці `JavaScript`, включаючи `DOM` та деякі `API`, такі як `XHR` або файли `cookie`.
Треба мати на увазі, що сервіс-воркери повинні обслуговуватися через `HTTPS`, інакше їх не зареєструє браузер.

**`@angular/pwa`** бібліотека, яка даж можливість викор. service workers в angular app

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

- Ця фіча дає можливість перетворити звичайний angular component в нативний web component (custom html element so to say), які є частиною DOM і які можна викор. в JS чи інших фреймворках.

> Реалізація описана в ng-elements застосунку.
> npm install @angular/elements - для інсталяції пакета
