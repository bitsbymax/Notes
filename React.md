# Building Blocks of a Web Application

There are a few things you need to consider when building modern applications. Such as:

- User Interface - how users will consume and interact with your application.
- Routing - how users navigate between different parts of your application.
- Data Fetching - where your data lives and how to get it.
- Rendering - when and where you render static or dynamic content.
- Integrations - what third-party services you use (CMS, auth, payments, etc) and how you connect to them.
- Infrastructure - where you deploy, store, and run your application code (Serverless, CDN, Edge, etc).
- Performance - how to optimize your application for end-users.
- Scalability - how your application adapts as your team, data, and traffic grow.
- Developer Experience - your team’s experience building and maintaining your application.

JavaScript frameworks were created to make this kind of work a lot easier — they exist to provide a better developer experience. They don't bring brand-new powers to JavaScript; they give you easier access to JavaScript's powers so you can build for today's web.

## Steps to be done to move an application from development to production

- **compile**

  Compiling refers to the process of taking code in one language and outputting it in another language or another version of that language

- **minify**

  Minification is the process of removing unnecessary code formatting and comments without changing the code’s functionality. The goal is to improve the application’s performance by decreasing file sizes

- **bundle**

  Bundling is the process of resolving the web of dependencies and merging (or ‘packaging’) the files (or modules) into optimized bundles for the browser, with the goal of reducing the number of requests for files when a user visits a web page

- **code split**
  Developers usually split their applications into multiple pages that can be accessed from different URLs. Each of these pages becomes a unique entry point into the application.
  Code-splitting is the process of splitting the application’s bundle into smaller chunks required by each entry point. The goal is to improve the application's initial load time by only loading the code required to run that page.

**Build time** (or build step) is the name given to a series of steps that prepare your application code for production.

**Runtime** (or request time) refers to the period of time when your application runs in response to a user’s request, after your application has been built and deployed.

