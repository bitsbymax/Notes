# Angular docs

## How an Angular App gets Loaded and Started

- Entry point це файл `index.html` в якому після збірки angular cli додав свої скрипти, які завантажуються першими і серед яких буде бандл з файлом `main.ts`, код якого і виконається спочатку.
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

`{{ someValue }}` where `someValue` is an expression which evaluates to **string**

```javascript
<h1>Welcome to the {{ hotelName }}</h1>
<p>Here will be some string: {{ returnString() }}</p>
```

---

## Property binding (One-way binding)

```html
[disabled]="!allowNewServer"
<p [innerText]="allowNewServer"></p>
```

Через таку прив'язку ми можемо для будь-якої властивості елемента, через його атрибут, встановити значення будь-якої властивості нашого компонента

---

## Event binding

```html
<button (click)="toggle($event)">Toggle</button>
```

Прив'язати можна всі доступні в JS для конкретного елемента методи. Береться назва метода без частинки **on**.

---

## Two-way data binding

```JavaScript
[(ngModel)]="serverName"
```

---

## Directives

- Directives are use to change the behavior and appearance of DOM element
- Directives can implement all lifecycle hooks
- Directives can't have template

### Structural directive

!!!> На одному і тому ж елементі можна мати лише одну **structural directive**

> - Notice that we don't have to import the @if, @for, @switch directives from @angular/common into our component templates anymore.
> - This is because the @if, @for, @switch syntax is part of the template engine itself, and it is not a directive.
> - The new @if, @for, @switch are built-in directly into the template engine, so it's automatically available everywhere.

#### `*ngIf`

`*ngIf="varName; else localRefName"` + `<ng-template #localRefName></ng-template>`

Умовний рендеринг, де при `true` справа від `*ngIf` буде рендеритись розмітка елементу, на якому директива додана, а при `false` буде рендеритись та розмітка, яку ми додамо в `ng-template`

> New syntax

```javascript
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

#### `*ngFor`

`*ngFor="let server of servers"` - Outputting Lists (цикл for для перебору елементів)

> New syntax

```javascript
@for (i of items ; track i.name) {
  <li>{{i.name}}</li>
}  @empty {
  <li>There are no items.</li>
}
```

#### `*ngSwitch`

`[ngSwitch]` - дає можливість умовного рендерингу через `switch case`.

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

#### `ng-content`

`<ng-content></ng-content>` - директива, яка рендерить розмітку, яку ми вставляємо поміж тегів селектора компонента в тому місці, де ми цю директиву пропишемо:

```html
<app-server-element>
  <p>
    <strong>{{ serverElement.content }}</strong>
    <em> {{ serverElement.content }} </em>
  </p>
</app-server-element>
```

Потім верстку, яка поміж `<app-server-element></>` ми можемо вставити всередину темплейта того компонента, у якого відповідно селектор _"app-server-element"_ додавши туди `<ng-content></ng-content>`

#### `ng-template`

#### `ng-container`

Працює як **Fragment** в React, тобто його можна використовувати коли ми хочемо додати обгортку для якогось HTML елемента, але не хочемо додавати лишній HTML елемент на сторінку. На _`ng-container`_ також можна додавати структурні директиви.

### Attribute directives

!!!> Unlike structural directives, attribute directives don't add or remove elements. They only change the element they were placed on

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
- **`@ViewChild()`** - працює схожим чином з _`Local Reference`_ через атрибут _`#someMeaningfulName`_ але дані в метод ми не передаємо для їх отримання в компоненті, вони доступні одразу за посиланням: _`someMeaningfulName.nativeElement.value`_, посилання зберігатиме об'єкт `ElementRef{nativeElement: htmlEl.className}`.

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

  ngAfterViewInit(): void {
    this.header.title = 'Hotel inventory'; //- тут це значення зміниться лише на наступному циклі change детектора
  }
  //або
  ngAfterViewChecked(): void {
    this.header.title = 'Hotel inventory'; //- тут це значення зміниться лише на наступному циклі change детектора
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
Додамо в наш темплейт тег з _`template reference`_

```html
<ng-template #bookRoom></ng-template>
```

Далі через _`@ViewChild()`_ ми в нашому класі вже можемо екземпляр потрібного компонента отримати і вставити в наш темплейт:

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
  componentRef.instance.buttonLabel = 'Book new room';
}
```

- **`@ContentChild()`** - дає доступ до елементів з атрибутом #someName в темплейті, але тих, що додані через `<ng-content></ng-content>`
- **`HostListener('any supported event')`** - дає можливість слухати будь-яку подію, яка підтримується JS і виконувати потрібну нам функцію, яка приймає eventData в момент спрацювання події. Приклад:
  @HostListener('mouseenter') mouseover(eventData: Event) {
  do something
  }
- **`HostBinding('property.sub_property')`** - в прикладі нижче ми говоримо Angular на елементі, де використання ця директива, звернутися до властивості style під властивості backgroundColor і встановити значення 'red'. Пізніше через this.backgroundColor можна встановити інше значення
  @HostBinding('style.backgroundColor') backgroundColor: string = 'red';

## Template Reference

**`#someMeaningfulName`** - is often a reference to a DOM element within a template. It can also be a reference to an Angular component or directive or a web component.

