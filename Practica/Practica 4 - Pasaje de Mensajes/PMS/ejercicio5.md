## 5. En un estadio de fútbol hay una máquina expendedora de gaseosas que debe ser usada por E Espectadores de acuerdo al orden de llegada. Cuando el espectador accede a la máquina en su turno usa la máquina y luego se retira para dejar al siguiente. Nota: cada Espectador una sólo una vez la máquina.

```cpp
process Espectador[id: 1..E] {
    Buffer!llegada(id);
    Maquina?usar();
    // Usando máquina;
    Maquina!salida();
}

process Buffer {
    cola espectadores;
    while (true) {
        do ❏ Espectador[*]?llegada(id) ->
            espectadores.push(id);
        ❏ not espectadores.empty(); Maquina?pedido() ->
            Maquina!siguiente(espectadores.pop());
    }
}

process Maquina {
    int id_espectador;
    while (true) {
        Buffer!pedido();
        Buffer?siguente(id_espectador);
        Espectador[id_espectador]!usar();
        Espectador[id_espectador]?salida();
    }
}
```