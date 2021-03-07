# React

[React](https:reactjs.org) es una biblioteca para crear aplicaciones SPA desarrollada por **Facebook**.
Es muy usada actualmente.
Por esta razón, **Dogma** soporta de manera nativa el desarrollo con **React**.
Actualmente, se ha probado su uso en varios proyectos con [Semantic UI React](https://react.semantic-ui.com/).

## Directiva #!react

Una directiva `#!react`, al comienzo de un módulo dogmático, indica que estamos ante un módulo que define o trabaja con **React**.
Cuando el compilador se encuentra con esta directiva, añade el siguiente código al archivo **JavaScript** final:

```
import React, {Component} from "react";
```

Si no declara la directiva `#!react`, el compilador propagará un error si intenta declarar elementos XML.

## Definición de componentes

Una vez indicado que el archivo va a trabajar con **React**, puede definir los componentes mediante funciones y/o tipos.
En el caso de los tipos, no hay más que heredar `Component` (importado implícitamente por la directiva `#!react`) u otro componente.

Ejemplo de componente funcional:

```
#!react

#imports
from "semantic-ui-react" use Button

#My button component.
@component
export fn MyButton() = <Button>"Click here"</Button>
```

Y ahora un componente de clase:

```
#!react

#imports
from "semantic-ui-react" use Button

#My button component.
@component
export type MyButton(props) : Component(props)

@override
fn MyButton.render() = <Button content="Click here" onClick=(.handleClick) />

#Handle the click.
@bind
fn MyButton.handleClick(e)
  #...
```

## Uso de JSX

**Dogma** soporta una versión propia de **JSX**, para ayudar a escribir componentes **React**.
Se parecen algo, pero si desarrolla con **Dogma** tendrá que hacer un pequeño cambio de chip.
Mínimo pero hay que hacerlo.
La parte positiva es que se ha acercado al máximo al lenguaje **Dogma**, lo que facilita su aprendizaje.

### Definición de elementos

Por un lado, tenemos cómo definir los elementos **React**.
Al igual que **JSX**, mediante:

```
<elemento ...>contenido</elemento>
<elemento .../>
```

### Declaración de propiedades

Las propiedades de los elementos contienen la mayor parte de las diferencias con **JSX**.
Por un lado, toda propiedad tiene un nombre y un valor, el cual:

- Puede ser omitido, en cuyo caso, el compilador trabaja igual que con los mapas literales: a la propiedad le asignará el valor de la variable homónima.
- Puede ser una expresión dogmática, en cuyo caso, se debe delimitar por paréntesis (`()`).
  Sólo si el valor de la propiedad procede de una variable, puede omitir los paréntesis.
  Ejemplo: `visible=sidebarOpened`.
- Puede ser un valor literal, en cuyo caso, los paréntesis no son necesarios.
  Ejemplo: `vertical=true` o `size="large"`.

También es posible indicar la sintaxis de los mapas literales que se muestra a continuación:

```
{propiedad} = (valor)

#similar a:
propiedad = (valor.propiedad)
```

Además, se puede indicar que incluya los campos de un objeto, mediante las siguientes sintaxis:

```
...variable
...constante
...(expresión)
```

Veamos unos ejemplos ilustrativos:

```
<Image src="/images/viewframe/image.png" size="small" wrapped=true />
<Image src=("/images" + "/viewframe" + "/image.png") size=("small") wrapped />
<div style={color="white", backgroundImage=fmt("url(%s)", imgUrl)}>
```

Recordemos que si una propiedad omite su valor, debe existir una variable con el mismo nombre.
Así pues, en el ejemplo anterior, `wrapped` es lo mismo que `wrapped=wrapped`.
Esto es muy diferente a **JSX**, donde la declaración de una propiedad sin valor lo que hace es asignarle el valor `true`.

### Declaración de contenido

Cuando un elemento no presenta contenido, puede declararse como sigue:

```
#sin contenido
<elemento propiedades />
<elemento propiedades></elemento>
```

Pero si tiene contenido, las diferencias con **JSX** son mayúsculas.
Tenga mucho cuidado.
El contenido de un elemento consiste en una secuencia de expresiones dogmáticas y/u otros elementos.
Donde las expresiones se delimitan por paréntesis (`()`).
Ejemplos:

```
#contenido simple: una expresión
<div>(1+2+3)</div>                        #similar a <div>{1+2+3}</div> en JSX
<title>("My title")</title>               #similar a <title>{"My title"}</title> en JSX
<title>(fmt("Hello %s!", user))</title>   #similar a <title>{fmt("Hello %s!", user)}</title>

#contenido múltiple formado por varios elementos
<div>
  <one/>
  <two/>
  <three/>
</div>

#contenido múltiple formado por varias expresiones
<div>
  (one)
  (two)
  (three)
</div>

#contenido múltiple mixto
<div>
  <one />
  (two)
  <three />
</div>
```

Recordemos que en **JSX**, las expresiones se delimitan por llaves (`{}`), pero en **Dogma**, por paréntesis (`()`).

### Uso de la anotación @bind

Cada vez que se define un método de instancia con la anotación `@bind`, el compilador crea en cada instancia del tipo un miembro que lo ata al método.
Por ejemplo, cuando el compilador se encuentra con lo siguiente:

```
@bind
fn DesktopContainer.hideFixedMenu()
  .setState({fixed=false})
```

Lo que hace es crear automáticamente el campo `hideFixedMenu`, en el constructor del tipo `DesktopContainer`, como sigue:

```
this.hideFixedMenu = (function() {
  this.setState({fixed: false});
}).bind(this);
```

De esta manera, se puede indicar muy fácilmente los controladores de los eventos como, por ejemplo:

```
<Visibility onBottomPassed=(.showFixedMenu) onBottonPassedReverse=(.hideFixedMenu)>
  ...
</Visibility>
```

En vez de:

```
<Visibility onBottomPassed=(.showFixedMenu.bind(self)) onBottonPassedReverse=(.hideFixedMenu.bind(self))>
  ...
</Visibility>
```

Para que `@bind` se aplique a las definiciones de métodos, es necesario que su tipo se haya declarado como `@component`.
En nuestro ejemplo, tendrá que ser definido como sigue:

```
@component
type DesktopContainer(props) : Component(...)
```

Por convenio y buenas prácticas, también se recomienda marcar los componentes funcionales como `@component`.
Ejemplo:

```
@component
fn HomeHeading(props=>{mobile}) = <Container>
  <Header
    as = "h1"
    content = "Dogma"
    inverted = true
    style = {
      fontSize = if mobile then "2em" else "4em" end
      fontWeight = "normal"
      marginBottom = 0
      marginTop = if mobile then "1.5em" else "3em" end
    }
  />

  <Header
    as = "h2"
    content = "Lenguaje de programación de scripts"
    inverted = true
    style = {
      fontSize = if mobile then "1.5em" else "1.7em" end
      fontWeight = "normal"
      marginTop = if mobile then "0.5em" else "1.5em" end
    }
  />

  <Button primary=true size="huge">
    ("Introducción")
    <Icon name="right arrow" />
  </Button>
</Container>

HomeHeading.propTypes = {
  mobile = PropTypes.bool
}
```
