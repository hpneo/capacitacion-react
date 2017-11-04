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

`<Field />` debe recibir un _prop_ llamado `name` que lo identifique dentro de Redux Form. También tiene un _prop_ llamado `component` que puede tomar los siguientes valores:

* Un componente de React: Puede ser un _Class Component_ o un _Functional Component_.
* Una cadena: `'input'`, `'select'` o `'textarea'`.

Todos los otros _props_ pasados a `<Field />` son pasados directamente al componente pasado en el _prop_ `component`, de ser el caso.

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

const renderField = ({
  input,
  label,
  type,
  meta: { touched, error, warning }
}) => (
  <div>
    <label>{label}</label>
    <div>
      <input {...input} placeholder={label} type={type} />
      {touched &&
        ((error && <span>{error}</span>) ||
          (warning && <span>{warning}</span>))}
    </div>
  </div>
)

let ContactForm = props => {
  const { handleSubmit } = props;

  return (
    <form onSubmit={ handleSubmit }>
      <Field name="name" component={renderField} label="Nombre" type="text" />
      <Field name="email" component={renderField} label="E-mail" type="email" />
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

`validate`es una función que recibe un objeto con los valores ingresados en el formulario y retorna un objeto con mensajes de error. En ambos casos, los _keys_ de los objetos son los nombres de los campos (definidos con el _prop_ `name`).

### Validaciones a nivel de campos

Para validar campo por campo se utiliza el prop `validate`en cada `<Field />`, el cual es una función que recibe 3 parámetros:

* `value`, que es el valor del campo.
* `allValues`, que es un objeto con todos los valores ingresados al formulario.
* `props`, que son propiedades pasados al formulario.

Si el valor del campo es válido, la función debe retornar `undefined`. Si el valor del campo no pasa la validación debe retornar un error (normalmente, una cadena con el mensaje de error).

```javascript
import React from 'react';
import { Field, reduxForm } from 'redux-form';

const required = value =>
  (value ? undefined : 'Campo requerido');
const email = value =>
  value && !/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$/i.test(value)
    ? 'E-mail inválido'
    : undefined;

const renderField = ({
  input,
  label,
  type,
  meta: { touched, error, warning }
}) => (
  <div>
    <label>{label}</label>
    <div>
      <input {...input} placeholder={label} type={type} />
      {touched &&
        ((error && <span>{error}</span>) ||
          (warning && <span>{warning}</span>))}
    </div>
  </div>
)

let ContactForm = props => {
  const { handleSubmit } = props;

  return (
    <form onSubmit={ handleSubmit }>
      <Field
        name="name"
        component={renderField}
        label="Nombre"
        type="text"
        validate={required}
      />
      <Field
        name="email"
        component={renderField}
        label="E-mail"
        type="email"
        validate={[required, email]}
      />
      <button type="submit">Enviar</button>
    </form>
  )
};

ContactForm = reduxForm({
  form: 'contact'
})(ContactForm);

export default ContactForm;
```

## Validaciones asíncronas

Las validaciones asíncronas son necesarias cuando se necesita validar el formulario utilizando un servidor. Por ejemplo, si se quiere saber si un nombre de usuario ya ha sido tomado, o si un código existe en una base de datos.

En Redux Form, las validaciones asíncronas se ejecutan si las otras validaciones ya pasaron satisfactoriamente, y se definen de manera similar a las validaciones a nivel de formulario. Esto es, como propiedades de configuración de `reduxForm`.

Existen dos propiedades a configurar dentro de `reduxForm`. La primera es `asyncValidate`, una función que toma como parámetro y debe devolver un `Promise`, donde puede retornar un objeto en caso la validación no pase. La segunda propiedad es `asyncBlurFields` y es necesaria para saber qué campos del formulario van a activar la validación asíncrona.

```javascript
import React from 'react';
import { Field, reduxForm } from 'redux-form';

const required = value =>
  (value ? undefined : 'Campo requerido');

const email = value =>
  value && !/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$/i.test(value)
    ? 'E-mail inválido'
    : undefined;

const sleep = ms =>
  new Promise(resolve => setTimeout(resolve, ms));

const asyncValidate = values =>
  sleep(1000).then(() => {
    // realizar validación en el servidor
    if (['pepito@example.com', 'juanito@example.com'].includes(values.email)) {
      throw { email: 'Este correo ya es usado por otro usuario' };
    }
  });

const renderField = ({
  input,
  label,
  type,
  meta: { touched, error, warning }
}) => (
  <div>
    <label>{label}</label>
    <div>
      <input {...input} placeholder={label} type={type} />
      {touched &&
        ((error && <span>{error}</span>) ||
          (warning && <span>{warning}</span>))}
    </div>
  </div>
)

let SignUpForm = props => {
  const { handleSubmit } = props;

  return (
    <form onSubmit={ handleSubmit }>
      <Field
        name="email"
        component={renderField}
        label="E-mail"
        type="email"
        validate={[required, email]}
      />
      <Field
        name="password"
        component={renderField}
        label="Contraseña"
        type="password"
        validate={required}
      />
      <button type="submit">Enviar</button>
    </form>
  )
};

SignUpForm = reduxForm({
  form: 'contact',
  asyncValidate,
  asyncBlurFields: ['email']
})(SignUpForm);

export default SignUpForm;
```

## Field Arrays

Existen casos donde requieren tener grupos de controles para agregar dinámicamente más de un elemento del mismo tipo (por ejemplo, agregar un conjunto de notas a un alumno o el detalle de una orden a una orden). En estos casos es util el componente `<FieldArray />`.

`<FieldArray />` acepta los mismos _props_ que `<Field />`: `name`, `component` y `validate`, entre otros. En el caso de `component`, `<FieldArray />` acepta componentes (ya sea _Class_ o _Funciontal_) y es utilizado para renderizar cada campo del grupo de campos.

```javascript
import React from 'react';
import { Field, FieldArray, reduxForm } from 'redux-form';

const renderField = ({
  input,
  label,
  type,
  meta: { touched, error, warning }
}) => (
  <div>
    <label>{label}</label>
    <div>
      <input {...input} placeholder={label} type={type} />
      {touched &&
        ((error && <span>{error}</span>) ||
          (warning && <span>{warning}</span>))}
    </div>
  </div>
)

const renderStudents = ({ fields }) => (
  <ul>
    <li>
      <button type="button" onClick={() => fields.push()}>
        Agregar alumno
      </button>
    </li>
    {
      fields.map((student, index) => (
        <li key={index}>
          <button
            type="button"
            title="Quitar"
            onClick={() => fields.remove(index)}
          />
          <h4>Alumno #{index + 1}</h4>
          <Field
            name={student}
            type="text"
            component={renderField}
            label="Nombre"
          />
        </li>
      ))
    }
  </ul>
);

let StudentsForm = props => {
  const { handleSubmit } = props;

  return (
    <form onSubmit={ handleSubmit }>
      <FieldArray name="students" component={renderStudents} />
      <button type="submit">Enviar</button>
    </form>
  )
};

StudentsForm = reduxForm({
  form: 'students'
})(StudentsForm);

export default StudentsForm;
```

Los componentes pasados como `component` reciben un _prop_ llamado `fields`, el cual es un objeto parecido a un _array_ que tiene varios métodos para agregar o eliminar campos de un grupo de campos de manera dinámica. Entre los métodos que tiene disponible están `insert`, `pop`, `push` y `remove`.

### Validación

La validación de un grupo de campos puede ser hecha a nivel de formulario a través de la propiedad de configuración `validate` de `reduxForm`. La función pasada a `validate` recibe todos los valores ingresados al formulario, incluyendo los grupos de campos.

```javascript
const validate = values => {
  const errors = {};

  if (!values.students || values.students.length === 0) {
    errors.students = {
      _error: 'Debe haber al menos un alumno registrado'
    };
  } else {
    const studentsErrors = [];

    values.students.forEach((student, studentIndex) => {
      if (!student) {
        studentsErrors[studentIndex] = 'Debe ingresar un nombre';
      }
    });

    errors.students = studentsErrors;
  }

  return errors;
};
```

## Valores iniciales

Redux Form soporta la creación de formularios con data inicial. Para esto es necesario conectar a Redux el componente devuelto por el _Higher Order Component_ `reduxForm`.

```javascript
import React from 'react';
import { connect } from 'react-redux';
import { Field, reduxForm } from 'redux-form';

let EditUser = props => {
  const { handleSubmit } = props;

  return (
    <form onSubmit={ handleSubmit }>
      <div>
        <label htmlFor="firstName">Nombres</label>
        <Field name="firstName" component="input" type="text" />
      </div>
      <div>
        <label htmlFor="lastName">Apellidos</label>
        <Field name="lastName" component="input" type="text" />
      </div>
      <button type="submit">Guardar</button>
    </form>
  )
};

const mapStateToProps = state => ({
  initialValues: {
    firstName: 'Juan',
    lastName: 'Perez'
  }
});

EditUser = reduxForm({
  form: 'edit-user'
})(EditUser);

EditUser = connect(mapStateToProps)(EditUser);

export default EditUser;
```