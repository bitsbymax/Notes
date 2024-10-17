# Understanding the Critical Rendering Path

>useful resources: [1](https://bitsofco.de/understanding-the-critical-rendering-path/), [2](https://medium.com/@mustafa.abdelmogoud/how-the-browser-renders-html-css-27920d8ccaa6), [3](https://developer.mozilla.org/en-US/docs/Web/Performance/Critical_rendering_path), [4](https://developer.mozilla.org/en-US/docs/Web/Performance/How_browsers_work)

## Critical Rendering Path

Це процес між отриманням даних (HTML, CSS, JS) браузером та перетворенням їх на пікселі в браузерному вікні.

- `Critical resource` - ресурс, що може блокувати початковий рендеринг. Це все, що може впливати на відтермінування `first contentful paint`
- `First Contentful Paint (FCP)` is a performance metric that measures the time it takes for the browser to render the first piece of `DOM` content on a page. This content can include text, images, SVG elements, or non-white canvas elements
  `FCP` is crucial because it gives users visual feedback that the page is loading, improving their perception of the site’s speed3. A good `FCP` score is typically 1.8 seconds or less
- `Critical path length` - Кількість запитів байт інформації або загальний час на завантаження всіх критичних ресурсів
- `Critical bytes` - Сумарна кількість байт, які необхідно завантажити, щоб відбувся перший рендеринг сторінки
- `Time to First Byte, TTFB` - Час до першого байта це час між моментом, коли користувач відправив запит, скажімо, натиснувши на посилання, і моментом отримання першого пакета даних HTML. Перший пакет зазвичай містить 14КБ даних

1. Constructing the DOM Tree
1. Constructing the CSSOM Tree
1. Running JavaScript
1. Creating the Render Tree
1. Generating the Layout
1. Painting

![alt text](<Rendering_assets/Critical Rendering Path.avif>)

## `DOM`

The `DOM` (Document Object Model) Tree is an Object representation of the fully parsed HTML page.

- `DOM` складається з DOM вузлів, яких є **12** типів:

1. `Document` Node: Represents the entire document. It’s the root of the DOM tree.
2. `Element` Node: Represents an HTML element, such as _`<div>`_, _`<p>`_, _`<ul>`_, etc.
3. `Text` Node: Contains the text content within an element.
4. `Attribute` Node: Represents an attribute of an HTML element, such as id, class, src, etc.
5. `Comment` Node: Contains comments within the HTML document.
6. `DocumentFragment` Node: Represents a lightweight document object that can hold a portion of the document.
7. `DocumentType` Node: Provides an interface to the entities defined for the document.
8. `ProcessingInstruction` Node: Represents a processing instruction.
9. `CDATASection` Node: Represents a CDATA section in a document (text that will not be parsed by a parser).
10. `EntityReference` Node: Represents an entity reference.
11. `Entity` Node: Represents an entity.
12. `Notation` Node: Represents a notation declared in the DTD (Document Type Definition)

A good thing about HTML is that it can be executed in parts. The full document doesn't have to be loaded for content to start appearing on the page. However, other resources, CSS and JavaScript, can block the render of the page.

## `CSSOM`

The `CSSOM` (CSS Object Model) is an Object representation of the styles associated with the DOM.

- Після побудови DOM дерева, браузер будує `CSSOM` дерево.

  - Починає будуватися з вбудованих стилів (ті, що зашиті в самі `html` елементи по замовчуванні і стилі в тегах `<style>`)
  - Теж дерево, але з CSS правил
  - Перебудовується щоразу, як додаються нові стилі, так як інформації про те, куди саме і які стилі були додані - немає.
  - Також стилі наслідуються по ієрархії від батьківських до дочірніх за рахунок деревовидності.

## Render

Rendering steps include **style, layout, paint, and in some cases compositing**. The CSSOM and DOM trees created in the parsing step are combined into a **render tree** which is then used to compute the layout of every visible element, which is then painted to the screen.

У деяких випадках вміст може бути винесено на окремі шари і поєднано (`composition`) - такий підхід збільшує продуктивність, даючи змогу відтворювати вміст екрана на графічному процесорі (GPU) замість ЦПУ (CPU). Це звільняє основний потік.

### Style

Побудова `Render-tree`:

- Це похідне дерево, яке збирається докупи з `DOM` і `CSSOM` дерев.
- Перебудовується кожного разу, коли змінюється `DOM` або `CSSOM`
- Складається з `renderer objects`
- `Renderer object` містить в собі посилання на `DOM` вузол та відповідний йому набір CSS правил з `CSSOM`
- Є основою для `layout calculation` (**`reflow`**) (розрахунок розмірів і положення елементів) тобто блочного макету на основі `box models`
- Містить лише ті елементи, що є частиною `layout`, тобто впливають на `flow` навколо себе. Тобто такі елементи як `<script>` і `<meta>` теги виключені з нього

### Layout

`Reflow` - це будь-яке наступне визначення розміру і положення будь-якої частини сторінки або всього документа.

- Етап, коли розраховуються всі розміри, положення, відступи та взаємозалежності між елементами. `Layout` це результат завершення цього процесу.
- Використовується так звана "потокова" модель (`flow`), яка дозволяє обробити більшість елементів за один прохід зліва-направо зверху-вниз
- Таблиць це не стосується. Таблиці будуються рядками і ширина кожної чарунки розраховується окремо таким чином, щоб в кожній колонці вона була однакова
- Потокова модель означає, що зміни властивостей елемента спричиняють наслідки та зміни в макеті далі за потоком (вправо-вниз), але не перед елементом.
- Є найдорожчою операцією з точки зору продуктивності

### Paint

- Процес, який відбувається одразу після `Layout`, коли браузер по наявній блочній моделі відмальовуватиме вміст кожного елемента на сторінці.
- Момент, коли це відбувається вперше, називається **`first meaningful paint`** (перше значуще відтворення)
- Створюються окремі "шари", в основному за `z-index`
- Кожен окремий шар відмальовується окремо
- Далі відбувається `layer-composition` - склеювання усіх шарів в одне зображення
- Браузери застосовують оптимізації, намагаючись зменшити навантаження на GPU і розмір оновлень в загальному. Тобто перераховуються і перемальовуються лише ті елементи, які того потребують

### Compositing

- Коли розділи документа відмальовані на різних шарах, а один шар розташований над іншим або перекриває його, стає необхідною композиція. Цей крок дає змогу браузеру гарантувати, що кожен шар відмальований на екрані в правильному порядку, а вміст відображається коректно.

- Під час довантаження раніше запитаних ресурсів (наприклад, зображень) може знадобитися перерахувати розміри та положення елементів відносно один одного.
Цей перерахунок - `reflow` - запускає перемальовування (`repaint`) і перекомпозицію (`re-composite`).
Якщо ми заздалегідь визначили розмір зображення, перерахунок не буде необхідний, і в цьому разі тільки той шар, який має бути перемальований, буде перемальований. Але якщо ми не визначили розмір зображення заздалегідь, то браузер, після отримання відповіді від сервера, буде змушений відмотати процес рендерингу назад до кроку компонування (`layout`) і почати процес відтворення ще раз.

### Interactivity

- Можна було б подумати, що щойно основний потік завершує відтворення сторінки - «все готово». Це не завжди так. Якщо серед ресурсів, що завантажуються, є `JavaScript`, завантаження якого було коректно відкладено, а запуск якого відбувається тільки після події `onload`, основний потік починає обробку скриптів. **Під час цієї обробки браузер не може обробляти події прокрутки, натискань тощо.**

- `Time to Interactive` (`TTI`, час до інтерактивності) - це показник того, як багато часу проходить між найпершим мережевим запитом і моментом, коли сторінка стає інтерактивною.
У хронології цей етап слідує одразу за `First Contentful Paint`. Інтерактивністю називається показник того, що сторінка відреагувала на дію користувача за час у `50мс`. Якщо процесор зайнятий опрацюванням, компіляцією та виконанням JavaScript, то браузер не може відреагувати досить швидко, а отже сторінка вважається не інтерактивною.

## Blocking behavior (parse/render blocking)

- Усі скрипти та стилі блокуючі в принципі, тобто вони на тому чи іншому етапі переривають процес парсингу чи рендерингу сторінки
- Синхронне завантаження `JS` скриптів блокує і парсинг і рендер
- Виконання `JS` скриптів теж перериває процес парсингу і рендеру
- Побудова `CSSOM` блокує виконання `JS` коду. Це потрібно для того, щоб якщо всередині скрипта відбуваються якісь маніпуляції з `DOM`, вони проводились з найактуальнішим і найсвіжішим `render tree`
- Завантаження `CSS` стилів блокує процес рендерингу
- Макет (`layout`) не буде будуватися, поки не завантажаться всі підключені ресурси

## Preload Scanner

While the browser builds the `DOM` tree, this process occupies the main thread. As this happens, the **preload scanner** will parse through the content available and request _high-priority resources_ like `CSS`, `JavaScript`, and `web fonts`.
Thanks to the preload scanner, we don't have to wait until the parser finds a reference to an external resource to request it. It will retrieve resources in the background so that by the time the main HTML parser reaches the requested assets, they may already be in flight or have been downloaded. The optimizations the preload scanner provides reduce blockages.

## СRP optimization

- Використовувати атрибут `async` для завантаження скриптів.

  - Парсинг в такому разі не блокуватиметься під час завантаження, лише під час виконання.
  - Такий скрипт почне виконуватися щойно завантажиться
  - Порядок виконання залежить лише від порядку завантаження
  - Недоліком є те, що з цим атрибутом скрипт почне виконуватися одразу ж як завантажиться.

- А ще краще використовувати атрибут `defer`

  - Завантажується асинхронно як і з `async`
  - Почне виконуватись тільки після спрацювання події `DOMContentLoaded`
  - Порядок виконання такий же як порядок підключення
  - Виконання такого скрипта парсинг і побудову `DOM` не блокує взагалі

- Використання `Service Workers`

- `CDN`
  - Content Delivery Network - забезпечує швидшу доставку ресурсів за рахунок географічної близкості серверів і паралелізму. Також ця мережа кешує статичні ресурси
  - Також CDN часто підтримує HTTP/2 який, в свою чергу забезпечує multiplexing and header compression, що впливає на час завантаження ресурсів.

### Multiplexing and header compression

- **`Multiplexing`**:

  - One of the most beneficial features of **HTTP/2** is `multiplexing`. It allows multiple requests and responses to be sent simultaneously over a single connection.
  - In traditional HTTP/1.1, each asset (like images, scripts, stylesheets) required a separate connection, leading to latency due to connection setup and teardown overhead.
  - With multiplexing, multiple streams share the same connection, allowing several assets to download in parallel without the need for establishing multiple connections.
  - This eliminates the head-of-line blocking issue seen in HTTP/1.1, where one slow resource could delay the loading of other resources.

- **`Header Compression`**:
  - **HTTP/2** improves performance by compressing HTTP headers sent over the network.
  - In HTTP/1.1, headers were verbose and redundant, consuming unnecessary bandwidth.
  - With header compression, the size of headers is minimized, reducing the overhead and improving efficiency.
  - Prioritizing network resource allocation is another benefit of HTTP/2, ensuring critical resources are delivered promptly.

In summary, `HTTP/2`’s multiplexing and header compression enhance performance, reduce latency, and make web applications faster and more efficient!

### `Preconnect`, `prefetch`, `preload`

- `Preconnect`
  - **Purpose**: Establishes a connection to a server before it’s actually needed.
  - **Benefit**: Speeds up future loads from that origin by preemptively performing part or all of the handshake (DNS+TCP for HTTP, and DNS+TCP+TLS for HTTPS origins)1.
  - **Usage**: Include `<link rel="preconnect">` in your HTML to improve perceived page speed by reducing connection time to other origins.
- `Prefetch`
  - **Purpose**: Fetches resources needed for the next navigation (i.e., the next page the user might visit).
  - **Benefit**: Optimizes loading by fetching assets in advance.
  - Example: Use `<link rel="prefetch" href="next-page.js">` to load resources for subsequent pages.
- `Preload`
  - **Purpose**: Offers more control over how specific resources are fetched for the current navigation.
  - **Benefit**: Allows prioritization of critical resources (e.g., images, CSS, JavaScript, fonts).
  - **Example**:
    - Preloading an image: `<link rel="preload" href="image.png">`
    - Preloading fonts (with CORS): `<link rel="preload" href="font.woff" as="font" crossorigin>`
    - Preloading a stylesheet via JavaScript:

```html
<script>
  var res = document.createElement('link');
  res.rel = 'preload';
  res.as = 'style';
  res.href = 'my-styles.css';
  document.head.appendChild(res);
</script>
```

## Як прискорити рендеринг сторінки

- Кешувати усі повторні звернення до `DOM` елементів. Тому що навіть при простому зверненні через `getElementBy` до `DOM` дерева, відбудеться `reflow`.
- По можливості виконувати усі заплановані операції з `DOM` за один раз (`batch`)
- Можна використовувати хак з встановленням для елемента, для якого ми хочемо зробити багато змін, `display: none` або просто склонувати його, зробити зміни і замінити оригінальний елемент новим зміненим. Так як поки елемента має значення властивості `display`: `none`, він в процесі `reflow` участі не бере
- Не модифікувати живий `DOM` в циклах
- Зважати, що `HTMLCollection` - це "живий" об'єкт, що оновлюється на кожне звернення через `DOM api` до елементів `DOM` дерева. А оновлюється він за рахунок `reflow`
- Використовувати CSS-анімації, так як вони не запускають `reflow`, лише `repaint`, який є досить малозатратним процесом з точки зору продуктивності
- Використовувати `requestAnimationFrame` для керованих анімацій
- Використовувати `will-change` для CSS властивостей, які будуть змінюватися для залучення оптимізації анімацій. Таким чином ми вказувати браузеру, які властивості ми плануємо анімувати
- Делегувати `DOM` події
- Використовувати `debounce` для подій, які викликаються як реакція на якусь дію користувача (зміна розміру вікна чи введення символів в інпуті)
