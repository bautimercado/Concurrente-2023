## 5. En un corralón de materiales se deben atender a N clientes de acuerdo con el orden de llegada. Cuando un cliente es llamado para ser atendido, entrega una lista con los productos que comprará, y espera a que alguno de los empleados le entregue el comprobante de la compra realizada.

- La idea es tener dos tipos de monitores
  - Uno donde se administre la entrada de los clientes (ordenarlos, hacer su recepción y asignarles el empleado)
  - Otro monitor con la lógica de antención al cliente.

### a) Resuelva considerando que el corralón tiene un único empleado.

```cpp
Monitor Recepcion {
    bool libre = true;
    int cant_dormidos = 0;
    cond clientes_dormidos;

    procedure esperar_atencion() {
        if (not libre) {
            cant_dormidos++;
            wait(clientes_dormidos);
        }
        else -> libre = false;
    }

    procedure llamar_al_siguiente_cliente() {
        if (cant_dormidos > 0) {
            cant_dormidos--;
            signal(clientes_dormidos);
        }
        else -> libre = true;
    }
}

Monitor Atencion {
    List<Producto> productos_a_comprar;
    cond esperando_lista, esperando_comprobante, cliente_tome_comprobante;
    Comprobante comprobante_aux;

    procedure brindar_lista_de_productos(productos: List<Producto> in; comprobante: Comprobante out) {
        productos_a_comprar = productos;
        signal(esperando_lista);
        wait(esperando_comprobante);
        comprobante = comprobante_aux;
        signal(cliente_tome_comprobante);
    }

    procedure esperar_lista_de_productos(productos: List<Producto> out) {
        if (productos_a_comprar == null)  // Debería hacerlo con un booleano?
            wait(esperando_lista);
        productos = productos_a_comprar;
    }

    procedure brindar_comprobante(comprobante: Comprobante in) {
        comprobante_aux = comprobante;
        signal(esperando_comprobante);
        wait(cliente_tome_comprobante);
        productos_a_comprar = null;
    }
}

Process Empleado {
    List<productos> productos;
    Comprobante comprobante;
    for i = 1 to N {
        Recepcion.llamar_al_siguiente_cliente();
        Atencion.esperar_lista_de_productos(productos);
        //comprobante = GenerarComprobante(productos); 
        Atencion.brindar_comprobante(comprobante);
    }
}

Process Cliente[id=1 to N] {
    Comprobante comprobante;
    List<Producto> productos;
    Recepcion.esperar_atencion();
    Atencion.brindar_lista_de_productos(productos, comprobante);
}
```

### b) Resuelva considerando que el corralón tiene E empleados (E > 1).

```cpp
Monitor Recepcion {
    int cant_clientes = N, cant_libres = E, clientes_esperando = 0;
    cola id_empleados;
    cond esperando_empleado;

    procedure get_cant_clientes(cant: int out) {
        cant = cant_clientes;
    }

    procedure esperar_antencion(id: int out) {
        if (cant_libres == 0) {
            clientes_esperando++;
            wait(esperando_empleado);
        }
        else
            cant_libres--;
        id = id_empleados.pop();
    }

    procedure atender_a_cliente(id: int in) {
        id_empleados.push(id);
        if (clientes_esperando > 0) {
            signal(esperando_empleado);
            clientes_esperando++;
        }
        else
            cant_libres++;
        cant_clientes--;
    }
}

Monitor Atencion[i=1 to E] {
    List<Producto> productos_a_comprar;
    Comprobante comprobante_aux;
    cond esperando_productos, esperando_comprobante, cliente_toma_comprobante;

    procedure brindar_lista_productos(productos: List<Producto> in; comprobante: Comprobante out) {
        productos_a_comprar = productos;
        signal(esperando_productos);
        wait(esperando_comprobante);
        comprobante = comprobante_aux;
        signal(cliente_toma_comprobante);
    }

    procedure esperar_lista_productos(productos: List<Producto> out) {
        if (productos_a_comprar == null)
            wait(esperando_productos);
        productos = productos_a_comprar;
    }

    procedure brindar_comprobante(comprobante: Comprobante in) {
        comprobante_aux = comprobante;
        signal(esperando_comprobante);
        wait(cliente_toma_comprobante);
        productos_a_comprar == null;
    }
}

Process Empleado[id=1 to E] {
    int cant_clientes;
    List<Producto> productos;
    Comprobante comprobante;
    Recepcion.get_cant_clientes(cant_clientes);
    while (cant_clientes > 0) {
        Recepcion.atender_a_cliente(id);
        Atencion[id].esperar_lista_productos(productos);
        //comorobante = GenerarComprobante(productos);
        Antencion[id].brindar_comprobante(comprobante);
        Recepcion.get_cant_clientes(cant_clientes);
    }
}

Process Cliente[id=1 to N] {
    int id_empleado;
    List<Producto> productos;
    Comprobante comprobante;
    Recepcion.esperar_atencion(id_empleado);
    Antencion[id_empleado].brindar_lista_productos(productos, comprobante);
}
```