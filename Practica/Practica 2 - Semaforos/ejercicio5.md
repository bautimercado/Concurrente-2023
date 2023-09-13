## 5. En una empresa de logística de paquetes existe una sala de contenedores donde se preparan las entregas. Cada contenedor puede almacenar un paquete y la sala cuenta con capacidad para N contenedores. Resuelva considerando las siguientes situaciones:

### a) La empresa cuenta con 2 empleados: un empleado Preparador que se ocupa de preparar los paquetes y dejarlos en los contenedores; un empelado Entregador que se ocupa de tomar los paquetes de los contenedores y realizar la entregas. Tanto el Preparador como el Entregador trabajan de a un paquete por vez.
```
int contenedores[N] = ([N] 0)
sem baton = 1, mutex_prep = 0, mutex_entr = 0;
int cant_paquetes = 0;
bool d_prep = false, d_entr = false;

process Preparador {
    int paquete;
    while (true) {
        preparando paquete;
        P(baton);
        
        if (cant_paquetes == N) { d_prep = true; V(baton); P(mutex_prep); }
        
        //preparando paquete;
        contenedores[cant_paquetes] = paquete;
        cant_paquetes = cant_paquetes + 1;

        V(baton);

        P(baton);
        
        if (d_entr) { d_entr = false; V(mutex_entr); } 
        else V(baton);
    
    }
}

process Entregador {
    int paquete;
    while (true) {
        P(baton);

        if (cant_paquetes == 0) { d_entr = true; V(baton); P(mutex_entr); }
        paquete = contenedores[cant_paquetes];
        cant_paquetes = cant_paquetes - 1;

        V(baton);
        entregar paquete;

        P(baton);
        if (d_prep) { d_prep = false; V(mutex_prep) }
        else V(baton);

    }
}

```
### b) Modifique la solución a) para el caso en que haya P empleados Preparadores.
```
int contenedores[N] = ([N] 0)
sem baton = 1, mutex_prep = 0, mutex_entr = 0;
int cant_paquetes = 0, d_prep = 0, d_entr = 0;

process Preparador[i=1 to P] {
    int paquete;
    while (true) {
        preparando paquete;
        P(baton);
        
        if (cant_paquetes == N) { d_prep = d_prep + 1; V(baton); P(mutex_prep); }
        
        //preparando paquete;
        contenedores[cant_paquetes] = paquete;
        cant_paquetes = cant_paquetes + 1;

        V(baton);

        P(baton);
        
        if (d_entr == 1) { d_entr = d_entr - 1; V(mutex_entr); } 
        else V(baton);
    
    }
}

process Entregador {
    int paquete;
    while (true) {
        P(baton);

        if (cant_paquetes == 0) { d_entr = d_entr + 1; V(baton); P(mutex_entr); }
        paquete = contenedores[cant_paquetes];
        cant_paquetes = cant_paquetes - 1;

        V(baton);
        entregar paquete;

        P(baton);
        if (d_prep > 0) { d_prep = d_prep - 1; V(mutex_prep) }
        else V(baton);

    }
}

```
### c) Modifique la solución a) para el caso en que haya E empleados Entregadores.
```
int contenedores[N] = ([N] 0)
sem baton = 1, mutex_prep = 0, mutex_entr = 0;
int cant_paquetes = 0, d_prep = 0, d_entr = 0;

process Preparador {
    int paquete;
    while (true) {
        preparando paquete;
        P(baton);
        
        if (cant_paquetes == N) { d_prep = d_prep + 1; V(baton); P(mutex_prep); }
        
        //preparando paquete;
        contenedores[cant_paquetes] = paquete;
        cant_paquetes = cant_paquetes + 1;

        V(baton);

        P(baton);
        
        if (d_entr > 0) { d_entr = d_entr - 1; V(mutex_entr); } 
        else V(baton);
    
    }
}

process Entregador[i=1 to E] {
    int paquete;
    while (true) {
        P(baton);

        if (cant_paquetes == 0) { d_entr = d_entr + 1; V(baton); P(mutex_entr); }
        paquete = contenedores[cant_paquetes];
        cant_paquetes = cant_paquetes - 1;

        V(baton);
        entregar paquete;

        P(baton);
        if (d_prep == 1) { d_prep = d_prep - 1; V(mutex_prep) }
        else V(baton);

    }
}

```
### d) Modifique la solución a) para el caso en que haya P empleados Preparadores y E empleadores Entregadores.
```
int contenedores[N] = ([N] 0)
sem baton = 1, mutex_prep = 0, mutex_entr = 0;
int cant_paquetes = 0, d_prep = 0, d_entr = 0;

process Preparador[i=1 to P] {
    int paquete;
    while (true) {
        preparando paquete;
        P(baton);
        
        if (cant_paquetes == N) { d_prep = d_prep + 1; V(baton); P(mutex_prep); }
        
        //preparando paquete;
        contenedores[cant_paquetes] = paquete;
        cant_paquetes = cant_paquetes + 1;

        V(baton);

        P(baton);
        
        if (d_entr > 0) { d_entr = d_entr - 1; V(mutex_entr); } 
        else V(baton);
    
    }
}

process Entregador[i=1 to E] {
    int paquete;
    while (true) {
        P(baton);

        if (cant_paquetes == 0) { d_entr = d_entr + 1; V(baton); P(mutex_entr); }
        paquete = contenedores[cant_paquetes];
        cant_paquetes = cant_paquetes - 1;

        V(baton);
        entregar paquete;

        P(baton);
        if (d_prep > 0) { d_prep = d_prep - 1; V(mutex_prep) }
        else V(baton);

    }
}

```