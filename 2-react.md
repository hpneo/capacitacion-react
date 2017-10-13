# React Tips

## Manejo de Eventos

Al definir un componente en React se puede necesitar capturar eventos del navegador. Esto se hace de manera declarativa en el `render()` mediante el uso de _props_ dentro del componente del que se quiere capturar uno o más eventos. Internamente, React toma estos _props_ y añade _listeners_, o funciones que _escuchan_ a los eventos declarados en el componente.

En este ejemplo se declara un evento `click` utilizando el _prop_ `onClick`:

```javascript
class Toggle extends React.Component {
  constructor() {
    super();

    this.state = {
      isVisible: false
    };
  }
  render() {
    const isVisible = this.state.isVisible;

    return (
      <div>
        <button onClick={() => this.setState({ isVisible: !isVisible })}>
          { isVisible ? 'Ocultar contenido' : 'Mostrar contenido' }
        </button>
        <div className={isVisible ? 'is-visible' : 'is-hidden'}>
          ...Contenido desplegable
        </div>
      </div>
    );
  }
}
```

> React soporta todos los eventos nativos del navegador. Puedes encontrar la lista completa [en la documentación oficial](https://reactjs.org/docs/events.html#supported-events).

Cada función asociada a un evento recibe un parámetro, llamado `SyntheticEvent`, que contiene toda la información relacionada al evento. Este parámetro es un objeto que engloba las propiedades de un evento nativo, y se comporta de manera similar. De esta forma React se asegura que el evento funcione igual en todos los navegadores.

Esto significa que este parámetro también tiene acceso a los métodos `stopPropagation()` y `preventDefault()`:

```javascript
class Form extends React.Component {
  onFormSubmit(event) {
    event.preventDefault();
    console.log(event.type);
  }
  render() {
    return (
      <form onSubmit={this.onFormSubmit}>
        ...componentes
        <button type='submit'>Enviar formulario</button>
      </form>
    );
  }
}
```

## Formularios

Para manejar campos de formulario, como `<input>`, `<textarea>` o `<select>` se utiliza el prop `onChange`, el cual ejecuta una función cada vez que un input cambie su valor.

```javascript
class Form extends React.Component {
  onInputChange(event) {
    console.log('input', event.target.value);
  }
  onSelectChange(event) {
    console.log('select', event.target.value);
  }
  render() {
    return (
      <form>
        <input type={this.props.type} onChange={this.onInputChange} />
        <select value={this.props.selectValue}  onChange={this.onSelectChange}>
          <option value='angular'>Angular</option>
          <option value='jquery'>jQuery</option>
          <option value='react'>React</option>
        </select>
      </form>
    );
  }
}
```

### Controlled / Uncontrolled Components

React permite manejar los campos de formulario de dos formas. Una es utilizando _uncontrolled components_ y la otra es utilizando _controlled components_. Un _uncontrolled component_ es un componente que no es, valga la redundancia, controlado por React. Esto significa que React no se encarga de guardar su valor (y tampoco puede leerlo). Un _controlled component_ lee y guarda el valor de un componente en el _state_ a través de los eventos del componente.

Para obtener el valor de un input que es _uncontrolled_ necesitas leer el valor directamente desde el DOM. Para esto se utiliza el _prop_ especial `ref`:

```javascript
class UncontrolledInput extends React.Component {
  onFormSubmit(event) {
    event.preventDefault();

    console.log(this.input.value);
  }
  render() {
    return (
      <form onSubmit={this.onFormSubmit.bind(this)}>
        <input type={this.props.type} ref={(input) => (this.input = input)} />
      </form>
    );
  }
}
```

> `ref` es un _prop_ de los componentes React que tiene como valor una función. Esta función es ejecutada en cada render y permite guardar una referencia el elemento original del DOM dentro del componente; de esta forma, el elemento nativo puede ser usado por React en los métodos de su ciclo de vida.

En un _controlled component_, React necesita un evento `onChange` para transmitir el valor del elemento nativo al _state_ de React:

```javascript
class ControlledInput extends React.Component {
  constructor() {
    super();

    this.state = {
      input: ''
    };

    this.onFormSubmit = this.onFormSubmit.bind(this);
    this.onInputChange = this.onInputChange.bind(this);
  }
  onFormSubmit(event) {
    event.preventDefault();

    console.log(this.state.input);
  }
  onInputChange(event) {
    this.setState({ input: event.target.value });
  }
  render() {
    return (
      <form onSubmit={this.onFormSubmit}>
        <input type={this.props.type} onChange={this.onInputChange} value={this.state.input} />
      </form>
    );
  }
}
```

La ventaja de utilizar _controlled components_ es que permite mantener los valores de los elementos de un formulario dentro de React, por lo que validaciones o renderizados condicionales dentro de un componente se pueden manejar declarativamente según los valores del _state_.

> Mientras que en los _controlled components_ el valor inicial se define con el _prop_ `value`, en los _uncontrolled components_ se define con el _prop_ `defaultValue`.

## Arquitectura de una aplicación React

> **Importante**: Existen muchas formas de organizar las aplicaciones que contienen componentes de React. Esta es una forma de ellas y no necesariamente es la mejor o la que deberías utilizar en tu aplicación. Puedes revisar [otras versiones](https://medium.com/@alexmngn/how-to-better-organize-your-react-applications-2fd3ea1920f1) y comparar las ventajas y desventajas de cada forma de organizar tus archivos.

Una aplicación React medianamente compleja necesita una forma de organizar todos los componentes. El primer paso es identificar cuáles son los componentes que integran la aplicación a nivel visual y lógico. Por su funcionalidad, los componentes pueden dividirse en:

* De Presentación: Encargados de presentar información de manera visual. Puede agrupar varios componentes del DOM o de React, solo recibe _props_ y no define ni interactúa con el estado del componente.
* De Lógica: Manejan su propio estado y manejan datos ingresados por el usuario. Pueden agrupar componentes de presentación o de lógica.

Teniendo en cuenta la distinción entre ambos tipos de componentes se podría definir estructura que puede servir para varios casos:

* `sections`
  * `sectionA`
    * `components`
      * `sectionAComponent1.js`
      * `sectionAComponent2.js`
    * `index.js`
  * `sectionB`
    * `components`
      * `sectionBComponent1.js`
      * `sectionBComponent2.js`
    * `index.js`
  * `sectionC`
    * `components`
      * `sectionCComponent1.js`
      * `sectionCComponent2.js`
    * `index.js`
* `shared`
  * `components`
    * `sharedComponent1.js`
    * `sharedComponent2.js`
    * `sharedComponent3.js`

Donde:
* `sections` agrupa las secciones de una aplicación. Dichas secciones pueden ser partes de una misma pantalla o una sola pantalla de toda la aplicación. Una _sección_ puede estar compuesta por varios _componentes_. Generalmente las secciones son componentes de lógica.
* `components` agrupan componentes (tanto de presentación como de lógica) que son específicos para esa _sección_.
* `shared` contiene todos los componentes que son de uso general y pueden ser usados en más de una _sección_.