## 1. Se dispone de un puente por el cual puede pasar un solo auto a la vez. Un auto pide permiso para pasar por el puente, cruza por el mismo y luego sigue su camino.

```cpp
Monitor Puente
    cond cola;
    int cant= 0;
    Procedure entrarPuente()
        while ( cant > 0) wait (cola);
        cant = cant + 1;
    end;
    Procedure salirPuente()
        cant = cant – 1;
        signal(cola);
    end;
End Monitor;

Process Auto [a:1..M]
    Puente.entrarPuente(a);
    “el auto cruza el puente”
    Puente.salirPuente(a);
End Process;
```

### a. ¿El código funciona correctamente? Justifique su respuesta.

- Funciona correctamente. Llega un auto y entra al puente correctamente (cant = 1), después llegan un par más pero se quedan encolados porque ya hay un auto en el punete. Cuando este sale, decrementa cant (cant = 0) y despierta a alguno de los que estaba dormido.
  - Si el monitor es tomado por un proceso que recién llega (no por el que se despertó) entonces hace cant++ y entra al punete. Cuando el proceso que se despertó retome el monitor (sabiendo que hay un auto en el puente) el while hace que se vuelva a dormir.
  - Si el monitor es tomado por el proceso que se despertó, sale del while, incrementa cant y está en el puente.

### b. ¿Se podría simplificar el programa? ¿Sin monitor? ¿Menos procedimientos? ¿Sin variable condition? En caso afirmativo, rescriba el código.

- Un poco sí, podríamos hacer que en vez de que haya un cant haya una variable booleana (ya que siempre es 1 o 0), también podríamos hacer un solo procedimiento donde el proceso use el puente (recordar que la ejecución de los monitores es de atómica).

```cpp
Monitor Puente {
    Procedure cruzarPuente() {
        "el auto cruza el puente"
    }
}

Process Auto[a=1 to M] {
    Puente.cruzarPuente();
}
```

### c. ¿La solución original respeta el orden de llegada de los vehículos? Si rescribió el código en el punto b), ¿esa solución respeta el orden de llegada?

- No, no respeta el orden de llegada. Ya que si un proceso que llegó al puente y desea cruzarlo pero no puede (porque otro está cruzandolo), se queda dormido. Cuando el proceso auto que había tomado el puente despierte al proceso que se había dormido anteriormente, pero llega otro proceso nuevo, toma el monitor y como no hay auto en el puente, toma la SC, el proceso que se había dormido quedará dormido nuevamente.