# Operadores

Un **operador** (*operator*) es un símbolo que denota una determinada operación a realizar.
Ya hemos presentado algunos operadores como, por ejemplo, `>>>`, `<<<` y `[]`.
Los operadores disponibles son:

```
+         #suma o concatenación si binario; si unario, confirmación
-         #resta si binario; si unario, negación
*         #multiplicación
**        #pow
/         #división
%         #resto
&         #AND a nivel de bits
|         #OR a nivel de bits
~         #NOT a nivel de bits
<<        #desplazamiento a la izquierda a nivel de bits
>>        #desplazamiento a la derecha a nivel de bits
&&        #AND lógico
and       #AND lógico
||        #OR lógico
or        #OR lógico
!         #NOT lógico
not       #not lógico
in        #inclusión
not in    #versión contraria de in
like      #comprobación de patrón mediante expresión regular
not like  #versión contraria de like
= := .=   #asignación
?=        #asignación de valor si valor actual es nil
. :       #acceso a campo
?         #acceso a campo: x?y es similar a if x != nil then x.y else nil end

#asignaciones compuestas
+= -= *= **= /= %= <<= >>= |= &= ^=
```

## like

```
text [not] like pattern
text [not] like [pattern, pattern, ...]
```

## Operadores de comparación

```
==        #igualdad
!=        #desigualdad

===       #igualdad estricta
!==       #desigualdad estricta

==^       #igualdad en profundidad
!=^       #desigualdad en profundidad

<         #menor que
<=        #menor o igual que
>         #mayor que
>=        #mayor o igual que

==~       #comparación con elemento de enumeración
!=~       #comparación con elemento de enumeración
```
