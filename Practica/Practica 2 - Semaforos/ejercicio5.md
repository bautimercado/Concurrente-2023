## 5. En una empresa de logística de paquetes existe una sala de contenedores donde se preparan las entregas. Cada contenedor puede almacenar un paquete y la sala cuenta con capacidad para N contenedores. Resuelva considerando las siguientes situaciones:

### a) La empresa cuenta con 2 empleados: un empleado Preparador que se ocupa de preparar los paquetes y dejarlos en los contenedores; un empelado Entregador que se ocupa de tomar los paquetes de los contenedores y realizar la entregas. Tanto el Preparador como el Entregador trabajan de a un paquete por vez.
```
sem contenedores_libres = N, entregas = 0, mutex = 1;
int primero_libre = 0, primero_ocupado = 0;
Paquete paquetes[n];

process Preparador {
    Paquete paquete;
    while (true) {
        //preparar paquete;
        P(contenedores_libres);
        paquetes[primero_ocupado] = paquete;
        primero_ocupado = (primero_ocupado + 1) % n;
        V(entregas);
    }
}

process Entregador {
    Paquete paquete;
    while (true) {
        P(entregas);
        paquetes[primero_libre] = paquete;
        primero_libre = (primero_libre + 1) % n;
        V(contenedores_libres);
        //realizar entrega;
    }
}

```
### b) Modifique la solución a) para el caso en que haya P empleados Preparadores.
```
sem contenedores_libres = N, entregas = 0, mutex = 1;
int primero_libre = 0, primero_ocupado = 0;
Paquete paquetes[n];

process Preparador[i= 1 to P]{
    Paquete paquete;
    while (true) {
        //preparar paquete;
        P(contenedores_libres);
        P(mutex);
        paquetes[primero_ocupado] = paquete;   
        primero_ocupado = (primero_ocupado + 1) % n;
        V(mutex);
        V(entregas);
    }
}

process Entregador {
    Paquete paquete;
    while (true) {
        P(entregas);
        P(mutex);
        paquetes[primero_libre] = paquete;
        V(mutex);
        primero_libre = (primero_libre + 1) % n;
        V(contenedores_libres);
        //realizar entrega;
    }
}
```
### c) Modifique la solución a) para el caso en que haya E empleados Entregadores.
```
sem contenedores_libres = N, entregas = 0, mutex = 1;
int primero_libre = 0, primero_ocupado = 0;
Paquete paquetes[n];

process Preparador {
    Paquete paquete;
    while (true) {
        //preparar paquete;
        P(contenedores_libres);
        P(mutex);
        paquetes[primero_ocupado] = paquete;   
        V(mutex);
        primero_ocupado = (primero_ocupado + 1) % n;
        V(entregas);
    }
}

process Entregador[i=1 to E] {
    Paquete paquete;
    while (true) {
        P(entregas);
        P(mutex);
        paquetes[primero_libre] = paquete;
        primero_libre = (primero_libre + 1) % n;   
        V(mutex);
        V(contenedores_libres);
        //realizar entrega;
    }
}
```
### d) Modifique la solución a) para el caso en que haya P empleados Preparadores y E empleadores Entregadores.
```
sem contenedores_libres = N, entregas = 0, mutex = 1;
int primero_libre = 0, primero_ocupado = 0;
Paquete paquetes[n];

process Preparador[i=1 to P] {
    Paquete paquete;
    while (true) {
        //preparar paquete;
        P(contenedores_libres);
        P(mutex);
        paquetes[primero_ocupado] = paquete;
        primero_ocupado = (primero_ocupado + 1) % n;
        V(mutex);
        V(entregas);
    }
}

process Entregador[i=1 to E] {
    Paquete paquete;
    while (true) {
        P(entregas);
        P(mutex);
        paquetes[primero_libre] = paquete;
        primero_libre = (primero_libre + 1) % n;   
        V(mutex);
        V(contenedores_libres);
        //realizar entrega;
    }
}
```
