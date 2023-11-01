# 1. Suponga que existe un antivirus distribuido que se compone de R procesos robots Examinadores y 1 proceso Analizador. Los procesos Examinadores están buscando continuamente posibles sitios web infectados; cada vez que encuentran uno avisan la dirección y luego continúan buscando. El proceso Analizador se encarga de hacer todas las pruebas necesarias con cada uno de los sitios encontrados por los robots para determinar si están o no infectados.

## a) Analice el problema y defina qué procesos, recursos y comunicaciones serán necesarios/convenientes para resolver el problema.

- En este caso, necesitamos los R procesos examinadores, el proceso analizador y un proceso intermedio (buffer) que nos permita encolar los mensajes y que puedan seguir trabajando.

## b) Implemente una solución con PMS.

```cpp

process Examinador[id:1..R] {
    String url;
    while(true) {
        // Buscando sitios web infectados;
        Buffer!sitio(url); 
    }
}

process Buffer {
    cola buffer;
    String url;
    do Examinador[*]?sitio(url) -> buffer.push(url);
        ❏ not empty(buffer); Analizador?pedido() ->
            Analizador!sitio_a_analizar(buffer.pop())
}

process Analizador {
    String url;
    while (true) {
        Buffer!pedido();
        Buffer?sitio_a_analizar(url);
        //Analizar infección de sitio;
    }
}
```
