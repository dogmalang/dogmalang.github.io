# Control de flujo

El **control de flujo** (*flow control*) hace referencia a las sentencias que permiten ejecutar unas proposiciones u otras según determinadas situaciones.

## Sentencia if

La sentencia `if` permite seleccionar un bloque u otro a partir de unas condiciones.
Al igual que sus homónimos de otros lenguajes.
La sintaxis de esta sentencia es como sigue:

```
#sin declaración implícita de variable/constante
if Expresión then
  Bloque
else if Expresión then
  Bloque
else
  Bloque

#con definición de variable local
if var nombre = Expresión then
  Bloque
else if Expresión then
  Bloque
else
  Bloque

if nombre := Expresión then
  Bloque
else if Expresión then
  Bloque
else
  Bloque

#con definición de constante local
if const nombre = Expresión then
  Bloque
else if Expresión then
  Bloque
else
  Bloque

if nombre ::= Expresión then
  Bloque
else if Expresión then
  Bloque
else
  Bloque
```

Se puede utilizar tantos `else if` como sea necesario y como máximo un único `else`.
Siempre deben estar alineados al `if` origen.

Ejemplo:

```
if db ::= svc.db then
  const cli = await(db.acquire())
  args.db = cli
  #...
else
  nop
```

La constante `db` estará disponible tanto en el bloque `if` como en sus hermanos `else if` y `else`.
Dejando de existir fuera de toda la sentencia `if`.

En algunas ocasiones, se desea proporcionar un `if` con o sin `else` en una única línea.
Esto es posible usando las siguientes sintaxis:

```
if Expresión then Proposición
if Expresión then Expresión else Proposición
```

Existe una versión de la sentencia `if` mediante la cual se puede, por un lado, indicar una inicialización y, por otro lado, la condición del primer `if`:

```
if Inicializador; Condición then...
#similar a
Inicializador
if Condición then

if Definición; Condición then...
#similar a
Definición
if Condición then
```

Esto nos permite tener cosas como:

```
if [ok, err] ::= pawait(init.call(args)); not ok then
  cli.release(nop)
  return rej(err)
```

### Operador if

Para implementar el operador `?:` de **C**, **C++**, **JavaScript** y otros lenguajes, utilizar la siguiente sintaxis:

```
if condición then expresión end
if condición then expresión else expresión end
```

No olvide el `end` final.
Por otra parte, cuando no se indica `else`, devuelve `nil` si la condición no se cumple.

Ejemplo:

```
const resp = if eb == nil then
  await(http.endpoint("/pi/param/PRO_INT/tmpl").get())
else
  await(http.endpoint("/pi/param/PRO_INT/tmpl/&eb").get({eb}))
end
```

Si lo deseamos, podemos utilizar una multiexpresión como la siguiente:

```
const res = if eb == nil then (
  #una expresión
  #otra
  #y la última es la que se devolverá
) else (
  #ídem al then
  #la última es la que se devolverá
)
```

Cuando el `else` es una multiexpresión, el `end` no es necesario.

## Sentencia with

La sentencia `with` facilita la escritura de sentencias `if`s.
Es similar al `switch` o `case` de otros lenguajes.
Su sintaxis es la siguiente:

```
with Exp do
  if Exp then
    Bloque
  if Exp then
    Bloque
  ...
  else
    Bloque
```

Las cláusulas `if` pueden tener varias expresiones, solo hay que separarlas por comas.

Ejemplo ilustrativo:

```
with len(args) do
  if 0, 1 then #...
  if 2 then #...
  else #...
```

Si se desea, se puede asignar el valor a una variable o constante para su utilización dentro de la sentencia.
Recuerde: `:=` para definir variables y `::=` para constantes.
Ejemplo:

```
with status ::= resp.status() do
  if 204 then return
  if 404 then throw("not found: %s.", org)
  else throw("unknown status received: %s.", status)
```

Si lo que se desea es seleccionar a partir del tipo de un valor, se puede usar la siguiente sintaxis:

```
with type(valor) do
  if tipo then #...
  if tipo then #...
  else #...
```

### Operador with..do

Se puede usar el operador `with..do` de manera similar a la sentencia homónima.
Ejemplo:

```
status = with resp.status() do
  if 204 then "ok"
  if 400 then "not found"
  else "other thing"
end
```

## Sentencia while

La sentencia `while` se utiliza para iterar mientras se cumple una determinada condición:

```
while ExpresiónODeclaración do
  Bloque

while ExpresiónODeclaración do Proposición

while Declaración; Expresión do
  Bloque

while Expresión; Expresión do Proposición
```

El `while` debe tener una expresión que actúa como condición de iteración.
Y si es necesario una declaración de variable.
Si se indican ambas, separar las expresiones por punto y coma (`;`).