- Щоб отримати доступ до елементу в коді, цей атрибут потрібно передати в метод, який викликається, наприклад на клік, або ж це можна зробити через _`@ViewChild()`_

## Lifecycle Hooks

> - Component instance has lifecycle hooks which can help you to hook into different events on Components
> - Lifecycle ends when component is destroyed

- **ngOnChanges** - викликається лише при наявності властивості з декоратором, наприклад _`@Input()`_. Перший раз при ініціалізації компонента перед _`ngOnInit`_, і кожного разу, коли властивість з декоратором зміниться.
- **ngOnInit** - хук, який викликатиметься лише один раз після ініціалізації компонента. Викликається він наступним після _`ngOnChanges`_, якщо останній використовується, інакше першим. В цьому хуці як правило відбуваються підписки і робляться запити на отримання даних з бекенду.
- **ngDoCheck** - викликається при кожному спрацюванні системи _`change detection`_ в скоупі всього застосунку. Тому майже не використовується, бо може спричиняти падіння продуктивності через дуже часте спрацювання, та і по суті дублює собою хук _`ngOnChanges`_.
- **ngAfterContentInit** - викликається **1 раз** після метода _`ngDoCheck()`_ після додавання коду `html` через _`<ng-content></ng-content>`_
- **ngAfterContentChecked** - викликається кожного разу, коли контент, доданий через _`<ng-content></ng-content>`_ перевірено системою _`change detection`_
- **ngAfterViewInit** - викликається Angular після ініціалізації уявлення (темплейта) компонента. Викликається лише один раз одразу після першого виклику методу _`ngAfterContentChecked()`_
- **ngAfterViewChecked** - викликається Angular після того, як стандартний детектор змін закінчив один повний цикл перевірки подання компонента. Викликається після першого виклику методу _`ngAfterViewInit()`_ та після кожного наступного виклику _`ngAfterContentChecked()`_
- **ngOnDestroy** - викликається перед тим, як компонент буде прибрано з DOM дерева.

## Custom Attribute Directive

- Створити файл some-name.directive.ts
- В файлі:
  import { **Directive** } from '@angular/core'
  _@Directive_({
  selector: '[someName]'
  })
  export class SomeBasicDirective implements OnInit {
  constructor(private elementReference: ElementRef) {}
  ngOnInit() {
  this.elementReference.nativeElement.style.color = 'red';
  }
  }
- В _NgModule_ в _declarations_ додати SomeBasicDirective
- В потрібний елемент DOM додати атрибут _someName_, назва якого має відповідати тій, що у властивості selector нашої директиви - таким чином ми задамо колір нашому елементу

  ---- Цей спосіб(звернення до елемента напряму) не є найкращим варіантом його модифікації, так як Angular може рендерити темплейти без DOM дерева і тоді властивість, наприклад, style, буде недоступною.

- Тому краще викор. другий варіант з **Renderer2**:
  import { **Renderer2** } from '@angular/core'
  _@Directive_({
  selector: '[someName]'
  })
  export class SomeBasicDirective implements OnInit {
  constructor(private elementReference: ElementRef, private _renderer_: **Renderer2**) {}
  ngOnInit() {
  this.renderer.setStyle(this.elementReference.nativeElement, 'background-color', 'blue');
  }
  }
