# React Tips

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