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


## React hooks

  _Правила використання хуків_:
  - Викор. їх тільки в реакт компонентах або в кастомних хуках
  - Викликати їх потрібно лише на верхньому рівні. Тобто всередині умов чи циклів чи вкладених функцій це робити заборонено.
  
  **useState** ---> _const [value, setValue] = useState(defaultValue)_ - хук для роботи зі станом, який приймає дефолтне значення для змінної value і повертає масив з встановленим value і метод, який це value змінює. І на кожну таку зміну Реакту буде перемальовувати компонент.
  При використанні *useState()* в компоненті, який перевикористовується, стан буде незалежний для кожного виклику цього компоненту. Так само це працюватиме і для використання декількох станів в одному компоненті.
  Дефолтне значення для value буде встановлено при першій ініціалізації компонента і після його зміни, повертатиметься вже нове значення.
  У випадках, коли ми використовуємо цей хук для відслідковування і оновлення стану полів форми, можна викор. два підхода:
    ```js
    INITIATING STATE
    const [enteredTitle, setEnteredTitle] = useState(''); // підхід зі створенням стейту для кожного поля
    const [enteredAmount, setEnteredAmount] = useState('');
    const [enteredDate, setEnteredDate] = useState('');
    // const [userInput, setUserInput] = useState({ // підхід зі створенням об'єкту зі всіма полями
    //   enteredTitle: '',
    //   enteredAmount: '',
    //   enteredDate: '',
    // });

    STATE UPDATING
    setEnteredTitle(event.target.value); //оновлення стану конкретного поля через відповідний метод
      'STATE UPDATING' при використанні підходу з об_єктом зі всіма полями
      ВАРІАНТ 1
      //  setUserInput({
      //   ...userInput,
      //   enteredTitle: event.target.value,
      // });
      ВАРІАНТ 2 - більш коректний підхід з використанням попереднього стану. Так як Реакт оновлює стан не одразу, а в порядку черги, у випадку використання ВАРІАНТУ 1, якщо буде заплановано багато оновлень стану одночасно, можуть виникати ситуації, коли ми отримуватимемо як попередній, некоректний або застарілий стан. ВАРІАНТ 2 цьому запобігає.
      // setUserInput((prevState) => {
      //   return { ...prevState, enteredTitle: event.target.value };
      // });
    ```

  **useEffect**(() => {}, [dependencies]) - хук для виконання так званих _Side effects_(http запити, работа з таймера, localStorage і в той же час, наприклад, перевірка і оновлення стану валідності форми після кожного нового введеного символу це теж side effect, який спрацьовуватиме за певних обставин, в залежності від того, як його використовувати.

  For someone who really not understand the useEffect hook.
  1. useEffect hook without mentioning any dependency array like - useEffect(someCallbackFuction) runs for every render of the functional component in which its included AFTER component is rendered.
  2. useEffect hook with an empty dependency array like this - useEffect(callbackFunc, []) is executed only for the the initial render of the component. And then it will not run in the further renders of the same functional Component until page will refresh in browser.
  3. useEffect hook with some dependencies inside the dependency array like this - useEffect(callbackFunc , [dependency]) will run for the initial render as well as when the render happen due to change in dependencies mentioned in the dependency array.
  
  useEffect(() => {
    ...
    return () => {} <--- _CLEAN UP FUNCTION_. Ця фу-ція викликатиметься перед тим, як викликатиметься callback, всередині якого знаходиться ця clean up функція, але за виключенням першого рендерингу. І також перед тим, як компонент буде розмонтовано, тобто видалено з DOM.
  }, [])

  * Що передавати в [dependencies] а що ні:
    - You DON'T need to add state updating functions (as we did in the last lecture with setFormIsValid): React guarantees that those functions never change, hence you don't need to add them as dependencies (you could though)
    - You also DON'T need to add "built-in" APIs or functions like fetch(), localStorage etc (functions and features built-into the browser and hence available globally): These browser APIs / global functions are not related to the React component render cycle and they also never change
    - You also DON'T need to add variables or functions you might've defined OUTSIDE of your components (e.g. if you create a new helper function in a separate file): Such functions or variables also are not created inside of a component function and hence changing them won't affect your components (components won't be re-evaluated if such variables or functions change and vice-versa)

  * So long story short: You must add all "things" you use in your effect function if those "things" could change because your component (or some parent component) re-rendered. That's why variables or state defined in component functions, props or functions defined in component functions have to be added as dependencies!

  * Також, коли нам потрібно передати в хук useEffect() в [dependencies] дані з якогось об'єкту, ми завжди передаємо лише якусь конкретну проперті, а не весь об'єкт 

  **useReducer()** --> _const [state, dispatchFn] = useReducer(reducerFn, initialState, initFn)_ - хук, який виступає заміною useState(), якщо нам треба більше потужний state management. Наприклад, коли нам потрібно оновити стан, який залежить від іншого стану. Для таких випадків useState() не дуже підходить, так як це порушує правило функціонального оновлення стану на основі попереднього стану.
  * _state_ - містить у собі знімок останнього стану, тобто інфо-цію про те, яким він є на момент, коли ми викликатимемо функцію для його оновлення
  * _dispatchFn_ - функція, яка оновлює стан. Але працює вона таким чином, що вона не просто встановлює нове значення стану, а відправляє чи посилає(*dispatch*) якусь дію(*action*), яка автоматично попадатиме у *reducerFn*. Як правило це об'єкт з двома ключами {type: 'someType', payload: 'someData'}
  *  _reducerFn_ - (prevState, action) => newState - функція, яку Реакт автоматично викликатиме кожного разу, коли викликатиметься *dispatchFn* і яка автоматично отримує актуальний на момент виклику стан(*state*) і дію(*action*), яку ми передали в *dispatchFn*. І повертає оновлений стан.