- <https://angular.io/api/core/Renderer2> - тут більше методів класу **Renderer2**

## Custom Structural Directive

- Приклад реалізаці:
  _@Directive_({
  selector: '[appUnless]'
  })
  export class UnlessDirective {
  @Input() **set** appUnless(condition: boolean) { //appUnless() це властивість, хоч і є мотодом, до якої ми прив'язуємось, спрацює, коли зміниться параметр, який передається в цей метод. Ключове слово set це _setter_
  if (!condition) {
  this.vcRef.createEmbeddedView(this.templateRef); //цей метод створює розмітку у ViewContainer до якого ми звертаємось
  } else {
  this.vcRef.clear(); //цей метод видаляє розмітку з ViewContainer
  }
  }

  constructor(private templateRef: TemplateRef<any>, private vcRef: ViewContainerRef) { }
  //в templateRef буде посилання на наш HTML код, тобто те, ЩО потрібно відобразити, а в vcRef буде вказано ДЕ це треба відобразити
  }

## Services & Dependency injection

- **Services** - допоміжні методи або якийсь функціонал, яким ми будемо використовувати в різних місцях програми. Вони роблять код чистішим, більш лінійним, централізованим, таким, який легше підтримувати
- В окремому файлі створюється клас, в який додається потрібний метод.
- Далі за рахунок **Dependency injection** ми можемо використовувати цей сервіс в потрібному компоненті
  приклад: constructor(private methodName: serviceName) {} - для цього в constructor передаємо параметр, який буде властивістю нашого класу і в якому буде зберігатися екземпляр класу serviceName з методами, які ми туди додали.
- Для того, щоб викор. цей сервіс, потрібно передати його в AppModule у властивість providers: [serviceName], або ж використати _@Injectable({providedIn:'root'})_ В обох випадках він буде доступним **application wide**

## Routing

- Для підключення роутінгу потрібно import { **RouterModule**, **Routes** } from "@angular/router";
- Далі передати в imports: [RouterModule.forRoot(appRoutes)], де appRoutes - змінна, куди ми прописали наші шляхи ось в такому вигляді:
  const appRoutes: Routes = [
  { path: "", component: HomeComponent },
  { path: "users", component: UsersComponent },
  { path: "servers", component: ServersComponent },
  ];
- В самих лінках викор. **routerLink**='/somePath' замість href. Тоді при переході сторінка не буде перезавантажуватися, як з викор href. **routerLink** завжди знає, для якого компонента він застосовується, так як для нього прописаний конкретний шлях.
- Для рендерингу темплейту, в розмітку додається <router-outlet></router-outlet>, куди буде підставлятися теплейт компонета, який відповідає певному шляху.
- **Абсолютний шлях**: "/somePath" - завжди додається до кореневого домену
- **Відносний шлях**: "somePath" - додається до поточного шляху
- Для додавання стилів темплейту, шлях якого зараз обраний, викор. атрибут **routerLinkActive**="cssClass" і атрибут [routerLinkActiveOptions]="{ exact: true }" - якщо потрібно, щоб перевірявся і порівнювався весь шлях, бо по дефолту для шляху /somePath, при тому, що просто / це кореневий шлях, стилі будуть застосовуватися і для кореневого шляху і для /somePath.

- _Navigating Programmatically_

  Для цього підключається модуль **Router** через конструктор і викликається метод **navigate**, куди передається **абсолютний** шлях
  constructor(private router: Router) { }
  onLoadServers() {
  this.router.navigate(['/servers'])
  }

  при використанні **відносного** шляху в методі **navigate**, потрібно передати другий аргумент, так як цей метод не знає, на якому компоненті він застосовується і який його шлях. Аргумент, який вказує, відносно якого шляху робити перехід, тобто до якого додавати шлях з першого аргумента.
  Для цього підключається модуль **ActivatedRoute** і як другий аргумент передаєм { **relativeTo**: this.route }
  constructor(
  private route: ActivatedRoute
  )

      onReload() {
        this.router.navigate(['servers'], { relativeTo: this.route })
      }

