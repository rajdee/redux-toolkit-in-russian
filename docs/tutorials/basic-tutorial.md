---
id: basic-tutorial
title: Basic Tutorial
sidebar_label: Basic Tutorial
hide_title: true
---

# Базовое руководство: Представляем Redux Toolkit

Добро пожаловать в Redux Toolkit! Это руководство покажет вам основные функции, которые включены в Redux Toolkit (также известный как "RTK", для краткости).

В этом руководстве предполагается, что вы уже знакомы с концепциями основной библиотеки [Redux](https://redux.js.org), а также с тем, как использовать ее с [React](https://reactjs.org). Если это не так, пожалуйста, сначала прочтите [документацию Redux](https://redux.js.org) и [React-Redux](https://react-redux.js.org), поскольку объяснения здесь сфокусированы на том, чем использование RTK отличается от "типичного" кода Redux.

## Введение: пишем счетчик

Мы начнем с рассмотрения самого маленького примера Redux: простого счетчика.

### Пример Redux "Counter-Vanilla"

Документация Redux содержит [пример "counter-vanilla"](https://redux.js.org/introduction/examples#counter-vanilla), который демонстрирует, как создать простой Redux стор с редьюсером, который хранит числовое значение и реагирует на типы действий "INCREMENT" и "DECREMENT". Вы можете увидеть [полный код в CodeSandbox](https://codesandbox.io/s/github/reduxjs/redux/tree/master/examples/counter-vanilla), но вот важный раздел:

```js
function counter(state, action) {
  if (typeof state === 'undefined') {
    return 0
  }

  switch (action.type) {
    case 'INCREMENT':
      return state + 1
    case 'DECREMENT':
      return state - 1
    default:
      return state
  }
}

var store = Redux.createStore(counter)

document.getElementById('increment').addEventListener('click', function() {
  store.dispatch({ type: 'INCREMENT' })
})
```

В этом разделе создается функция-редьюсер, называемая `counter`, проверяется, что она использует  для состояния по умолчанию `0`, прослушиваются типы экшенов `"INCREMENT"`и `"DECREMENT"` и диспатчится экшен `"INCREMENT"`, когда кнопка нажата.

### Более типичный пример счетчика

Хотя этот пример прост, он также и несколько нереалистичен. Большинство приложений Redux написано с использованием синтаксиса ES6, включая аргументы по умолчанию для параметров функции, которые не определены. Также распространена практика написания [функций для создания экшенов](https://redux.js.org/basics/actions#action-creators), вместо использования объектов непосредственно в коде и использования констант для типов экшенов, вместо написания простых строк каждый раз.

Давайте перепишем приведенный выше пример, используя эти подходы, чтобы увидеть, как он будет выглядеть:

```js
const INCREMENT = 'INCREMENT'
const DECREMENT = 'DECREMENT'

function increment() {
  return { type: INCREMENT }
}

function decrement() {
  return { type: DECREMENT }
}

function counter(state = 0, action) {
  switch (action.type) {
    case INCREMENT:
      return state + 1
    case DECREMENT:
      return state - 1
    default:
      return state
  }
}

const store = Redux.createStore(counter)

document.getElementById('increment').addEventListener('click', () => {
  store.dispatch(increment())
})
```

Поскольку пример небольшой, это не сильно влияет на внешний вид. Что касается размера, мы сократили пару строк, используя аргумент по умолчанию, но добавление этих генераторов экшенов увеличило размер кода. И здесь есть некоторое дублирование. Написание `const INCREMENT = 'INCREMENT`' выглядит довольно глупым :) Особенно, когда оно используется только в двух местах - в генераторе экшена и редьюсере.

Кроме того, многих людей беспокоят операторы switch. Было бы неплохо, если бы мы могли заменить его какой-нибудь таблицей поиска.

### Представляем: `configureStore`

Redux Toolkit включает несколько функций, которые помогают упростить ваш код Redux. Первая функция, которую мы рассмотрим, это [`configureStore`](../api/configureStore.mdx).

Обычно вы создаете Redux стор, вызывая `createStore()` и передавая вашу корневую функцию-редьюсер. Redux Toolkit имеет функцию `configureStore()`, которая обертывает `createStore()`, чтобы делать то же самое, но также настраивает для вас некоторые полезные инструменты разработки как часть процесса создания стора.

Мы можем легко заменить существующий вызов `createStore` на `configureStore`. `configureStore` принимает один объект с именованными полями, вместо нескольких аргументов функции, поэтому нам нужно передать нашу функцию-редьюсер, как поле с именем` reducer`:

```js
// Было:
const store = createStore(counter)

// Стало:
const store = configureStore({
  reducer: counter
})
```

Вроде бы ничего не изменилось, но, под капотом, стор был настроен на использование [Redux DevTools Extension](https://github.com/zalmoxisus/redux-devtools-extension) для просмотра истории отправленных экшенов и того, как изменилось состояние стора и в него были включены [некоторые Redux мидлвары по умолчанию](../api  getDefaultMiddleware.mdx). Мы рассмотрим их более подробно в следующем уроке.

### Представляем: `createAction`

Далее, давайте посмотрим на [`createAction`](../api/createAction.mdx).

`createAction` принимает  тип экшена в качестве аргумента и возвращает генератор экшена, который использует этот строковый тип. (Да, согласны, имя немного неверно - мы создаем «функцию генератора экшена», а не "объект экшена", но оно короче и легче запоминается, чем `createActionCreator`.) Итак, эти два примера эквивалентны :

```js
// Оригинальный подход: написать тип экшена и генератор экшена вручную
const INCREMENT = 'INCREMENT'

function incrementOriginal() {
  return { type: INCREMENT }
}

console.log(incrementOriginal())
// {type: "INCREMENT"}

// Или, используя `createAction`
const incrementNew = createAction('INCREMENT')

console.log(incrementNew())
// {type: "INCREMENT"}
```

Но что, если нам нужно ссылаться на строку типа экшена в редьюсере? С `createAction` это можно сделать двумя способами. Во-первых, метод генератора экшена `toString ()` был переопределен и возвращает строку типа экшена. Во-вторых, строка типа также доступна, как поле `.type` функции:

```js
const increment = createAction('INCREMENT')

console.log(increment.toString())
// "INCREMENT"

console.log(increment.type)
// "INCREMENT"
```

Мы можем использовать `createAction`, чтобы упростить предыдущий пример счетчика.

```js
const increment = createAction('INCREMENT')
const decrement = createAction('DECREMENT')

function counter(state = 0, action) {
  switch (action.type) {
    case increment.type:
      return state + 1
    case decrement.type:
      return state - 1
    default:
      return state
  }
}

const store = configureStore({
  reducer: counter
})

document.getElementById('increment').addEventListener('click', () => {
  store.dispatch(increment())
})
```

Это снова сэкономило нам несколько строк, и, по крайней мере, мы не повторяем слово `INCREMENT` везде.

### Представляем: `createReducer`

Теперь давайте посмотрим на функцию редьюсера. Хотя вы можете использовать любую условную логику, какую захотите, в редьюсере Redux, включая операторы  `if` и циклы, наиболее распространенным подходом, является проверка поля`action.type` и выполнение определенной логики для каждого типа экшена. редьюсер также предоставит значение начального состояния и вернет существующее состояние, если экшен его не интересует

Redux Toolkit предоставляет  [функцию`createReducer`](../ api / createReducer.mdx), которая позволяет вам писать редьюсеры, используя "таблицу поиска", где каждый ключ в объекте представляет собой строку типа экшена Redux, а значения функции редьюсера. Мы можем использовать ее для прямой замены существующего определения функции `counter`. Поскольку нам нужно использовать строки типа экшена в качестве ключей, мы можем использовать [синтаксис "вычисляемого свойства" объекта ES6](http://javascript.info/object#computed-properties) для создания ключей из строковых переменных типа.

```js
const increment = createAction('INCREMENT')
const decrement = createAction('DECREMENT')

const counter = createReducer(0, {
  [increment.type]: state => state + 1,
  [decrement.type]: state => state - 1
})
```

Или, поскольку синтаксис вычисленных свойств будет вызывать `toString()` для любой переменной внутри, мы можем просто использовать функции генератора экшена напрямую без поля `.type`:

```js
const counter = createReducer(0, {
  [increment]: state => state + 1,
  [decrement]: state => state - 1
})
```

Чтобы увидеть полный код на данный момент, см. [этот CodeSandbox, показывающий использование `createAction` и` createReducer`](https://codesandbox.io/s/counter-vanilla-redux-toolkit-sjouq).

### Представляем: `createSlice`

Давайте рассмотрим, как выглядит пример счетчика на этом этапе:

```js
const increment = createAction('INCREMENT')
const decrement = createAction('DECREMENT')

const counter = createReducer(0, {
  [increment]: state => state + 1,
  [decrement]: state => state - 1
})

const store = configureStore({
  reducer: counter
})

document.getElementById('increment').addEventListener('click', () => {
  store.dispatch(increment())
})
```

Это неплохо, но мы можем внести в это еще одно серьезное изменение. Зачем нам вообще нужно писать генераторы экшенов отдельно или записывать эти типов экшенов? Действительно важная часть здесь - это функции редьюсера.

Именно здесь на помощь приходит  [функция `createSlice`](../ api / createSlice.mdx). Она позволяет нам предоставить объект c  функциями редьюсера и автоматически генерирует строки типов экшенов и функции генераторов экшенов на основе названия перечисленных нами редьюсеров.

`createSlice` возвращает объект "slice", который содержит сгенерированную функцию редьюсерв в виде поля с именем "reducer" и сгенерированные генераторы экшенов, внутри объекта с именем "actions".

Вот как будет выглядеть наш пример счетчика, с использованием вместо этого `createSlice`:

```js
const counterSlice = createSlice({
  name: 'counter',
  initialState: 0,
  reducers: {
    increment: state => state + 1,
    decrement: state => state - 1
  }
})

const store = configureStore({
  reducer: counterSlice.reducer
})

document.getElementById('increment').addEventListener('click', () => {
  store.dispatch(counterSlice.actions.increment())
})
```

В большинстве случаев вы, вероятно, захотите использовать синтаксис деструктуризации ES6, чтобы вытащить функции генератора экшена в качестве переменных, а также, возможно, редьюсер:

```js
const { actions, reducer } = counterSlice
const { increment, decrement } = actions
```

## Резюме

Напомним, что делают функции:

- `configureStore`: создает экземпляр стора Redux, как исходный` createStore` из Redux, но принимает именованный объект параметров и автоматически устанавливает расширение Redux DevTools.
- `createAction`: принимает строку типа экшена и возвращает функцию генератора экшена, которая использует этот тип
- `createReducer`: принимает значение начального состояния и таблицу соответствия типов экшенов и функций редьюсера и создает редьюсер, который обрабатывает все эти типы экшенов.
- `createSlice`: принимает начальное состояние и таблицу поиска с именами и функциями редьюсера и автоматически генерирует функции генератора экшена, строки типа экшена и функцию редьюсера.

Обратите внимание, что ничего из этого не изменило того, как работает Redux. Мы все еще создаем стор Redux, отправляем объекты экшенов, описывающие "что произошло" и возвращаем обновленное состояние с помощью функции редьюсера. Также обратите внимание, что функции Redux Toolkit могут использоваться независимо от того, какой подход использовался для создания пользовательского интерфейса, поскольку они просто обрабатывают часть кода, содержащую «простой стор Redux». В нашем примере мы использовали стор с "vanilla JS" UI, но мы могли бы использовать этот же стор с React, Angular, Vue или любым другим уровнем пользовательского интерфейса.

Наконец, если вы внимательно посмотрите на пример, вы увидите, что есть одно место, где мы написали некоторую асинхронную логику - кнопка 
"increment async":

```js
document.getElementById('incrementAsync').addEventListener('click', function() {
  setTimeout(function() {
    store.dispatch(increment())
  }, 1000)
})
```

Вы можете видеть, что мы сохраняем асинхронную обработку отдельно от логики редьюсера и отправляем экшен, когда необходимо обновить стор. Redux Toolkit ничего в этом не меняет.

Вот полный пример в песочнице:

<iframe src="https://codesandbox.io/embed/counter-vanilla-createslice-redux-toolkit-6gkxx?fontsize=14&hidenavigation=1&theme=dark&view=editor"
     style={{ width: '100%', height: '500px', border: 0, borderRadius: '4px', overflow: 'hidden' }} 
     title="counter-vanilla createSlice - Redux Toolkit"
     allow="geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb"
     sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"
></iframe>
Теперь, когда вы знаете основы каждой функции, следующий шаг - попытаться использовать их в _более_ крупном примере, чтобы увидеть, как они работают. Мы рассмотрим это в [руководстве для среднего уровня](./intermediate-tutorial.md).