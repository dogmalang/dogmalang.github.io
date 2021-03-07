# Empaquetamiento

El **empaquetamiento** (*packing*) es una operación mediante la cual se puede coger varios elementos y empaquetarlos en un nuevo objeto.
Se puede usar con listas y mapas para obtener nuevas copias completas o parciales.
Para estos fines, se utilizan operadores de empaquetado especiales.

## Operador de empaquetado de mapas

**Dogma** proporciona el operador de empaquetado o copia para obtener un mapa de otro mapa.
Es decir, para crear una copia independiente de un objeto.

Por un lado, podemos utilizar el operador `{campo,campo,campo...}`, el cual devuelve la copia con los campos indicados, pudiendo crear nuevos si fuera necesario:

```
objeto{campo,campo,campo}
```

Donde cada campo puede ser:

- `nombre`, el cual indica el nombre del campo a copiar.
- `nombre=valor`, el cual indica el nombre de un campo y su valor a asignar en la copia.
- `...variable`, indica se añadan los campos del objeto almacenado en una variable o constante.

Vamos a ver un ejemplo ilustrativo:

```
artist = {
  name = "Night Moves"
  origin = "US"
  web = "nightmovesmpls.com"
}

a = artist{name,web,origin="United States"}
#aquí: a = {name="Night Moves", web="nightmovespls.com", origin="United States"}
```

Este operador deja intacto el objeto origen.
Crea un nuevo objeto al que le copia los campos indicados.
Y lo devuelve.

Si lo que deseamos es hacer una copia completa o modificada, se pueden utilizar los operadores `{*}` (*shallow copy*, copia superficial) o `{**}` (*deep copy*, copia profunda):

```
objeto{*,campo,campo...}
objeto{**,campo,campo...}
```

Si se desea, se puede:

- Añadir nuevos campos o sobrescribir sus valores copiados.
  Usar la siguiente sintaxis: `{*,campo=valor}` o bien `{*,campo}`.
  En este segundo caso, a `campo` se le asignará el valor de la variable homónima `campo`.
- Se puede suprimir campos de la copia mediante `{*,-campo}`. Puede leerse `-password` como *menos el campo password*.
- Se puede renombrar campos en la copia usando `{*,actual=>nuevo}`. Se puede leer `db=>database` como *db pasa a llamarse database*.
- Se puede añadir los campos de otro objeto mediante `{*,...variable}`.
  El objeto debe encontrarse almacenado en una variable o constante.

He aquí un ejemplo ilustrativo:

```
opts = {
  user = "me"
  password = "mypass"
  db = "mydb"
  desc = "My description."
}

copy = opts{*,-desc,db=>database,password="mynewpass"}
```

Tras lo anterior, tendremos:

- `opts` exactamente igual.
- `copy` igual a: `{user="me", password="mynewpass", database="mydb"}`.
  El campo `desc` se ha suprimido de la copia.
  Se ha cambiado el valor del campo `password`.
  Y se ha cambiado el nombre al campo `db`.

## Operador de empaquetado de listas

También es posible hacer una copia y añadir nuevos valores al final de una lista.
En este caso, se usa el operador `[*]`.
Ejemplos:

```
x = [1, 2, 3, 4, 5]
y = [5, 4, 3, 2, 1]

#devuelve una copia de x, o sea, [1, 2, 3, 4, 5]
l = x[*]

#devuelve una copia de x con los valores 4 y 5 adicionales: [1, 2, 3, 4, 5]
l = x[*,4, 5]

#devuelve una copia de x con el valor 6 adicional así como los de y
#o sea: [1, 2, 3, 4, 5, 6, 5, 4, 3, 2, 1]
l = x[*,6,...y]
```