- _Fetching Route Parameters_

  Також можна отримати дані з адресного рядка з відповідних параметрів, які ми додаємо сюди: { path: "users/:id/:name", component: UserComponent }, при першій ініціалізації компонента. Якщо в адресний рядок ввести localhost:4200/users/10/Max, то, підключивши модуль **ActivatedRoute**, передавши його в constructor(private route: ActivatedRoute) {} і використавши метод _this.route.snapshot.params_["id"] - ми повернемо значення 10. Але якщо потім ми програмно передамо нові параметри запиту з середини цього компонента, наприклад додамо в темплейт лінк:
  <a [routerLink]="['/users', 5, 'Ira']"></a>
  Angular не буде оновлювати компонент. Тому, якщо ми використовували дані з параметрів запиту на сторінці, вони не будуть оновлені.

- _Fetching Route Parameters Reactively_

  Для того, щоб асинхронно оновлювати дані з параметрів запиту, можна викор. Observables. Спочатку import { ActivatedRoute, Params } from "@angular/router";
  import { Subscription } from "rxjs";
  створюємо властивість paramsSubscription: **Subscription**;
  this.paramsSubscription = this.route.params.subscribe( // цей метод може приймати 3 функції як аргументи
  (params: Params) => {
  this.user.id = params['id'];
  this.user.name = params['name'];
  } // **Params** - належить до Observables. Це функціонал, який доданий сторонньою бібліотекою _rxjs_, але широко використовується в Angular. Він реалізує паттерн "асинхронного наглядача", що в даному випадку і відбувається. Observable це легий спосіб підписатись на якусь асинхронну подію, яка може відбутися колись в певний момент.
  // Весь код всередині _subscribe()_ буде виконано лише тоді, коли відбудуться якісь зміни в params
  );

  ngOnDestroy(): void {
  this.paramsSubscription.unsubscribe(); //цей метод тут використовувати не обов'язково, так як Angular очищає підписки автоматично, коли компонент видаляється. Але це потрібно буде робити, коли ми будемо створювати свої власні Observables.
  }

- _Passing Query parameters and Fragments_

      <http://localhost:4200/servers/5/edit?allowEdit=1#loading>
      [routerLink]="['/servers', 5, 'edit']"
      За додаткові параметри і фрагменти відповідають ці атрибути:
      [queryParams]="{ allowEdit: '1' }"
      [fragment]="'loading'"/fragment="loading"

  - Також ці дані можна передати через метод _.navigate()_ модуля **Router**
    this.router.navigate(["/servers", id, "edit"], {
    queryParams: { allowEdit: "1" },
    fragment: "loading",
    });

- _Retrieving Query parameters and Fragments_

      Отримати їх можна через модуль **ActivatedRoute**
        constructor(private serversService: ServersService, private route: ActivatedRoute) { }
      _Aсинхронний_ спосіб --->
        this.route.queryParams.subscribe();
        this.route.fragment.subscribe();

- _Child Routes_

      Вкладені або дочірні маршрути додаються через відповідну властивість у модулі Routes = [
        { path: "", component: HomeComponent },
        {
          path: "users",
          component: UsersComponent,
          _children_: [{ path: ":id/:name", component: UserComponent }],
        },

- _queryParamsHandling_

      Для того, щоб передати queryParams далі по маршруту, наприклад якщо ми на клік викликаємо метод _navigate()_ потрібно другим параметром в цей метод передати { queryParamsHandling: 'preserve/merge' }
      merge - щоб об'єднати параметри і preserve - щоб перезаписати старі на нові

- _Redirecting and Wildcard Routes_

      Для обробки маршрутів, які не прописані в основному модулі, використовується Wildcard маршрут ** і властивість redirectTo, куди запусується шлях, на який редіректити при переході на не існуючих маршрут
      Приклад:
        { path: 'page-not-found', component: PageNotFoundComponent },
        { path: '**', redirectTo: '/page-not-found' }

## Guards

- Захист реалізовується задопомогою вбудованого в пакет @angular/router інтерфейсу **СanActivate**. Для цього потрібно створити сервіс (all GUARDS are SERVICE), який буде імплементити цей інтерфейс і прописати потрібну логіку:
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
resolve: { server: ServerResolver }, server - довільна назва, ServerResolver - написанй нами СЕРВІС
}

## Observables

