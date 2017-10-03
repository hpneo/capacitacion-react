# ECMAScript 6 e introducción a React

## ECMAScript 6

ECMAScript 6 es el nuevo estándar de JavaScript e incluye nuevas características del lenguaje que ayudan a escribir código más conciso y elegante.

### Imports

ECMAScript 6 tiene una sintaxis nativa para importar módulos, la cual permite cargar referencias de todo el módulo o solo una parte:

```javascript
import React, { Component } from 'react';

console.log(React);
console.log(React.Component);
console.log(Component);
```

### Exports

ECMAScript 6 también tiene una sintaxis nativa para exportar módulos. Se pueden definir exports por defecto y también exportar solo una parte del módulo:

```javascript
import { compile } from 'path-to-regexp';

const routes = {
  root: '/',
  about: '/about',
};

export const compileRoute = (path, params) =>
  compile(path)(params);

export default routes;
```

### `const` y `let`

Parte de la nueva especificación de ECMAScript 6 define dos nuevos tipos de variable. Las variables constantes, las cuales se crean con la palabra reservada `const`, y las variables de ámbito de bloque, que se crean con `let`.

`const` permite crear variables que no pueden ser reasignadas:

```javascript
const currentUserName = 'Juan Perez';
currentUserName = 'John Doe';

// => Uncaught TypeError: Assignment to constant variable.
```

Por otro lado, `let` permite crear variables que solo existen en el ámbito de un bloque o expresión. Mientras que `var` crea variables que son accesibles a nivel global, o dentro del ámbito de una función, `let` crea variables que solo existen dentro de expresiones como `if` o `for`. Las variables creadas con `let` no son accesibles en el ámbito global, y tampoco pueden redeclararse, pero sí reasignar su valor:

```javascript
let currentUserName = 'Juan Perez';
currentUserName = 'John Doe';

console.log(currentUserName);

// => John Doe
```

```javascript
let currentUserName = 'Juan Perez';
let currentUserName = 'John Doe';

// => Uncaught SyntaxError: Identifier 'currentUserName' has already been declared
```

### Clases

ECMAScript 6 introduce clases propiamente dichas al lenguaje.

```javascript
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }
  area() {
    return this.width * this.height;
  }
}

const rectangle = new Rectangle(3, 2);
rectangle.area();
// => 6
```

Las clases en ECMAScript 6 también permiten herencia de clases:

```javascript
class Square extends Rectangle {
  constructor(side) {
    super(side, side);
  }
}

const square = new Square(2);
square.area();
// => 4
```

### Arrow functions

Las _arrow functions_ permiten crear funciones con una sintaxis más corta que utilizando la expresión tradicional.

```javascript
const numbers = [1, 2, 3, 4];

numbers.map(item => item * 2);
// => [2, 4, 6, 8]
```

Si el cuerpo de un _arrow function_ tiene más de una línea, se utilizan llaves (los paréntesis engloban los parámetros de un _arrow function_ son opcionales):

```javascript
const numbers = [1, 2, 3, 4];

numbers.map((item) => {
  console.log(item);

  return item * 2;
});

// => 1
// => 2
// => 3
// => 4
// => [2, 4, 6, 8]
```

Las diferencias no terminan aquí. Tampoco se pueden utilizar como funciones constructoras, no define `arguments` y no crean su propio contexto (`this`), por lo que toman el contexto padre.

```javascript
numbers.map(item => console.log(arguments));
// => Uncaught ReferenceError: arguments is not defined
numbers.map(function(item) { console.log(arguments); });
// => [1, 0, Array(4), callee: ƒ, Symbol(Symbol.iterator): ƒ]
// => [2, 1, Array(4), callee: ƒ, Symbol(Symbol.iterator): ƒ]
// => [3, 2, Array(4), callee: ƒ, Symbol(Symbol.iterator): ƒ]
// => [4, 3, Array(4), callee: ƒ, Symbol(Symbol.iterator): ƒ]

class Logger {
  logArrow() {
    setTimeout(() => console.log(this), 0);
  }

  logFunction() {
    setTimeout(function() { console.log(this); }, 0);
  }
}

const logger = new Logger();

logger.logArrow();
// => Logger {}

logger.logFunction();
// => undefined
```

### Spread operator

El _spread operator_ permite expandir objetos y arrays en lugares donde se requiere más de un elemento, como pasar argumentos a una función:

