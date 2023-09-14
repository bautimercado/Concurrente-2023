## 5. En una empresa de logística de paquetes existe una sala de contenedores donde se preparan las entregas. Cada contenedor puede almacenar un paquete y la sala cuenta con capacidad para N contenedores. Resuelva considerando las siguientes situaciones:

### a) La empresa cuenta con 2 empleados: un empleado Preparador que se ocupa de preparar los paquetes y dejarlos en los contenedores; un empelado Entregador que se ocupa de tomar los paquetes de los contenedores y realizar la entregas. Tanto el Preparador como el Entregador trabajan de a un paquete por vez.
```
sem contenedores_libres = N, entregas = 0;

process Preparador {
    while (true) {
        preparar paquete;
        P(contenedores_libres);
        depositar paquete en contenedor;
        V(entregas);
    }
}

process Entregador {
    while (true) {
        P(entregas);
        extraer paquete;
        V(contenedores_libres);
        realizar entrega;
    }
}

```
### b) Modifique la solución a) para el caso en que haya P empleados Preparadores.
```
sem contenedores_libres = N, entregas = 0, mutex_preparador = 1;

process Preparador[id=1 to P] {
    while (true) {
        preparar paquete;
        P(contenedores_libres);
        P(mutex_preparador);
        depositar paquete en contenedor;
        V(mutex_preparador);
        V(entregas);
    }
}

process Entregador {
    while (true) {
        P(entregas);
        extraer paquete;
        V(contenedores_libres);
        realizar entrega;
    }
}
```
### c) Modifique la solución a) para el caso en que haya E empleados Entregadores.
```
sem contenedores_libres = N, entregas = 0, mutex_entregador = 1;

process Preparador {
    while (true) {
        preparar paquete;
        P(contenedores_libres);
        depositar paquete en contenedor;
        V(entregas);
    }
}

process Entregador[id=1 to E] {
    while (true) {
        P(entregas);
        P(mutex_entregador);
        extraer paquete;
        V(mutex_entregador);
        V(contenedores_libres);
        realizar entrega;
    }
}
```
### d) Modifique la solución a) para el caso en que haya P empleados Preparadores y E empleadores Entregadores.
```
sem contenedores_libres = N, entregas = 0, mutex_entregador = 1, mutex_preparador = 1;

process Preparador[id=1 to P] {
    while (true) {
        preparar paquete;
        P(contenedores_libres);
        P(mutex_preparador);
        depositar paquete en contenedor;
        V(mutex_preparador);
        V(entregas);
    }
}

process Entregador[id=1 to E] {
    while (true) {
        P(entregas);
        P(mutex_entregador);
        extraer paquete;
        V(mutex_entregador);
        V(contenedores_libres);
        realizar entrega;
    }
}
```