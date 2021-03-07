# Errores

**Dogma** proporciona soporte para errores o excepciones, donde un **error** o **excepción** (*exception*) representa un evento anómalo que requiere procesamiento especial.
Para ello, proporciona las funciones `throw()` y `peval()` y la sentencia `do`.

## Propagación de error

Mediante la `propagación de error` (*error throwing* o *error raising*) se genera un error.
Se debe utilizar la función `throw()`:

```
throw(error)
throw(format:text, ...args:text)
```

Ejemplos:

```
throw("esto es el mensaje de error.")
throw("InternalError: %s.", "Esto es el mensaje")  #con formateo del mensaje mediante fmt() implícito
```

## Captura de errores

Cuando deseamos capturar un posible error, en un fragmento de código que sabemos genera errores, se utiliza la sentencia `do`, similar al `try` de otros lenguajes:

```
do [variables]
  Bloque
catch
  Bloque
finally
  Bloque

do [variables]
  Bloque
catch Nombre
  Bloque
finally
  Bloque
```

El bloque del `do` contiene el código a supervisar.
La cláusula `catch` el código que debe ejecutarse para controlar el error.
Si se desea acceso al objeto error, se puede indicar a continuación el nombre de la variable a crear y en la que depositarlo.
Finalmente, la cláusula `finally` se utiliza para especificar un bloque de código que se ejecutará al finalizar el `do`, tanto si propaga error o no lo hace.
Es ideal para asegurar código que deseamos que siempre se ejecute como, por ejemplo, el cierre de un archivo o *socket*.

Las cláusulas `catch` y `finally` son opcionales.

Como esta estructura es muy común, se ha extendido a otras (`while`, `with`, `for`, `fn`, `proc` y `type`) para hacer el código más limpio y elegante.
Ejemplo:

```
while Expresión do
  #bloque
catch e
  #bloque
finally
  #bloque
```

Lo anterior es lo mismo que:

```
while Expresión do
  do
    #bloque
  catch e
    #bloque
  finally
    #bloque
```

Cuando la cláusula `do` se define junto con variables definidas con `:=` y `::=`, serán accesibles en las cláusulas `catch` y `finally`.
Así pues, el siguiente código:

```
var cxn

do
  cxn = pool.acquire()
  ...
finally
  if cxn then cxn.release()
```

es similar a:

```
do cxn := pool.acquire()
  ...
finally
  if cxn then cxn.release()
```

Siempre hay que indicar el valor de las variables.
Si no desea asignárselo inicialmente, asigne `nil`.
Por otra parte, si desea indicar varias variables, separe pr coma (`,`).

## Función reservada peval()

En **Dogma**, una **función reservada** (*reserved function*) es un tipo especial de función.
Que se invoca como cualquier otra función, pero lleva aparejada un comportamiento especial que pueden no tener las funciones normales.
Algunas funciones reservadas son `await()`, `expect()`, `pawait()` y `peval()`.

La función reservada `peval()` (*protected eval*, evaluación protegida) evalúa/ejecuta una expresión y devuelve una lista con dos elementos:

- El primero indica si la ejecución se llevó a cabo correctamente o se produjo un error: `true`, todo bien; `false`, en otro caso.
- El segundo contiene el error propagado o el resultado de la expresión.
  Cuando el primero es `true`, contendrá el resultado de la expresión.
  Cuando es `false`, el error propagado.

Su sintaxis es como sigue:

```
peval(expresión)
```

Ejemplo:

```
if const [ok, res] = peval(sum(1, 2, 3)); ok then
  print("El resultado de la suma es: %s.", res)
else
  printf("Se ha producido el siguiente error: %s.", res)
```

Observe que `peval()` espera la expresión a monitorizar y devuelve cómo finalizó y su resultado o error capturado.

## Función reservada catch()

La función reservada `catch()` ejecuta la expresión adjunta.
Si ésta propaga error, lo captura y devuelve `nil`.
En otro caso, devuelve el valor de la expresión.

Signatura:

```
catch(exp)
```

## Anotación @catch

Cuando una función o procedimiento se anota con `@catch`, el compilador añade el código necesario para capturar cualquier error.
De tal manera que si la función propaga un error, este código lo interceptará y la función devolverá `nil`.
En otro caso, se devolverá el valor devuelto por la propia función.

## Operadores && y ||

El operador `&&` tiene como objeto ejecutar una expresión y, si no propaga ningún error, ejecutar la siguiente.
Por su parte, el operador `||` tiene como objeto ejecutar una expresión y, si propaga error, ejecutar la siguiente.
