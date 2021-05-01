# Tipos de usuario

Un **tipo de usuario** (*user type*) es aquel que definimos nosotros o un tercero.
No viene de fábrica con el lenguaje.

Para definir nuevos tipos, se utiliza la sentencia `type`:

```
type Nombre(Parámetros)
  Bloque

type Nombre(Parámetros) : Nombre
  Bloque

type Nombre(Parámetros) : Nombre(Argumentos)
  Bloque
```

Vamos a describir un poco mejor la sentencia.
En primer lugar, hay que dejar claro que `type` es una manera de definir un tipo o clase de datos.
Es similar a la sentencia `class` de **JavaScript** y **Python**.
Este tipo tiene sus propios campos, los cuales se deben definir en el constructor del tipo, el cual se indica como su bloque.

Comencemos con un ejemplo ilustrativo:

```
type Coord2D(x, y)
  .x = x  #similar a self.x = x
  .y = y  #similar a self.y = y
```

Si un parámetro se va a asignar a un campo, se puede indicar el campo directamente como parámetro.
El ejemplo anterior se puede reducir a lo siguiente:

```
type Coord2D(.x, .y)
```

El operador `.` es la manera compacta de `self.`; se utiliza para acceder a miembros públicos.
Para miembros protegidos, se utiliza `:`.
Y para miembros privados, `!`.

Si tenemos que definir un campo explícitamente en el cuerpo del constructor o de un método, podemos utilizar los siguientes operadores de asignación:

```
=   #crea el campo como variable
::= #crea el campo como constante
```

Por ejemplo, si usamos `.x ::= 123`, lo que estamos definiendo es el campo `x` como público (`.`) y constante (`::=`) en el objeto `self`.
Cuando se traduzca el código a **JavaScript**, el compilador generará la siguiente proposición:

```
Object.defineProperty(this, "x", {value: 123, writable: false});
```
Si se desea usar la versión compacta parametrizada, no hay más que preceder el parámetro por la palabra reservada `const`.
Ejemplo:

```
type Coord2D(const .x:num, const .y:num)
```

Lo anterior es similar a:

```
type Coord2D(x:num, y:num)
  .x ::= x   #recordemos, similar a 'self.x ::= x'
  .y ::= y
```

## Visibilidad

La **visibilidad** (*visibility*) indica cómo de visible es un miembro y cómo debe ser accedido.

Tenemos tres tipos de visibilidad:

- La **visibilidad pública** (*public visibility*) es aquella que permite el acceso desde cualquier parte del programa.
- La **visibilidad interna** (*internal visibility*) es aquella que permite el acceso siempre que se sepa que existe.
- La **visibilidad privada** (*private visibility*) sólo permite su acceso desde el archivo en que se creó.

En **Dogma**, la visibilidad se indica mediante el operador de acceso al miembro:

- `.`, para acceso a un miembro público.
- `:`, para acceso a un miembro interno.
- `!`, para acceso a un miembro privado.

Ejemplos:

```
self.x    #miembro público x de self
self:x    #miembro protegido x de self
self!x    #miembro privado x de self

abc.x     #miembro público x de abc
abc:x     #miembro protegido x de abc
abc!x     #miembro privado x de abc
```

Veamos ahora unos ejemplos ilustrativos de asignación:

```
#creación de campo público y variable
.x = 123
self.x = 123
abc.x = 123

#creación de campo público y constante
.x ::= 123
self.x ::= 123
abc.x ::= 123

#creación de campo protegido y variable
:x = 123
self:x = 123
abc:x = 123

#creación de campo protegido y constante
:x ::= 123
self:x ::= 123
abc:x ::= 123

#creación de campo privado y variable
!x = 123
self!x = 123
abc!x = 123

#creación de campo privado y constante
!x ::= 123
self!x ::= 123
abc!x ::= 123
```

## Herencia

Para indicar que un tipo hereda otro, indicar el heredado tras los parámetros mediante `: TipoBase`.
Ejemplo:

```
type Coord3D(x?, y?, z) : Coord2D
  super(x, y)
  .z = z
```

Si deseamos pasar directamente los argumentos del constructor base, podemos hacerlo en la propia definición tal como muestra el siguiente ejemplo:

```
type Coord3D(x?, y?, z) : Coord2D(x, y)
  .z = z
```

Y si deseamos ser más compactos:

```
type Coord3D(x?, y?, .z) : Coord2D(x, y)
```

Analicemos la definición anterior:

