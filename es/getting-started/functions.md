---
layout: page
previous: ./flow-control
next: ./errors
---

# Funciones

Una **función** (*function*) representa un objeto de código llamable con o sin argumentos.
En **Dogma**, las funciones se definen mediante la sentencia `fn`:

```
fn nombre(parámetros)
  Bloque

fn nombre(parámetros) = expresión
```

La primera sintaxis define una función cuyo cuerpo está formada por las proposiciones indicadas.
En caso de alcanzarse el final de la función, sin un `return`, la llamada devolverá `nil`.
Ejemplo:

```
fn suma(x, y)
  return x + y
```

La segunda sintaxis devuelve el valor resultante de ejecutar la expresión adjunta.
Ejemplo:

```
fn suma(x, y) = x + y
```

## Parámetros

Un **parámetro** (*parameter*) es un contenedor de datos local cuyo valor inicial se obtiene de un argumento pasado a la función en su llamada.
Siguen las siguientes sintaxis:

```
Nombre
Nombre = Expresión
Nombre : Tipo
Nombre : Tipo = Expresión
Nombre := Expresión
```

Vamos a analizar un poco más los parámetros.
En primer lugar, aunque **Dogma** es un lenguaje dinámico, se recomienda encarecidamente indicar los tipos de los parámetros.
Para ello, usar dos puntos seguidos del nombre del tipo.
Ejemplo:

```
fn sum(x: num, y: num) = x + y
```

De manera predeterminada, todos los parámetros son *obligatorios*, requiren un valor distinto de `nil`.
Cuando `dogmac` genera código, por ejemplo, a **JavaScript**, añadirá automáticamente proposiciones con las que comprobar que los argumentos pasados a la función cumplen las restricciones de tipo, propagando un error con un mensaje similar a `nombreParámetro expected.`.
De esta manera, no tendremos que añadirlo nosotros mismos.

### Parámetros de múltiples tipos

Si se desea, se puede indicar varios tipos para un parámetro.
En estos casos, delimitarlos entre paréntesis:

```
(Nombre, Nombre, Nombre...)
```

Ejemplo:

```
fn suma(x: (text,num), y: (text,num)) = num(x) + num(y)
```

Además de indicarse varios tipos, también es posible indicar que uno de ellos será el final.
Para ello, todos los demás se pasarán a uno de ellos.
En estos casos, la sintaxis es como sigue:

```
Tipo(OtroTipo1, OtroTipo2)
```

P.ej., es posible convertir el valor de un parámetro a uno de tipo lista, de manera implícita como sigue:

```
fn sum(values: list(num))
  var res = 0
  for each val in values do res += val
  return res
```

Lo anterior es lo mismo que definir el parámetro `values` como `values: (list,num)` y, además, añadir en el cuerpo de la función la expresión `if values is not list then values = list(values)`.
De esta manera, esta expresión no hay que añadirla explícitamente.

### Parámetros de tipo lista

Para una comprobación de tipo lista, usar:

```
#sin importar su tamaño
tipo[]

#indicando el tamaño exacto
tipo[tamaño]

#indicando un tamaño mínimo
tipo[min..*]

#indicando tamaño mínimo y máximo
tipo[min..max]
```

Ejemplos:

```
fn sum(values: num[2..*])
  var res = 0
  for each val in values do res += val
  return res
```

### Parámetros por copia

En **Dogma**, se puede indicar que se haga una copia del argumento pasado precediendo al tipo de un `*`.
Ejemplo:

```
fn f(x: num, y: *map)
  #aquí y tendrá una copia de su argumento
  #cualquier modificación de y no afectará a su argumento
```

### Parámetros opcionales

Si deseamos que el parámetro sea opcional, indicar un `?` después del nombre del parámetro:

```
Nombre ?
Nombre ? : Tipo
```

Cuando no se indica ningún tipo y además no se indica `?`, el compilador añadirá las proposiciones para comprobar que el parámetro recibe un valor, sin importar su tipo.
Así pues, en la siguiente función se realizará la comprobación de que al menos se recibe valor:

```
fn sum(x, y) = x + y
```

Si no deseamos que la haga:

```
fn sum(x?, y?) = x + y
```

Cuando se indica `?:Tipo` lo que se está indicando es: en caso de pasar un valor, deberá ser del tipo indicado.

### Valores predeterminados de los parámetros