**Observable** --> Various Data Sources(User Input, Events, Http Requests, Triggered in Code)
**Observer** --> You write the code which gets executed(Handle Data, Handle Error, Handle Completion)
Власний _Observable_ створюється через new Observable((observer) => {})

_Operators_-------------

Такі собі посередники, які можна застосовувати до даних, які ми отримуємо від Observable перед тим, як ці дані будуть передані як Subscription до Observer для подальшої робити з ними.
Їх можна застосовувати до будь-яких _Observable_ викликаючи метод **pipe()** з rxjs, який є у кожного _Observable_. Цей метод якраз і використовує чи приймає один з операторів, наприклад map(), filter(), select(), merge(), of(), from(), shareReplay(), tap() - дає можливість виконати якийсь код не чіпаючи при цьому дані, які приходять нам в subscribe() , mergeMap(), swichMap(), concatMap(), exhaustMap():
_pipe(map(() => {}),)_ Кількість операторів, які можна передати - необмежена, вказуються через кому

_Subjects_--------------also _BehaviorSubjects_, _ReplaySubjects_, _AsyncSubjects_

**new Subject<>();** - спеціальний вид _Observable_, який є активним варіантом, коли звичайний _Observable_ пасивний, бо, наприклад, викликати метод _next()_ для **new Observable()** можна тільки з середини, а от для _Subject_ можна і ззовні. Їх доречно використовувати замість EventEmitter для крос-компонентної комунікації, вони більше ефективні і рекомендовані до використання в таких випадках. Для них також потрібно робити _unsubscribe()_.
**!ВАЖЛИВО!** - коли нам потрібно використати, наприклад, @Output - і кастомну подію, викор. EventEmitter а не _Subject_

## Handling Forms

В Angular є 2 підходи до роботи з формами - _Template-Driven_ і _Reactive_
Перший більш простий, де Angular самостійно "прочитає" структуру форми задопомогою спеціального механізму, яку було прописано як html код в темплейті. Другий спосіб складніше, коли ми самостійно визначаємо структуру форми в TypeScript, потім в HTML коді і потім вручну їх з'єднуємо.
Робота зі стилями форм і контролів відбувається для обох підходів однаково, за рахунок класів, які Angular автоматично додає на кожен тег.

_Template-Driven Approach_

- Спочатку в imports[] додаємо _FormsModule_
- Для того, або Angular контролював форму, потрібно додати як трибути **ngModel**
- Для відправки даних з форми потрібно додати на тег _form_ атрибут _(ngSubmit)_="someMethod()" і передати в нього метод, яким ми будемо виконувати потрібну нам логіку
- Для того, щоб отримати структуру _форми_ як {}, потрібно додати в тег _form_ template reference **#f="ngForm"**, передати його в метод ось так (ngSubmit)="someMethod(f)" і в компоненті, у цьому методі отримати форму someMethod(form: NgForm) {}. Але при даному варіанті ми отримуємо доступ лише при сабміті форми.
- Щоб отримати доступ до форми без сабміта, можна викор. @ViewChild('localRefName') propName: **NgForm**;
  Передавати в метод нічого не потрібно при цьому: (ngSubmit)="onSubmit()"
- Для того, щоб отримати структуру конкретного _інпута_ як {}, достатньо додати template reference ось так: #someName=**"ngModel"**
- Для встановлення дефолтного значення поля форми, створюємо властивість з потрібним значенням в компоненті, і далі робимо _Property binding_ на самому інпуті: [ngModel]="propName"
- Для того, щоб одразу значення, яке ми вводимо в інпуті, можна було використовувати, потірбно викор. _Two-way data binding_. Додаємо властивість в компонент з потрібним значенням і робимо прив'язку: [(ngModel)]="propName"
- Також можна згрупувати декілька інпутів в одну групу, тобто вони будуть додані всередину окремої властивісті в середині властивості value. Робиться це через атрибут **ngModelGroup**="anyName". Якщо потрібно отримати доступ до значень інпутів і інших властивостей, викор. template reference ось так: #anyName="ngModelGroup"
- Для того, або, наприклад, по кліку на кнопку перезаписати всі дані форми або лише деякі, викор методи _patchValue()_ і _setValue()_ Вони доступні лише на формі, яка обгорнута в ngForm. Приклади в коді forms-td
- Для очистки полів форми і також всі вбудовані властивості форми, як от valid, touched і так далі можна використовувати метод _reset()_. В нього також можна передати {} в якому вказати конкретно, які поля форми ми хочемо очистити.