- Por buenas prácticas, generalmente, los parámetros que se pasan tal cual al constructor padre se marcan como opcionales.
  Dejando que sea el padre quien decida si son realmente opcionales u obligatorios.
  Recordemos pues que podemos indicar los tipos para cualquiera de los parámetros y que las comprobaciones de tipos se ejecutan antes de invocar finalmente al constructor base.
  Por lo que si el base también realiza la misma comprobación de tipos, ésta se realizará dos veces.

- En el ejemplo, el nuevo tipo define un nuevo campo, `z`. Los otros dos, los define el tipo padre.

- Es posible indicar que al constructor padre se pasen los argumentos del hijo mediante `...`.
  Ejemplo: `type Coor3D(x?, y?, z) : Coord2D(...)`.

## Métodos

Para definir métodos, se utiliza la sentencia `fn`.
Sólo hay que indicar el nombre del tipo antes del nombre del método.
Ejemplo:

```
#Return an endpoint.
fn Messenger.endpoint(path:text) : Endpoint
  #(1) arguments
  if path not like "^https?://.+" and (not .domain and not peval(window)[0]) then
    throw("scheme expected.")

  #(2) return
  return if path like "/&([a-zA-Z]+)/?" then
    ParamEndpoint(self, path)
  else
    NonParamEndpoint(self, path)
  end
```

Los métodos también tienen visibilidad.
Pueden ser públicos, protegidos o privados.
Para definir un método público, separar el tipo del nombre por un punto (`.`).
Para definirlo como protegido, por dos puntos (`:`).
Y para privado, por el signo de admiración (`!`).
Ejemplo:

```
#público
fn Messenger.endpoint(path:text)

#protegido
fn Messenger:endpoint(path:text)

#privado
fn Messenger!endpoint(path:text)
```

### Métodos abstractos

Un **método abstracto** (*abstract method*) es aquel que no tiene definición y se espera sea implementado por los subtipos.
Se definen mediante la anotación `@abstract`:

```
@abstract
fn Tipo.método()
```

Sólo se puede definir como abstractos métodos públicos y protegidos.

## Propiedades

Una **propiedad** (*property*) es un campo calculado: cuando se accede, se ejecuta automáticamente su método asociado y se devuelve el valor devuelto por éste.
Para definir una propiedad, hay que utilizar la anotación `@prop`:

```
@prop
fn Tipo.prop()
  #...
```

Para definir una propiedad abstracta, se utiliza la anotación `@abstract` junto con `@prop`:

```
@abstract @prop
fn Tipo.prop()
```

Generalmente, las propiedades acceden a campos protegidos o privados.
Se define un campo protegido o privado que se accede *internamente* en modo L/E y se proporciona una propiedad pública para que el usuario pueda acceder en modo lectura a su contenido.
Así el usuario no puede modificar el campo privado, pero sí leer su contenido.
Veamos un sencillo ejemplo:

```
type App(conf:map) : Component(conf.http)
  :server = nil
  :db = nil
  :published = false
  .services ::= {}

@prop
fn App.server = :server

@prop
fn App.db = :db

@prop
fn App.published = :published
```

En estos casos, se puede utilizar el operador `.=`.
Este operador lo que hace es definir un campo protegido o privado, asignarle su valor inicial y crear una propiedad de sólo lectura, a nivel de instancia y no de clase como en el ejemplo.
Así pues, todo lo anterior podría reducirse a lo siguiente:

```
type App(conf:map) : Component(conf.http)
  :server .= nil
  :db .= nil
  :published .= false
  .services ::= {}
```

`:server .= nil` lo que hace es crear el campo protegido `server` en el objeto `self` y, además, la propiedad pública `server` para acceder al valor actual de este campo protegido.

Actualmente, **Dogma** sólo soporta propiedades de lectura.
No se pueden usar para asignar valores.
Sólo para leerlos.

Si el cuerpo de la propiedad contiene una única expresión, se puede definir sin la necesidad de la anotación `@prop`, como sigue:

```
fn Tipo.propiedad = Expresión
```

## Anotación @Self

Mediante la anotación `@Self`, el compilador crea una constante `Self` que referencia al tipo bajo desarrollo.
De esta manera, se puede definir los métodos, propiedades y demás mediante `Self.nombre`.
Esto facilita el cambio de nombre de un tipo.

Ejemplo:

```
@Self
type App(conf:map) : Component(conf.http)
  self:{server=nil, db=nil, published=false}.{services::={}}

@prop
fn Self.server() = :server

@prop
fn Self.db() = :db

@prop
fn Self.published() = :published
```

