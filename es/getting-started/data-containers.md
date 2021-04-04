# Contenedores de datos

Un **contenedor de datos** (*data container*) es un recipiente para almacenar algo.
Se distingue entre variables y constantes.

## Variables

Una **variable** es un contenedor de datos que puede cambiar su valor almacenado en cualquier momento.
Para definir variables, se usa la sentencia `var`:

```
#una única variable
var nombre [= valor]

#varias de una vez
var nombre [= valor], nombre [= valor]...

var (
  nombre [= valor]
  nombre [= valor]
  ...
)
```

Cuando no se indica ningún valor explícitamente, se inicializan a `nil`.

Ejemplos:

```
var x, y = 123, z = "ciao mondo!"

var (
  x
  y
  z = "ciao mondo!"
)
```

## Constantes

Una **constante** (*constant*) es un contenedor que no permite modificar su valor.
Por esta razón, deben inicializarse siempre.
Se definen mediante la sentencia `const`:

```
#una única constante
const nombre = valor

#varias de una vez
const nombre = valor, nombre = valor...

const (
  nombre = valor
  nombre = valor
  ...
)
```

Ejemplos:

```
const x = 1, y = 2, z = 3

const (
  x = 1
  y = 2
  z = 3
)
```

También es posible definir constantes con la sentencia `var`.
En este caso, se utiliza el operador `::=` en vez de `=`.
Ejemplo:

```
var x = 1, y ::= 2

#es similar a
var x = 1
const y = 2
```

## Identificadores

Un **identificador** (*identifier*) es una palabra que identifica una cosa.
Se distingue entre nombres y palabras reservadas.
Un **nombre** (*name*) es aquel que identifica una función, un tipo, una variable, etc.
Mientras que una **palabra clave** (*keyword*) es aquel reservado por el lenguaje y que, en principio, no puede utilizarse como nombre.

Los identificadores deben comenzar por un subrayado o una letra, seguido de cero, uno o más caracteres (letras, dígitos o subrayados).
He aquí unos ejemplos ilustrativos:

```
x
_x
x123
x_12_34
```

Las palabras reservadas del lenguaje son:

```
and       as        async   await
break
catch     const     copy    cwait
defer     do        dogma
each      else      emit    end     enum    export    extern
false     finally   fn      for     from
if        impl      in      intf    intl    is
like
native    new       next    nil     nop     not
op        or
pawait    pcwait    peval   pr      proc    pub       pvt     pwait
return
self      static    struct    super
then      throw     true    type
until     use
var
while     with
yield
```

Si se desea, se puede utilizar palabras reservadas como nombres, pero deben delimitarse por comillas simples (`'`).
Ejemplo:

```
'var'
```

Aunque si la palabra reservada está precedida por los operadores `.`, `:` o `!`, automáticamente se considerará como nombre sin necesidad de delimitarla por comillas simples.
Esto facilita tener cosas como:

```
cx.open({
  host = "localhost"
  port = 6379
}).then(
  ...
)

#en lugar de:
cx.open({
  host = "localhost"
  port = 6379
}).'then'(
  ...
)
```

## Comentarios

Un **comentario** es un texto usado principalmente para explicar algo.
En **Dogma**, los comentarios comienzan por una almohadilla (`#`):

```
#esto es un comentario hasta fin de línea

#[esto es un comentario delimitado por corchetes
que puede expandirse en varias líneas
si así lo deseamos]
```

Observe que cuando un comentario comienza por `#[` se cierra con `]`.
Así se permite los comentarios multilínea o *in-line*.

### Comentarios de documentación

Un **comentario de documentación** (*doc comment*) se utiliza para describir detalladamente un objeto como, p.ej., un tipo o una función.
Se puede usar cualquiera de las siguientes sintaxis:

```
/**
 * comentario
 */

### comentario
```