_Reactive Approach_

- Спочатку в imports[] of **main.module.ts** додаємо _ReactiveFormsModule_
- Для ініціалізації форми викор хук ngOninit(), створивши перед цим властивість з довільною назвою, але з типом **FormGroup**
- Далі за рахунок ініціалізації об'єкту класу FormGroup, записуємо в цю властивість нову форму, яку цей клас створить для нас: this.signupForm = new FormGroup({}); - такий запис стоврить порожню форму.
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
  !signupForm.*get*('username').valid &&
  signupForm.*get*('username').touched
  "
  //2 варіант працює лише якщо у нас немає вкладених контролів тобто групування
  *ngIf="
  !signupForm.valid &&
  signupForm.touched
  "
  де signupForm - це назва властивості з типом _FormGroup_ в нашому компоненті.
- Для групування полів форми викор. атрибут _formGroupName_, який додається до тегу, яким ми обгортаємо згруповані поля форми. В атрибут передається як рядок назва властивості в яку ми вклали наші поля в TS коді formGroupName="userData":
  "userData": new FormGroup({
  "username" : new FormControl(null, Validators.required),
  "email" : new FormControl(null, [Validators.required, Validators.email]),
  }),
- Якщо нам потрібно декілька полів додати, використовуючи лише одну властивість всередені форми, можна юзати **FormArray** --> 'anyName': new FormArray([]). Далі туди можна додавати потрібні нам дані, ітерувати їх і через _Property binding_ і індекс в масиві, використовувати їх в темплейті. Можна навіть додавати поля форми в цей масив ось так: (<FormArray>this.signupForm.get('anyName')).push(new FormControl());
- Для того, аби підписатися на зміни значення або статуса всієї форми або конкретних її полів, викор. вбудовані observables valueChanges() і statusChanges(). Приклади в коді forms-reactive
- _patchValue()_,_setValue()_ і _reset()_ також доступні для _Reactive_ підходу

## Pipes

Pipes використовується для зміни вихідних даних.
Pipes працюють як синхронно так і асинхронно.
Pipes don't change actual object

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

Наприклад, є властивість _username_: 'Max'; яку ми показуємо на сторінці `<p>{{ username }}</p>`, і якщо нам по якійсь причині потрібно вивести ім'я великими літерами, можна використати вбудований _pipe_ **uppercase**:

```javascript
<p>{{ username | uppercase }}</p>
```

для трансформації виводу. Вбудовані пайпи можна глянути в документації.

- застосування _date pipe_ з параметризацією

```javascript
<td>{{ room.checkOutTime | date : 'longDate' }}</td>
```

Також пайпи можна поєднувати в ланцюг:

```javascript
{{ server.started | date: 'fullDate' | uppercase }}
```

Angular парсить їх зліва направо.

Можна створювати _кастомні_ пайпи, до яких, в тому числі, можна додавати _параметризацію_:
'{{ server.name | shorten: 15 }}' - **shorten** - назва пайпу, 15 - параметр, який ми передаємо.  
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

_PipeName_ додається в _declarations_ of _@NgModule_.
Потрібно пам'ятати, що при використанні пайпу під час ітерації, він не викликається повторно при оновленні масиву чи об'єкту _by default_. Щоб змінити цю поведінку, потрібно в декоратор додати властивість `_pure_ : false`

1.  _Dynamic Components_--------------------------------------------------------------------------------------------------------

    Динамічний компонент - це компонент, який ми за певної умови додаємо в DOM під час виконання коду. Наприклад ми хочемо показати попередження чи модальне/діалогове вікно яке повинно відобразитись лише за певної дії чи умови.
    Як правило, ми будемо використовувати \*ngIf="" для цього:
    <div class="row auth-wrapper">
      <div class="col-xs-12">
        <app-alert *ngIf="error"></app-alert> <----------------Динамічно використовуємо компонент
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

    Інший спосіб це _Dynamic Component Loader_
    Загальний концепт, за яким ми створюємо компонент і вручну додаємо його в DOM зсередини коду. Ми самі в коді будемо контролювати за яких умов цей компонент ініціалізовуватиметься, які дані в нього будуть передані і коли він буде видалиний. Цей підхід може бути корисним, так як по суті ми можемо все контролювати з коду, нам не потрібно чіпати темплейт.
    Як це робить можна подивитися в course_project --> auth.component --> showErrorAlert()

