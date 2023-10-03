## 5. En un corralón de materiales se deben atender a N clientes de acuerdo con el orden de llegada. Cuando un cliente es llamado para ser atendido, entrega una lista con los productos que comprará, y espera a que alguno de los empleados le entregue el comprobante de la compra realizada.

### a) Resuelva considerando que el corralón tiene un único empleado.

- Debería implementar la lógica de si que los procesos solo se encolen si no hay un empleado libre. (ver ejercicio 4 de la exp. practica 3)

```cpp
Monitor Corralon {
    cond clientes, empleado;
    list<Producto> productos_por_cliente[N] = ([N] nil);
    cola<int> id_cliente;
    double comprobante_por_cliente[N];
    int clientes_dormidos = 0;

    procedure solicitar_comprobante(id: int in; lista_productos: list<Producto> in; comprobante: double out) {
        productos_por_cliente[id] = lista_productos;
        id_cliente.push(id);
        clientes_dormidos++;
        signal(empleado);
        wait(clientes);
        clientes_dormidos--;
        comprobante = comprobante_por_cliente[id];
    }

    procedure brindar_comprobante() {
        int id;
        if (clientes_dormidos == 0) { wait(empleado); }
        id = id_cliente.pop();
        //comprobante = generar_comprobante(productos_por_cliente[id])
        comprobante_por_cliente[id] = comprobante;
        signal(clientes);
    }
}

Process Empleado {
    for i = 1 to N {
        Corralon.brindar_comprobante();
    }
}

Process Cliente[id=1 to N] {
    double comprobante;
    list<Producto> lista_productos = ...;
    Corralon.solicitar_comprobante(id, lista_productos, comprobante);
}
```

### b) Resuelva considerando que el corralón tiene E empleados (E > 1).
```cpp
Monitor Corralon {
    int nro_clientes = N, clientes_dormidos = 0;
    cond empleados, clientes;
    cola<int> id_clientes;
    list<Producto> productos_por_cliente[N] = ([N] nil);
    double comprobante_por_cliente[N];

    procedure cant_clientes(cant: int out) {
        nro_clientes--;
        cant = nro_clientes;
    }

    procedure solicitar_comprobante(id: int in; lista_productos: list<Producto> in; comprobante: double out) {
        productos_por_cliente[id] = lista_productos;
        id_clientes.push(id);
        clientes_dormidos++;
        signal(empleados);
        wait(clientes);
        clientes_dormidos--;
        comprobante = comprobante_por_cliente[id];
    }

    procedure brindar_comprobante() {
        int id;
        if (clientes_dormidos == 0) { wait(empleados); }
        id = id_clientes.pop(id);
        //comprobante = generar_comprobante(productos_por_cliente[id])
        comprobante_por_cliente[id] = comprobante;
        signal(cliente);
    }   
}

Process Empleado[id=1 to E] {
    int nro_clientes;
    Corralon.cant_clientes(nro_clientes);
    while (nro_clientes > 0) {
        Corralon.brindar_comprobante();
        Corralon.cant_clientes(nro_clientes);
    }
}

Process Cliente[id=1 to N] {
    double comprobante;
    list<Producto> lista_productos = ...;
    Corralon.solicitar_comprobante();
}
```