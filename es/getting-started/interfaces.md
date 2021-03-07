# Interfaces

Una **interfaz** (*interface*) declara la estructura de datos de un objeto.
Es similar a las interfaces de otros lenguajes, pero en Dogma sólo indica los campos que un objeto puede tener.
Básicamente, se utilizan para declarar los campos que necesitamos tenga un determinado objeto para poder operar con él.

## Definición de interfaces

Para definir una interfaz, hay que utilizar la sentencia `intf`:

```
#sin interfaz madre
intf Nombre
  Miembros

#con interfaz madre
intf Nombre: Nombre
  Miembros
```

Donde los miembros pueden ser campos y/o métodos.
Los campos se definen como sigue:

```
#el campo es opcional, pero si existe y su valor es distinto de nil, deberá ser del tipo indicado
nombre?:tipo

#el campo es obligatorio y no puede ser nil
nombre

#el campo es obligatorio y su valor debe ser del tipo indicado
nombre:tipo
```

Mientras que los métodos:

```
async fn|proc nombre(parámetros): tipo
```

Donde `async`, los parámetros y la parte del tipo de retorno son opcionales.
De los parámetros sólo hay que indicar su nombre.

Ejemplo:

```
intf Tarea
  nombre:text
  desc:text

intf Calcul
  fn sum(x, y): num
  fn sub(x, y): num
```

## Operador is

El operador `is`, y su variante negada `is not`, se puede usar para comprobar si un determinado objeto cumple una determinada interfaz.
Recordemos que este operador se usa para comprobar si un objeto es de un determinado tipo.
Cuando como tipo se indica una interfaz, comprobará si presenta los campos de la interfaz.

Ejemplo:

```
intf Tarea
  nombre:text
  desc:text

task = {
  nombre = "mi tarea"
  desc = "mi descripción"
  otrocampo = 1234
}

task is Tarea #true
```

Cuando la interfaz tiene métodos, el operador sólo comprueba si el método existe, sin tener en cuenta sus parámetros y/o tipo de retorno.

## Anotación @impl

En **Dogma**, al igual que en otros lenguajes como **Go**, los tipos no necesitan indicar las interfaces que implementan.
Se dice que un tipo u objeto implementa una interfaz siempre que defina sus miembros, sin necesidad de indicarlo explícitamente.
A pesar de todo, y sólo a modo de documentación, puede usar la anotación `@impl`:

```
@impl(InterfazImplementada)
struct EstructuraQueImplementaLaInterfaz
  #...
```