2.  _Angular Modules_---------------------------------------------------------------------------------------------

    - Angular modules - це будівельні блоки застосунку. Angular аналізує всі _@NgModules_ в проекті, щоб зрозуміти його і всі його деталі і можливості. _@NgModule()_ декоратор трансформує звичайний TypeScript клас в модуль Angular
    - В Angular modules визначаються всі **компоненти**, **директиви**, **пайпи**. Вони додаються в масив `declarations: []`. В застосунку має бути хоча б один модуль.
    - Всі основні можливості Angular додані як _@NgModules_,наприклад FormsModule, щоб можна було швидко і просто їх додати, коли вони нам потрібні.
    - Також потрібно пам'ятати, що кожен модуль в Angular працює самостійно, вони не комунікують один з одним.
    - Тому якщо нам потрібен функціонал для роботи з формами в декількох компонентах, які в свою чергу в окремих модулях, нам потрібно додавати відповідний модуль, наприклад, FormsModule, в _imports[]_ кожного з цих модулів. _BrowserModule_, який відповідає за різні базові фічі, як _ngFor/ngIf_, додаємо в основний модуль, і якщо він потрібен в інших модулях, то додавати треба _CommonModule_.
    - Потрібно пам'ятати, що вказувати в _declarations[]_ чи то компонент, чи директива, чи пайп можна лише раз в якомусь одному модулі. Далі їх просто потрібно експортувати і імпортувати де нам потрібно.
    - Стосовно **сервісів** є 5 варіантів їх додавання з різними особливостями.
    - Перший і рекомендований, це використовувати _@Injectable({providedIn: 'root'})_ що робить його доступним **application wide** надаючи доступ до одного і того ж екземпляру класу. В такому разі його не потрібно додавати в _providers[]_ в _AppModule_, що і є ще одним варіантом, який є аналогом першому.
    - Те ж саме буде відбуватись, якщо ми додаватимемо сервіс в будь-який інший модуль, який є жадібно завантажений, тобто одразу ж, а не на вимогу. Але в такому разі краще додавати сервіс в AppModule.
    - При додаванні сервіса в _Lazy loaded_ модуль, такий модуль матиме свій власний екземпляр класу сервіса, використовуючи _child injector_
    - Також можливий варіант, коли єдиний екземпляр класу сервіса буде доступний лише в компоненті або в дереві компонентів.
    - Сервіси не потрібно експортувати щоби потім імпортувати.

**Lazy Loading**
Суть полягає в тому, щоб завантажувати певні модулі(і весь їх вміст) лише тоді, коли ми переходитимо по певним маршрутам, які відповідатимуть цим модулям. Тобто при використанні цього підходу ми на початку завантажуватимемо лише модуль кореневого, тобто початкового маршруту.
Приклад імплементації є в app-routing.module.ts
Також там є приклад використання _preloadingStrategy_ - інструмент, класу _RouterModule_ який дає можливість передзавантаження lazy-loaded коду для того, щоб прибрати можливу затримку, яка може виникнути при його завантаженні(цього коду).

## Standalone Components

- Standalone components are regular Angular components with one important twist: They don't require a **@NgModule** as a wrapper.
- Instead, you can use them, well, "standalone". They define their own dependencies and can be imported and used anywhere.
- It's also not just "standalone components", but also "_standalone directives_" and "_standalone pipes_".
- Також самостійні компоненти можна використовувати разом із стандартним підходом з декоратором `@NgModule`

```javascript
@Component({
standalone: true, //ця властивість зі значенням true говорить Ангуляру про те, що це самостійний компонент. Його не потрібно передавати в _declarations[]_ модулю
selector: 'app-test',
templateUrl: './test.component.html',
styleUrls: ['./test.component.css'],
})
```

- Якщо ми хочемо використовувати такий компонент в інших компонентах якогось модуля, потрібно передати його в `imports[]` цього модуля

### Standalone directives

