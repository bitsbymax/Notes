## Building Blocks of a Web Application
There are a few things you need to consider when building modern applications. Such as:

* User Interface - how users will consume and interact with your application.
* Routing - how users navigate between different parts of your application.
* Data Fetching - where your data lives and how to get it.
* Rendering - when and where you render static or dynamic content.
* Integrations - what third-party services you use (CMS, auth, payments, etc) and how you connect to them.
* Infrastructure - where you deploy, store, and run your application code (Serverless, CDN, Edge, etc).
* Performance - how to optimize your application for end-users.
* Scalability - how your application adapts as your team, data, and traffic grow.
* Developer Experience - your team’s experience building and maintaining your application.

JavaScript frameworks were created to make this kind of work a lot easier — they exist to provide a better developer experience. They don't bring brand-new powers to JavaScript; they give you easier access to JavaScript's powers so you can build for today's web.

## Steps to be done to move an application from development to production. 
 * *compile*
    Compiling refers to the process of taking code in one language and outputting it in another language or another version of that language
 * *minify*
    Minification is the process of removing unnecessary code formatting and comments without changing the code’s functionality. The goal is to improve the application’s performance by decreasing file sizes
 * *bundle*
    Bundling is the process of resolving the web of dependencies and merging (or ‘packaging’) the files (or modules) into optimized bundles for the browser, with the goal of reducing the number of requests for files when a user visits a web page
 * *code split*
    Developers usually split their applications into multiple pages that can be accessed from different URLs. Each of these pages becomes a unique entry point into the application.
    Code-splitting is the process of splitting the application’s bundle into smaller chunks required by each entry point. The goal is to improve the application's initial load time by only loading the code required to run that page.
  
**Build time** (or build step) is the name given to a series of steps that prepare your application code for production.
**Runtime** (or request time) refers to the period of time when your application runs in response to a user’s request, after your application has been built and deployed.

_tree-shaking_ (removal of any code that isn't actually used in the app during the build process)

* npx - the npm tool that allow you to download npm package and execute it
* Хуки не можна використовувати в умовах чи циклах
* Чому не можна мутувати state? Тому що в процесі діффінгу, Реакт не заходить в об'єкти і не порівнює їх властивості, він порівнює лише посилання на ці об'єкти. І якщо воно не змінилось, то Реакт вважатиме, що state не змінився
* Simpler way to pass single component:
  ```javascript
    import { FC, PropsWithChildren } from 'react';
    type Props = PropsWithChildren<{}>;

    export const AppH1: FC<Props> = (props) => {
      return <h1>{props.children}</h1>
    };

    const App = () => <AppH1><TranslatedText text='hello' /></AppH1> 
  ```
  Таким чином ми передаємо в компонент AppH1 інший компонент TranslatedText, до якого буде доступ через props.children. Це називається *Components Composition*

* Component Lifecycle
  # Class based                          Functional
  Initialization                         -
  render()                               function()
  ComponentDidMount                      useEffect(()=>{},[])
  ShouldComponentUpdate                  аналогом є служб. мет. useMemo()
  ComponentDidUpdate                     useEffect(()=>{})
  ComponentDidCatch                      -
  ComponentWillUnmount                   useEffect(()=>{return ()=>{}},[])

* 60fps досягається, якщо час, який потрібен на рендеринг не перевищує 16.6ms
* Three pillars of React:
  View is derivative of your data. To change the VIEW - change the DATA
  Data is the single source of truth. Separating VIEW and DATA is super important!
  Declarative over Imperative. You write what you want to get, but not a way how to get it
  Based on components

* При використанні *useState()* в компоненті, який перевикористовується, стан буде незалежний для кожного виклику цього компоненту. Так само це працюватиме і для використання декількох станів в одному компоненті.

## Lifting state up pattern
  Як передавати дані з дочірнього компонента в батьківський?
  Є компонент А і дочірній В. В компоненті В є дані, які потрібні нам в компоненті А, наприклад inputData. Для цього ми в компоненті А створюємо handlerFunction, яка як аргумент прийматиме дані, які ми передаватимемо з компонента В. Далі в компонент В ми передаватимемо props onSomething={handlerFunction}. І вже в компоненті B ми викликатимемо цю функцію, яку ми передали через props і передаватимемо туди наші дані з компонета В -> props.onSomething(inputData); 
  
  Також за допомогою цього паттерна ми можемо ділитися даними між компонентами, які мають спільний батьківський компонент. Тобто якщо є спільний батьківський компонент А і два дочірніх В і С, і дані з С нам потрібні в Б, ми ці дані "піднімаємо" з С в А і далі передаємо в В.

* Компоненти в React діляться на Stateful components і Presentational components, Smart or Dumb. Також компоненти можуть буть Controlled і Uncontrolled (часто таку характеристику можна побачити і для форм). Форма, чи інпут, для контролю якого викор. *Two-way binding* через прив'язку значення input'a до змінної з useState() до атрибуту *value* вважається контрольованою. Інпут, значення якого отримується через _ref_ вважається неконтрольованим, так як React напряму ніяк не контролює і не модифікує значення цього input'a, а лише читає.

* Компонент обгортка (_Wrapper_)- дає можливість замінити необов'язковий <div> у випадках, коли цей <div> нам потрібен лише для того, щоб відмалювати більше одного кореневого елемента.
  ```js
  const Wrapper = props => props.children;
  ``` 
  Також можна використати _Fragment_ <></>

*React Portals* - дає зручний спосіб рендерингу компонентів поза межами їх батьківського компонента, хоча при цьому в структурі компонентів, визначених в React нічого не міняється. Розташування розмітки, яку цей компонент повертає, зміниться вже в реальній DOM.
```js
ReactDOM.createPortal(child, container)
```
Цей інструмент зазвичай викор. для Modal dialog boxes, Tooltips, Hovercards, Loaders
## Re-render in React
  There are four reasons why a component would re-render itself: state changes, parent (or children) re-renders, context changes, and hooks changes.
  It always goes “down” the tree: the re-render of a child doesn’t trigger the re-render of a parent.