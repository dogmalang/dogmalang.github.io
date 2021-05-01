# Anotaciones

Una **anotación** (*annotation*) no es más que un apunte sobre alguna cosa.
Su sintaxis es como sigue:

```
@Nombre
@Nombre(argumentos)
```

Las anotaciones se pueden asociar a enumeraciones (`enum`), tipos (`type`) y funciones (`fn`).

Por ejemplo, se puede utilizar para indicar que lo que viene es la sobrescritura de un método heredado:

```
@override
fn Tipo.método()
  #...
```

Las anotaciones más comunes son las siguientes:

```
#indica que se trata de un tipo o función abstracta
@abstract

#solicita al compilador que cree el alias indicado para la función anotada
@alias("alias")

#indica un método a atar automáticamente al objeto self
@bind

#indica que estamos ante un tipo de datos con la capacidad de emitir eventos.
@emitter

#indica un método o función generadora de iteradores
@iter

#indica que el elemento al que anota sólo debe compilarse cuando se compile a JavaScript
@js

#indica un tipo mixable
@mixable

#indica que el método es una sobrescritura de uno heredado
@override

#indica la operación de lectura de una propiedad
@prop

#indica que el elemento al que anota sólo debe compilarse cuando se compile a Python
@py

#indica un tipo, función o método pendiente de hacer. Inyecta automáticamente todo() en el cuerpo
@todo

#imprime por pantalla los argumentos pasados en una llamada a la función
@input
```
