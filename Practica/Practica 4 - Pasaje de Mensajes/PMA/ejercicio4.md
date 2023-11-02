# 4. Simular la atención en un locutorio con 10 cabinas telefónicas, el cual tiene un empleado que se encarga de atender a N clientes. Al llegar, cada cliente espera hasta que el empleado le indique a qué cabina ir, la usa y luego se dirige al empleado para pagarle. El empleado atiende a los clientes en el orden en que hacen los pedidos, pero siempre dando prioridad a los que terminaron de usar la cabina. A cada cliente se le entrega un ticket factura. Nota: maximizar la concurrencia; suponga que hay una función Cobrar() llamada por el empleado que simula que el empleado le cobra al cliente.

```cpp
chan pedidos_cabina(int), pagar(int, cabina), pedido();
chan clientes_cabinas[N](Cabina), clientes_factura[N](Factura);

procedure Cliente[id:1..N] {
    Cabina cabina;
    Factura factura;
    send pedido();
    send pedidos_cabina(id);
    receive clientes_cabinas[id](cabina);
    // Usando cabina;
    send pedido();
    send pagar(id, cabina);
    receive clientes_factura[id](factura);
}

procedure Empleado {
    Cola cabinas;  // Suponiendo que arranca con 10 instancias de cabina;
    int id_cliente;
    Cabina cabina;
    while (true) {
        // se evita Busy Waiting?
        receive pedido();
        if (empty(pagar)) {
            receive pedidos_cabinas(id_cliente);
            send clientes_cabinas[id_cliente](cabinas.pop());
        }
        else {
            receive pagar(id_cliente, cabina);
            cabinas.push(cabina);
            send clientes_factura[id_cliente](cabina);
        }
    }
}
```