Ejemplo:

```
while x := 1; x < 100 do
  print(x)
  x += 1
```

Lo anterior es similar a lo siguiente en **JavaScript**:

```
for (let x = 1; x < 100; ) {
  print(x)
  x += 1;
}
```

## Sentencia for

La sentencia `for` de **JavaScript** y de otros lenguajes se puede implementar también en **Dogma**:

```
for Variables; Expresión do
  Bloque

for Variables; Expresión do Proposición

for Variables; Expresión; Expresión do
  Bloque

for Variables; Expresión; Expresión do Proposición
```

La primera sección se puede utilizar para definir variables locales del bucle.
Cada variable se separa de la anterior por una coma (`,`), no siendo necesario preceder la sección de `var`, pues es implícita a la sección.
La segunda es la expresión condicional de iteración.
Y la tercera la de actualización, la cual es opcional.

Ejemplo:

```
for x = 1; x < 100; x += 1 do
  print(x)
```

## Sentencia for each

También está disponible la versión `for each` para iterar a través de objetos iterables:

```
#para iterar por listas
for each Nombre in Expresión do
  Bloque

for each Nombre in Expresión do Proposición

#para iterar por mapas
for each Nombre, Nombre in Expresión do
  Bloque

for each Nombre, Nombre in Expresión do Proposición
```

Ejemplo:

```
for each x in seq(1, 100) do
  print(x)

for each clave, valor in objeto do
  print(clave, valor)
```

En vez de `in`, también se puede usar `:=` y `::=`.
`::=` es similar a `in`, indica que se usará constantes, impidiendo su modificación en el cuerpo del bucle.
En cambio, con `:=`, se declaran variables.
Ejemplo:

```
for each i := array do print(i)
```

Si se desea, se puede indicar los tipos de clave y valor, lo que hará que el compilador añada automáticamente las comprobaciones correspondientes.
Ejemplo:

```
for each i: num in [1, 2, 3] do
  #...
```

## Listas literales for

Como ya sabemos, una lista literal se crea mediante corchetes (`[]`).
En determinadas ocasiones, se puede utilizar una variación que permite iterar por una lista y obtener otra mediante un `for`.
La sintaxis de este tipo de literal es como sigue:

```
[for variable in expresión do expresión]
[for each variable in expresión do expresión]

[for variable, variable in expresión do expresión]
[for each variable, variable in expresión do expresión]
```

La idea es que la lista se forma mediante la iteración de cada elemento de la cláusula `in`.
Para cada uno de ellos, ejecuta la expresión del `do` y lo añade a la nueva lista.
Así, p.ej., la siguiente lista:

```
[for i in [1, 2, 3, 4] do i*2]
```

Devolverá:

```
[2, 4, 6, 8]
```

## Sentencia until

La sentencia `until` es similar al `while` pero repite el bucle mientras no se cumpla la condición:

```
until ExpresiónODeclaración do
  Bloque

until ExpresiónODeclaración do Proposición

until Declaración; Expresión do
  Bloque

until Declaración; Expresión do Proposición
```

## Sentencias break y next

La sentencia `break` termina un bucle `until`, `while`, `for` o `for each` al igual que su homónima de otros lenguajes:

```
break
```

Mientras que `next` es similar al `continue` de otros lenguajes, manda la ejecución a la siguiente iteración del bucle:

```
next
```

## for, for each, until y while con else

Las sentencias `for`, `for each`, `until` y `while` disponen de una cláusula `else`, similar a *Python*.
Ejemplo:

```
for i = 0; i < len(inits); i += 1 do
  if item ::= inits[i]; item.id != "*" then
    inits.splice(i, 0, init)
    break for
else
  inits <<< init
```

Esta cláusula se ejecuta cuando el bucle finaliza sin salir prematuramente mediante `break for`, `break for each`, `break until` o `break while`, según corresponda.
Cuando se sale con un simple `break` o con el finalizado normal del bucle, la sentencia `else` se ejecutará.
Observe el uso de `break for`, `break for each`, `break until` y `break while`, en vez de sólo `break`.
No lo olvide.

## nop

**Dogma** soporta un tipo especial de expresión para representar la *no operación*.
Similar al `pass` de **Python**.
Concretamente, `nop` tiene dos objetivos:

- Por un lado, poder indicar que no se desea hacer nada.
- Por otro lado, representa una función que, si se invoca, no hará nada.

Vamos a ver unos ejemplos ilustrativos:

```
#definición de función que indica que no hace nada
fn myfn()
  nop

#pasa nop como handler, el cual si se invoca no hará nada
#muy útil cuando es obligatorio pasar un handler
cli.release(nop)
fs.unlink(path, nop)

#también muy útil con promesas
q.subscribeTo(queue).then(nop).catch(nop)
```
