# Eventos

Un **evento** (*event*) representa un acontecimiento acaecido en el sistema.
Los eventos son soportados por multitud de lenguajes de programación, entre ellos, **Dogma**.

Si lo deseamos, podemos indicar procedimientos a ejecutar cada vez que un determinado evento se produzca.
Los cuales se conocen formalmente como **controladores** (*listeners* o *handlers*).

## Anotación @emitter

Mediante la anotación `@emitter`, se define un tipo de datos que tiene la capacidad de emitir o generar eventos.
Cuando marcamos un tipo con esta anotación, se añaden automáticamente los siguiente métodos.

### Método on()

Mediante el método de instancia `on()`, se puede registrar un controlador a ejecutar cada vez que la instancia genere un determinado evento:

```
fn EventEmitter.on(eventName:text, listener:proc) -> self
```

El controlador del evento es un procedimiento que invocará la instancia cuando genere el evento.
Le pasará como parámetro el argumento pasado en la emisión/generación del evento.

Supongamos que la variable `e` representa una instancia de un tipo marcado como `@emitter`, para registrarle un controlador de eventos, podemos registrar tantos como deseemos, he aquí un ejemplo de cómo hacerlo:

```
e.on("click", proc(data)
  #...
end)
```

### Método off()

Mediante el método de instancia `off()` se suprime un determinado controlador:

```
fn EventEmitter.off(eventName:text, listener:proc) -> self
```

## Función emit()

Para generar o emitir un evento, hay que utilizar la función `emit()`, en alguno de los métodos de instancia del tipo marcado como `@emitter`:

```
fn emit(eventName:text, arg)
```

El evento se genera para la instancia actual, o sea, para el objeto `self`.

He aquí un ejemplo:

```
emit("start", {id, task, ts = timestamp(), title})
```

## Definición de eventos

Las estructuras deben definir los eventos que pueden propagar.
Si se intenta propagar un evento sin estar en su lista de eventos posibles, la función `emit()` fallará.

Para definir un evento, se utiliza la sintaxis siguiente:

```
pub event nombreEvento
pub event nombreEvento: Tipo
```

Cuando se indica un tipo, la función `emit()` comprobará que la instancia del evento cumple con él.

Ejemplo:

```
/**
 * Event generated when the task run starts.
 */
pub event start: {id: text, task: Task, ts: timestamp, title: text}
```
