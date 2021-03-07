# Programación asíncrona

**Dogma** soporta de manera nativa la programación asíncrona.
Para ello, se utiliza la sentencia `async`, las funciones asíncronas, las promesas y las funciones reservadas `await()` y `pawait()`.

## Sentencia `async`

Mediante la sentencia `async` se puede indicar una o más proposiciones a ejecutar asíncronamente.
Por ejemplo, cuando se compila a **JavaScript**, la sentencia `async` desemboca en un `setImmediate()`, `setTimeout()` o `setInterval()`, lo que añadirá el bloque al bucle de eventos.

La sintaxis de la sentencia es:

```
#para ejecutar asíncronamente una única proposición
async [with {opciones}] Proposición

#para ejecutar asíncronamente una o más proposiciones
async [with {opciones}]
  Bloque
[catch [Nombre]
  Bloque]
```

Mediante la cláusula `with` se puede proporcionar opciones:

- `delay`, espera mínima, en milisegundos, antes de comenzar la ejecución.
  Su valor puede ser: un número, en cuyo caso indica los milisegundos de espera; o bien, un texto con un número seguido de `s` (segundos), `m` (minutos) o `h` (horas). Por ejemplo, `60m` y `1h` representan una hora.
- `repeat`, repetir cero, una o más veces.
  Su valor puede ser un booleano o un número.
  Cuando es booleano, indica si repetir hasta el infinito y más allá.
  Si es un número, el número de veces a repetir.

Ejemplo:

```
#una única vez
async
  #...

async with {delay="2s"}
  #...

#dos veces
async with {repeat=2, delay="2s"}
  #...

#sin fin
async with {repeat=true, delay="2s"}
  #...
```

## Promesas

Una **promesa** (*promise*) representa una operación a ejecutar asíncronamente.
Son similares a los conceptos homónimos de **JavaScript** y **Python**.
Para definir una promesa, se utiliza el tipo `promise`.

El constructor del tipo `promise` es como sigue:

```
promise(proc)

#donde la función esperada tiene la siguiente signatura
proc(rslv, rej)
  #código asíncrono
end
```

El constructor de la promesa espera una función que contiene el código asíncrono.
Cuando finalice, debe ejecutar las funciones `rslv()` o `rej`().

Si el código asíncrono adjunto finaliza bien, hay que ejecutar el parámetro `rslv()`:

```
pr rslv()
pr rslv(value:any)
```

Esta función sirve para notificar que el código ha finalizado y lo ha hecho correctamente.
Si se desea, se puede pasar el valor que devuelve la función asíncrona.
Por ejemplo, si se está leyendo el contenido de un archivo, su contenido.

Por otra parte, tenemos la función parámetro `rej()`, la cual se debe utilizar para notificar que la función ha acabado y lo ha hecho con error:

```
proc rej()
proc rej(error)
```

Si se desea, se puede pasar el error en cuestión.

Cada vez que se crea una promesa, automáticamente se añade a la cola de ejecución asíncrona.
Como ya sabemos, el código asíncrono se debe pasar como una función al constructor de la promesa.
Pero cómo sabemos o somos notificados de que la promesa ha finalizado para así poder continuar.
Fácil: mediante los métodos de instancia `then()` y/o `catch()` de la propia promesa.

Cuando la promesa finalice correctamente, es decir, la función que tiene asociada invoque su parámetro `rslv()`, la promesa ejecutará la función registrada mediante su método `then()`:

```
then(fn)
```

En cambio, si finaliza en error, o sea, su función asociada invocó `rej()`, ejecutará la definida mediante su método `catch()`:

```
catch(fn)
```

Ambos métodos esperan funciones a ejecutar.
Y en ambos casos, pueden definirse con o sin parámetro, a través del cual recibir el valor pasado, por la función adjunta a la promesa, en su llamada a `rslv()` o `rej()`.
Si se desea, se puede usar la siguiente sobrecarga del método `then()` que permite indicar ambos controladores:

```
then(fn, fn)
```

El primer parámetro hace referencia a la función de éxito; y el segundo, a la de error.

He aquí un ejemplo ilustrativo, donde `pool.connect()` se utiliza para obtener una conexión asíncronamente, mediante una promesa:

```
pool.connect().then(proc(cxn)
  #lo que hacer cuando la conexión se haya establecido con éxito
end).catch(proc(err)
  #lo que hacer si se produce un error durante la conexión
end)
```

## Funciones asíncronas

Una **función asíncrona** (*async function*) permite lidiar más fácilmente con operaciones asíncronas en un mismo bloque de código, de manera muy similar a como lo hacen otros lenguajes como **C#**, **JavaScript** o **Python**.
Su intención es ayudar a escribir funciones asíncronas como si fueran síncronas que son mucho más fáciles de leer y escribir.

