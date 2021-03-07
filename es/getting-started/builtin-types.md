# Tipos predefinidos

Los **tipos predefinidos** (*predefined types* o *built-in types*) son aquellos que vienen de fábrica con el lenguaje, sin necesidad de importarlos explícitamente.
Éstos son:

- `text`
- `num`
- `bool`
- `timestamp`
- `list`
- `map`
- `re`
- `set`

## Tipo text

**Dogma** proporciona el tipo `text` similar al tipo cadena de otros lenguajes.
Un texto literal se delimita entre comillas dobles (`"`) o bien entre un triple comilleado doble (`"""`).

Ejemplos:

```
"Esto es un texto literal."
"Esto es otro texto literal,
pero expandido en varias líneas."
"""Otro ejemplo."""
```

Cuando se usa `"""` hay que tener en cuenta lo siguiente:

- Si el primer/último carácter de la cadena literal es un salto de línea, se omitirá.
- Los caracteres anteriores a la columna de inicio del literal se omiten.

Esto permitirá tener cosas como la siguiente:

```
.fs.append(
  "src/plugin.dog"

  fmt(
    """ <- este salto de línea es omitido
    pub const %s = simple(
      {
        id = "%s"
        desc = "%s"
        fmt = fn(params) = nil end
      }

      use("./%s")
    ) <- este salto de línea es omtido
    """

    answers.id
    answers.desc
    answers.id
  )
)
```

Para obtener un valor cualquiera en su equivalente de texto, puede utilizar la función `text()`:

```
fn text(...args) : text
```

Ejemplo:

```
text(123)       #devuelve "123"
text(true)      #devuelve "true"
text(123, 456)  #devuelve "123456"
```

### Textos literales plantillas

Cuando se precede el texto literal con `$`, estamos ante una plantilla, la cual puede tener expresiones del tipo `${expresión}` cuyo valor se inserta en el punto en el que aparece.
P.ej., supongamos que tenemos la variable `x` cuyo valor es `123`.
Si tenemos `$"el número es: ${x}."`, el texto real será `"el número es: 123."`.
Es muy similar a las plantillas de JavaScript.

## Tipo num

El tipo `num` identifica un número entero o real.

Para obtener valores numéricos a partir de otros no numéricos, se puede utilizar la función `num()`:

```
fn num(valor) : num
```

Ejemplo:

```
num("123")  #devuelve el valor numérico 123 desde la cadena de texto 123
```

## Tipo bool

El tipo `bool` tiene los valores cierto y falso, representados mediante las palabras reservadas `true` y `false`.

## Tipo timestamp

El tipo `timestamp` representa una fecha y hora.
Mediante la instanciación del tipo, se obtiene la fecha y hora actual:

```
fn timestamp() : timestamp
```

Ejemplo:

```
const now = timestamp()
```

## Tipo list

El tipo `list` representa una lista variable de elementos, similar a las listas, colecciones o arrays de otros lenguajes.

Una lista literal se representa mediante unos corchetes seguidos de los elementos:

```
[Exp, Exp, Exp, ...]

[
  Exp
  Exp
  Exp
  ...
]
```

Cuando la lista de elementos se extiende a lo largo de la misma línea, cada elemento se separa del anterior por una coma.
En cambio, cuando se enumera uno encima de otro, la coma es opcional.

He aquí unos ejemplos ilustrativos:

```
#en la misma línea
[1, 2, 3, 4, 5]

#cada elemento en su propia línea
[
  1
  2
  3
  4
  5
]

#una mezcla de los anteriores
[
  1, 2
  3
  4, 5
]
```

Mediante la función `list()` se puede obtener una lista o hacer una copia de una lista existente:

```
fn list(l : list) : list
fn list(...items) : list
```

### Operador de indexación

Para acceder a un elemento, de una lista o a un carácter de un texto, hay que utilizar el operador de indexación (`[]`):

```
l[1]
```

Si deseamos acceder a un fragmento:

```
l[1, 3]
```

Los índices comienzan en 0.
Además, se puede utilizar valores negativos donde el índice -1 hace referencia al último elemento, -2 al penúltimo y así sucesivamente.

Con los operadores de indexación (`[]` y `[,]`), tenemos que recordar:

- **JavaScript** soporta el operador de indexación binario (`[]`), no así el ternario (`[,]`).
  El compilador desemboca en una llamada al método `slice()` cuando se usa el operador `[,]`.
  Así pues, `x[y, z]` se transformará en `x.slice(y, z)`.
  Actualmente, el tipo string y los arrays de **JavaScript** soportan este método.

