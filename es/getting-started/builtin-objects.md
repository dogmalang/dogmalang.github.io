# Objetos predefinidos

Un **objeto predefinido** (*built-in object*) es aquel que viene de fábrica con el lenguaje y no necesita ser importado explícitamente.


## Tipos predefinidos

Por un lado, tenemos los objetos que representan tipos predefinidos como, por ejemplo, `bool`, `num`, `promise`, `text`, etc.

## Funciones predefinidas

Por otro lado, disponemos de varias funciones predefinidas, algunas de las cuales se han presentado a lo largo del manual como, por ejemplo, `expect()`, `use()`, `typename()` y `bind()`.
Las restantes se muestran a continuación.

### Función predefinida coalesce()

La función `coalesce()` devuelve el primer valor distinto de `nil`:

```
fn coalesce(...values) : any
```

#### Operador ??

El operador `??` se puede usar para indicar sólo dos operandos.
Se usa principalmente en operaciones de asignación.
Ejemplo:

```
#asigna a "a" el valor "b" si es distinto de nil;
#en otro caso, c
a = b ?? c
```

Es importante saber que el operador `??` trabaja en modo cortocircuito:
el operando derecho sólo se evalúa si el izquierdo es `nil`.

### Funciones date() y year()

La función `date()` devuelve una fecha actual, sin tener en cuenta la hora:

```
fn date() : timestamp
```

Mientras que `year()` devuelve el año de una fecha; si no se especifica, de la fecha actual:

```
fn year() : num
fn year(timestamp) : num
```

### Funciones predefinidas echo() y echof()

Mediante `echo()` se muestra por la consola los argumentos indicados sin salto de línea al final;
mientras que `echof()` se utiliza para lo mismo pero donde el primer parámetro indica el formato de la salida:

```
fn echo(...args)
fn echof(fmt:text, ...values)
```

Ejemplos:

```
echo("Hello Luci!")
echof("Hello %s!", name)
```

### Función predefinida exec()

Mediante la función `exec()`, se puede ejecutar un comando síncrona o asíncronamente:

```
fn exec(...cmd, opts?:map) : (text | buffer | subps)
fn exec(...cmd, done:func) : subps
```

- `opts` (map), opciones de ejecución:
  - `wd` o `workDir` (text), el directorio de trabajo.
  - `env` (map), variables de entorno.
  - `encoding` o `enc` (text), salida del comando: `buffer` o `text`. Predeterminado: `text`.
  - `detach` (bool), ejecución asíncrona, sin esperar su finalización? Predeterminado: `false`.
  - `async` (promise), ejecución asícrnona, esperando su finalización y devolviendo una promesa? Predeterminado: `false`.
  - `done` (func), función a ejecutar cuando finalice la ejecución asíncrona.
    Cuando `done`, se asumirá `detach=true`.
    Signatura: `fn(err, stdout, stderr)`.

`exec()` acepta uno o más argumentos cuya concatenación forma el comando final a ejecutar.
Si no se indica la opción `detach`, `exec()` se ejecuta síncronamente y devuelve la salida del comando en un `buffer` o `text`.
Cuando se ejecuta síncronamente, si el comando finaliza con un valor distinto de cero, propagará un error.
En cambio si se especifica `detach=true`, entonces se ejecuta asíncronamente, devolviendo el objeto representante del proceso hijo.

Ejemplo:

```
#síncronamente
print(exec("ls -l"))
print(exec("ls", "-l"))

#asíncronamente
subps = await(exec("ls -l", {detach=true}))
subps = await(exec("ls -l", fn(err, stdout, stderr)
  #...
end))
```

### Función predefinida pexec()

`pexec()` es similar a `exec()`, aunque actúa en modo protegido y siempre ejecuta en modo síncrono:

```
fn pexec(...cmd) : list
```

La función no propaga error, sino que devuelve una lista de dos elementos: el primero indica cómo acabó; y el segundo, la salida o error capturado.

Ejemplo:

```
if const [ok, res] = pexec("ls -l"); ok then
  print(res)
else
  printf("Error: %s.", res)
```

### Función predefinida execf()

La función `execf()` es similar a `exec()`, aunque acepta un formato como primer argumento, mientras que el resto se usan para rellenarlo:

```
fn execf(fmt:text, ...values, opts?:map) : (text | buffer | subps)
fn execf(fmt:text, ...values, done:func) : subps
```