```javascript
const sum = (a, b) => a + b;
const numbers = [1, 2];

sum(...numbers);
```

También se usa para crear nuevos arrays:

```javascript
const arrayA = [1, 2, 3, 4];
const arrayB = [5, 6, 7, 8];

const mergedArrays = [...arrayA, ...arrayB];
// => [1, 2, 3, 4, 5, 6, 7, 8]
```

Y de igual manera funciona para crear nuevos objetos a partir de otros:

```javascript
const basicInformation = {
  firstName: 'John',
  lastName: 'Doe',
  age: 37,
};
const extraInformation = {
  email: 'john@doe.com',
  role: 'admin',
};

const currentUser = {
  ...basicInformation,
  ...extraInformation,
};

// => {
//  firstName: 'John',
//  lastName: 'Doe',
//  age: 37,
//  email: 'john@doe.com',
//  role: 'admin',
// }
```

----

## React

React es una biblioteca JavaScript encargada de manejar la vista de una aplicación. Puede ser utilizada tanto como para manejar una vista completa de una aplicación (como en el caso de las _Single Page Applications_), como para mejorar el comportamiento de un componente dentro de una vista en una aplicación tradicional.

React permite manejar cambios de estado de un componente visual de manera *declarativa*. Esto quiere decir que, como desarrolladores, podemos definir qué aspecto va a tener dicho componente dependiendo de su estado. Este es un cambio con respecto al desarrollo tradicional, o *imperativo*, donde tenemos que programar cada cambio hecho en el componente.

### JSX

JSX es la sintaxis que usa React para definir el aspecto que va a tener un componente de React. Es similar a XML, con algunas diferencias notables, como los nombres de algunos atributos (por ejemplo: `className` en vez de `class` o `forHtml` en vez de `htmlFor`) o la forma de cerrar los elementos de una sola línea (como `img` o `br`).

```javascript
class UserAvatar extends React.Component {
  render() {
    return (
      <img src={this.props.url} alt={this.props.userName} title={this.props.userName} />
    );
  }
}
```

### State y props

React maneja 2 conceptos para mostrar información dentro de los componentes. _State_ es el estado del componente, el cual puede cambiar varias veces en el tiempo; y los _props_, que son atributos de un componente, permiten personalizar los componentes que se utilizan en React, y son definidos una sola vez, al crear una instancia de dicho componente.

Una diferencia importante es que los componentes de React tienen un método específico para cambiar el estado, llamado `setState()`. `setState(newState)` reemplaza el estado actual (guardado en `this.state`) por `newState`. Cada vez que el estado es cambiado, se realiza un proceso llamado **_re-rendering_**.

> _Rendering_ es el proceso de actualizar un componente de React en el navegador. Esto significa cambiar la apariencia o el contenido de un componente en el DOM. React se encarga de manejar el _rendering_ de manera inteligente haciendo una comparación entre el estado anterior y el nuevo estado (también hace la misma comparación a nivel de _props_, pero es recomendable usar el estado si se requiere actualizar un componente en el navegador).
>
> React solo actualiza la parte del componente que es afectado por el nuevo estado, y deja intacto el resto de elementos del DOM que forman parte del componente pero no están en contacto con el nuevo estado.
>
> Por otro lado, _re-rendering_ solo es el proceso de hacer un nuevo renderizado en el mismo componente.

Otra diferencia importante es que los _props_ se definen externamente, en la instancia del componente, mientras que el _state_ es definido internamente, en la definición del componente:

```javascript
class MyComponent extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      isClicked: false,
    };
  }
  render() {
    const isButtonClicked = this.state.isClicked;

    return (
      <button onClick={() => this.setState({ isClicked: true })}>
        {isButtonClicked ? 'You clicked it!' : 'Click the button'}
      </button>
    );
  }
}
```

En el ejemplo anterior se puede ver cómo se define un _prop_ `onClick`, que define externamente en la instanciación del elemento `button`, y un _state_ `isClicked`, definido internamente en la definición de `MyComponent`.

