# Iteradores y funciones generadoras

Un **iterador** (*iterator*) es un objeto que permite acceder uno a uno a los elementos de otro, por ejemplo, en una sentencia `for each`.
Mientras que una **función generadora de iteradores** (*iterator generator function*) es aquella que devuelve un iterador para recorrer uno a uno una secuencia de elementos.

## Anotación @iter

En **Dogma**, una función generadora se define con la anotación `@iter`.
He aquí un ejemplo:

```
@iter
fn mygen()
  for each item in [1, 2, 3, 4] do yield item
```

## Sentencia yield

Por su parte, la sentencia `yield` se utiliza para devolver el elemento de la iteración actual, en una función generadora.
Cada vez que la función generadora se encuentra con un `yield`, se detiene la ejecución y se devuelve el valor indicado.
Una vez el consumidor del elemento haya terminado con el elemento devuelto, solicita el siguiente elemento a la función generadora.
La cual reactiva su ejecución tras el último `yield` ejecutado.
Así hasta que se encuentre el final de la función o bien un `return`.

La sintaxis de la sentencia `yield` es como sigue:

```
yield Expresión
```

## Métodos generadores de iteradores

Todo tipo puede disponer de tantos métodos generadores de iteradores como sea necesario, siendo el predeterminado aquel cuyo nombre es `iter` y se define con la anotación `@iter`.
Ejemplo:

```
@iter
fn Task.iter()
  for each item in :items do yield item
```
