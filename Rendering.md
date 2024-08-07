# Rendering process in Browser

## `DOM`

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

## `CSSOM`

- Після побудови DOM дерева, браузер будує `CSSOM` дерево.

  - Починає будуватися з вбудованих стилів (ті, що зашиті в самі `html` елементи по замовчуванні і стилі в тегах `<style>`)
  - Теж дерево, але з CSS правил
  - Перебудовується щоразу, як додаються нові стилі, так як інформації про те, куди саме і які стилі були додані немає.
  - Також стилі наслідуються по ієрархії від батьківських до дочірніх за рахунок деревовидності.

## `Render tree`

- Наступним, 3 етапом є побудова `Render-tree`.
  - Це похідне дерево, яке збирається докупи з `DOM` і `CSSOM` дерев.
  - Перебудовується кожного разу, коли змінюється `DOM` або `CSSOM`
  - Складається з `renderer objects`
  - `Renderer object` містить в собі посилання на `DOM` вузол та відповідний йому набір CSS правил з `CSSOM`
  - Є основою для `layout calculation` (**`reflow`**) (розрахунок розмірів і положення елементів) тобто блочного макету на основі `box models`
  - Містить лише ті елементи, що є частиною `layout`, тобто впливають на `flow` навколо себе. Тобто такі елементи як `<script>` і `<meta>` теги виключені з нього

## `Reflow` (Layout calculation)

- Етап, коли розраховуються всі розміри, положення, відступи та взаємозалежності між елементами
- Використовується так звана "потокова" модель (`flow`), яка дозволяє обробити більшість елементів за один прохід зліва-направо зверху-вниз
- Таблиць це не стосується. Таблиці будуються рядками і ширина кожної чарунки розраховується окремо таким чином, щоб в кожній колонці вона була однакова
- Потокова модель означає, що зміни властивостей елемента спричиняють наслідки та зміни в макеті далі за потоком (вправо-вниз), але не перед елементом.
- Є найдорожчою операцією з точки зору продуктивності

## `Repaint`

- Процес, який відбувається одразу після `reflow`, коли браузер по наявній блочній моделі відмальовуватиме вміст кожного елемента на сторінці
- Створюються окремі "шари", в основному за `z-index`
- Кожен окремий шар відмальовується окремо
- Далі відбувається `layer-composition` - склеювання усіх шарів в одне зображення
- Браузери застосовують оптимізації, намагаючись зменшити навантаження на GPU і розмір оновлень в загальному. Тобто перераховуються і перемальовуються лише ті елементи, які того потребують

## `Blocking behavior` (parse/render blocking)

- Усі скрипти та стилі блокуючі в принципі, тобто вони на тому чи іншому етапі переривають процес парсингу чи рендерингу сторінки
- Синхронне завантаження `JS` скриптів блокує і парсинг і рендер
- Виконання `JS` скриптів теж перериває процес парсингу і рендеру
- Побудова `CSSOM` блокує виконання `JS` коду. Це потрібно для того, щоб якщо всередині скрипта відбуваються якісь маніпуляції з `DOM`, вони проводились з найактуальнішим і найсвіжішим `render tree`
- Завантаження `CSS` стилів блокує процес рендерингу
- Макет (`layout`) не буде будуватися, поки не завантажаться всі підключені ресурси
- Існує механізм `preload scanner`, який будує `DOM` в фоні, поки основний процес заблоковано

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
  var res = document.createElement("link");
  res.rel = "preload";
  res.as = "style";
  res.href = "my-styles.css";
  document.head.appendChild(res);
</script>
```

## Critical Rendering Path

Це процес між отриманням даних (HTML, CSS, JS) браузером та перетворенням їх на пікселі в браузерному вікні.

- `Critical resource` - ресурс, що може блокувати початковий рендеринг. Це все, що може впливати на відтермінування `first contentful paint`
    > First Contentful Paint (`FCP`) is a performance metric that measures the time it takes for the browser to render the first piece of `DOM` content on a page. This content can include text, images, SVG elements, or non-white canvas elements.
    >
    > FCP is crucial because it gives users visual feedback that the page is loading, improving their perception of the site’s speed3. A good `FCP` score is typically 1.8 seconds or less.
- `Critical path length` - Кількість запитів байт інформації або загальний час на завантаження всіх критичних ресурсів
- `Critical bytes` - Сумарна кількість байт, які необхідно завантажити, щоб відбувся перший рендеринг сторінки

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
