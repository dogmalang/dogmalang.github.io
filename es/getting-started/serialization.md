# Serialización

La **serialización** (*serialization*) es una operación mediante la cual se convierte un objeto de datos en un formato para su almacenamiento o transmisión.
En **Dogma**, hay soporte integrado para **JSON**.

## JSON

El objeto `json` dispone de operaciones para convertir datos a formato **JSON** y de formato **JSON**:

```
#devuelve el valor indicado en formato JSON
fn json.encode(val): text

#devuelve el valor de un texto en formato JSON
fn json.decode(text): any
```

Ejemplos:

```
json.encode({x=1, y=2})           #devuelve: {"x":1,"y":2}
json.decode("""{"x":1,"y":2}""")  #devuelve: {x=1, y=2}
```

### Serialización a JSON

Las estructuras pueden tener un método con el que convertir una instancia de la estructura a formato **JSON**.
Este método de instancia debe anotarse como `@json` y `@encode` y debe devolver una texto con la instancia en formato **JSON**.
Ejemplo:

```
struct Ejemplo
  @json @encode
  pub fn toJson() -> serialized: text
    #...
```

Cada vez que invocamos a `json.encode()`, se solicitará la serialización al método de instancia anterior.

A su vez, las estructuras también pueden disponer de una función, *estática* implícitamente, que crea una instancia de la estructura a partir de un texto en formato **JSON**.
En este caso, usar las anotaciones `@json` y `@decode`:

```
struct Ejemplo
  @json @decode
  pub fn fromJSON(serialized: text) -> instance: Ejemplo
    #...
```

Para obtener una instancia de una estructura a partir de una texto en formato **JSON**, usar el siguiente formato:

```
json.decode<Tipo>(textoEnFormatoJSON)
```

Si el tipo indicado no define la función estática `@json @decode`, la función usa el comportamiento estándar de `json.decode()` y, a continuación, le asigna como tipo el indicado.