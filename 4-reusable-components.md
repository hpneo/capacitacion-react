# Enfoque en el desarrollo de componentes reutilizables

Al trabajar con React se hace necesario pensar en el desarrollo de componentes reutilizables que puedan incluirse en más de una pantalla de la aplicación. Estos componentes no solo pueden ser usados para presentar datos si no también para manejar lógica de la aplicación o de la interfaz de usuario.

## Presentational vs Container Components

Un _Presentational Component_ es un componente que solo se utiliza para presentar información, y utiliza _props_ para ello. Es por esto que normalmente se definen como _Functional Components_ y no se conectan con Redux. Puede haber casos donde requieran tener un _state_ (y por lo tanto necesiten ser escritos como _Class Components_) pero este _state_ solo se encarga de manejar estados de la interfaz de usuario relacionados a dicho componente.

Un __Container Component_es un componente que no solo se encarga de presentar información al usuario, si no también manejar lógica de la aplicación e interacciones con el usuario. Para esto utiliza el _state_ y los props, y es común conectarlos a Redux cuando se requiere manejar un _state_ más complejo y global.

> Ambos tipos de componentes pueden contener tanto _presentational_ como _container_ components.

Diferenciar los componentes de React de una aplicación entre estos dos tipos permite separar responsabilidades entre componentes y definir una jerarquía entre ellos. De esta forma, por ejemplo, una aplicación puede tener una lista de elementos que son traídos vía AJAX, y para manejar la parte de la petición asíncrona se crea un _container component_, mientras que cada elemento de esa lista puede ser un _presentational component_.

## Conexión con Redux

Los _container components_ se conectan con Redux y utilizan el _store_ y los _actions_ para manejar parte de la lógica de la aplicación.

Luego de configurar un _store_ y englobar el componente raíz de React en un `<Provider>`, se pueden conectar a Redux cuantos _container componentes_ se requieran. Incluso, cada _container component_ puede tener su propio _reducer_ (aunque esto no impida que se puedan utilizar los _reducers_ de otros _containers_ o _reducers_ generales de la aplicación o utilitarios).

## Redux-Forms