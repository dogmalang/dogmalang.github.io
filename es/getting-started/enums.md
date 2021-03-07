# Enumeraciones

Una **enumeración** (*enumeration*) es un tipo que lista un posible conjunto explícito de valores.
Se definen mediante la sentencia `enum`:

```
enum Nombre {Elemento, Elemento, Elemento...}

enum Nombre
  Elemento
  Elemento
  Elemento
```

Cada elemento está formado por dos componentes, el nombre y su valor:

```
Nombre
Nombre = Valor
```

El valor puede ser un literal de tipo texto o número.
Si no se indican los valores, el compilador les asignará valores aleatorios.

He aquí unos ejemplos:

```
#valores asignados por el compilador
enum Color {ROJO, VERDE, AZUL}

enum Color
  ROJO
  VERDE
  AZUL

#valores asignados por nosotros mismos
enum Color {ROJO = 1, VERDE = 2, AZUL = 3}

enum Color
  ROJO = 1
  VERDE = 2
  AZUL = 3
```

Para acceder a un elemento de la enumeración, se utiliza el operador `.`:

```
c = Color.AZUL
```

Los elementos de una enumeración tienen los siguientes campos:

- `name` (text) que contiene su nombre como, por ejemplo, `ROJO`, `VERDE` o `AZUL`.
- `value` (text o num) que contiene su valor.

## Operadores `==~` y `!=~`

Para comparar el valor de una variable o constante con un elemento de la enumeración, se usan los operadores `==`, `===`, `!=` y `!==`, según el caso.
Ejemplo:

```
if c == Color.ORANGE then
  #...
else
  #...
```

Aunque para facilitar la comprobación del valor de una variable con un elemento de una enumeración, **Dogma** proporciona los siguientes operadores adicionales `==~` y `!=~`.
`==~` se utiliza para comparar por igualdad; mientras que `!=~` por desigualdad.
Veamos unos ejemplos ilustrativos:

```
if c ==~ ORANGE then
#similar a:
if c == Color.ORANGE then

if c !=~ ORANGE then
#similar a:
if c != Color.ORANGE then
```

En este caso, hay que indicar únicamente el nombre del elemento, sin necesidad de precederlo de la enumeración.
El tipo enumeración lo obtendrá a partir del valor a comparar.

## Operador `=~`

El operador `=~` se puede usar para asignar otro valor de la enumeración a una variable, de manera similar a como hacemos con `==~` y `!=~`.
Ejemplo:

```
c = Color.ORANGE
c =~ RED

#similar a:
c = Color.ORANGE
c = Color.RED
```

## Anotación `@value`

Cuando se define una enumeración con la anotación `@value`, lo que hace es generar una con sus valores literales.
Este tipo de enumeración no permite el uso de los operadores: `==~`, `!=~` y `=~`.
Y por lo tanto, el acceso a un valor de la enumeración siempre debe venir acompañado del objeto enumeración.

A continuación, un ejemplo:

```
@value
enum Color
  RED
  GREEN
  BLUE

#es similar a:
const Color = const({RED=1, GREEN=2, BLUE=3})
```
