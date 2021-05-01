# Plantillas

Una **plantilla** (*template*) es una estructura genérica que pueden adaptar otras estructuras.
Las plantillas no se compilan al menos que una estructura la herede explícitamente.
Y se definen en archivos `.dogt`.

He aquí un ejemplo ilustrativo:

```
struct Map<T>
  #The items.
  pvt const items := {}

  #Return an item by name.
  pub fn get(name:text) : <T> = !items[name]

  #Add an item, overwritting if needed.
  pub proc set(item:<T>) !items[item.name] = item

  #Create a new instance.
  pub static fn 'from'(opts:(func,map)) -> s:Self
    #(1) call function if needed
    if opts is func then
      if (opts = opts()) is not map then throw("function must return map/object.")

    #(2) new instance
    s = Self()
    for each item in opts do s.set(<T>(item))
```

## Parámetros

Un **parámetro plantilla** (*template parameter*) representa un tipo de datos que se debe pasar cuando la estructura es extendida por otra.
Se definen tras el nombre de la plantilla delimitados por corchetes (`<>`) como, p.ej., `<T>`.
Y se referencian en el cuerpo de la plantilla con el mismo formato, es decir, mediante `<T>`.

## Self

Las plantillas definen de manera implícita la anotación `@Self`, permitiendo el uso de `Self` para referenciar la propia estructura.

## Uso de una plantilla

Para reutilizar una plantilla, por un lado, tenemos que importarla mediante la sentencia `use`, con la siguiente sintaxis:

```
Nombre<T>
```

No olvide indicar el parámetro plantilla `<T>`.

Por otra parte, la estructura, que extiende o usa la plantilla, debe heredarla como sigue:

```
: Plantilla<Tipo>
```

El tipo indicado se pasará como parámetro a la plantilla.

He aquí un ejemplo:

```
#imports
use (
  Map<T>
  Network
)

#A map of metworks.
export struct Networks : Map<Network>
```

En este ejemplo, el compilador lee el archivo plantilla `Map.dogt` y lo usa para definir la estructura `Networks`.
El parámetro `<T>` será sustituido por `Network`.
Así pues, lo anterior es lo mismo que escribir:

```
@Self
export struct Networks
  #The items.
  pvt const items := {}

  #Return an item by name.
  pub fn get(name:text) : Network = !items[name]

  #Add an item, overwritting if needed.
  pub proc set(item:Network) !items[item.name] = item

  #Create a new instance.
  pub static fn 'from'(opts:(func,map)) -> s:Self
    #(1) call function if needed
    if opts is func then
      if (opts = opts()) is not map then throw("function must return map/object.")

    #(2) new instance
    s = Self()
    for each item in opts do s.set(Network(item))
```

## Restricciones o cosas importantes a tener en cuenta

A continuación, presentamos algunas restricciones a tener en cuenta, algunas de las cuales se resolverán en el futuro:

- Sólo puede aplicarse a estructuras (`struct`s).
- Sólo se puede definir un parámetro, el cual siempre debe ser `<T>`.
- Las plantillas deben definirse en el mismo directorio que las estructuras que las usan.
- Cada archivo `.dogt` debe definir una única estructura plantilla.
