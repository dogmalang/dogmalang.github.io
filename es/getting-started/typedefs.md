# Typedefs

Un **typedef** es un alias para un tipo o lista de elementos.
A veces, necesitamos asignar otro nombre a un tipo o bien definir un tipo para una lista.
Para estos casos, podemos usar la siguiente sintaxis de la sentencia `type`:

```
#alias de otro tipo
type Length = num

#definición de una lista con tipo específico
type Items = Item[]     #sin importar su tamaño
type Items = Item[2]    #con un tamaño de 2 elementos
type Items = Item[2..*] #con al menos 2 elementos
type Items = Item[2..4] #con entre 2 y 4 elementos

#definición de una lista con tipos específicos
type Items = (text,num)[]     #con elementos de tipo num o text sin importar tamaño
type Items = (text,num)[2]    #con 2 elementos de tipo text o num
type Items = (text,num)[2..*] #con al menos 2 elementos de tipo text o num
type Items = (text,num)[2..4] #con entre 2 y 4 elementos de tipo text o num
```

Estos tipos se pueden indicar tanto como tipos de parámetro como de campos de estructuras.
El compilador añade la lógica necesaria para comprobar que los valores de los elementos de una lista cumplen lo especificado.

También se puede usar con el operador `is`.
Ejemplos:

```
type NumList = num[2..4]

[] is NumList               #false
[1, 2] is NumList           #true
[1, 2, 3] is NumList        #true
[1, 2, 3, 4] is NumList     #true
[1, 2, 3, 4, 5] is NumList  #false
```
