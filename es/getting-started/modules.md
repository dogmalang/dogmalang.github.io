# Módulos

Un **módulo** (*module*) es un archivo de código, con extensión `.dog`, que contiene objetos reutilizables o bien un *script* dogmático.

## APIs de los módulos

Los módulos son objetos y tienen miembros públicos y privados.
De manera predeterminada, son privados.
Para hacerlos públicos, hay que usar las palabras reservadas `export` o `pub`.

Las funciones, las variables, las constantes y los tipos puede hacerse públicos, permitiendo su acceso mediante las sentencias `use` y `from`.
Para aquellos módulos que exportan un *único* objeto, siendo éste el que representa al propio módulo, se utiliza `export`.
Sólo puede haber un `export` por archivo y si hay un `export` no puede haber `pub`s.
En cambio, cuando un módulo publica varios objetos, se utiliza `pub` para hacerlos públicos y, por tanto, importables desde otros módulos.

### Sentencia export

La sentencia `export` puede exportar una variable, una constante, una función, un tipo o un valor.
Su sintaxis es la siguiente:

```
export objeto
```

He aquí unos ejemplos:

```
#el módulo consiste en un mapa
export {
  name = "test"
  desc = "App for testing the services."
  version = "1.0.0"
  conn = use("./conn")
  servers =
    http = use("./http")
    q = use("./q")
}

#el módulo consiste en el tipo File
export type File(...) : Resource(...)

#el módulo consiste en una función
export fn suma(x, y) = x + y
export fn(x, y) = x + y       #cuando se indica export, se puede omitir el nombre de la función

#el módulo consiste en un valor constante
export const x = 123

#el módulo consiste en un valor variable
export var x = 123
```

No hay que olvidar que un módulo sólo puede tener un único `export` y, en caso de hacerlo, no podrá tener `pub`s.

### Sentencia pub

Cuando se desea que el módulo exporte varios objetos, debemos usar `pub`.
Es similar a `export`.
Ejemplo:

```
pub type Coord2D(.x:num, .y:num)
pub type Coord3D(x?, y?, .z:num) : Coord2D(x, y)
```

Adicionalmente, `pub` puede realizar una importación y una exportación, un dos en uno.
Para ello, en vez de indicar un nombre, hay que proporcionar un texto literal.
Así,

```
pub "./Connection"
```

es lo mismo que

```
use "./Connection"
pub Connection
```

Para hacer una exportación múltiple:

```
pub Item, Item, Item...

pub (
  Item
  Item
  Item
  ...
)
```

Ejemplo:

```
pub (
  "./Task"
  "./SimpleTask"
  "./Simple"
  "./Op"
  "./Init"
  "./Fin"
  "./CompositeTask"
  "./Macro"
  "./DeferredMacro"
  "./Workflow"
  "./TestTask"
  "./Test"
  "./Suite"
  "./Call"
  "./Creator"
  "./Catalog"
)
```

Si va a exportar una serie de objetos importados, puede usar la siguiente sintaxis:

```
pub use(
  task/Task
  task/SimpleTask
  task/Init
  task/Fin
  task/Setup
  task/Teardown
  task/Simple
  task/Macro
  task/Pipeline
  task/Workflow
  runner/Runner
  Catalog
  Creator
  Context
)
```

## Importaciones

Para importar o usar módulos, se puede utilizar las sentencias `use` y `from`, así como la función `use()`.

### Sentencia use

Mediante `use`, se importa un objeto módulo al completo.
Su sintaxis es una de las siguientes:

```
use item, item, item...

use (
  item
  item
  item
  ...
)
```

Los items pueden ser de los siguientes tipos:

- `"módulo"`. Indica la ruta a un módulo interno o uno externo.
  Al importar el módulo `mi/módulo`, se creará la variable `módulo` donde se depositará el objeto del módulo `mi/módulo`.
  Cuando se usa un módulo cuyo nombre contiene puntos como, por ejemplo, `justo.assert`, el módulo se importará implícitamente como el último nombre.
  En el ejemplo anterior, como `assert`.
  De manera similar trabaja el compilador cuando se encuentra con módulos cuyo nombre contiene guiones (`-`).
  Así, por ejemplo, `mi-módulo` se importará como `módulo`.
  Si el módulo finaliza con `/`, se importan todos los archivos que no comiencen por `_`.

- `"módulo" as nombre` o `nombre = "módulo"`. Indica el módulo y la variable a crear y en la que depositar su objeto.

- `nombre`. Indica el nombre de un módulo del directorio actual.
  Es similar a: `"./nombre" as nombre`.
  Este tipo de importación es muy frecuente.

- `../ruta`. Indica un módulo a encontrar a partir del directorio padre.
  Si el módulo finaliza con `/`, se importan todos los archivos que no comiencen por `_`.

