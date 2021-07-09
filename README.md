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

## Reducer 

Это чистая функция, которая отвечает за обновление состояния. Принимает текущий state и action, если необходимо, то обновляет и возвращает новый state. Если обновления не требуется, тогда возвращает тот же state, что и был. 

Все reducers приложения собирает Store, формируя таким образом общее изначальное состояние для приложения.

Хорошей практикой является деление reducers на более компактные. Каждый reducer отвечает за свою часть состояния и его изменения. Далее rootReducer склеивает все reducers и возвращает новое состояние и передает его store. А store уже обновляет приложение. 

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

В больших приложениях будет много reducers, для реализации этой задачи Redux предоставляет доступ к функции-утилите combineReducers.

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