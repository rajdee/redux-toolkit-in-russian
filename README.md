# Redux Toolkit

[![build status](https://img.shields.io/travis/reduxjs/redux-toolkit/master.svg?style=flat-square)](https://travis-ci.org/reduxjs/redux-toolkit)
[![npm version](https://img.shields.io/npm/v/@reduxjs/toolkit.svg?style=flat-square)](https://www.npmjs.com/package/@reduxjs/toolkit)
[![npm downloads](https://img.shields.io/npm/dm/@reduxjs/toolkit.svg?style=flat-square&label=RTK+downloads)](https://www.npmjs.com/package/@reduxjs/toolkit)

**Официальный, самоуверенный набор инструментов с батарейками для эффективной разработки Redux**

(Ранее известный как "Redux Starter Kit")

## Установка

### Используя Create React App

Рекомендованный путь для старта новых приложений с React и Redux Toolkit, использовать [официальный шаблон Redux+JS](https://github.com/reduxjs/cra-template-redux) для [Create React App](https://github.com/facebook/create-react-app), который использует преимущества интеграции React Redux с компонентами React.

```sh
npx create-react-app my-app --template redux
```

### Как отдельное приложение

Redux Toolkit доступен, как NPM-пакет, для использования с бандлером или в Node приложении:

```bash
# NPM
npm install @reduxjs/toolkit

# Yarn
yarn add @reduxjs/toolkit
```

Он также доступен, в виде прекомпилированного UMD пакета, который определяет глобальную переменную  `window.RTK`
UMD пакет может использоваться напрямую через [тэг `<script>`](https://unpkg.com/@reduxjs/toolkit/dist/redux-toolkit.umd.js).

## Цель

**Redux Toolkit** предназначен для стандартного способа написания логики Redux. Первоначально он был создан, чтобы помочь решить три распространенные проблемы, связанные с Redux:

- "Конфигурирование Redux стора слишком сложное"
- "Мне нужно добавить много пакетов, чтобы Redux делал что-нибудь полезное"
- "Redux требует слишком много шаблонного кода"

Мы не можем покрыть все варианты использования, но в духе[`create-react-app`](https://github.com/facebook/create-react-app) и [`apollo-boost`](https://dev-blog.apollodata.com/zero-config-graphql-state-management-27b1f1b3c2c3), мы можем попыпаться предоставить некоторые инструменты, которые абстрагируют процесс настройки и покрывают наиболее распространенные варианты использования, а также предоставить некоторые полезные утилиты, которые позволят пользователю упростить код своего приложения.

Из-за этого размер этого пакета намеренно ограничен. Он _не_ обращается к таким концепциям, как "переиспользуемые инкапсулированные модули Redux", кэширование данных, структура папок или файлов, отношения управляющих сущностей в сторе и т. д.

## Что включено

Redux Toolkit предоставляет следующие API:

- [`configureStore()`](../api/configureStore.mdx): оборачивает createStore для передачи упрощенных параметров конфигурации и значений по умолчанию. Он может автоматически комбинировать ваши слайсы редьюсеров, добавлять любые мидлвары Redux, по умолчанию включает `redux-thunk` и позволяет использовать расширение Redux DevTools.
- [`createReducer()`](../api/createReducer.mdx): позволяет вам использовать таблицу соответствия типов экшенов с функциями редьюсера, вместо написания операторов switch. Кроме того, он автоматически использует [библиотеку `immer`](https://github.com/immerjs/immer) чтобы вы могли писать более простые иммутабельные обновления в обычном мутабельном стиле, например `state.todos[3].completed = true`.
- [`createAction()`](../api/createAction.mdx): создает генератор экшена для переданного типа. В самой функции определена функция `toString ()`, поэтому ее можно использовать вместо константы типа.
- [`createSlice()`](../api/createSlice.mdx): принимает объект с функцией редьюсера, название слайса, значение начального состояния и автоматически создает слайс-редьюсер, с соответствующими генераторами и типами экшенов.
- [`createAsyncThunk`](../api/createAsyncThunk.mdx): принимает строку типа экшена и функцию, которая возвращает промис и генерирует thunk, который диспатчит `pending/fulfilled/rejected` типы экшенов на основе этого промиса
- [`createEntityAdapter`](../api/createEntityAdapter.mdx): создает набор переиспользуемых редьюсеров и селекторов, для управления нормализованными данными в сторе
- [Утилита`createSelector`](../api/createSelector.mdx) из библиотеки [Reselect](https://github.com/reduxjs/reselect) , реэкспортируемая для удобства использования.

## Documentation

The Redux Toolkit docs are available at **https://redux-toolkit.js.org**.