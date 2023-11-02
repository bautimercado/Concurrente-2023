# 4. En una exposición aeronáutica hay un simulador de vuelo (que debe ser usado con exclusión mutua) y un empleado encargado de administrar su uso. Hay P personas que esperan a que el empleado lo deje acceder al simulador, lo usa por un rato y se retira. El empleado deja usar el simulador a las personas respetando el orden de llegada. Nota: cada persona usa sólo una vez el simulador.

```cpp
process Persona[id: 1..P] {
    Coordinador!persona_esperando(id);
    Empleado?pasar_a_simulador();
    // Usando simulador;
    Empleado!seguir();
}

process Coordinador {
    cola personas;
    int id_persona;
    while (true) {
        do ❏ Persona[*]?persona_esperando(id_persona) ->
            personas.push(id_persona);
        ❏ not personas.empty(); Empleado?.pedido() ->
            Empleado!atender_pedido(personas.pop());
    }
}

process Empleado {
    int id_persona;
    while (true) {
        Coordinador!pedido();
        Coordinador?atender_pedido(id_persona);
        Persona[id_persona]!pasar_a_simulador();
        Persona[id_persona]?seguir();
    }
}
```