# 3. Se debe modelar el funcionamiento de una casa de comida rápida, en la cual trabajan 2 cocineros y 3 vendedores, y que debe atender a C clientes. El modelado debe considerar que:
- Cada cliente realiza un pedido y luego espera a que se lo entreguen.
- Los pedidos que hacen los clientes son tomados por cualquiera de los vendedores y se
lo pasan a los cocineros para que realicen el plato. Cuando no hay pedidos para atender,
los vendedores aprovechan para reponer un pack de bebidas de la heladera (tardan entre
1 y 3 minutos para hacer esto).
- Repetidamente cada cocinero toma un pedido pendiente dejado por los vendedores, lo
cocina y se lo entrega directamente al cliente correspondiente.
Nota: maximizar la concurrencia.

```cpp
chan priv_cliente[C](Comida), priv_vendedor[3](int);
chan pedidos(int), vendedor_listo(int), confirmados(int);

process Cliente[id:1..C] {
    Comida comida;
    // Eligiendo;
    send pedidos(id);
    receive priv_cliente[id](comida);
}

process Coordinador {
    int id_vendedor, id_cliente;
    while (true) {
        receive vendedor_listo(id_vendedor);
        if (empty(pedidos))
            send priv_vendedor[id_vendedor](-1);   // -1 para que repongan las bebidas de la heladera.
        else {
            receive pedidos(id_cliente);
            send priv_vendedor[id_vendedor](id_cliente);
        }
    }
}

process Vendendor[id:1..3] {
    int id_cliente;
    while (true) {
        send vendedor_listo(id);
        receive priv_vendedor[id](id_cliente);
        if id_cliente == -1
            delay(1min, 3min);   // Suponiendo que lo hace entre 1 y 3 minutos.
        else
            send confirmados(id_cliente);
    }
}

process Cocinero[id:1..2] {
    Comida comida;
    int id_cliente;
    while (true) {
        receive confirmados(id_cliente);
        // Preparando comida;
        send priv_clientes[id_cliente](comida);
    }
}
```