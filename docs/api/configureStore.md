---
id: configureStore
title: configureStore
sidebar_label: configureStore
hide_title: true
---

# `configureStore`

Дружественная абстракция над стандартной функцией Redux `createStore`, которая добавляет полезные значения по умолчанию
в настройку стора для удобства разработки.

## Параметры

`configureStore` принимает едиственный параметр с объектом, со следующими свойствами:

```ts
type ConfigureEnhancersCallback = (
  defaultEnhancers: StoreEnhancer[]
) => StoreEnhancer[]

interface ConfigureStoreOptions<
  S = any,
  A extends Action = AnyAction,
  M extends Middlewares<S> = Middlewares<S>
> {
  /**
   * Единственная функция редъюсер, которая будет использована в качестве
   * корневого редьюсера или объект слайс-редьюсеров, которые будут переданы
   * в `combineReducers()`.
   */
  reducer: Reducer<S, A> | ReducersMapObject<S, A>

  /**
   * Массив с Redux мидлварами. Если не передано, то по умолчанию будет
   * использован набор мидлвар, возвращаемых `getDefaultMiddleware()`.
   */
  middleware?: ((getDefaultMiddleware: CurriedGetDefaultMiddleware<S>) => M) | M

  /**
   * Включать ли интеграцию Redux DevTools. По умолчанию `true`.
   *
   * Дополнительную настройку можно выполнить, передав параметры Redux DevTools.
   */
  devTools?: boolean | DevToolsOptions

  /**
   * Начальное состояние, аналогично Redux createStore.
   * При желании вы можете указать его для гидратации состояния
   * с сервера в универсальных приложениях или для восстановления
   * ранее сериализованной пользовательской сессии
   * Если вы используете `combReducers ()` для создания корневого редьюсера
   * (прямо или косвенно, путем передачи объекта как `reducer`),
   * это должен быть объект той же формы, что и объект редьюсера
   */
  preloadedState?: DeepPartial<S extends any ? S : S>

  /**
   * The store enhancers to apply. See Redux's `createStore()`.
   * All enhancers will be included before the DevTools Extension enhancer.
   * If you need to customize the order of enhancers, supply a callback
   * function that will receive the original array (ie, `[applyMiddleware]`),
   * and should return a new array (such as `[applyMiddleware, offline]`).
   * If you only need to add middleware, you can use the `middleware` parameter instead.
   */
  enhancers?: StoreEnhancer[] | ConfigureEnhancersCallback
}

function configureStore<S = any, A extends Action = AnyAction>(
  options: ConfigureStoreOptions<S, A>
): EnhancedStore<S, A>
```

### `reducer`

Если это единственная функция, то она будет напрямую использована, как корневой редьюсер для стора.