> `onClick` es la forma de React de definir eventos en elementos del DOM. Cada elemento nativo del DOM está mapeado en el formato `on[EventName]`. [Lee más sobre los eventos soportados por React](https://reactjs.org/docs/events.html#supported-events).

### Component Lifecycle

Un componente de React tiene un ciclo de vida dentro de la aplicación, desde que es creado, es insertado en el DOM (_mounting_), cambia su estado (_updating_) y luego es eliminado del DOM (_unmounting_). Cada fase del ciclo de vida de un componente está representado por un método que puede ser sobreescrito.

1. `constructor()`
2. `componentWillMount()`
3. `render()`
4. `componentDidMount()`
5. `componentWillReceiveProps()`
6. `shouldComponentUpdate()`
7. `componentWillUpdate()`
8. `componentDidUpdate()`
9. `componentWillUnmount()`

#### `constructor(props)`

El constructor es un método propio de las clases de ECMAScript 6, y contiene código que es ejecutado al momento de crear una instancia de dicha clase. Dentro del constructor podemos definir el estado inicial de una instancia de un componente React, así como definir propiedades de instancia (no confundir con los _props_ de una instancia de un componente React) o métodos. El constructor recibe como parámetros a los `props` definidos al momento de instanciar el componente React.

#### `componentWillMount()`

Este método se ejecuta inmediatamente antes de insertar un componente en el DOM y se ejecuta antes del primer `render()`. Aquí también puede definirse el estado de la instancia del componente sin preocuparse porque ocurra un _re-rendering_ (dado que el componente aún no está insertado en el DOM).

#### `render()`

Un componente de React debe tener definido al menos el método `render()`, el cual debe retornar un elemento de React (escrito en JSX, puede ser un elemento del DOM o la instancia de un componente creado en React), `null`, o un booleano.

> Si `render()` devuelve un booleano o `null`, no renderizará en el DOM.

> `render()` debe ser una función pura. Es decir, una función que, pasándole el mismo input, devuelve el mismo output siempre. Tampoco debe modificar el DOM.

#### `componentDidMount()`

`componentDidMount()` es una función que se ejecuta inmediatamente después de haber insertado el componente en el DOM y de haber ejecutado `render()` por primera vez. Dentro de este método puede modificarse el elemento del DOM insertado por el componente (por ejemplo, para aplicar alguna biblioteca JavaScript que muta al DOM) o hacer peticiones asíncronas (como peticiones AJAX o lanzar `setInterval` o `setTimeout`).

#### `componentWillReceiveProps(nextProps)`

`componentWillReceiveProps()` es llamado antes de que un componente ya insertado en el DOM reciba nuevos _props_. Los nuevos _props_ que el componente va a recibir están guardados en el parámetro `nextProps` y pueden usarse para compararse con los _props_ actuales (guardados en `this.props`) para lanzar una actualización del estado utilizando `this.setState()`.

#### `shouldComponentUpdate(nextProps, nextState)`

`shouldComponentUpdate()` es llamado antes del _rendering_ y puede retornar `true` o `false`, indicando si el componente va a re-renderizarse o no. Si esta función devuelve `false`, tanto `componentWillUpdate()` como `render()` y `componentDidUpdate()` no se ejecutarán.

> Retornar `false` en este método no evita que los componentes hijos se _re-rendericen_ si el estado de esos componentes hijos cambia.

#### `componentWillUpdate(nextProps, nextState)`

Este método se ejecuta inmediatamente antes de realizar un _re-rendering_ cuando nuevos _props_ o un nuevo estado llega al componente. Este método no es llamado cuando se realiza el primer _rendering_ (en comparación a `componentWillMount()`, que si es llamado antes del primer _rendering_).

#### `componentDidUpdate(prevProps, prevState)`

Este método es llamado inmediatamente después de realizar un _re-rendering_. Los _props_ y el estado anteriores son pasados como parámetros y pueden usarse para realizar operaciones asíncronas comparando esos valores con los nuevos _props_ y el nuevo estado. Este método no es llamado cuando se realiza el primer _rendering_ (en comparación a `componentDidMount()`, que si es llamado antes del primer _rendering_).

#### `componentWillUnmount()`

`componentWillUnmount()` es ejecutado inmediatamente antes de que un componente es quitado del DOM y destruido en memoria. Dentro de este método pueden realizarse operaciones de limpiado y liberación de recursos (por ejemplo: regresar un nodo del DOM modificado a su valor original, cancelar peticiones asíncronas o eliminar llamadas a `setInterval` o `setTimeout`).

### Class Components y Functional Components