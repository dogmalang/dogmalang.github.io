# Patrones de diseño

un **patrón de diseño** (*design pattern*) es una manera de resolver un determinado problema común de programación.
**Dogma** viene de fábrica con soporte de varios patrones de diseño:

- Instancia única.
- Factoría.

## Instancia única

Mediante la **instancia única** (*singleton*), garantizamos que sólo pueda haber una única instancia de una estructura.
Para definir este tipo de patrón, se usa la anotación `@singleton`.
No hay más que anotar la estructura como `@singleton`:

```
@singleton
struct Estructura
  #...
```

Groso modo, a la hora de invocar la estructura, **Dogma** comprueba si la instancia única ya existe.
Si así es, la devuelve.
En otro caso, la instancia y la guarda para futuras invocaciones de la estructura.
Observe que es **Dogma** el encargado de gestionar la instancia única.

Una vez se dispone de la instancia única, hay que tener en cuenta lo siguiente.
Es posible que se invoque la estructura con argumentos diferentes a la invocación que generó la instancia única.
En este caso, **Dogma** permite que definamos un método estático que valide si los argumentos son compatibles con los de la instanciación.
Este método debe ser un procedimiento y debe anotarse también como `@singleton`.
Su definición es como sigue:

```
@singleton
struct Estructura
  #...

  @singleton
  pvt proc isCompatible(i, args)
    #...

  #...
```

La anotación `@singleton` aplicada a un procedimiento conlleva que éste se definirá como estático también.
Este procedimiento espera dos argumentos:

- `i`, la instancia única.

- `args`, los argumentos de la nueva llamada a la estructura.

La idea es que el procedimiento valide estos argumentos y propague un error si no son compatibles.
De esta manera, la estructura se puede invocar con distintos argumentos, pero siempre compatibles con la creación de la instancia.

## Factoría

El patrón de diseño **factoría** (*factory*) tiene como objeto crear instancias atendiendo a los argumentos pasados a la estructura.
Ahora, en vez de crearse la instancia directamente, la estructura pasa los argumentos a una función estática especial, la cual devuelve la instancia correcta.

Por un lado, debemos anotar la estructura como `@factory`.
Y por otro lado, definir una función, también anotada como `@factory`, que se encargará de instanciar la (sub)estructura correspondiente.
Las funciones marcadas como `@factory` son estáticas implícitamente.
Y reciben todos los argumentos pasados a la estructura en su llamada.

Ejemplo:

```
@factory
struct Button
  #...

  @factory
  pvt fn create(props)
    #...

  #...
```

### Función reservada new()

Como acabamos de ver, la función factoría se encarga de crear la instancia de la estructura correcta.
Cuando necesite crear una instancia de la propia estructura, y no de una subestructura, hay que usar la función `new()`.
Ojo, y no lo olvide, `new()` tiene como objeto crear una nueva instancia de la estructura factoría actual.
Si tiene que crear una instancia de una subestructura, invoque directamente la subestructura.

Su signatura es como sigue:

```
new(props: map): any
```