## React Context API

  _React Context_ - component-wide, "behind the scenes" State Storage.
  Цей API вирішує проблему передачі даних між компонентами, які не мають прямого зв'язку, а піднімати (передавати) їх догори по дереву компонентів до найближчого спільного компонента незручно.
  А з _React Context_ в нас є можливість цей стан зберігати і змінювати централізовано в одному місці і при потребі його використовувати в тих компонентах, де це потрібно.

  Створюється окремий файл, в якому ми ініціюємо створення об'єкту контексту.
  ```js
  const AuthContext = React.createContext({
    isLoggedIn: false //_defaultValue_ 
  });

  export default AuthContext;
  ```
  Code above creates a _Context_ object. When React renders a component that subscribes to this Context object it will read the current context value from the closest matching _Provider_ above it in the tree.
  The _defaultValue_ argument is only used when a component does not have a matching _Provider_ above it in the tree. This default value can be helpful for testing components in isolation without wrapping them. Note: passing undefined as a Provider value does not cause consuming components to use defaultValue.

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
  ----------Перший спосіб--->

  Дані, доступ до яких ми хочемо мати всюди, де ми використовуватимемо контекст, передаються через спец. атрибут Provider ==> value
  ```js
    <AuthContext.Provider
      value={{
        isLoggedIn: isLoggedIn, //тут ми повинні прив'язуватися до вже існуючої змінної, при змінні якої, Provider повідомить всіх своїх Consumers
      }}
    >
  ```
  І в потрібному нам компоненті ми на ці дані підписуємось через огортання jsx в <AuthContext.Consumer></AuthContext.Consumer>
  і повернення (jsx) всередині стрілкової функції
  ```js
    <AuthContext.Consumer>
      {(ctx) => { //ми автоматично отримуємо об'єкт контексту як аргумент
        return (
          <ul>
            {ctx.isLoggedIn && (//і далі беремо з нього потрібні нам дані для умовного рендерингу
              <li>
                <a href="/">Users</a>
              </li>
            )}
          </ul>
        );
      }}
    </AuthContext.Consumer>
  ```
  Також, якщо ми викор. _defaultValue_, передане в createContext(), ми можемо не викор. <AuthContext.Provider>
-----------Другий спосіб----->
Замість використання <AuthContext.Consumer></AuthContext.Consumer> ми просто використовуємо хуй useContext(AuthContext), куди передаємо створений раніше об'єкт контексту.
```js
const ctx = useContext(AuthContext);
```
Далі ми можемо його використовувати точно так же як і в коді вище, але прибравши AuthContext.Consumer тег і функцію стрілку.

* Хороший приклад пояснення коли викор. контекст а коли props це кнопка розлогіну з акаунта. Так як ця кнопка завжди робитиме одну і ту ж дію, доречно викор. контекст щоб передати в неї функцію, яка обробляє цю дію, але описана в іншому місці.
* Якщо ж наша кнопка буде робити різні дії в залежності від того, які дані вона отримуватиме на вхід, доречно викор. props.