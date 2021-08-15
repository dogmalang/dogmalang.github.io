# Estructuras

Una **estructura** (*struct*) es un tipo de datos especial, idéntico a uno definido con `type`, pero con las siguientes características:

- No puede definir un constructor explícitamente, pues lo hará el compilador implícitamente.
- Puede definir los campos o atributos de instancia del tipo.

Vamos a ver un ejemplo ilustrativo para ir abriendo boca:

```
struct Coord2D
  pub const x:num = 1
  pub const y:num = 1

#similar a:
type Coord2D(vals?:{x?:num, y?:num})
  vals ?= {}

  self.{
    x ::= vals.x or 1
    y ::= vals.y or 1
  }
```

La estructura de ejemplo define que toda instancia de `Coord2D` tendrá dos campos públicos `x` e `y`.
Las visibilidades de los campos son obligatorias y se indican con las palabras reservadas: `pub`, `pvt` e `intl`.

Las estructuras definen un constructor implícito que obliga a tener que crear sus instancias a partir de un mapa u otro objeto con esos mismos campos:

```
c = Coord2D({x=1, y=2})
```

Al igual que los tipos definidos con `type`, puede contener sus propios métodos, los cuales se definen de igual manera.
La idea es facilitar la creación de determinados tipos cuya instanciación procede de un único parámetro de tipo `map`.

## Sintaxis de struct

La sintaxis de la sentencia `struct` es como sigue:

```
struct Nombre [: Nombre] [:: mixins]
  miembro
  miembro
  miembro
  ...
```

El primer nombre es el nombre del tipo, mientras que el segundo el del tipo padre si hereda.

Se puede indicar cero, uno o más estructuras *mixables*.
En caso de indicar más de una, separarlas por comas.

## Atributos

Los atributos se definen como sigue:

```
#variable
Visib var nombre                  #sin valor predeterminado ni tipo indicado/inferido.
Visib var nombre:tipo             #sin valor predeterminado y tipo indicado.
Visib var nombre?:tipo            #sin valor predeterminado y tipo indicado. Es opcional.
Visib var nombre:tipo=expresión   #con valor predeterminado y tipo indicado.
Visib var nombre?                 #sin valor predeterminado ni tipo indicado/inferido. Es opcional.
Visib var nombre=expresión        #con valor predeterminado, sin tipo indicado/inferido.
Visib var nombre:=expresión       #con valor predeterminado y tipo inferido.

#constante
Visib const nombre:tipo           #sin valor predeterminado y tipo indicado.
Visib const nombre:tipo=expresión #con valor predeterminado y tipo indicado
Visib const nombre:=expresión     #con valor predeterminado y tipo inferido.
Visib const nombre                #sin valor predeterminado ni tipo indicado/inferido.
Visib const nombre?:tipo          #Opcional, iniciado a nil si ningún valor especificado.
```

Donde la visibilidad se indica mediante las palabras reservadas `pub` (público), `intl` (interno) y `pvt` (privado).

### Anotación `@abstract`

Cuando un campo o atributo se define como `@abstract`, se está indicando que una subestructura debe definirlo:

```
@abstract
pub const constante

@abstract
pub var variable
```

### Visibilidad `pub` con la anotación `@hidden`

Cada visibilidad tiene su propio espacio de nombres.
Siendo necesario el uso del operador correspondiente para acceder a cada uno de ellos:

- `.` al espacio de nombres público.

- `:` para el espacio de nombres interno.

- `!` para el espacio de nombres privado.

Al haber un espacio de nombres para cada visibilidad, puede haber miembros homónimos en cada espacio.
Esto se resuelve añadiendo prefijos a los nombres según el operador usado para el acceso.

La única visibilidad que no añade ningún prefijo implícito es la pública.
El problema es que a veces deseamos definir un campo en el espacio de nombres público pero que sólo sea accesible si sabemos de su existencia.
Para *ocultar* su definición, se puede anotar el campo con `@hidden`.
Ejemplo:

```
@hidden
pub const oculto = 100
```

### Valor predeterminado (anotación `@strict`)

Cuando se indica un valor predeterminado, éste se usa si el valor obtenido en la instanciación es `nil` o no se ha pasado ninguno.
En el siguiente ejemplo, a `x` se le fijará el valor `123` si no se pasa ninguno explícitamente o el pasado es `nil`:

```
pub var x = 123
```

Si deseamos fijar siempre un valor de nuestra propia cosecha, omitiendo el pasado, se debe anotar el campo con `@strict`.
Con `_` se puede hacer uso del objeto pasado en la instanciación.
Ejemplo:

```
@strict
pub const context = Context(_.context)
```

Cuando se usa la anotación `@strict`, siempre hay que pasar un valor, tanto con constantes como variables.

### Anotación `@prop`

Si definimos un atributo o campo con la anotación `@prop`, el compilador creará automáticamente una propiedad pública homónima.
La idea es permitir el acceso en modo sólo lectura al campo a través de una propiedad pública.
Esto sólo se aplica cuando el campo se define como `intl` o `pvt`.

Ejemplo:

```
@prop
intl var x

#similar a:
intl var x

@prop pub fn x() = :x
```

## Métodos

