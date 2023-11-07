## 5. Resolver la administración de las impresoras de una oficina. Hay 3 impresoras, N usuarios y 1 director. Los usuarios y el director están continuamente trabajando y cada tanto envían documentos a imprimir. Cada impresora, cuando está libre, toma un documento y lo imprime, de acuerdo con el orden de llegada, pero siempre dando prioridad a los pedidos del director. Nota: los usuarios y el director no deben esperar a que se imprima el documento.

```cpp
chan pedidos_director(Documento), pedidos_usuarios(Documento);
chan pedido(); // Se puede esto?

process Usuario[id: 1..N] {
    Documento documento, aux;
    cola documentos;
    while (true) {
        // Generando documento;
        send pedido();
        send pedidos_usuario(documento, id);
    }
}

process Director {
    Documento documento, aux;
    cola documentos;
    while (true) {
        // Generando documento;
        send pedido();
        send pedidos_director(documento);
    }
}

process Impresora[id: 1..3] {
    Documento documento;
    while (true) {
        receive pedido();  // Evita busy waiting?
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