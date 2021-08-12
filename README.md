# Learn-Redux

*yarn add redux*

## Redux

JavaScript библиотека с открытым исходным кодом, предназначенная для управления состоянием приложения. Чаще всего используется в связке с React для разработки клиентской части. В основе лежит однонаправленный поток данных. Это означает, что любое действие в приложении обрабатывается по единому сценарию — в одну сторону. Синхронный по-умолчанию.

## Flux 

Архитектурный паттерн разработанный Facebook в 2014 году. Использование архитектуры flux подразумевает строгое ограничение в виде однонаправленного потока данных. Однонаправленный поток данных – это когда изменение данных в состоянии приложения может быть осуществлено только в строго определенной последовательности. Реализацию паттерна flux использует redux. 

## Basic Flow

Всё глобальное состояние приложения хранится в дереве объектов внутри store. Единственный способ изменить state, создать action и  отправить его в store с помощью dispatch. Чтобы определить, как state должен обновиться в ответ на action, нужно создать чистую функцию reducer, которая вычислит новый state основываясь на старом state и action. Если состояние изменилось, то оно направляется обратно в store, а далее во view.

![redux flow](https://github.com/Jonnynsk/Learn-Redux/blob/main/README-IMG/redux%20flow.png)

## Store  

Хранилище данных. Запускает actions по цепочке потока данных Redux с помощью метода store.dispatch(action). Может быть доступен из любой точки приложения. После запуска store, action передается всем reducers, чтобы они могли обновить состояние, если это необходимо. Если состояние было изменено, rootReducer склеивает все reducers и передает новую его версию store, после чего новое состояние может быть передано view для отображения на экране пользователя.

Store также может:

    подписываться и отписываться на каждое запущенное действие: 
    store.subscribe(listener);

    получать версию текущего состояния приложения: 
    store.getState();

    Осуществлять подмену reducer приложения: 
    store.replaceReducer(nextReducer);


Store можно создать, вызвав функцию createStore. 
```javascript
// store.js
import { createStore } from 'redux'
import { rootReducer } from './rootReducer'

export const store = createStore(rootReducer) // первый и обязательный аргумент будет reducer.
```

## View

Отображает приложение для пользователя. Должен быть синхронизирован со store, чаще всего с помощью React. Реагирует на действия пользователя и знает о самой свежей версии состояния. Чаще всего становится отправной точной потока данных flux.

## State 

Единый JavaScript объект, который хранит всю информацию о приложении. Находится в store и доступен для чтения из любой точки приложения. Изменить это состояние можно только с помощью reducer.

## Action 

Простой JavaScript объект, который описывает суть изменения в приложении. Имеет обязательное свойство type. Type – как правило является строкой и выносится в отдельную переменную (константу), для повторного использования в других частях приложения.  Записывается заглавными буквами, а название должно содержать действие.

Action также может иметь дополнительные свойства (только эти 3), такие как:

    payload – может быть любого типа данных. Несет дополнительную информацию о действии. 
    
    error – этому свойству может быть присвоено значение true, если действие представляет собой ошибку. 
    По конвенции FSA (Flux Standard Action), если error: true, то payload должен содержать объект с ошибкой.
    
    meta – может быть любого типа данных. Это свойство предназначено для любой дополнительной информации, 
    которая не является частью полезной нагрузки (payload).  

Хорошая практика: один action делает одно действие.

```javascript
const SHOW_SIDEBAR = 'SHOW_SIDEBAR'  

const showSidebar = {
	type: SHOW_SIDEBAR
}
```

## Action Creator 

Чистая функция, которая создает и возвращает action. Далее этот action попадает в dispatch. Использование action creator позволяет автоматизировать написание множества кода вручную, существенно упрощая работу. 

```javascript
const showSidebar = {
	type: 'SHOW_SIDEBAR’
}
```
```javascript
const showSidebarAC = () => {
	return showSidebar
}
```
или
```javascript
const REMOVE_USER = 'REMOVE_USER'

const removeUserAC = (userId) => {
	return {
	    type: REMOVE_USER,
	    payload: userId
    }
}
```

## Dispatch 

Единственный способ обновить state, это вызвать метод dispatch и передать в него action. Store вызовет функцию reducer который обработает action и обновит соответствующие поля state. 
store.dispatch(removeUserAC(5))

## Reducer 

Это чистая функция, которая отвечает за обновление состояния. Принимает текущий state и action, если необходимо, то обновляет и возвращает новый state. Если обновления не требуется, тогда возвращает тот же state, что и был. 

Все reducers приложения собирает Store, формируя таким образом общее изначальное состояние для приложения.

Хорошей практикой является деление reducers на более компактные. Каждый reducer отвечает за свою часть состояния и его изменения. 
Далее rootReducer склеивает все reducers и возвращает новое состояние и передает его store, а он в свою очередь уже обновляет приложение. 

Логика такова: 
	когда в reducer приходит нужный action, reducer делает копию state, обновляет эту копию новыми значениями и возвращает новый state. Если reducer не нужен данный action, то вернётся текущий state без изменений.

```javascript
// appReducer.js
const initialState = {  // объект, представляющий начальное состояние.
    initialized: false
}

const appReducer = (state = initialState, action) => { 
    switch (action.type) { // action попадает в конструкцию switch/case.
        case INITIALIZED_SUCCESS: 
            return {
                ...state, 
                initialized: true // при совпадении, initialized будет изменен на true.
                } 
        default: 
            return state // если совпадений нет, то reducer должен вернуть прежний state.
            } 
}
```

Есть несколько правил:
	
    Создавая новое состояние, reducer должен основываться только на входящие аргументы, state и action.

	Нельзя модифицировать текущий state. Вместо этого должно произойти неизменяемое обновление 
    (immutable updates), копируя текущий state, и изменяя уже эту копию. 
    
    Не должно выполняться никакой асинхронной логики, вычислять случайные 
    значения или вызывать другие побочные эффекты (side effects).

В больших приложениях будет много reducers, для реализации этой задачи Redux предоставляет доступ к функции-утилите *combineReducers*, которая позволяет объединить несколько reducers в один, каждый из которых отвечает за обработку только одного типа событий и обновления определённого поля.

```javascript
// rootReducer.js
import { combineReducers } from 'redux'
import { appReducer } from '../app/reducer'
import { usersReducer } from '../users.reducer'

export const rootReducer = combineReducers({ // склеивает все reducers.
    app: appReducer,
    users: usersReducer,
})
```

# Асинхронность в Redux

Асинхронность в Redux считается нестандартным сценарием, потому что по умолчанию Redux работает в синхронном режиме. Это означает, что запуск и обработка reducers
каждого action выполнится последовательно, предсказуемо и всегда по одному и тому же порядку.

Асинхронный режим выполнения кода в Redux-приложении можно активировать, добавив соответствующую асинхронную составляющую в виде middleware.

## Middleware 

Расширяет функционал обычного потока данных Redux, добавляя дополнительную логику между точкой после запуска action (store.dispatch(action)) и точкой перед передачей объекта action к reducer. Т.е. вклинивается в синхронный поток, перехватывет и пропускает через себя все actions проходящие по потоку, не блокируя при
этом основной поток данных Redux. Чаще всего middleware используют для логирования, краш-репортов, общения с удаленным API в асинхронном виде, и даже может заблокировать action, тогда он не попадет в reducer. Может быть любое количество middleware, они выстраиваются в виде цепочки, и передают между собой action, а после, уже отправляют его в reducer. 

![middleware в потоке](https://github.com/Jonnynsk/Learn-Redux/blob/main/README-IMG/middleware%20в%20потоке.png)

Чтобы внедрить middleware в приложение, нужно воспользоваться вспомогательной
функцией *applyMiddleware* из пакета redux. Это не обязательно, но это позволит выразить асинхронные actions в удобном виде. Асинхронный middleware, типа redux-thunk или redux-promise, оборачивает метод store.dispatch() и позволяет вызывать что-то, что не является action, например, функции или Promise.

```javascript
// store.js
import { createStore, applyMiddleware } from 'redux'
import thunk from 'redux-thunk'
import { rootReducer } from './rootReducer'

export const store = createStore(rootReducer, applyMiddleware(thunk))
```

Самые популярные middleware для включения асинхронного выполнения кода: <br />
*redux-thunk*<br />
*redux-promise*<br />
*redux-saga*

## Enhancer 

Функция высшего порядка, расширяющая обычный store дополнительным функционалом. 

В каком-то смысле концепт enhancer похож на концепт middleware, но на самом деле работает совсем по другому. 
Enhancer Redux больше похож на компонент высшего порядка в React. Enhancer — это оболочка, абстракция вокруг обычного Store делающая его более мощным. 
В то время как middleware — это не что иное, как плагин или деталь приложения, которую можно поместить «внутрь» цепочки потока данных Redux.

В качестве примера Redux Enhancer предоставляет популярный инструмент разработчика Redux-приложения — Redux DevTools.

Чтобы запустить Redux DevTools в приложении, нужно установить Redux DevTools и обновить инструкцию создания Store следующим кодом.

```javascript
// store.js
import { createStore, applyMiddleware } from 'redux'
import thunk from 'redux-thunk'
import { rootReducer } from './rootReducer'

const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__

export const store = createStore(rootReducer, composeEnhancers(applyMiddleware(thunk)))
```

В Chrome DevTools появится новая вкладка Redux, отображающая детальную информацию о всём состоянии приложения, включая саму модель состояния, все
actions, запущенные с момента загрузки приложения, и историю изменений состояния приложения на каждый запущенный action.

# React-Redux

*yarn add react-redux*

Этот пакет - специальная прослойка между react и redux. Чтобы не общаться к redux напрямую, мы будем общаться к этой прослойке. 

## Provider 

Для того, чтобы не прокидывать store в каждый компонент руками, рекомендуется обернуть всё приложение компонентой Provider и передать в качестве аргумента store. Теперь этот store будет доступен в каждой компоненте react. Т.е. provider открывает доступ к store для connect. Компонент Provider использует React context API, чтобы открыть доступ к Store всему твоему приложению.

```javascript
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App'
import { store } from './app/store'
import { Provider } from 'react-redux'

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

## Connect 

Компонент высшего порядка, который предоставляет доступ к хранилищу redux внутри компонентов react. Т.е. некое связующее звено между React и Redux. Функция берет react компонент, как аргумент и возвращает новый компонент с данными redux store, как props. Принимает два необязательных аргумента: mapStateToProps и mapDispatchToProps. Автоматически оборачивает actions в метод store.dispatch(). Поэтому UI всегда синхронизован с состоянием.

    mapStateToProps: это функция, которая принимает state в качестве параметра и возвращает объект. 
    Определяет части состояния, которые нужно привязать к компоненту.

    mapDispatchToProps: это также функция, которая принимает dispatch в качестве параметра и 
    возвращает объект с функциями dispatch. Определяет actions, которые нужно привязать.

```javascript
import { connect } from 'react-redux'

const mapStateToProps = state => {
  return {
    photos: state.photos,
    selectedPhotoIndex: state.selectedPhotoIndex,
  }
}
```
```javascript

const mapDispatchToProps = dispatch => {
  return {
    showNextPhoto: showNextPhoto
  }
}

export default connect(mapStateToProps, mapDispatchToProps)(App)
```
Мы вызываем функцию connect(), передав две функции и react компонент, теперь мы получаем доступ к функциям store state и dispatch в качестве props reaсt компоненты. Если нам не нужен state или dispatch, то в качестве аргументов можно передать null или просто пропустить этот аргумент.
```javascript
export default connect(null, mapDispatchToProps)(App)
```

# Redux Toolkit

*npx create-react-app app-name --template redux* <br>
*yarn add @reduxjs/toolkit*

Это пакет, облегчающий работу с Redux. Он был разработан для решения трех главных проблем:

	Слишком сложная настройка хранилища (store)
	Для того, чтобы заставить Redux делать что-то полезное, приходится использовать дополнительные пакеты
	Слишком много шаблонного кода (boilerplate)

Redux Toolkit предоставляет инструменты для настройки хранилища и выполнения наиболее распространенных операций, а также содержит полезные утилиты, позволяющие упростить код.

## configureStore

Обертка для createStore, упрощающая настройку хранилища с настройками по умолчанию. Позволяет автоматически комбинировать отдельные частичные reducers (slice reducers), добавлять промежуточные слои или посредников (middlewares), по умолчанию включает redux-thunk, позволяет использовать расширение Redux DevTools (инструменты разработчика Redux).
```js
// app/store.js
import { configureStore, combineReducers } from '@reduxjs/toolkit'

const rootReducer = combineReducers({
	count: countReducer
})

export const store = configureStore({
  reducer: rootReducer
})
```
Теперь, с помощью пакета react-redux, нам нужно обернуть всё приложение компонентой Provider и передать в качестве аргумента store. Теперь store будет доступен в каждой компоненте react.
```js
// index.js
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App'
import { store } from './app/store'
import { Provider } from 'react-redux'

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```
## createReducer

Позволяет использовать таблицу поиска (lookup table) операций для reducers случая (case reducers) вместо инструкций switch. В данном API используется библиотека immer, позволяющая напрямую изменять иммутабельный код, например, так: state.todos[3].completed = true.

```js
const initialState = {
	count: 0
}

export const increment = createAction("INCREMENT")
export const decrement = createAction("DECREMENT")

const default createReducer(initialState, {
	[increment]: (state) => {
		state.count = state.count + 1
	},
	[decrement]: (state) => {
		state.count = state.count - 1
	}
})
```

## createAction 

Генерирует action creator для переданного типа операции. Функция имеет переопределенный метод toString(), что позволяет использовать ее вместо константы типа.

```jsx
import { createAction } from "@reduxjs/toolkit"

export const addTask = createAction("TASK_ADD")
export const toggleTask = createAction("TASK_TOGGLE")
export const removeTask = createAction("TASK_REMOVE")
```
В качестве параметра для функции createAction нам необходимо передать текст, который будет использоваться в качестве type.

## createSlice

Принимает объект, содержащий reducer, название части состояния (state slice), начальное значение состояния, и автоматически генерирует частичный reducer с соответствующими создателями и типами операции.
```jsx
const postsSlice = createSlice({
  name: 'counter',
  initialState: {
  	count: 0,
	todo: []
  },
  reducers: {
    increment(state) {
    	state.count = state.count + 1
    },
    decrement(state) {
    	state.count = state.count - 1
    },
    addTodo(state, action) {
    	state.todo.push(action.payload)
    }
  }
})

export default postsSlice.reducer
export const {increment, decrement, addTodo} = postsSlice.action
```
## createAsyncThunk

Упрощает процесс выполнения асинхронных запросов. Принимает тип операции и функцию, возвращающую промис, и генерирует thunk, отправляющий типы операции pending/fulfilled/rejected на основе промиса.
