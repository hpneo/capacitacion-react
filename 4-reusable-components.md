# Enfoque en el desarrollo de componentes reutilizables

Al trabajar con React se hace necesario pensar en el desarrollo de componentes reutilizables que puedan incluirse en más de una pantalla de la aplicación. Estos componentes pueden ser usados tanto para presentar datos como para manejar lógica de la aplicación o de la interfaz de usuario.

## Presentational vs Container Components

Un _Presentational Component_ es un componente que solo se utiliza para presentar información, y utiliza _props_ para ello. Es por esto que normalmente se definen como _Functional Components_ y no se conectan con Redux. Puede haber casos donde requieran tener un _state_ (y por lo tanto necesiten ser escritos como _Class Components_) pero este _state_ solo se encarga de manejar estados de la interfaz de usuario relacionados a dicho componente.

Un _Container Component_ es un componente que no solo se encarga de presentar información al usuario, sino también manejar lógica de la aplicación e interacciones con el usuario. Para esto utiliza el _state_ y los props, y es común conectarlos a Redux cuando se requiere manejar un _state_ más complejo y global.

> Ambos tipos de componentes pueden contener tanto _presentational_ como _container_ _components_.

Diferenciar los componentes de React de una aplicación entre estos dos tipos permite separar responsabilidades entre componentes y definir una jerarquía entre ellos. De esta forma, por ejemplo, una aplicación puede tener una lista de elementos que son traídos vía AJAX, y para manejar la parte de la petición asíncrona se crea un _container component_, mientras que cada elemento de esa lista puede ser un _presentational component_.

## Conexión con Redux

Los _container components_ se conectan con Redux y utilizan el _store_ y los _actions_ para manejar parte de la lógica de la aplicación.

Luego de configurar un _store_ y englobar el componente raíz de React en un `<Provider>`, se pueden conectar a Redux cuantos _container componentes_ se requieran. Incluso, cada _container component_ puede tener su propio _reducer_ (aunque esto no impida que se puedan utilizar los _reducers_ de otros _containers_ o _reducers_ generales de la aplicación o utilitarios).

## Higher Order Components

Aparte de los _presentational_ y _container_ _components_, existe una técnica que permite darle comportamiento y manejo de lógica a un componente, y aún así mantener la capacidad de reusabilidad. Esta técnica es llamada _Higher Order Components_ y un ejemplo se encuentra en Redux a través de la función `connect`.

Un _Higher Order Component_ es una función que toma como parámetro un componente, le agrega funcionalidades y devuelve un nuevo componente mejorado, en base al componente pasado como parámetro. Los _Higher Order Components_ permiten hacer un _debugging_ más preciso porque el componente retornado por esta función es distinto al ingresado como parámetro, por lo que existe un _stack trace_ explícito donde se puede saber en qué punto de la definición del componente ocurre un error.

Por ejemplo, se puede crear un _Higher Order Component_ que tome como parámetros dos componentes y decida cuál renderizar según el _media query_ que está activo.

```javascript
const withMobileView = (mediaQuery) =>
  (DesktopComponent, MobileComponent) => (
  class ResponsiveComponent extends React.Component {
    constructor(props) {
      super(props);

      this.matchMediaForMobile = window.matchMedia(mediaQuery);
      this.onChangeMatchMedia = matchMedia =>
        this.setState({ isMobile: matchMedia.matches });

      this.state = {
        isMobile: this.matchMediaForMobile.matches,
      };
    }
    componentDidMount() {
      this.matchMediaForMobile.addListener(this.onChangeMatchMedia);
    }
    componentWillUnmount() {
      this.matchMediaForMobile.removeListener(this.onChangeMatchMedia);
    }
    render() {
      if (this.state.isMobile && MobileComponent) {
        return <MobileComponent {...this.props} />;
      }

      return <DesktopComponent {...this.props} />;
    }
  }
);
```