- `~/ruta`. Indica un módulo a encontrar a partir del directorio actual de trabajo (referido al proceso, no al módulo actual).

- `alias://ruta`. Indica un alias de módulo indicado en el archivo `dogmac.yaml` del proyecto.

- `dep://ruta`. Indica un paquete del que se depende.

Se recomienda encarecidamente utilizar las sintaxis `nombre` y `../ruta` para hacer referencia a módulos del paquete bajo desarrollo.
De tal manera que cuando encontremos uno indicado mediante un texto literal, es decir, delimitado por comillas dobles (`""`), sabremos que es una dependencia del paquete.

He aquí unos ejemplos ilustrativos:

```
#importa el módulo 'justo.assert' como 'assert'
use "justo.assert"

#importa el paquete fs
use dep://fs
use fsx = dep://fs-extra

#importa el módulo './Task' como 'T'
use Task as T
use T = Task

#importa el módulo './Task' como Task
use Task

#importa los módulos 'fs' y 'http'
use "fs", "http"
use dep://fs, dep://http

use (
  "fs"
  "http"
)

use(
  dep://fs
  dep://http
)
```

Si el objeto importado es un mapa, es posible utilizar la siguiente sintaxis para desempaquetar algunos campos en variables locales:

```
use {variable, variable, variable...} = módulo
```

Lo anterior es lo mismo que:

```
const {variable, variable, variable...} = use("módulo")
```

Y si se desea importar con otro nombre, hay que usar la cláusula `as`.
Ejemplo:

```
use (
  {Catalog as _Catalog} = alias://task
)
```

Para importar varios objetos del mismo directorio:

```
use (
  mi/dir/{Objeto1,Objeto2}
)
```

#### Archivo dogmac.yaml

Todo proyecto puede tener un archivo `dogmac.yaml`, el cual contiene configuración variada para el compilador.
Este archivo de texto de tipo YAML debe contener un objeto donde la propiedad `uses` contiene una relación de aliases a nombres de módulos.
He aquí un ejemplo:

```yaml
uses:
  fs:
    js: @dogmalang/fs
    py: dogmalang.fs
  path:
    js: @dogmalang/path
    py: dogmalang.path
```

Cada vez que indicamos en un `use` un módulo a través de un alias, esto es, mediante la sintaxis `alias:ruta`, el compilador irá a esta propiedad y extraerá el nombre real del módulo.
P.ej., si tenemos `alias://fs` y el compilador está compilando a **JavaScript**, el nombre real a usar será `@dogmalang/fs`;
en cambio, si estamos compilando a **Python**, `dogmalang.fs`.

Cada alias se representa como una propiedad de `imports`, con un valor de tipo objeto el cual indica qué módulo usar atendiendo al lenguaje al que estamos compilando:

```
"alias": {
  "js": "módulo JavaScript",
  "py": "módulo Python",
  "php": "módulo PHP"
}
```

### Sentencia from

También es posible importar sólo determinados miembros públicos de un módulo.
En este caso, se utiliza la sentencia `from`:

```
from módulo use miembro1 [as alias1], miembro2 [as alias2], miembro3 [as alias3]...

from módulo use (
  miembro1
  miembro2
  miembro3
  ...
)
```

Si desea importar cada miembro con un nombre distinto, use la cláusula `as` de manera similar a `use`.

Ejemplos:

```
from "fs" use watch
from "fs" use watch as w
```

Ejemplos:

```
from "semantic-ui-react" use Button, Container, Header

from "semantic-ui-react" use (
  Button
  Container
  Header
)

from "semantic-ui-react" use (
  Button,
  Container,
  Header
)
```

Al igual que con la sentencia `use`, es posible desempaquetar un miembro importado.
He aquí un ejemplo ilustrativo:

```
from "justows.http.rest.postgres" use jsonb=>{GET,MGET,POST,PUT,PATCH,DELETE}

#similar a:
from "justows.http.rest.postgres" use jsonb
var {GET,MGET,POST,PUT,PATCH,DELETE} = jsonb
```

### Función use()

La función `use()` es similar al `require()` de **Node.js**.
Tiene como objeto importar el módulo indicado como argumento, devolviendo su objeto módulo:

```
use(module:text) : any
```

Ejemplo:

```
const fs = use("fs")
```

## Uso de paquetes y módulos escritos en JavaScript

No hay ningún problema para importar, con `use` y `from`, bibliotecas escritas nativamente en **JavaScript**.
Sólo hay que recordar que cuando se importa una clase nativa de **JavaScript**, ésta se instancia mediante el operador `new`, tal como veremos más adelante.
Este operador no está disponible en **Dogma**, pero es fácil la instanciación mediante la función reservada `native()`.
He aquí un ejemplo ilustrativo:

```
pool = native("new Pool(opts)")
```