_tree-shaking_ (removal of any code that isn't actually used in the app during the build process)

- npx - the npm tool that allow you to download npm package and execute it
- 60fps досягається, якщо час, який потрібен на рендеринг не перевищує 16.6ms
- Чому не можна мутувати state? Тому що в процесі діффінгу, Реакт не заходить в об'єкти і не порівнює їх властивості, він порівнює лише посилання на ці об'єкти. І якщо воно не змінилось, то Реакт вважатиме, що state не змінився
- Simpler way to pass single component:

  ```javascript
  import { FC, PropsWithChildren } from 'react';
  type Props = PropsWithChildren<{}>;

  export const AppH1: FC<Props> = (props) => {
    return <h1>{props.children}</h1>;
  };

  const App = () => (
    <AppH1>
      <TranslatedText text='hello' />
    </AppH1>
  );
  ```

  Таким чином ми передаємо в компонент AppH1 інший компонент TranslatedText, до якого буде доступ через props.children. Це називається _Components Composition_

## Components Lifecycle methods/hooks

| Class based           |            Functional             |
| --------------------- | :-------------------------------: |
| Initialization        |                 -                 |
| render()              |            function()             |
| ComponentDidMount     |       useEffect(()=>{},[])        |
| ShouldComponentUpdate | аналогом є служб. мет. useMemo()  |
| ComponentDidUpdate    |         useEffect(()=>{})         |
| ComponentDidCatch     |                 -                 |
| ComponentWillUnmount  | useEffect(()=>{return ()=>{}},[]) |

## Three pillars of React

- View is derivative of your data. To change the VIEW - change the DATA
- Data is the single source of truth. Separating VIEW and DATA is super important!
- Declarative over Imperative. You write what you want to get, but not a way how to get it
- Based on components

## Lifting state up pattern

Як передавати дані з дочірнього компонента в батьківський?
Є компонент `А` і дочірній `В`. В компоненті `В` є дані, які потрібні нам в компоненті `А`, наприклад `inputData`. Для цього ми в компоненті `А` створюємо `handlerFunction`, яка як аргумент прийматиме дані, які ми передаватимемо з компонента `В`. Далі в компонент `В` ми передаватимемо `props` `onSomething={handlerFunction}`. І вже в компоненті `B` ми викликатимемо цю функцію, яку ми передали через `props` і передаватимемо туди наші дані з компонета `В` -> `props.onSomething(inputData);`

Також за допомогою цього паттерна ми можемо ділитися даними між компонентами, які мають спільний батьківський компонент. Тобто якщо є спільний батьківський компонент А і два дочірніх В і С, і дані з С нам потрібні в Б, ми ці дані "піднімаємо" з С в А і далі передаємо в В.

## React Components

- Компоненти в React діляться на **Stateful** components і **Presentational** components, **Smart** or **Dumb**. Також компоненти можуть буть **Controlled** і **Uncontrolled** (часто таку характеристику можна побачити і для форм). Форма, чи інпут, для контролю якого викор. _Two-way binding_ через прив'язку значення input'a до змінної з useState() до атрибуту _value_ вважається контрольованою. Інпут, значення якого отримується через _ref_ вважається неконтрольованим, так як React напряму ніяк не контролює і не модифікує значення цього input'a, а лише читає.

- Компонент обгортка **_Wrapper_** - дає можливість замінити необов'язковий `<div>` у випадках, коли цей `<div>` нам потрібен лише для того, щоб відмалювати більше одного кореневого елемента.
  
```js
  const Wrapper = (props) => props.children;
```

  Також можна використати _Fragment_ <></>

## React Portals

_React Portals_ - дає зручний спосіб рендерингу компонентів поза межами їх батьківського компонента, хоча при цьому в структурі компонентів, визначених в React, нічого не міняється. Розташування розмітки, яку цей компонент повертає, зміниться вже в реальній DOM.

```js
ReactDOM.createPortal(child, container);
```

Тобто ми в потрібному нам місці створюємо контейнер, задаємо йому id, і потім в компоненті React ми повертатимемо `jsx` з викликом метода `createPortal()` і переданими в нього компонентом і контейнером.

```js
const portalElement = document.getElementById('overlays');

const Modal = (props) => {
  return (
    <>
      {ReactDOM.createPortal(<Backdrop />, portalElement)}
      {ReactDOM.createPortal(
        <ModalOverlay>{props.children}</ModalOverlay>,
        portalElement,
      )}
    </>
  );
};
```

Цей інструмент зазвичай викор. для Modal dialog boxes, Tooltips, Hovercards, Loaders

## Re-rendering in React

There are **four - 4** reasons why a component would re-evaluate itself

1. state changes
2. parent component re-renders
3. context changes
4. hooks changes

- Але врешті-решт повторний виклик компонента (`re-evaluating`) і, якщо потрібно, `re-rendering` відбуваються через зміну стану, так як і `Context` і `props` так само залежать від даних і, так би мовити, підписуються на їх зміну.

- It always goes “down” the tree: the re-render of a child doesn’t trigger the re-render of a parent. При цьому re-rendering батьківського компоненту завжди по ланцюгу вниз тригерить re-evaluating або виклик дочірніх компонентів.

---

**_React hooks lifecycle_**

Загалом є 3 умовних етапи життя компонентів -> **Mounting, Updating, Unmounting** і 2 фази, які присутні на кожному з етапів.

## Render phase

- Pure and has no side effects. May be paused, aborted or restarted by React

## Commit phase

- Can work with DOM, run side effects, schedule updates

---

**_React rendering optimization_**

Якщо ми хочемо, щоб компонент не викликався повторно, коли відбувся виклик батьківського компонента але при цьому в ньому не змінилися _props_, викор. `React.memo()`.

```js
export default React.memo(SomeCompName);
```

- Але треба розуміти, що використовуючи цей прийом оптимізації ми також можемо і знизить продуктивність застосунку, так як Реакту потрібно буде для таких компонентів кожного разу, коли батьківський комп. оновлюється, додатково порівнювати дані з props на предмет того, чи змінились вони. Тобто Реакту потрібно зберігати дані з _props_ в пам'яті і порівнювати їх.
  Тому ми не завжди виграватимемо в продуктивності, використовуючи цей прийом в порівнянні зі стандартною поведінкою. Це залежить від кількості властивостей в _props_, складності компонента, кількості дочірніх компонентів.
- Також потрібно пам'ятати, що якщо в компонент як _props_ передаються не примітиви, тобто `[], {}, f()` - кожного разу це буде новий екземпляр, тому навіть якщо ми викор. `React.memo()` - для компонента і в _props_ наче як нічого не міняється напряму - це не так, бо під час виклику функції (компонента), непримітивні дані перестворюються JS кожного разу і тому вони будуть нові кожного разу, хоч і суть їх буде та сама.

---

- Щоб цю проблему вирішити для функцій, існує хук `useCallback(() => {}, [])`
  При його використанні React гарантує, що це завжди буде одна і та ж функції в пам'яті. Масив залежностей для цього хука потрібен по тій причині, що функціє може використовувати зовнішні змінні, наприклад змінну, яка зберігає стан з `useState()`. І для того, щоб при зміні стану, нове значення попадало всередину цією функції, її потрібно передати в масив залежностей. Тоді функція всередині `useCallback()` перествориться з новим значенням цієї змінної.

---

Ще один хук для оптимізації обчислень в компоненті це `useMemo(() => {})`
Цей хук дозволяє закешувати якесь значення доти, доки не зміниться одна з прив'язок в масиві залежностей.

```js
const token = useMemo(() => {
  return Math.random();
}, [deps]);
```

Якщо в масив `[deps]` нічого не передати, токен згенерується лише раз. Повторна генерається відбудеться якщо компонент буде видалено з DOM і додано знову.
Використання `useMemo()` без масиву `[deps]` немає сенсу, так як значення всередині хука буде змінюватися при кожному перерендерингу.
Корисно викор. `useMemo()` коли ми робимо _`sort()`_ або _`filter()`_ масива чи коли генерується якесь унікальне значення чи токен.

Тобто `useMemo()` вирішує 2 задачі:

- повторна фільтрація чи сортування, що є досить ресурсоємними завданнями, не виконуватиметься, якщо не змінився масив, що сортується(його звичайно ж треба додати в масив залежностей).
- в змінній, в якій зберігається результат сортування, при повторному рендерингу буде збережено посилання на той же масив в пам'яті, що і раніше, тобто не створюватиметься новий з тим же набором елементів.

---

**_State scheduling & batching_**

Оновлення стану в Реакт відбуваються не миттєво. Спочатку Реакт їх заплановує. Також Реакт може відкласти на певний час зміну стану, якщо в цей момент відбуваються якісь більш пріоритетні завдання. Наприклад, якщо юзер вводить текст в інпуті, для Реакта оновлення стану цього інпута буде більш пріоритетним ніж зміна тексту на екрані. Але Реакт гарантує, що порядок оновлення стану для однієї і тієї ж сутності завжди буде збережено. Тобто якщо ми двічі дуже швидко викличемо метод оновлення стану для конкретного _useState_ з компонента, то дані будуть змінені завжди в правильному порядку.

Ще один оптимізаційний механізм, це групування оновлень стану. Наприклад, якщо в коді виклики методів для оновлення стану ідуть один за одним, вони є синхронними і між ними немає ніяких коллбеків чи промісів, Реакт їх згрупує і оновить компонент лише раз, змінивши одночасно обидва стани.

## React hooks

_Правила використання хуків_:

- Викор. їх тільки в реакт компонентах або в кастомних хуках
- Викликати їх потрібно лише на верхньому рівні. Тобто всередині умов чи циклів чи вкладених функцій це робити заборонено.

**`useState`** ---> `const [value, setValue] = useState(defaultValue)` - хук для роботи зі станом, який приймає дефолтне значення для змінної `value` і повертає масив з встановленим `value` і `function`, яка це `value` змінює. І на кожну таку зміну, Реакт буде перемальовувати компонент.

При використанні `useState()` в компоненті, який перевикористовується, стан буде незалежний для кожного виклику цього компоненту. Так само це працюватиме і для використання декількох станів в одному компоненті.
Дефолтне значення для `value` буде встановлено при першій ініціалізації(first render) компонента, і після його зміни повертатиметься вже нове значення.
У випадках, коли ми використовуємо цей хук для відслідковування і оновлення стану полів форми, можна викор. два підхода:

```js
  <!-- //INITIATING STATE -->
  const [enteredTitle, setEnteredTitle] = useState(''); // підхід зі створенням стейту для кожного поля
  const [enteredAmount, setEnteredAmount] = useState('');
  const [enteredDate, setEnteredDate] = useState('');
  const [userInput, setUserInput] = useState({ // підхід зі створенням об'єкту зі всіма полями
      enteredTitle: '',
      enteredAmount: '',
      enteredDate: '',
  });

  <!-- // STATE UPDATING -->
  setEnteredTitle(event.target.value); //оновлення стану конкретного поля через відповідний метод
  'STATE UPDATING' при використанні підходу з об_єктом зі всіма полями
  //  ВАРІАНТ 1
    setUserInput({
      ...userInput,
      enteredTitle: event.target.value,
    });

  /* ВАРІАНТ 2 - більш коректний підхід з використанням попереднього стану.
    Так як Реакт оновлює стан не одразу, а в порядку черги.
    А у випадку використання ВАРІАНТУ 1, якщо буде заплановано багато оновлень стану одночасно, можуть виникати ситуації, коли ми отримуватимемо як попередній, некоректний або застарілий стан.
  ВАРІАНТ 2 цьому запобігає. */
    setUserInput((prevState) => {
      return { ...prevState, enteredTitle: event.target.value };
    });
```

---

`useEffect(() => {}, [deps])` - хук для виконання так званих _`Side effects`_(http запити, работа з таймерами, localStorage і в той же час, наприклад, перевірка і оновлення стану валідності форми після кожного нового введеного символу, це теж side effect, який спрацьовуватиме за певних обставин, в залежності від того, як його використовувати.

This may sound strange at first, but effects defined with `useEffect` are invoked **AFTER** render. To be more specific, it runs both after the first render and after every update. In contrast to lifecycle methods, **_effects don’t block the UI because they run asynchronously_**.

**_The key concepts of using effects_**

- You must have a thorough understanding of when components (re-)render because effects run after every render cycle.
- Effects are always executed after render, but you have options to opt out from this behavior.
- To opt out or skip effects, you have to understand basic JavaScript concepts about values. An effect is only rerun if at least one of the values specified as part of the effect’s dependencies has changed since the last render cycle.
- You should ensure that components are not re-rendered unnecessarily. This constitutes another strategy to skip unnecessary reruns of effects.
- You have to understand that functions defined in the body of your function component get recreated on every render cycle. This has an impact if you use it inside of your effect. There are strategies to cope with it (hoist them outside of the component, define them inside of the effect, use useCallback).
- You have to understand basic JavaScript concepts such as stale closures, otherwise you might have trouble tackling problems with outdated props or state values inside of your effect. There are strategies to solve this, e.g., with an effect’s dependency array or with the useRef Hook.
- You should not ignore suggestions from the React Hooks ESLint plugin. Do not blindly remove dependencies (and rashly ignore ESLint warnings) or carelessly use ESLint’s disable comments; you most likely have introduced a bug. You may still lack understanding of some important concept.
- Do not mimic the lifecycle methods of class-based components. This way of thinking does more harm than good. Instead, think more in terms of data flow and state associated with effects because you run effects based on state changes across render cycles.
- The question is not ‘when does this effect run,’ the question is ‘with which state does this effect synchronize with’?

For someone who really not understand the `useEffect` hook:

1. useEffect hook without mentioning any dependency array like - _`useEffect(someCallbackFuction)`_ runs for every render of the functional component in which its included **AFTER** component is rendered.
2. useEffect hook with an empty dependency array like this - _`useEffect(callbackFunc, [])`_ is executed only for the initial render of the component. And then it will not run in the further renders of the same functional Component until page will refresh in browser.
3. useEffect hook with some dependencies inside the dependency array like this - _`useEffect(callbackFunc , [dependency])`_ will run for the initial render as well as when the render happen due to change in dependencies mentioned in the dependency array.

**_CLEAN UP function_**

```js
useEffect(() => {
  ...
  return () => {} //<--- _CLEAN UP FUNCTION_. Ця фу-ція викликатиметься перед тим, як викликатиметься callback, всередині якого знаходиться ця clean up функція, але за виключенням першого рендерингу. І також перед тим, як компонент буде розмонтовано, тобто видалено з DOM, якщо настоупного циклу рендерингу не буде.
}, [])
```

- Що передавати в [dependencies] а що ні:

  - You DON'T need to add state updating functions (as we did in the last lecture with setFormIsValid): React guarantees that those functions never change, hence you don't need to add them as dependencies (you could though)
  - You also DON'T need to add "built-in" APIs or functions like `fetch()`, `localStorage` etc (functions and features built-into the browser and hence available globally): These browser APIs / global functions are not related to the React component render cycle and they also never change
  - You also DON'T need to add variables or functions you might've defined _OUTSIDE_ of your components (e.g. if you create a new helper function in a separate file): Such functions or variables also are not created inside of a component function and hence changing them won't affect your components (components won't be re-evaluated if such variables or functions change and vice-versa)

- So long story short: You must add all "things" you use in your effect function if those "things" could change because your component (or some parent component) re-rendered. That's why variables or state defined in component functions, props or functions defined in component functions have to be added as dependencies!

- Також, коли нам потрібно передати в хук `useEffect()` в [dependencies] дані з якогось об'єкту, ми завжди передаємо лише якусь конкретну проперті, а не весь об'єкт

---

**`useReducer()`** --> `const [state, dispatchFn] = useReducer(reducerFn, initialState, ?initFn)` - хук, який виступає заміною `useState()`, якщо нам треба більше потужний `state management`. Наприклад, коли нам потрібно оновити стан, який залежить від іншого стану. Для таких випадків `useState()` не дуже підходить, так як це порушує правило функціонального оновлення стану на основі попереднього стану.

- _`state`_ - містить у собі знімок останнього стану, тобто інфо-цію про те, яким він є на момент, коли ми викликатимемо функцію для його оновлення
- _`dispatchFn`_ - функція, яка оновлює стан. Але працює вона таким чином, що вона не просто встановлює нове значення стану, а відправляє чи посилає(_`dispatch`_) якусь дію(_`action`_), яка автоматично попадатиме у _`reducerFn`_. Як правило це об'єкт з двома ключами {type`: 'someType', payload: 'someData'}`
- _`reducerFn`_ - `(prevState, action) => newState` - функція, яку Реакт автоматично викликатиме кожного разу, коли викликатиметься _`dispatchFn`_ і яка автоматично отримує актуальний на момент виклику стан(_`state`_) і дію(_`action`_), яку ми передали в _`dispatchFn`_. І повертає оновлений стан.
- optional _`initFn`_: The initializer function that specifies how the initial state is calculated. If it’s not specified, the initial state is set to _`initialState`_. Otherwise, the initial state is set to the result of calling _`initFn(initialState)`_.

## React Custom Hooks

Кастомні хуки це механізм, за допомогою якого ми можемо виокремлювати якусь логіку і перевикористовувати її в потрібних нам компонентах, як звичайні функції. Але всередині кастомних хуків ми також можемо використовувати звичайні хуки, тобто додавати якийсь стан чи виконувати якісь побічні дії. При цьому, якщо в кастомному хуці викор. хук, який додає стан, і цей кастомний хук викликається в декількох компонентах, цей стан буде для кожного компонента свій.

Назва кастомного хука повинна починатися з use --> useSomething, щоб react розумів, що це кастомний хук і застосовував відповідні правила до нього в плані використання.

## React Context API

_**React Context**_ - `component-wide, "behind the scenes" State Storage`.
Цей API вирішує проблему передачі даних між компонентами, які не мають прямого зв'язку, а піднімати (передавати) їх догори по дереву компонентів до найближчого спільного компонента незручно.
А з _React Context_ в нас є можливість цей стан зберігати і змінювати централізовано в одному місці і при потребі його використовувати в тих компонентах, де це потрібно.

Створюється окремий файл, в якому ми ініціюємо створення об'єкту контексту.

```js
const AuthContext = React.createContext({
  isLoggedIn: false, //_defaultValue_
});

export default AuthContext;
```

Code above creates a _`Context`_ object. When React renders a component that subscribes to this Context object it will read the current context value from the closest matching _`Provider`_ above it in the tree.
The _`defaultValue`_ argument is only used when a component does not have a matching _`Provider`_ above it in the tree. This default value can be helpful for testing components in isolation without wrapping them.
Note: passing `undefined` as a `Provider value` does not cause consuming components to use `defaultValue`.
Також додавати дефолтне значення для контексту є сенс, тому що тоді у VSCode буде працювати автодоповнення коли ми звертатимемось до об'єкта контексту.

Далі, для того, щоб передати або надати доступ потрібним нам компонентам до контексту, їх потрібно огорнути в цей контекст як в тег. Всі дочірні компоненти цих обгорнутих компонентів теж отримають доступ до нього.

```js
<AuthContext.Provider>
  <MainHeader />
  <Footer />
</AuthContext.Provider>
```

Далі наші компоненти мають якось підписатися на дані з контексту. Є два способи:

```js
<AuthContext.Consumer></AuthContext.Consumer> та використання хуку
useContext(AuthContext);
```

----------**Перший спосіб**--->

Дані, доступ до яких ми хочемо мати всюди, де ми використовуватимемо контекст, передаються через спец. атрибут Provider ==> `value`

```js
  <AuthContext.Provider
    value={{
      isLoggedIn: isLoggedIn, //тут ми повинні прив'язуватися до вже існуючої змінної, при змінні якої, Provider повідомить всіх своїх Consumers
    }}
  >
```

І в потрібному нам компоненті ми на ці дані підписуємось через огортання `jsx` в `<AuthContext.Consumer></AuthContext.Consumer>`
і повернення (jsx) всередині стрілкової функції

```js
<AuthContext.Consumer>
  {(ctx) => {
    //ми автоматично отримуємо об'єкт контексту як аргумент
    return (
      <ul>
        {ctx.isLoggedIn && ( //і далі беремо з нього потрібні нам дані для умовного рендерингу
          <li>
            <a href='/'>Users</a>
          </li>
        )}
      </ul>
    );
  }}
</AuthContext.Consumer>
```

Також, якщо ми викор. _`defaultValue`_, передане в `createContext()`, ми можемо не викор. `<AuthContext.Provider>`

-----------**Другий спосіб**----->

Замість використання `<AuthContext.Consumer></AuthContext.Consumer>` ми просто використовуємо хук `useContext(AuthContext)`, куди передаємо створений раніше об'єкт контексту.

```js
const ctx = useContext(AuthContext);
```

Далі ми можемо його використовувати точно так же як і в коді вище, але прибравши `AuthContext.Consumer` тег і функцію стрілку.

- Хороший приклад пояснення коли викор. контекст а коли props це кнопка розлогіну з акаунта. Так як ця кнопка завжди робитиме одну і ту ж дію, доречно викор. контекст щоб передати в неї функцію, яка обробляє цю дію, але описана в іншому місці.
  Якщо ж наша кнопка буде робити різні дії в залежності від того, які дані вона отримуватиме на вхід, доречно викор. props.
- Props for configuration, Context for state management across components or possibly across the entire app.
- Також хорошою практикою є створювати окремий компонет для контексту, в якому ми якби централізовано описуємо якусь логіку, наприклад логіку авторизації, і огортаємо цим компонентом весь застосунок. З такого компоненту нам потрібно повернути ось такий код:

```js
return (
  <AuthContext.Provider
    value={{
      //дані контексту, де ми ассайнимо дані зі стейту і обробники дій як value, а до key ми потім звертаємось в потрібних нам компонентах для доступу до цих value
      isLoggedIn: isLoggedIn,
      onLogout: logoutHandler,
      onLogin: loginHandler,
    }}
  >
    {props.children}
  </AuthContext.Provider>
);
```

І кожного разу коли дані в цьому об'єкті контексту змінююється, компоненту, які їх використовують, знамитуть про це.

- React Context is NOT optimized for high frequency changes! Це означає, що його краще не використовувати коли оновлення даних відбувається кожну секунду або ще частіше. От для авторизації чи зміни теми чи локалізації це хороший варіант.

## React forms handling

Існуючі підходи до валідації форм:

- Після відправки форми - `onSubmit()`
- Після втрити фокусу інпутом - `onBlur()`
- На кожен введений символ - `onChange()`

- Якщо нам потрібно значення інпута одразу, як тільки в нього введено якісь дані, використовуємо _`useState()`_, атрибут _`value`_ на інпуті з прив'язкою до `value` зі `state` і метод інпута _`onChange()`_, який на кожну зміну викликатиме якийсь хендлер, який ми йому передемо --> _`onChange={nameInputChangeHandler}`_. В цьому хендлері ми з об'єкта _`event`_ братимемо _`target.value`_ і передаватимемо в метод зі state.
- Якщо значення інпута нам достатньо мати на момент відправки форми, простіше використовувати хук _`useRef()`_ --> _`const nameInputRef = useRef()`_; Додаємо на інпут атрибут _`ref`_ зі значенням зі змінної `nameInputRef` --> _`ref={nameInputRef}`_. Далі на _`onSubmit()`_ форми ми зможемо отримати значення інпута через доступ до _`nameInputRef.current.value`_.
