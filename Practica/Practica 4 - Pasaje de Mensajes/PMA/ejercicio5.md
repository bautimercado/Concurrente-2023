## 5. Resolver la administración de las impresoras de una oficina. Hay 3 impresoras, N usuarios y 1 director. Los usuarios y el director están continuamente trabajando y cada tanto envían documentos a imprimir. Cada impresora, cuando está libre, toma un documento y lo imprime, de acuerdo con el orden de llegada, pero siempre dando prioridad a los pedidos del director. Nota: los usuarios y el director no deben esperar a que se imprima el documento.

```cpp
chan pedidos_director(Documento), pedidos_usuarios(Documento);
chan pedido(bool);

process Usuario[id: 1..N] {
    Documento documento, aux;
    cola documentos;
    while (true) {
        // Generando documento;
        send pedidos_usuario(documento, id);
        send pedido(true);
    }
}

process Director {
    Documento documento, aux;
    cola documentos;
    while (true) {
        // Generando documento;
        send pedidos_director(documento);
        send pedido(true);
    }
}

process Impresora[id: 1..3] {
    Documento documento;
    bool msj;
    while (true) {
        receive pedido(msj);
        if (empty(pedidos_director)) {
            receive pedidos_usuario(documento);
            // Imprimiendo documento;
        }
        else {
            receive pedidos_director(documento);
            // Imprimiendo documento;
        }
    }
}
```
