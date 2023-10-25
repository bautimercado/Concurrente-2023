# 2. Se desea modelar el funcionamiento de un banco en el cual existen 5 cajas para realizar pagos. Existen P clientes que desean hacer un pago. Para esto, cada una selecciona la caja donde hay menos personas esperando; una vez seleccionada, espera a ser atendido. En cada caja, los clientes son atendidos por orden de llegada por los cajeros. Luego del pago, se les entrega un comprobante. Nota: maximizando la concurrencia.

```cpp
int cant_mensajes_por_caja[5] = ([0] 5);
chan cajas[5](int), comprobante[P](Comprobante);

process Cliente[id:1..P] {
    int id_caja;
    Comprobante comprobante;
    id_caja = obtener_minimo(cant_mensajes_por_caja[*]);  // Inteferencias??
    send cajas[id_caja](id);
    receive comprobante[id_caja](comprobante);
}

process Cajero[id:1..5] {
    int id_cliente;
    Comprobante comprobante;
    while(true) {
        receive cajas[id](id_cliente);
        cant_mensajes_por_caja[id]++;
        //Generar comprobante;
        send comprobante[id_cliente](comprobante);
        cant_mensajes_por_caja[id]--;
    }
}
```

- Añadir proceso intermedio que calcule el minimo e modifique las cantidades