También se puede indicar métodos *dentro* de la definición de la estructura.
En este caso, el nombre de la estructura queda implicito, al encontrarse dentro de la estructura, y hay que utilizar visibilidades textuales.
Ejemplo:

```
#A network.
@abstract
export struct Net
  #Network name.
  pub const name:text

  #Implementation type.
  pub const 'impl':text

  #Endpoints.
  pub const endpoints:list

  #Use Docker for starting the network.
  pub const docker:bool

  #Network state.
  intl var state = State.PENDING

  #Client.
  intl var cli = nil

  @post
  pvt proc validate()
    if not .docker then
      throw("docker field must be true.")

    for each ep in .endpoints do
      if ep not like "^(http|https|ws)://.+:[0-9]+$" then
        throw("Invalid endpoint pattern: %s.", ep)

  #Started?
  @prop
  pub fn started() = :state =~ STARTED

  ...
```

### Método `@init`

Las estructuras pueden tener un método anotado como `@init`, el cual se invoca automáticamente tras realizar la construcción de una nueva instancia.
Se utiliza para inicializar campos o realizar operaciones adicionales que no se pueden hacer con el constructor implícito.
La signatura del método es como sigue:

```
@init
pvt proc init(props)
```

En el parámetro `props` se encuentra el objeto pasado en la instanciación.

Si propaga un error, la instanciación fallará.

### Método `@post`

Las estructuras pueden tener un método anotado como `@post`, el cual se invoca automáticamente tras realizar la construcción de una nueva instancia.
Se proporciona para poder realizar revisiones de valores.
Si propaga un error, la instanciación fallará.

La signatura de este método es como sigue:

```
@post
pvt proc post()
```

### Método `@validate`

El método `@validate` se puede utilizar para validar si una instancia cumple las restricciones que tiene marcadas.
Se proporciona para poder realizar revisiones finales de valores.
Si propaga un error, la instanciación fallará.

Signatura:

```
@validate
pvt proc validate()
```

El orden de ejecución de los métodos anotados es:

1. `@init`
2. `@post`
3. `@validate`

## Miembros estáticos

Los miembros estáticos se definen con el modificador `static` a continuación de la visibilidad:

```dogma
pub static var x = 1

pub static fn método()
  #...
```

## Anotación `@sealed`

Cuando una estructura se anota con `@sealed`, sus instancias no permiten la añadidura de nuevos campos.
Ejemplo:

```
@sealed
struct Coord2D
  pub var x
  pub var y

@sealed
struct Coord3D: Coord2D
  pub var z
```

Pero ojo, si se define una subestructura, a menos que ésta se anote también con `@sealed`, sí se podrá añadir nuevos campos en las instancias de la subestructura.

## Anotación `@const`

Si una estructura se define como `@const`, sus instancias son constantes.
Esto significa que:

- No se les puede añadir nuevos campos.

- No se pueden modificar sus campos, ni aún si se han definido como variables.

Al igual que con `@sealed`, si se define una subestructura, a menos que ésta se anote también como `@const`, la restricción no se aplicará a las instancias de la subestructura.

## Expresiones diferidas

Una **expresión diferida** (*deferred expression*) es aquella que se delega para más tarde.
Se definen con `self.defer()` y se ejecutan mediante `self.deferred()`.
En este caso, hay que tener en cuenta lo siguiente:

- La estructura se debe definir con la anotación `@defer`.

- Para añadir una expresión diferida, se usa `self.defer(expresión)`.

- Para solicitar la ejecución de las expresiones diferidas, se debe hacer explícitamente con `self.deferred()`.
  En el mismo u otro método.

Ejemplo:

```
@defer
struct Connection
  #...

  pub async proc connect()
    await(self:db.connect())
    self.defer(self:db.disconnect())

    await(self:queue.open())
    self.defer(self:queue.close())

    self.state = CONNECTED
  catch e
    self.deferred()
    throw(e)

  pub async proc disconnect()
    self.deferred()
  finally
    self.state = DISCONNECTED
```

Observemos que el método `connect()` contiene expresiones diferidas.
Si su ejecución va bien, se ejecutarán en `disconnect()`.
Pero si alguna conexión falla, se captura en el `catch` y se cierra cualquier conexión abierta.

Si alguna expresión `self.deferred()` acaba en error, se continuará con el resto.
La idea es que no generen una interrupción con respecto al resto.
Aunque tras terminar, se propagará error.

Si la expresión diferida es asíncrona, no se esperará a su finalización.
Se pasará a la siguiente.

Finalmente, el orden de ejecución de las expresiones diferidas es en orden inverso,
es decir, se utiliza un algoritmo LIFO (último en entrar, primero en salir).

## Función de conversión `cast()`

Cuando sabemos que un objeto cumple la interfaz de datos de un tipo, podemos asignarle su tipo mediante la función reservada `cast()` sin necesidad de crear una nueva instancia:

```
cast<Tipo>(objeto)
```

Si el objeto indicado no es de tipo `map`, el operador propagará un error.

Ejemplo:

```
struct Coord2D:
  pub var x: num
  pub var y: num

p = {x = 0, y = 0}
print(p is Coord2D) #false

p = cast<Coord2D>(p)
print(p is Coord2D) #true
```