# Compilación

La **compilación** (*compilation*) es el proceso u operación mediante el cual transformamos código dogmático a otro lenguaje.
En estos momentos, a **JavaScript** y, próximamente, a **Python** también.
Es posible que en un futuro no muy lejano compile también a **PHP**, pero esta opción se encuentra actualmente bajo evaluación.

## Directivas de compilación

Una **directiva de compilación** (*compilation directive*) es un comando que permite especificar fragmentos de código que sólo deben compilarse bajo determinadas condiciones como, por ejemplo, que se esté compilando a **JavaScript** o **Python** o bien se esté compilando una determinada edición del producto.

Las directivas se referencian mediante `#!`.

### Directiva #!env

La directiva `#!env` escribe en su lugar `#!/usr/bin/env node` o `#!/usr/bin/env python`, según el lenguaje al que se está compilando:

```
#!env
```

### Directiva #!include

Mediante la directiva `#!include`, le indicamos al analizador que inserte el contenido de otro archivo dogmático:

```
#!include archivo
```

El archivo debe indicarse sin extensión, la cual debe ser `dogi` y no `.dog`.
Ejemplos: `#!include db` o `#!include ../db`.

He aquí un ejemplo ilustrativo:

```
######################
# archivo: http.dogi #
######################
var http

init(proc()
  http = repo.get("cxn").geo
end).title("Get http object to use")

#######################
# archivo: search.dog #
#######################
#imports
from "ethronjs" use suite, test, init, fin, repo
use "@ethronjs/assert"

#Test.
export suite(__filename, proc()
  ########
  # init #
  ########
  #!include http

  #...
end)
```

### Directiva #!if

Mediante la directiva `#!if` se puede compilar determinados fragmentos de código.
Su sintaxis es como sigue:

```
  #!if [not] Nombre then
  código
  #!else if Nombre then
  código
  #!else
  código
  #!end
```

Actualmente, sólo se puede indicar una cláusula `else if` y/o una cláusula `else`.
Como nombre hay que indicar una variable directiva de compilación como, por ejemplo, `py` o `js`.
Estas variables son siempre booleanas e indican que se está compilando a **Python** o **JavaScript**, respectivamente.

Ejemplo de uso:

```
  #!if py then
  código a compilar sólo cuando se está compilando a Python
  #!end

  #!if not py then
  código a compilar sólo cuando NO se está compilando a Python
  #!end
```

Las variables `py` y `js` son implícitas.
Pero si necesitamos definir las nuestras propias, se puede utilizar la opción `-d`.
Ejemplo:

```
dogmac js -d postgres -o build src
```

### Directiva de compilación #!cov

Mediante la directiva `#!cov` se puede indicar una directiva de *code coverage*:

```
#!cov ignore if           Ignora el siguiente if
#!cov ignore next         Ignora la siguiente proposición: sea una expresión o una sentencia
#!cov ignore              Alias de #!cov ignore next
#!cov ignore else         Ignora el siguiente else
```

Ejemplo:

```
export async fn enableOrg(params=>[org], http)
  const resp = await(http.endpoint("/re/org/&org/enable").patch({org}))

  #!cov ignore else
  with resp.status()
    if 204 then return
    if 404 then throw("not found: %s.", org)
    else throw("unknown status received: %s.", _)
```

## Compilación a JavaScript

Para compilar de **Dogma** a **JavaScript**, usar el comando `dogmac js`.
He aquí un ejemplo ilustrativo mediante el cual se indica que compile el código dogmático del directorio `src`, dejando los archivos generados en `build`, definiendo la variable directiva `postgres` e implícitamente `js`:

```
dogmac js -d=postgres -o=build src
```

Mediante la opción `-o`, indicamos el directorio donde se depositará el archivo o archivos generados.

Para conocer la ayuda del comando, usar `dogmac js -h`.

El comando sólo compila los archivos con las extensiones `.dog` y `.js.dog`.
Así pues, los archivos `.py.dog` no se compilarán.
Esto permite que cuando tengamos un tipo específico de uno u otro *target*, podamos ubicar cada definición en su correspondiente `.js.dog` y `.py.dog`.
Los comunes en simplemente `.dog`.

### Uso de la anotación @js

Mediante la anotación `@js` se puede definir una función o tipo que sólo debe compilarse a **JavaScript**.
Si estamos compilando a otro lenguaje como, por ejemplo, **Python**, no se compilará.
Ejemplo:

```
@js
fn suma(x, y) = x + y
```

Lo anterior es lo mismo que:

```
  #!if js then
  fn suma(x, y) = x + y
  #!end
```

### Paquete @dogmalang/core

Cuando compilamos **Dogma** a **JavaScript**, nuestro proyecto **JavaScript** debe tener el paquete `@dogmalang/core` como dependencia de producción.
El cual se encuentra disponibles a través de **NPM**.

### Babel

`dogmac` compila a **Node.js** 8 o superior.
La manera más sencilla de usar `babel` para compilar el código JavaScript generado es la siguiente:

```
"presets": [
  [
    "@babel/preset-env",
    {
      "targets": {
        "node": "8"
      }
    }
  ]
]
```

## Comprobación sintáctica

Mediante `dogmac check` se comprueba si el contenido de uno o más archivos es sintácticamente correcto.
El siguiente ejemplo muestra cómo comprobar la sintaxis de los archivos ubicados en las carpetas `src` y `test`:

```
dogmac check src test
```
