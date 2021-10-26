# Быстрый старт c Redux Toolkit

## Цель

**Redux Toolkit** предназначен для стандартного способа написания логики Redux. Первоначально он был создан, чтобы помочь решить три распространенные проблемы, связанные с Redux:

- "Конфигурирование Redux стора слишком сложное"
- "Мне нужно добавить много пакетов, чтобы Redux делал что-нибудь полезное"
- "Redux требует слишком много шаблонного кода"

Мы не можем покрыть все варианты использования, но в духе[`create-react-app`](https://github.com/facebook/create-react-app) и [`apollo-boost`](https://www.apollographql.com/blog/announcement/frontend/zero-config-graphql-state-management/), мы можем попыпаться предоставить некоторые инструменты, которые абстрагируются от процесса установки и покрывают наиболее распространенные варианты использования, а также предоставить некоторые полезные утилиты, которые позволят пользователю упростить код своего приложения.

Redux Toolkit also includes a powerful data fetching and caching capability that we've dubbed ["RTK Query"](#rtk-query). It's included in the package as a separate set of entry points. It's optional, but can eliminate the need to hand-write data fetching logic yourself.

Redux Toolkit также включает мощную возможность запроса и кэширования данных, которую мы назвали ["RTK Query"](#rtk-query). Он входит в комплект как отдельный набор точек входа. Это необязательно, но может избавить от необходимости вручную писать логику запроса данных.

**Эти инструменты должны быть полезны всем пользователям Redux**. Независимо от того, являетесь ли вы новым пользователем Redux, настраиваете свой первый проект или опытный пользователь, который хочет упростить существующее приложение, **Redux Toolkit** может помочь вам сделать ваш Redux код лучше.

## Что включено

Redux Toolkit предоставляет следующие API:

- [`configureStore()`](../api/configureStore.mdx): оборачивает createStore для передачи упрощенных параметров конфигурации и значений по умолчанию. Он может автоматически комбинировать ваши слайсы редьюсеров, добавлять любые мидлвары Redux, по умолчанию включает `redux-thunk` и позволяет использовать расширение Redux DevTools.
- [`createReducer()`](../api/createReducer.mdx): позволяет вам использовать таблицу соответствия типов экшенов с функциями редьюсера, вместо написания операторов switch. Кроме того, он автоматически использует [библиотеку `immer`](https://github.com/immerjs/immer) чтобы вы могли писать более простые иммутабельные обновления в обычном мутабельном стиле, например `state.todos[3].completed = true`.
- [`createAction()`](../api/createAction.mdx): создает генератор экшена для переданного типа. В самой функции определена функция `toString ()`, поэтому ее можно использовать вместо константы типа.
- [`createSlice()`](../api/createSlice.mdx): принимает объект с функцией редьюсера, название слайса, значение начального состояния и автоматически создает слайс-редьюсер, с соответствующими генераторами и типами экшенов.
- [`createAsyncThunk`](../api/createAsyncThunk.mdx): принимает строку типа экшена и функцию, которая возвращает промис и генерирует thunk, который диспатчит `pending/fulfilled/rejected` типы экшенов на основе этого промиса
- [`createEntityAdapter`](../api/createEntityAdapter.mdx): создает набор переиспользуемых редьюсеров и селекторов, для управления нормализованными данными в сторе
- [Утилита`createSelector`](../api/createSelector.mdx) из библиотеки [Reselect](https://github.com/reduxjs/reselect) , реэкспортируемая для удобства использования.

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

## Помощь и обсуждение

**[Канал #redux](https://discord.gg/reactiflux)** в **[Reactiflux Discord сообществе](http://www.reactiflux.com)** - наш официальный ресурс для всех вопросов, связанных с изучением и использованием Redux. Reactiflux - отличное место, где можно пообщаться, задать вопросы и поучиться - приходите к нам!

Вы также можно задать вопрос на [Stack Overflow](https://stackoverflow.com) используя **[тэг #redux](https://stackoverflow.com/questions/tagged/redux)**.

