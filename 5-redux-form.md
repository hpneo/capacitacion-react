# Redux Form

Redux Form es una biblioteca que facilita la creación de formularios utilizando React y conectándolos con Redux. De esta forma, Redux Form se encarga de la creación de campos, validaciones y serialización de la información ingresada.

Para utilizar Redux Form es importante recordar dos conceptos:

* _Reducer_: Redux Form se conecta al _store_ de Redux para manejar las acciones internas de la biblioteca, así como para guardar la información de sus formularios en el _state_ del _store_. Para esto utiliza el reducer `formReducer`.
* _Higher Order Component_: Redux Form utiliza el Higher Order Component `reduxForm`. para conectar un componente de React con Redux y registrar el formulario en su _state_.

Para utilizar Redux Form se debe configurar el _store_ de Redux para agregar el _reducer_ de Redux Form:

```javascript
import { createStore, combineReducers } from 'redux';
import { reducer as formReducer } from 'redux-form';

const rootReducer = combineReducers({
  form: formReducer,
  //... otros reducers
});

const store = createStore(rootReducer);
```

> Redux Form requiere que el _key_ asignado al _reducer_ `formReducer` sea `form`. Si se quiere cambiar el _key_ es necesario obtener la referencia al _state_ manualmente cada vez que se cree un formulario con Redux Form.

El siguiente paso es crear un componente de React y conectarlo usando `reduxForm`:

```javascript
import React from 'react'
import { reduxForm } from 'redux-form'

let ConnectedForm = props => {
  const { handleSubmit } = props;
  return (
    <form onSubmit={ handleSubmit }>
      { /* contenido del formulario */ }
    </form>
  )
}

ConnectedForm = reduxForm({
  form: 'form'
})(ConnectedForm);

export default ConnectedForm;
```

> `reduxForm` requiere una propiedad `form` que es el nombre del formulario que se usará para identificarlo dentro del _state_ de Redux.

> `handleSubmit` es un _prop_ que provee Redux Form y ejecuta un callback, al que se le pasan los datos del formulario ya validados.

## Creación de campos

Para agregar un campo a un formulario de Redux Form se utiliza el componente `<Field />`. Este componente permite renderizar los campos de formulario y agregarle más información relacionada al campo, como mensajes de error (cuando se validan campos con el _prop_ `validate`), así como indicar cómo se va a mostrar el valor ingresado en el campo y cómo se guardará el valor en Redux (esto es con los _props_ `format` y `normalize`).

```javascript
import React from 'react';
import { Field, reduxForm } from 'redux-form';

let ContactForm = props => {
  const { handleSubmit } = props;

  return (
    <form onSubmit={ handleSubmit }>
      <div>
        <label htmlFor="name">Nombre</label>
        <Field name="name" component="input" type="text" />
      </div>
      <div>
        <label htmlFor="email">E-mail</label>
        <Field name="email" component="input" type="email" />
      </div>
      <button type="submit">Enviar</button>
    </form>
  )
};

ContactForm = reduxForm({
  form: 'contact'
})(ContactForm);

export default ContactForm;
```

`<Field />` tiene un _prop_ llamado `component` que puede tomar los siguientes valores:

* Un componente de React: Puede ser un _Class Component_ o un _Functional Component_.
* Una cadena: `'input'`, `'select'` o `'textarea'`.

> Cuando se utilizan _Functional Components_ es importante definirlos fuera del método `render` del componente donde se va a utilizar, porque se crearía de nuevo cada vez que el componente sea renderizado, al ser considerado un _prop_ nuevo en cada renderizado.

## Validaciones

Existen dos formas de hacer validaciones en el lado del browser con Redux Form. La primera es utilizando la propiedad `validate` en la configuración del Higher Order Component `reduxForm`. La segunda es utilizando el _prop_ `validate` dentro de cada componente `<Field />`.

### Validaciones a nivel de formulario

Para validar un formulario entero se debe utilizar la propiedad `validate` de la configuración de `reduxForm`:

```javascript
import React from 'react';
import { Field, reduxForm } from 'redux-form';

const validate = (values) => {
  const errors = {};

  if (!values.name) {
    errors.name = 'Debe ingresar un nombre';
  }

  if (!values.email) {
    errors.name = 'Debe ingresar un e-mail';
  } else if (!/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$/i.test(values.email)) {
    errors.email = 'Debe ingresar un e-mail válido';
  }

  return errors;
};

let ContactForm = props => {
  const { handleSubmit } = props;

  return (
    <form onSubmit={ handleSubmit }>
      <div>
        <label htmlFor="name">Nombre</label>
        <Field name="name" component="input" type="text" />
      </div>
      <div>
        <label htmlFor="email">E-mail</label>
        <Field name="email" component="input" type="email" />
      </div>
      <button type="submit">Enviar</button>
    </form>
  )
};

ContactForm = reduxForm({
  form: 'contact',
  validate: validate
})(ContactForm);

export default ContactForm;
```

## Validaciones asíncronas
## ArrayFields
## Valores iniciales