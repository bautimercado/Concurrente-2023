# 2. Se desea modelar el funcionamiento de un banco en el cual existen 5 cajas para realizar pagos. Existen P clientes que desean hacer un pago. Para esto, cada una selecciona la caja donde hay menos personas esperando; una vez seleccionada, espera a ser atendido. En cada caja, los clientes son atendidos por orden de llegada por los cajeros. Luego del pago, se les entrega un comprobante. Nota: maximizando la concurrencia.

```cpp
chan cajas[5](int), comprobante[P](Comprobante), solicitar_cajero(int), cajero_correspondiente[P](int), decrementar_caja(int);

process Cliente[id:1..P] {
    int id_caja;
    Comprobante comprobante;
    send solicitar_cajero(id);
    receive cajero_correspondiente[id](id_caja);
    send cajas[id_caja](id);
    receive comprobante[id_caja](comprobante);
}

process Coordinador {
    int cant_mensajes_por_caja[5] = ([0] 5);
    int id_caja, id_cliente;
    while (true) {
        if (not empty(solicitar_cajero)) {
            id_caja = obtener_min(cant_mensajes_por_caja[*]);   // Obtiene la caja que menos clientes tenga esperando
            receive solicitar_cajero(id_cliente);
            send cajero_correspondiente[id_cliente](id_caja);
            cant_mensajes_por_caja[id_caja]++;
        }
        if (not empty(decrementar_caja)) {
            receive decrementar_caja(id_caja);
            cant_mensajes_por_caja[id_caja]--;
        }
    }
}

process Cajero[id:1..5] {
    int id_cliente;
    Comprobante comprobante;
    while(true) {
        receive cajas[id](id_cliente);
        //Generar comprobante;
        send comprobante[id_cliente](comprobante);
        send decrementar_caja(id):
    }
}
```