Para indicar el valor predeterminado del parámetro, indicarlo tras un `=`.
Si se utiliza `:=`, se puede omitir el tipo, el cual se infiere de la expresión de asignación.
Por ejemplo, de `x := 123` el compilador inferirá que el tipo de `x` debe ser `num`.
Es la manera compacta de escribir `x : num = 123`.

### Parámetros constantes

Los parámetros constantes se definen precediéndolos de la palabra reservada `const`.
Ejemplo:

```
fn sum(const x, const y) = x + y
```

### Parámetro de resto

Finalmente, el **parámetro de resto** (*rest parameter*), aquel al que se le asigna el resto de argumentos pasados a la función, se define mediante `...`:

```
...Nombre
```

Ejemplo:

```
fn sum(x, y, ...args)
  var res = x + y
  for each i in args do res += i
  return res
```

### Parámetros de tipo mapa

Cuando el parámetro es de tipo `map`, podemos hacer básicamente dos cosas:

- Indicar que se pasa un argumento de tipo `map`, indicando este tipo.
- Indicar un mapa que describa los campos del argumento pasado.

En este segundo caso, como tipo indicar un mapa que lo describa.
Ejemplo:

```
persona : {nombre:text, apellidos:text}
```

Los campos se pueden indicar como sigue:

```
Nombre
Nombre : Tipo
Nombre ?
Nombre ? : Tipo
```

Este mapa sólo describe los campos que deseamos se validen.
Podrán aparecer otros, pero no se validarán.
Cuando se indica un campo como opcional, mediante `?`, el tipo se utilizará sólo cuando tenga un valor distinto de `nil`.
Si se desea indicar varios tipos para un campo, delimitarlos entre paréntesis.

### Anotación `@noParamCheck`

Cuando una función se anota con `@noParamCheck`, el compilador no realiza la comprobación de tipos de los parámetros. Ejemplo:

```
@noParamCheck
fn sum(x: num, y: num) = x + y
```

### Tipos literales

Además de indicar un tipo como, por ejemplo, `bool`, `num` o `text`, también es posible indicar valores literales.
He aquí unos ejemplos:

```
x:true
x:false
x:"hello"
x:123
x:(true, "true")
```

### Desempaquetado de parámetro

Es posible desempaquetar un parámetro de tipo lista o mapa en varios parámetros adicionales.
Esto se consigue mediante las siguientes sintaxis:

```
#desempaquetado de lista
parámetro => [variable,variable,variable...]

#desempaquetado de mapa
parámetro => {variable,variable,variable...}
```

He aquí un ejemplo ilustrativo, donde el parámetro lista `params` asigna sus tres primeros elementos a las variables/parámetros `item`, `state` y `obs`:

```
export async fn setItemState(params=>[item,state,obs], http)
  const resp = await(http.endpoint("/dms/item/&item/state").patch({item}, {state,obs}))

  #!cov ignore else
  with resp.status()
    if 204 then return
    else throw("unknown status received: %s.", _)
```

Si `params` fuese un mapa, la definición siguiente `params=>{item,state,obs}` asignará los campos homónimos a las variables/parámetros `item`, `state` y `obs`.

En el caso de los mapas, también se puede utilizar la siguiente sintaxis, la cual permite indicar los tipos de los campos a desempaquetar:

```
parámetro :> {variable?:tipo, variable?:tipo, ...}

#similar a:
parámetro : {campo?:tipo, campo?:tipo, ...} => {campo, campo, ...}
```

### Función reservada expect()

En algunas ocasiones, no es posible delegar la comprobación de tipos al compilador, sino que tenemos que hacerla manualmente.
Para ayudar, **Dogma** proporciona la función `expect()` con la que comprobar el tipo de un parámetro:

```
expect(parámetro) : any
expect(parámetro, tipo) : any
```

Cuando la función recibe un argumento, comprueba si el valor recibido es distinto de `nil`.
Si es así, devuelve el valor; en otro caso, propaga `parámetro expected.`, donde `parámetro` es el valor del argumento pasado.
Como segundo argumento, se puede indicar el tipo del valor.
Ejemplos:

```
#comprobación sin tipo
expect(x)

#comprobación con tipo
expect(x, num)
```

## Tipo de retorno

Además del tipo de los parámetros, se puede indicar, a modo informativo, el tipo del valor de retorno, añadiendo `: Tipo` al final de la signatura.
He aquí un ejemplo ilustrativo:

```
fn sum(x:num, y:num) : num
  return x + y

fn sum(x:num, y:num) : num = x + y
```

## return

Mediante la sentencia `return` se marca el fin de la función y se puede indicar el valor a devolver:

