# Визначення стану застосунку можна розбити на 3 різних типи стану

- **`Local state`**
  - Належить окремому компоненту. Should be managed component-internal with _`useState()`_/ _`useReducer()`_
- **`Cross-component state`**
  - Стан, який впливає на декілька компонентів. Requires 'prop chains'/'prop drilling'
- **`App-Wide state`**
  - Стан, який стосується всього застосунку/всіх або майже всіх компонентів. Requires `prop chains`/`prop drilling` OR `React Context` or `Redux`

За рахунок того, що компоненти встановлюють підписку на центральне сховище, реалізація якого надається _`Redux`_ бібліотекою, вони знатимуть, коли дані в ньому змінюються і реагують на це, оновлюючи UI.

Також важливо розуміти, шо компоненти напряму не мають можливості впливати на дані в сховищі. Це відбувається за рахунок _`reducer function`_. Ця функція загалом є загальною концепцією, за якою такі функції отримують якісь дані на вхід і трансформують їх, як правило до єдиного значення.

> Reducers must be pure, side-effect free, synchronous functions.

---

## Thunk

- Функція, яка повертає _`callback`_, який в свою чергу приймає `dispatch` і далі виконує якісь дії з `redux store`, називається **`Thunk`**. Additionally it accepts also `getState()` method. Thunk functions are not directly called by application code. Instead they are passed to _`store.dispatch()`_.

- Thunk відкладає диспатчинг екшена до моменту, коли цей action буде створено. Тому їх ще прийнято називати _`action creator`_. Action creator не повертає сам action, а іншу функцію, яка вже в свою чергу повертає action.

Приклад:

```js
const sendData = (data) => async (dispatch) => {
  dispatch(reducer_name);
  //or we can make some rest call to the backend
};
```

_`Thunk`_ використовують у випадках, коли потрібно виконати якісь асинхронні дії зі _`state`_ в нашому _`slice`_. Наприклад зробити rest call на backend, де в свою чергу наприклад буде діставатися чи зберігатися інформація в базу даних.