- **JavaScript** no soporta índices negativos, pero puede usarlos.
  El compilador se encarga de añadir el código necesario para traducir los índices negativos a sus correspondientes positivos.

### Operadores <<< y >>>

Se puede usar el operador `<<<` para añadir al final de una lista, el equivalente del método `push()` de **JavaScript** o `append()` de **Python**; y `>>>` para añadir al comienzo:

```
l = [1, 2, 3]
l <<< 4   #l = [1, 2, 3, 4]
4 >>> l   #l = [4, 1, 2, 3]
```

A su vez, se puede utilizar las versiones unitarias para suprimir el primero o el último elemento:

```
l = [1, 2, 3, 4]
<<< l  #suprime y devuelve el primer elemento de la lista: devuelve 1 y l queda como [2, 3, 4]
>>> l  #suprime y devuelve el último elemento de la lista: devuelve 4 y l queda como [1, 2, 3]
```

## Tipo set

El tipo `set` representa un conjunto, donde nunca el mismo valor puede aparecer dos o más veces.
Para crear uno se utiliza la función `set()` o los literales `[| |]`.
Ejemplos:

```
[| valores |]
set(...valores) : set
```

Para añadir nuevos valores, se puede usar el operador binario `<<<`.
Si el valor no existe, lo añade; si existe, no lo añade, ni propaga error.
Ejemplo:

```
s = [|1, 2, 3, 4|]
s <<< 1234
```

## Tipo map

El tipo `map` se utiliza como el diccionario, objeto o mapa de otros lenguajes.

Un mapa literal se delimita entre llaves (`{}`), proporcionando los campos en formato `id = valor`:

```
#campos en la misma línea
{campo1 = valor1, campo2 = valor2, campo3 = valor3}

#cada campo en su propia línea
{
  campo1 = valor1
  campo2 = valor2
  campo3 = valor3
  ...
}
```

El nombre de un campo puede ser tanto un nombre como una palabra reservada.
En este último caso, no es necesario indicar la palabra reservada como `'palabra'`.

Al igual que con las listas, las comas no son necesarias cuando hay un salto de línea entre campos.

Ejemplos:

```
{band = "Echo & the Bunnymen", origin = "UK", year = 1978}

{
  band = "Echo & the Bunnymen", origin = "UK"
  year = 1978
}
```

Cuando el valor procede de una variable cuyo nombre es el mismo que el del campo, se puede indicar sólo el campo.
Ejemplo:

```
{band, website, year}
#similar a
{band = band, website = website, year = year}
```

Por otra parte, si el valor procede de un campo de un objeto cuyo nombre es el mismo que el del mapa literal, podemos usar la sintaxis `{nombre} = objeto`.
Ejemplo:

```
{
  {user} = opts
  {password} = opts
  {db} = opts
}

#similar a
{
  user = opts.user
  password = opts.password
  db = opts.db
}
```

Otra posibiidad es usar la siguiente sintaxis como especificador de campo:

```

{ {variable} }

#similar a:
{variable} = variable

#similar a:
variable = variable.variable
```

Ejemplo:

```
const resp = await(http.endpoint("/auth/user/&user/state").patch({ { {user} } }, { {state}=params, obs="my obs." }))
#similar a:
const resp = await(http.endpoint("/auth/user/&user/state").patch({ {user}=user }, { {state}=params, obs="my obs." }))
```

Cuando el nombre del campo es el resultado de una expresión, se puede usar la siguiente sintaxis:

```
[expresión] = valor
```

También es posible añadir a un mapa literal los campos de otro, mediante el operador de empaquetado `...`:

```
...variable
...constante
```

Ejemplo:

```
db = {host="localhost", port=5432, db="mydb"}
opts = {user="me", password="mypass", ...db}
```

En una mapa literal, se puede anidar fácilmente campos mapa sin necesidad de delimitar a estos últimos por `{}`.
Ejemplo ilustrativo:

```
#Connector metadata.
export {
  logger =
    host = $PGHOST
    port = $PGPORT
    user = $PGUSER
    password = $PGPASSWORD
    db = $PGDATABASE
    q = "log"

  db =
    host = $PGHOST
    port = num($PGPORT)
    db = $PGDATABASE
    user = $PGUSER
    password = $PGPASSWORD
    actions =
      init = async proc(req, db)
        if const token = req.token?data then
          await(db.query("CALL core.setSession($1)", [req.token.data?session]))
      end

      fin = async proc(db)
        await(db.query("CALL core.removeSession()"))
      end
}
```

