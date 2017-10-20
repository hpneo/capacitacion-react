# Redux

Al desarrollar aplicaciones más grandes y complejas con React, surge el problema de tener que manejar diferentes _states_, uno por componente, y pasar _props_ de componentes padres a componentes hijos. Es aquí cuando se hace necesario tener un _state manager_ (o manejador de estados) que centralice todos los cambios de la aplicación y que tenga un _state_ que pueda leerse desde cualquier componente sin necesidad de pasarlo como _props_ de componentes padres a hijos.

[Redux](http://redux.js.org/) es un _state manager_ que centraliza el estado de una aplicación en un contenedor llamado **store**. Para cambiar el _state_ del _store_, Redux provee los **actions**, que son objetos que definen una acción a realizar. Este objeto define un tipo y data adicional relacionada a la acción (que es opcional). Para ejecutar estas acciones, Redux ofrece los **action creators**, que son funciones que retornan _actions_ y se utilizan junto con una función de Redux llamada `dispatch`. Por último, Redux tiene **reducers** que reciben los _actions_ y modifican el _state_ del _store_, según la acción que recibe.

## Actions

Un _action_ es un objeto que define una acción y también puede contener más propiedades relacionadas a la acción.

Por ejemplo, si la acción define “Agregar un elemento de una lista”, también puede contener el elemento que quiere agregar:

```javascript
{
  type: 'ADD_TODO',
  text: 'Aprender Redux'
}
```

> Es recomendable que, en proyectos grandes, los nombres de las acciones se definan en constantes para evitar escribirlas mal en alguna parte de la aplicación.

## Action Creators

Los _action creators_ son funciones que retornan un _action_.

Pueden contener parámetros indicando qué valores asociados al _action_ se van a retornar (como en el caso anterior, donde tenemos el elemento que se quiere agregar):


```javascript
function addTodo(text) {
  return {
    type: 'ADD_TODO',
    text: text
  };
}
```

Un _action creator_ es usado por la función `dispatch` de Redux para enviar dicha acción al _store_.

```javascript
dispatch(addTodo('Aprender Redux'));
```

## Reducers

Los _reducers_ son funciones que toman como argumentos el estado anterior del _store_, y la acción que llega de `dispatch`, y devuelven un nuevo estado.

Al crear un _reducer_ siempre debemos verificar que exista, ya que por defecto el estado es `undefined` la primera vez que el reducer es llamado (a este escenario se le llama **inicialización**). Si no existe se le devuelve un objeto que se llama **estado inicial**.

Luego, se devuelve un nuevo estado según la acción recibida como segundo parámetro:

```javascript
function reducer(state, action) {
  if (typeof state === 'undefined') {
    return initialState;
  }

  if (action.type === 'ADD_TODO') {
    return {
      ...state,
      todos: [
        ...state.todos,
        {
          text: action.text
        }
      ]
    }
  }

  return state;
}
```

## Store

El store se configura una sola vez en la aplicación y debe contener todos los reducers utilizados por la aplicación.

Para crear un store se utiliza la función `createStore` de Redux.

```javascript
import { createStore } from 'redux';
import reducer from './reducer';

const store = createStore(reducer);
```

> Cuando se utiliza más de un _reducer_ en una aplicación, se utiliza la función `combineReducers` para crear un solo _reducer_ que luego pueda ser utilizado por la función `createStore`.

```javascript
import { combineReducers, createStore } from 'redux';
import todosReducer from './reducers/todos';
import notesReducer from './reducers/notes';

const reducer = combineReducers({
  todos: todosReducer,
  notes: notesReducer
});

const store = createStore(reducer);
```
