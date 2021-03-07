# Desempaquetado

El **desempaquetado** (*unpacking*) es una operación que permite extraer fácilmente objetos de listas y mapas.
En **Dogma**, podemos usar expresiones de desempaquetado de listas y mapas, así como el operador de desempaquetado en línea.

## Expresiones de desempaquetado

Por un lado, tenemos un tipo especial de expresión con la que se puede asignar elementos de una lista o mapa a varios contenedores de datos.

### Expresión de desempaquetado de lista

Por un lado, tenemos el desempaquetado de una lista.
Consiste en asignar a una, dos o más variables los elementos consecutivos de una lista.
Cada variable recibe el elemento de la lista que le corresponda por su posición.
Así pues, la primera variable recibirá el elemento `0`; la segunda, el `1`; y así sucesivamente.
Si se indica más variables que elementos tiene la lista, a las que no reciban valor se les asignará `nil`, a menos que indiquemos un valor explícitamente en la expresión de desempaquetado.
Su sintaxis es como sigue:

```
[variable, variable, variable...] = Exp
```

Cada variable tiene el siguiente formato:

```
Id
Id.Id
Id = Exp
Id.Id = Exp
```

Si el valor devuelto por la lista es `nil`, se puede indicar el valor que debe asignarse en su lugar.
Para ello, se indica el valor predeterminado de la variable siguéndola de un `=` y el valor en cuestión.

Existe una variable para recibir el resto de elementos de la lista.
Su sintaxis es muy similar al del parámetro de resto:

```
...Id
...Id = Exp
```

Ejemplo:

```
[x, y, z, ...rest] = [1, 2, 3, 4, 5, 6]

#aquí tendremos:
#  x = 1
#  y = 2
#  z = 3
#  rest = [4, 5, 6]
```

Además del operador `=`, podemos usar la expresión de desempaquetado con los operadores:

- `.=`, lo que creará campos privados y propiedades púbicas de sólo lectura para el acceso a los campos privados.
- `:=`, lo que creará campos de sólo lectura, en cuyo caso, deben indicar los operadores `.` o `:`.
- `?=`, el cual permite asignar a las variables del lado izquierdo el valor del lado derecho sólo si su valor actual es distinto de `nil`.

Veamos esto con un ejemplo ilustrativo:

```
[opts.host, opts.port, opts.db] ?= ["localhost", 6379, 0]

#similar a:
if opts.host == nil then opts.host = "localhost"
if opts.port == nil then opts.host = 6379
if opts.db == nil then opts.host = 0
```

Cuando se desea trabajar con varios campos de un mismo objeto, se puede utilizar la sintaxis `variable{id,id,id...}`.
Así pues, las dos líneas siguientes hacen lo mismo:

```
[opts.host, opts.port, db] ?= ["localhost", 6379, 0]
[opts{host,port}, db] ?= ["localhost", 6379, 0]
```

Para indicar campos protegidos y/o privados, indicar `:` o `!` antes del nombre.
Ejemplo: `opts{host,:port}`.

### Expresión de desempaquetado de mapa

También es posible desempaquetar a partir de los miembros/campos de un mapa u objeto.
En este caso, las variables se delimitan entre llaves (`{}`) en vez de entre corchetes (`[]`):

```
{variable, variable, variable...} = Exp
```

Ejemplo:

```
{band, website} = {band = "The National", origin = "US", year = 1999, website = "americanmary.com"}

#aquí:
#  band = "The National"
#  website = "americanmary.com"
```

Con el desempaquetado de mapas, no se puede utilizar el operador `?=`, ni las variables de tipo `a.b`, `a:b` y `...resto`.

## Definiciones de desempaquetado

Existen versiones de desempaquetado para las sentencias `var` y `const`.
Así, por ejemplo, para el desempaquetado de una lista tenemos:

```
var [variable, variable, variable...] = Exp
const [constante, constante, constante...] = Exp
```

Las variables declaradas pueden seguir las siguientes sintaxis:

- `nombre`. Variable a crear. Si no recibe ningún valor, se inicializará a `nil`.
- `nombre = expresión`. Cuando se indica una expresión, si la variable no recibe un valor, se inicializará al valor indicado.
- `...nombre`. Cuando se indica este formato, la variable recibirá los restantes elementos de la lista, o sea, aquellos no asignados a ninguna de las variables anteriores.
- `_`, para aquellos casos en los que deseamos omitir el valor recibido.

He aqui un ejemplo ilustrativo:

```
var [x, _, y, ...z] = [1, 2, 3, 4, 5]

#aquí:
# x = 1
# el valor 2 se omite por el uso de _
# y = 3
# z = [4, 5]
```

También está disponible una versión para mapas u objetos:

```
var {variable, variable, variable...} = Exp
const {constante, constante, constante...} = Exp
```

Ejemplo:

```
var {name, website} = {
  name = "Phoenix"
  origin = "FR",
  website = "wearephoenix.com"
}

#aquí:
# name = "Phoenix"
# website = "wearephoenix.com"
```

## Operador de desempaquetado en línea

También se encuentra disponible el operador de desempaquetado en línea, representado por `...`, que tiene como objeto una lista en una llamada a función, simulando que escribimos cada uno de sus elementos uno detrás de otro.

Por ejemplo, supongamos la siguiente función de ejemplo:

```
fn sum(...args) -> res:num
  for each arg in args do res += arg
```

Está claro que la función puede aceptar varios argumentos, tal como se muestra a continuación:

```
sum(1, 2, 3, 4) #devuelve 10
```

La idea del operador de desempaquetado en línea, `...`, es que si tenemos los argumentos a pasar en una lista podamos hacerlo como si estuviéramos escribiéndolos uno detrás de otro en la llamada. Así pues, lo siguiente:

```
l = [1, 2, 3, 4]
sum(...l) #devuelve 10
```

es lo mismo que escribir:

```
sum(1, 2, 3, 4)
```
