## Operadores de actualización

Para facilitar la actualización de varios campos de un objeto, se puede utilizar los siguientes operadores:

```
Objeto.{
  campo
  campo
  ...
}

Objeto:{
  campo
  campo
  ...
}

Objeto!{
  campo
  campo
  ...
}
```

Cuando se utiliza `.{}`, los campos son públicos; con `:{}`, protegidos; y con `!{}`, privados.

Veamos un ejemplo ilustrativo:

```
self:{stack ::= [], (report, errors) = nil, (ok, failed, total) = 0}

#similar a:
:stack ::= []
:report = nil
:errors = nil
:ok = 0
:failed = 0
:total = 0
```

Se puede utilizar los operadores de asignación `=` (variable), `::=` (constante) y `.=` (propiedad), tal como los conocemos.
Por otra parte, la sintaxis de los campos es como sigue:

```
nombre Operador Expresión
{nombre, nombre, nombre...} Operador Expresión
(nombre, nombre, nombre...) Operador Expresión
```

Cuando se necesita actualizar varios campos al mismo valor, se pueden delimitar todos ellos entre paréntesis (`()`).
Los dos ejemplos siguientes son iguales:

```
self:{(state, error, time) .= nil}
self:{state .= nil, error .= nil, time .= nil}
```

Si lo que necesitamos es actualizar varios campos a partir de los campos de un mismo objeto, se puede delimitar estos campos por llaves (`{}`).
Ejemplo:

```
self:{{state, error, time} .= obj}

#similar a
self:{state .= obj.state, error .= obj.error, time .= obj.time}
```

El operador devuelve el propio objeto para poder encadenarlo en la expresión en la que aparece.
Vamos a ver otro ejemplo que combina, en una misma expresión, ambos operadores sobre el mismo objeto:

```
(w = fn(...args) = task.call(...args) end):{wrapped ::= task}.{
  {id} ::= task

  fmt ::= fn(fun)
    task.fmt(fun)
    return w
  end

  title ::= fn(title?)
    if title == nil then
      return task.title()
    else
      task.title(title)
      return w
  end

  #...
}
```