Se definen mediante la cláusula `async` delante de la palabra reservada `fn` o `proc`, ya sea de una función con nombre o anónima.
Y *siempre devuelven una promesa* de manera implícita. Es importante que tenga esto muy claro.
Cuando se define una función asíncrona, automáticamente se está definiendo una promesa.
La idea es muy sencilla: ejecutar el código de la función dentro de una promesa (implícita), de tal manera que si la función finaliza bien, su valor devuelto se pasará implícitamente a `rslv()`, mientras que si acaba en error, el error se pasará al `rej()`.

Cuando dentro de la función asíncrona tengamos que ejecutar código asíncrono, el cual debe devolver siempre una promesa, éste se tiene que ejecutar mediante la función reservada `await()`, la cual ejecuta la función indicada y espera su finalización.
Si la función asociada acaba bien, el valor devuelto por su `rslv()` será devuelto por `await()`; en otro caso, `await()` propagará el error devuelto por `rej()`.

La signatura de la función reservada `await()` es como sigue:

```
await(Expresión) : any
```

Ejemplo de definición de función asíncrona:

```
async fn DataCxnUtil.openPostgresPool(opts:{host:text,port:num,db:text,user:text,password:text,pool?:map})
  const Pool = use("pg").Pool

  #(1) get pool object
  opts = opts{*,db=>database}
  opts.min = opts?pool?min or 0
  opts.max = opts?pool?max or 10

  #(2) connect to test options
  const pool = native("new Pool(opts)")
  const cxn = await(pool.connect())
  cxn.release(nop)
  return pool
```

En el ejemplo anterior, cuando se ejecuta `await(pool.connect())`, se espera a que `pool.connect()` finalice.
Si todo va bien, esta función devuelve la conexión a utilizar.
Pero si algo va mal, `await()` propagará el error devuelto por `pool.connect()`.

También es posible utilizar la función reservada `pawait()` para esperar la terminación de código asíncrono:

```
pawait(Expresión) : list
```

La función espera como argumento la función asíncrona a ejecutar bajo modo protegido.
Devuelve una lista de dos elementos, donde: el primero indica si acabo correctamente; y el segundo, el resultado o el error capturado o devuelto.
Esta función se utiliza cuando *no* se desea propagar un error, sino capturarlo.
Ejemplo:

```
if const [ok, res] = pawait(pool.connect()); ok then
  const cxn = res
  cxn.release(nop)
else
  printf("Se ha producido un error: %s.", res)
```

### Espera múltiple

También es posible esperar la finalización de varias promesas, usando las siguientes signaturas:

```
#mediante promesas pasadas como argumentos
await(promesa1, promesa2, ...)

#mediante lista de promesas
await(...lista)
```

Cuando se usa una espera múltiple, hay que tener en cuenta la función usada.

Cuando se utiliza `await()`:

- La función devuelve una lista con los valores devueltos por la promesa,
  si todas acaban bien.

- La función propaga un error para la primera promesa finalizada en error,
  propagando el error devuelto por ésta.

Por su parte, si usamos `pawait()`:

- La función espera a que todas las promesas acaben, devolviendo una lista de dos elementos:
  el primero es `true` si todas acabaron bien;
  por su parte, el segundo contiene los valores devueltos.
  Algo como: `[ok, valores]`.

- El segundo valor devuelto contiene una lista de ítems, cada uno de los cuales es otra lista de dos elementos:
  el primero, un valor booleano, que indica si la promesa acabo bien;
  y el segundo que contiene el valor devuelto si el primero es `true` o bien el error propagado en otro caso.
  Algo así como: `[[ok, valor], [ok, valor], ...]`.

## Funciones reservadas `cwait()` y `pcwait()`

En **Node.js**, es muy común ejecutar funciones asíncronas pasando una función de *callback*, es decir, una función a través de la cual notificar que la llamada ha finalizado.
Este tipo de funciones suelen tener la siguiente signatura:

```
done(error, resultado)
```

Para ayudar a trabajar con este tipo de funciones, se puede utilizar las funciones reservadas `cwait` y `pcwait`.
Con `cwait()`, la ejecución espera hasta que la función asíncrona finaliza.
Como función de *callback*, hay que pasar `done`.
Ejemplo:

```
const accts = cwait(eth.getAccounts(done))

#similar a:
const accts = await(promise(proc(rslv, rej)
  proc done(err?, rslt?)
    if err then rej(err)
    else rslv(rslt)

  eth.getAccounts(done)
end))
```

Por su parte, la función `pcwait()` es similar a la anterior pero trabajando en modo protegido, esto es, devolviendo una lista, tal como hace `pawait()`.

## Función predefinida `promisify()`

La función `promisify()` se puede utilizar para obtener una función asíncrona para funciones de *callback*.
Es similar a la función `util.promisify()` de **Node.js**.
Su definición es como sigue:

```
fn promisify(func:func) : func
```

Ejemplo:

```
const readFile = promisify(fs.readFile)
const content = await(readFile("/my/file.txt", "utf8"))
```

## Bucle `await for each`

Con `await for each` podemos recorrer un objeto iterable (a)síncrono.
Ejemplo:

```
await for each msg in socket do
  print("work:", text(msg))
```