Ejemplo:

```
execf("ls %s", "-l")
```

### Función predefinida fmt()

La función `fmt()` tiene como objeto formatear un texto o un objeto:

```
fn fmt(...args) : text
```

Si sólo se le pasa un argumento, formatea objeto recibido; en otro caso, formatea con `%s`.

Ejemplos:

```
msg = fmt("un texto")
msg = fmt({x=1, y=2, z=3})
msg = fmt("Hello %s!", name)
```

### Funciones predefinidas keys() y values()

Mediante la función `keys()` se obtiene una lista con los nombres de los campos de un mapa;
con `values()`, los valores de sus campos:

```
fn keys(map) : list
fn values(map) : list
```

### Función predefinida len()

Mediante la función `len()` se puede obtener:

- El tamaño de una lista o conjunto. Ejemplo: `len([1, 2, 3])`.
- El tamaño de una cadena de texto. Ejemplo: `len("ciao mondo!")`.
- El número de campos que tiene un objeto. Ejemplo: `len({a=1, b=2})`.
- El valor del campo `length` de un objeto.

Signatura:

```
len(obj?) : num
```

Si se pasa `nil` como argumento, devuelve `0`.

### Funciones predefinidas print() y printf()

Mediante las funciones `print()` y `printf()` se muestra texto en la consola con un salto de línea al final:

```
fn print(...args)
fn printf(fmt:text, ...values)
```

### Función predefinida sleep()

La función `sleep()` mantiene una espera:

```
fn sleep(time: (text | num)) : promise
```

El parámetro `time` tiene la misma sintaxis que el `delay` de la sentencia `with`.

Ejemplo:

```
await(sleep("2s"))
resp.redirectTo(fmt("/%s/dms/item/%s/download", $HTTP_MOUNTPOINT, offer.dmsItem))
```

### Función predefinida remove()

Mediante la función `remove()` se suprime uno o más miembros de un objeto:

```
fn remove(campo1:text, campo2:text, campo3:text..., obj:map) : map
```

La función devuelve el propio objeto sin los campos suprimidos.
Importante: no devuelve una copia, sino el propio objeto sobre el que ha trabajado.

El siguiente ejemplo muestra cómo suprimir los campos `a`, `b` y `c` del objeto `obj`:

```
remove("a", "b", "c", obj)
```

Si lo que se desea es obtener una copia sin esos campos, puede utilizar:

```
obj{*,-a,-b,-c}
```

## Objeto predefinido ps

Mediante el objeto `ps` tenemos acceso a datos del proceso actual:

```
#variables de entorno del proceso.
ps.env : map

#argumentos pasados al comando.
ps.argv : list

#PID del proceso.
ps.pid : num

#directorio de trabajo actual.
ps.workDir : text

#navegador?
ps.browser : bool

#Sale del programa.
proc ps.exit()
proc ps.exit(status:num)

#Cambia el directorio de trabajo.
proc ps.cd(new:text)

#Asigna un controlador al evento indicado.
fn ps.on(event:text, handler:func) : ps

#Asigna un controlador de un solo uso.
fn ps.once(event:text, handler:func) : ps
```

Las variables de entorno se pueden acceder con `ps.env.VARIABLE` y con `$VARIABLE`.

En el navegador, sólo se encuentran disponibles `ps.browser` y `ps.env`.
En cambio, en **Node.js**, todas.

## Objeto predefinido func

El objeto `func` representa el tipo función.
Con él se puede comprobar si un determinado objeto es una función.
Ejemplo:

```
x is func
x is "func"
```

Este objeto dispone de varios métodos estáticos con funciones interesantes:

```
#devuelve una lista con los nombres de los parámetros de la función indicada.
fn func.getParamsOf(func) : list

#comprueba si la función tiene definido un determinado parámetro.
fn func.exists(param:text, func) : bool

#devuelve la lista de argumentos para la función dada y los parámetros nombrados.
#a los parámetros definidos en la función pero que no tengan valor en args se les pasará nil.
fn func.buildArgs(args, func) : list
```

## Función reservada const()

Mediante la función `const()` se puede obtener una referencia inmutable de un determinado objeto.
Esto impide que se puedan modificar o suprimir campos.
Signatura:

```
immutable = const({x=1, y=2})
```

Cualquier intento de modificación de un campo propagará error.