Если это объект со слайс-редьюсерами, типа `{users : usersReducer, posts : postsReducer}`,
`configureStore` автоматически создаст корневой редъюсер, передавая этот объект в 
[Redux `combineReducers` утилиту](https://redux.js.org/api/combinereducers).

### `middleware`

An optional array of Redux middleware functions

If this option is provided, it should contain all the middleware functions you
want added to the store. `configureStore` will automatically pass those to `applyMiddleware`.

If not provided, `configureStore` will call `getDefaultMiddleware` and use the
array of middleware functions it returns.

Alternately, you may pass a callback function that will receive `getDefaultMiddleware` as its argument,
and should return a middleware array. This lets you skip importing `getDefaultMiddleware` separately. If using TypeScript, prefer using this syntax, as we provide a more strongly-typed version of `getDefaultMiddleware` that will correctly
retain the types of the provided middleware when constructing the store.

For more details on how the `middleware` parameter works and the list of middleware that are added by default, see the
[`getDefaultMiddleware` docs page](./getDefaultMiddleware.mdx).

### `devTools`

If this is a boolean, it will be used to indicate whether `configureStore` should automatically enable support for [the Redux DevTools browser extension](https://github.com/zalmoxisus/redux-devtools-extension).

If it is an object, then the DevTools Extension will be enabled, and the options object will be passed to `composeWithDevtools()`. See
the DevTools Extension docs for [`EnhancerOptions`](https://github.com/zalmoxisus/redux-devtools-extension/blob/master/docs/API/Arguments.md#windowdevtoolsextensionconfig) for
a list of the specific options that are available.

Defaults to `true`.

The Redux DevTools Extension recently added [support for showing action stack traces](https://github.com/zalmoxisus/redux-devtools-extension/blob/d4ef75691ad294646f74bca38b973b19850a37cf/docs/Features/Trace.md) that show exactly where each action was dispatched. Capturing the traces can add a bit of overhead, so the DevTools Extension allows users to configure whether action stack traces are captured.

If the DevTools are enabled by passing `true` or an object, then `configureStore` will default to enabling capturing action stack traces in development mode only.

### `preloadedState`

An optional initial state value to be passed to the Redux `createStore` function.

### `enhancers`

An optional array of Redux store enhancers, or a callback function to customize the array of enhancers.

If defined as an array, these will be passed to [the Redux `compose` function](https://redux.js.org/api/compose), and the combined enhancer will be passed to `createStore`.

This should _not_ include `applyMiddleware()` or the Redux DevTools Extension `composeWithDevTools`, as those are already handled by `configureStore`.

Example: `enhancers: [offline]` will result in a final setup of `[applyMiddleware, offline, devToolsExtension]`.

If defined as a callback function, it will be called with the existing array of enhancers _without_ the DevTools Extension (currently `[applyMiddleware]`),
and should return a new array of enhancers. This is primarily useful for cases where a store enhancer needs to be added
in front of `applyMiddleware`, such as `redux-first-router` or `redux-offline`.

Example: `enhancers: (defaultEnhancers) => [offline, ...defaultEnhancers]` will result in a final setup
of `[offline, applyMiddleware, devToolsExtension]`.

## Usage

### Basic Example

```ts
// file: reducers.ts noEmit
import { Reducer } from '@reduxjs/toolkit'
declare const rootReducer: Reducer<{}>
export default rootReducer

// file: store.ts
import { configureStore } from '@reduxjs/toolkit'

import rootReducer from './reducers'

const store = configureStore({ reducer: rootReducer })
// The store now has redux-thunk added and the Redux DevTools Extension is turned on
```

### Full Example

```ts
// file: todos/todosReducer.ts noEmit
import { Reducer } from '@reduxjs/toolkit'
declare const reducer: Reducer<{}>
export default reducer

// file: visibility/visibilityReducer.ts noEmit
import { Reducer } from '@reduxjs/toolkit'
declare const reducer: Reducer<{}>
export default reducer

// file: store.ts
import { configureStore } from '@reduxjs/toolkit'

// We'll use redux-logger just as an example of adding another middleware
import logger from 'redux-logger'

// And use redux-batch as an example of adding enhancers
import { reduxBatch } from '@manaflair/redux-batch'

import todosReducer from './todos/todosReducer'
import visibilityReducer from './visibility/visibilityReducer'

const reducer = {
  todos: todosReducer,
  visibility: visibilityReducer
}

const preloadedState = {
  todos: [
    {
      text: 'Eat food',
      completed: true
    },
    {
      text: 'Exercise',
      completed: false
    }
  ],
  visibilityFilter: 'SHOW_COMPLETED'
}

const store = configureStore({
  reducer,
  middleware: getDefaultMiddleware => getDefaultMiddleware().concat(logger),
  devTools: process.env.NODE_ENV !== 'production',
  preloadedState,
  enhancers: [reduxBatch]
})

// The store has been created with these options:
// - The slice reducers were automatically passed to combineReducers()
// - redux-thunk and redux-logger were added as middleware
// - The Redux DevTools Extension is disabled for production
// - The middleware, batch, and devtools enhancers were composed together
```