Стосовно директив, якщо ми хочемо викор. директиву в самостійному компоненті, є _два_ підхода.

1. Перший, зробити директиву теж standalone і передати її в `imports[]`.
2. Другий, проміжний, в нашому компоненті в `imports[]` додати модуль, де додана потрібна нам директива.

- Якщо ми хочемо зробити самостійним наш кореневий компонент, потрібно в `main.ts` викор. `bootstrapApplication(AppComponent)` де _AppComponent_ - кореневий компонент.

### Services and Standalone Components

- По дефолту, щоб сервіс був доступний всюди, викор. `@Injectable({ providedIn: 'root' })`. Це вирішує всі проблеми.
- Також, можна передати сервіс в `bootstrapApplication(AppComponent, {providers: SomeService})`. В обох випадках використовуватиметься єдиний екземпляр класу цього сервісу.
  Можна додати сервіс напряму в standalone компонент в _providers[]_. тоді у цьому компоненті буде власний екземпляр класу цього сервіса.

> Реалізацію Routing і lazy loading для самостійних компонентів можна глянути в standalone-components додатку.

## Offline Capabilities with Service Workers

У широкому сенсі воркер - це скрипт, що працює в потоці, окремому від потоку браузера. Звичайний Javascript файл, що додається в HTML документ через тег `<script>`, виконується в основному потоці. Якщо в основному потоці буде виконуватися багато операцій, це може уповільнити сайт, роблячи взаємодію смиконою і повільною. Веб-воркери, сервіс-воркери та ворклети - все це скрипти, які виконуються в окремому потоці. Також потрібно пам_ятати, що всі воркери не мають доступу до DOM

Сервіс-воркери - це тип воркерів, які служать певній меті - бути проксі(проміжною ланкою) між браузером та мережею та/або кешем. На відміну від звичайних веб-воркерів, сервіс-воркери мають додаткові можливості, які дозволяють виконувати свої функції проксі-сервера. Після встановлення та активації сервіс-воркери можуть перехоплювати будь-які запити з основного документа. Після перехоплення сервіс-воркери можуть, наприклад, відповісти, повернувши документ із кешу замість того, щоб йти за ним у мережу, тим самим дозволяючи веб-додатку працювати автономно!
_Service Worker_ працює у окремому потоці. Це означає, що ви не можете отримати доступ до багатьох об'єктів, доступних в основному потоці JavaScript, включаючи DOM та деякі API, такі як XHR або файли cookie.
Треба мати на увазі, що сервіс-воркери повинні обслуговуватися через HTTPS, інакше їх не зареєструє браузер.

**@angular/pwa** бібліотека, яка даж можливість викор. service workers в angular app
**ngsw-config.json** - файл, в якому конфігурується service-worker
npm run build:serve для скрипта "build:serve": "ng build --configuration production && http-server dist/app-name"
npm i -g **http-server/http-server-spa** - легкий node-based сервер, за допомогою якого ми можемо запустити нашу аппку і перевірити роботу service worker. Для цього треба перейти в папку dist/app-name після того, як ми збілдили застосунок. Команда для зауску сервера на вказаному порті: http-server -p 8081
Перевірити роботу воркера можна в дев тулзах браузера: Application-->Service Workers
Наприклад, якщо ми увімкнемо offline mode, на сторінці буде відображатись весь наш статичний контент, або якщо такого немає, просто порожня сторінка. Але не помилка про відсутнє з'єднання з інтернетом.

Налаштування роботи воркера в файлі _ngsw-config.json_
"assetGroups": [] - масив зі статичними даними
"dataGroups": [] - масив з динамічними даними
"installMode": "prefetch"/"lazy", - відповідає за те, як буде закешовано контент об'єкта _resources_ під час фази ініціалізації
"updateMode": "prefetch"/"lazy", - відповідає за те, як буде закешовано контент об'єкта _resources_ під час фази оновлення
В об'єкт _resources_ можна передати масив "urls" : [], куди можна передати лінки на якісь зовнішні ресурси

## Angular Elements

- Ця фіча дає можливість перетворити звичайний angular component в нативний web component (custom html element so to say), які є частиною DOM і які можна викор. в JS чи інших фреймворках.

> Реалізація описана в ng-elements застосунку.
> npm install @angular/elements - для інсталяції пакета