```
return
return Expresión
```

Si la función consiste en una expresión a devolver, mediante la sentencia `return`, se puede definir con la siguiente sintaxis:

```
fn Nombre(Parámetros) = Expresión
```

Que es similar a:

```
fn Nombre(Parámetros)
  return Expresión
```

Si la función devuelve el valor de una variable o parámetro, se puede indicar en la signatura de la función mediante la cláusula `->`.
He aquí un ejemplo:

```
fn suma(x, y) -> res
  res = x + y
```

Lo anterior es similar a:

```
fn suma(x, y)
  var res
  res = x + y
  return res
```

Observe que cuando se usa `->`:

- No hace falta indicar el `return`, queda implícito.
  El compilador añadirá una sentencia `return` al final del cuerpo de la función.

- La variable indicada en `->` se autodefine, si no es un parámetro ni el objeto `self`.
  Por lo que no tenemos que definirla explícitamente mediante una sentencia `var` en el cuerpo de la función.

Cuando a la variable de retorno se le adjunta el tipo de dato `bool`, `list`, `map`, `num` o `text`, la variable se inicia a `false`, `[]`, `{}`, `0` y `""`, respectivamente.
Ejemplo:

```
fn sum(x, y) -> res:list
  res <<< x+y

#similar a:
fn sum(x, y)
  var res = []
  res <<< x+y
  return res
```

## catch y finally

Las funciones pueden definir cláusulas `catch` y `finally`:

```
fn Nombre(Parámetros) : Tipo
  Bloque
catch [e]
  Bloque
finally
  Bloque
```

El código adicional añadido por el compilador para las restricciones de tipo de los parámetros pasará por encima de la cláusula `catch`.
Sólo el código explícito definido en el cuerpo de la función podrá ser capturado por `catch`.

Ejemplo:

```
fn createToken(req, opts:map) -> tok:Token
  var enc

  #(1) get token text
  if opts.authorization then
    if (enc = req.headers.get("Authorization")) and enc like "^Bearer " then
      enc = enc.replace(RegExp("^Bearer +"), "")

  if not enc and opts.cookie then enc = req.cookies.get(opts.cookie)

  #(2) verify and decode
  tok = Token(jwt.verify(enc, if opts.alg like "^HS*" then opts.secret else opts.key end, {
    algorithm = opts.alg
    issuer = opts.iss
    audience = opts.aud
    ignoreExpiration = if "exp" in opts then opts.exp == false end
  }))
catch
  return Token({})
```

## Invocación de funciones

Para invocar una función, se utiliza los paréntesis, al igual que en la mayoría de lenguajes.
Ejemplo:

```
print(sum(1, 2, 3))
```

### Argumentos

En **Dogma**, el paso de argumentos a los parámetros es posicional.
El primer argumento se asigna al primer parámetro;
el segundo argumento al segundo parámetro;
y así sucesivamente.

Pero permite lo que se conoce como **argumentos nombrados** (*named arguments*).
El compilador lo que hace es coger los argumentos nombrados, generar con ellos un mapa/objeto el cual pasa como último argumento.
Así, p.ej., lo siguiente:

```
f(1, 2, a=3, b=4)
```

Es lo mismo que:

```
f(1, 2, {a=3, b=4})
```

En **Dogma**, a diferencia de otros lenguajes como **Python**, no existe un parámetro especial que recibe los argumentos nombrados.
Simplemente los agrupa y los pasa como último argumento.

Aspectos a tener en cuenta:

- Una vez se indica un argumento con nombre, los restantes se considerarán como tales.
  Así pues, si indicamos, p.ej., como argumento únicamente un nombre de variable como, p.ej, `x`, el argumento se considerará como `x=x`.
  De manera similar a como sucede con los mapas literales.

- Es posible indicar un argumento nombrado como sigue: `nombre=`.
  Esto es similar a `nombre=nombre`.
  Generalmente, se utiliza cuando se desea indicar un argumento cuyo valor procede de una variable homónima;
  y además, es el primero de la lista de argumentos nombrados.
  Sin el `=`, el compilador lo consideraría un argumento posicional.
  Una vez estamos *dentro* de la lista de argumentos nombrados, el `=` es opcional.

- Si se desea hacer una asignación en una expresión de un argumento posicional, cómo se hace.
  Fácil, no hay más que rodearla por paréntesis.
  Ejemplo: `f(1, (x=2), a=1, b=2)`.
  Al rodear la expresión de asignación, que no deseamos pasar como argumento nombrado, el compilador la tratará como un argumento posicional.

