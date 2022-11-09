JavaScript frameworks were created to make this kind of work a lot easier — they exist to provide a better developer experience. They don't bring brand-new powers to JavaScript; they give you easier access to JavaScript's powers so you can build for today's web.

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
  Таким чином ми передаємо в компонент AppH1 інший компонент TranslatedText, до якого буде доступ через props.children
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