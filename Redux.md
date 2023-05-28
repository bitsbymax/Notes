# Визначення стану застосунку можна розбити на 3 різних типи стану

- Local state
    Належить окремому компоненту. Should be managed component-internal with useState()/ useReducer()
- Cross-component state
    Стан, який впливає на декілька компонентів. Requires 'prop chains'/'prop drilling'
- App-Wide state
    Стан, який стосується всього застосунку/всіх або майже всіх компонентів.
    Requires 'prop chains'/'prop drilling' OR React Context or Redux

За рахунок того, що компоненти встановлюють підписку на центральне сховище, реалізація якого надається *Redux* бібліотекою, вони знатимуть, коли дані в ньому змінюються і реагують на це, оновлюючи UI.
Також важливо розуміти, шо компоненти напряму не мають можливості впливати на дані в сховищі. Це відбувається за рахунок *reducer function*. Ця функція загалом є загальною концепцією, за якою такі функції отримують якісь дані на вхід і трансформують їх певним чином, наприклад отримують масив чисел і повертають суму цих чисел.
! Reducers must be pure, side-effect free, synchronous functions.

Функція, яка повертає callback, який в свою чергу приймає Dispatch і далі виконує якісь дії з redux store, називається Thunk. Additionaly it accepts also getState() method. Thunk functions are not directly called by application code. Instead they are passed to store.dispatch()

Thunk - функція, яка відкладає діспатчінг екшина до момента, коли цей action буде створено. Тому їх ще прийнято називати Action creator.
Action creator не повертає сам Action, а іншу функцію, яка вже в свою чергу повертає Action.
Приклад:

```js
cosnt sendData = (data) => async (dispatch) => {
  dispatch(reducer_name);
  //or we can make some rest call to the backend
};
```

Thunk використовують у випадках, коли потрібно виконати якісь асинхронні дії зі State в нашому Slice. Наприклад зробити rest call на backend, де в свою чергу наприклад буде діставатися чи зберігатися інформація в базу даних.