Los argumentos nombrados son muy útiles en la creación de instancias de estructuras.
Evita el uso de los corchetes (`{}`).
Ejemplo:

```
struct Coord2D
  pub const x: num
  pub const y: num

const zero = Coord2D(x=1, y=2)  #similar a: const zero = Coord2D({x=1, y=2})
```

## Funciones anónimas

Cuando sea necesario definir una función anónima en una expresión, usar una de las siguientes sintaxis:

```
fn(Parámetros) = Expresión end

fn(Parámetros) : Tipo = Expresión end

fn(Parámetros)
  Bloque
end

fn(Parámetros) : Tipo
  Bloque
end

fn(Parámetros) -> variable
  Bloque
end

fn(Parámetros) -> variable:Tipo
  Bloque
end
```

Ejemplo:

```
var scores = [1, 10, 21, 2]

#una manera
scores.sort(fn(a, b) = a - b end)

#otra manera
scores.sort(fn(a, b)
  return a - b
end)
```

En las funciones anónimas:

- No hay que indicar un nombre.
  Por eso son anónimas.
- No hay que olvidar finalizar la función con `end`.

## Procedimientos

Un **procedimiento** (*procedure*) es una función que no devuelve nada.
Se escriben de manera similar a las funciones, pero sin tipo de retorno y con las palabras reservadas `pr` o `proc`.
Ejemplo:

```
init("*", proc()
  http = HttpMessenger({domain="http://localhost:8080", path="/backend"})
end).title("Create http object")
```

Cuando un procedimiento dispone de una sentencia `return`, termina su ejecución.
Si además esta sentencia tiene adjunta una expresión, se ejecuta ésta, pero el procedimiento sigue sin devolver nada.

## Sobrecargas

La **sobrecarga** (*overloading*) permite definir varias funciones o procedimientos con el mismo nombre, pero distinta lista de parámetros.
**Dogma** no permite la sobrecarga, pero sí permite indicar distintas maneras de invocar una función.
Al final, siempre debe haber una definición de función que será invocada y atenderá todas las posibles sobrecargas.

### Anotación `@overload`

Mediante la anotación `@overload`, se indica una posible manera de invocar una función.
Pero sin definir su cuerpo.
La definición que atenderá todas las posibles sobrecargas, se define sin `@overload`.

He aquí un ejemplo ilustrativo:

```
/**
 * Suma dos números pasados en textos.
 */
@overload
fn sum(x: text, y: text): num

/**
 * Suma dos números.
 */
@overload
fn sum(x: num, y: num): num

/**
 * Suma dos números pasados como texto o número.
 */
fn sum(x: (text,num), y: (text, num)) -> num
  return num(x) + num(y)
```

Cuando el compilador se encuentra con una función marcada como `@overload` no la compila.
Se usan para informar fácilmente de un posible tipo de invocación.

### Función reservada `overload()`

A veces, tenemos funciones o procedimientos con sobrecargas muy diferentes.
Si se desea, se puede usar la función `overload()` para determinar si la invocación actual cumple con una determinada lista de parámetros:

```
fn overload(tipo1, tipo2, ...): bool
```

Esta función recibe los tipos de los parámetros y devuelve si los argumentos pasados se cumplen.
Ejemplo:

```
fn sum(x, y): num
  if overload(num, num) then
    nop
  else if overload(text, text) then
    x = num(x)
    y = num(y)
  else
    throw(TypeError("Invalid arguments."))

  return x + y
```

Esta función es muy especial.
Y permite que los tipos se indiquen de la siguiente manera:

```
#valor obligatorio y del tipo indicado
tipo

#valor opcional y, en caso de ser pasado, del tipo indicado
?tipo

#resto de argumentos no tenerlos en cuenta son opcionales y no
#nos importa su tipo
...
```

Ejemplo:

```
#comprueba si se llamó sin argumentos
overload()

#comprueba si se llamó la función con sólo dos argumentos de tipo num
overload(num, num)

#comprueba si se llamó la función con dos o más argumentos,
#siendo los dos primeros de tipo num, decartando todos los demás
overload(num, num, ...)

#comprueba si se llamó la función con 2, 3 ó más argumentos:
#los dos primeros son obligatorios y de tipo num;
#el tercero, en caso de aparecer, debe ser también de tipo num;
#los demás argumentos no nos interesan
overload(num, num, ?num, ...)
```