Para acceder a un campo, hay que utilizar el operador punto (`.`) o el de indexación (`[]`).
Ejemplo:

```
b.band
b["band"]
```

### Operador ?

También es posible usar el operador `?`.
Mediante `.`, si el objeto en el que se busca el campo es `nil`, se propaga un error.
En cambio, con `?`, se devuelve `nil`.
Ejemplo:

```
obj?x

#similar a:
if obj != nil then obj.x else nil end
```

Se recomienda usar `.`, siempre que se sepa que el objeto no será `nil`.
En otro caso, `?`.

### Definición condicional de campos

En un mapa literal, es posible indicar que un campo sólo se debe definir si se cumple una condición.
La sintaxis de estos campos es como sigue:

```
if condición then campo = valor
```

En el siguiente ejemplo, a `m` se le asignará un objeto que siempre tendrá los campos `a` y `b`;
en cambio, `c` lo tendrá sólo si se cumple la condición indicada:

```
m = {
  a = 1
  b = 2
  if cond then c = 3
}
```

## Valor nil

El valor `nil` se utiliza para identificar el valor nulo, similar al `nil`, `null` o `None` de otros lenguajes.

## Operador ?[]

Cuando se usa el operador de indexación `[]`, tanto en listas como en mapas, es necesario que el operando a indexar no sea `nil`.
Si lo es, se propaga un error.
Para evitar este tipo de situaciones cuando sabemos de antemano que es posible que el operando izquierdo sea `nil`,
se puede usar el operador de indexación opcional `?[]`, similar al operador `?` ya presentado anteriormente.
Ejemplo:

```
obj?["campo"]
```

Cuando el operando izquierdo es `nil`, el operador devuelve `nil` sin propagar error.

## Tipo re

Mediante el tipo `re` se representa una expresión regular.
Sus literales se representan a modo de comilla invertida.
Ejemplo:

```
if texto like `^[0-9]+$` then print("Es un número.")
if texto not like re("^[0-9]+$") then print("Es un número.")
```

Mediante `re`, se puede construir una expresión más compleja, similar al `RegExp` de JavaScript:

```
type re(pattern:text, flags?:text)
```

### Operador like

Mediante el operador `like` se puede comprobar si un texto cumple una determinada expresión regular.
Sintaxis:

```
texto like expresiónRegular
texto not like expresiónRegular
```

Si se desea, se puede pasar una lista de expresiones regular para comprobar si al menos cumple una:

```
texto like [expresión, expresión...]
texto not like [expresión, expresión]
```

## Expresiones regulares literales

## Función typename()

Mediante la función `typename()` podemos obtener el nombre del tipo de un valor:

```
fn typename(valor) : text
```

Ejemplos:

```
typename("hi")          #devuelve text
typename(123)           #devuelve num
typename(true)          #devuelve bool
typename(nil)           #devuelve nil
typename([1, 2, 3])     #devuelve list
typename({x = 1})       #devuelve map
typename(set(1, 2, 3))  #devuelve set
typename(timestamp())   #devuelve timestamp
typename(fn() end)      #devuelve fn
typename(Coord2D(1, 2)) #devuelve Coord2D
typename(`pattern`)     #devuelve re
```

## Operador is

Para conocer si un valor es de un determinado tipo, podemos utilizar el operador `is`:

```
valor is tipo
valor is not tipo
```

El tipo puede ser un objeto tipo como, por ejemplo, `list`, `map`, `text` o `Coord2D` o bien una cadena literal como, por ejemplo, `"list"`, `"map"`, `"text"` o `"Coord2D"`.
Ejemplos:

```
123 is num    #sí
123 is "num"  #sí
123 is text   #no
123 is "text" #no
```

## Operador in

Mediante eñ operador `in`, y su versión negada `not in`, se pude comprobar:

- Si un texto se encuentra dentro de otro: `subtexto in texto`.

- Si un mapa contiene un determinado campo: `"campo" in mapa`.

- Si un elemento se encuentra dentro de una lista: `elemento in lista`.

## Función reservada `copy()`

Mediante la función `copy()` se obtiene una copia de un determinado valor:

```
clone = copy(val)
```