## Instanciación de tipos

Para crear una nueva instancia de un tipo, basta con invocarlo:

```
c = Coord3D(1, 2, 3)
```

Esto sólo es posible si el tipo se ha escrito íntegramente en **Dogma**.

### Objeto self

El objeto `self` es similar al `this` de **JavaScript** y al homónimo de **Python**.
Cada vez que en un método de instancia se referencia al objeto `self`, se accede al objeto actual sobre el que está trabajando.

Recordemos que se puede omitir si se accede a través de los operadores unarios `.`, `:` y `!`, tal como hemos visto anteriormente.

### Función reservada native()

En ocasiones, es necesario añadir código *target* tal cual en el código generado por el compilador de **Dogma**.
Por ejemplo, en **JavaScript** para instanciar una clase es necesario el operador `new`.
Como en **Dogma** este operador no existe, ¿cómo se puede instanciar una clase escrita nativamente en **JavaScript**?
Con la función reservada `native()`, cuya sintaxis es la siguiente:

```
native(textoLiteral)
```

El argumento de la función *tiene que ser un texto literal*.

Ejemplo:

```
const pool = native("new Pool(opts)")
```

Lo anterior generará el siguiente código **JavaScript**:

```
const pool = new Pool(opts)
```

Cuando el compilador se encuentra con una invocación a la función reservada `native()`, coge su texto literal y lo copia tal cual en el archivo bajo generación.

## Comprobación de tipo

Para saber si un objeto es de un determinado tipo, hay que utilizar el operador `is`:

```
objeto is Tipo
objeto is [Tipo1, Tipo2, Tipo3...]
objeto is not Tipo
objeto is not [Tipo1, Tipo2, Tipo3...]
```

Cuando se para una lista, se comprobará si es alguno de los tipos indicados.

Ejemplos:

```
c is Coord2D
c is "Coord2D"
```

Es posible indicar el tipo como el propio objeto tipo o bien su nombre en un texto.

## Mixins

**Dogma** soporta herencia simple, es decir, sólo puede heredar un único tipo.
Al igual que **JavaScript**.
Pero diferente, por ejemplo, de **Python** que soporta herencia múltiple.

Para ayudar en la programación, **Dogma** soporta los *mixins* que permite que los tipos incorporen métodos de otros tipos.
De esta manera, se puede crear un tipo e incorporarlo a otros tipos, mediante *mixin*, lo que permitirá la reutilización de sus métodos.
Pero siempre *sin pertenecer a su árbol genealógico*.

En primer lugar, el tipo que se comporta como *mixable*, aquel que puede incorporarse a otros tipos, se debe definir con la anotación `@mixable`.
Ejemplo:

```
@mixable @abstract
export type HttpResource(props?:{status?:num, contentType?:text})
  #...

@prop
fn HttpResource.path() : text
  #...

@prop
fn HttpResource.fullPath() : text
  #...

fn HttpResource.status(...args) -> rslt
  #...

fn HttpResource.contentType(...args) -> rslt
  #...
```

Por su parte, un tipo que incorpore un tipo *mixable* debe indicarlo mediante la cláusula `::`, tal como muestra el siguiente ejemplo:

```
export type HttpFile(props) : File(props) :: HttpResource(props)
```

El tipo *mixable* tiene constructor y se puede invocar si lo deseamos en su incorporación a un tipo.
Para ello, indicar sus argumentos junto al nombre del tipo *mixable*.
Si así se hace, el compilador generará el código necesario para que el constructor *mixable* se ejecute también.
Pero si no se indica, por ejemplo, porque se indica sólo el nombre del tipo *mixable*, entonces, no se invocará.
En el ejemplo anterior, se define el tipo `HttpFile`, el cual hereda el tipo `File` e incorpora `HttpResource`.
Como `HttpResource` se indica como una llamada, se invocará su constructor y cualquier campo creado en él se creará en la nueva instancia de `HttpFile`.
En el momento del *mixin*, el compilador coge los métodos del tipo *mixable* y se los *copia* a `HttpFile`.
Cualquier método definido posteriormente al *mixin*, no se incorporará.

## Función bind()

La función `bind()` se utiliza para crear un objeto que representa una relación entre un método y un objeto:

```
fn bind(object, method:text) : func
```

Esta función es muy útil cuando hay que pasar un método como *callback*.
Ejemplo:

```
sum = bind(calcul, "sum")
print(sum(1, 2, 3, 4))
```
