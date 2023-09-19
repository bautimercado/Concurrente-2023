## 6. Existen N personas que deben imprimir un trabajo cada una. Resolver cada ítem usando semáforos:
### a) Implemente una solución suponiendo que existe una única impresora compartida por todas las personas, y las mismas la deben usar de a una persona a la vez, sin importar el orden. Existe una función Imprimir(documento) llamada por la persona que simula el uso de la impresora. Sólo se deben usar los procesos que representan a las Personas.
```
sem mutex = 1;

process Persona[i=1 to N] {
    P(mutex);
    Imprimir(documento);
    V(mutex);
}
```
### b) Modifique la solución de (a) para el caso en que se deba respetar el orden de llegada.
- Se podría hacer con una cola. Qué sería mejor?
```
sem mutex = 1; sem_priv[N] = ([N] 0)
int llegada = 1; turno_de = 1;

process Persona[i=1 to N] {
    int mi_turno;
    P(mutex);
    mi_turno = llegada;
    llegada = llegada + 1;
    V(mutex);

    P(mutex);
    if (turno_de != mi_turno) { V(mutex); P(sem_priv[mi_turno]) }
    Imprimir(documento);
    turno_de = turno_de + 1;
    V(mutex);

    if (mi_turno != N) V(sem_priv[mi_turno+1]);
}
```
### c) Modifique la solución de (a) para el caso en que se deba respetar estrictamente el orden dado por el identificador del proceso (la persona X no puede usar la impresora hasta que no haya terminado de usarla la persona X-1).
```
sem mutex = 1; sem_priv[N] = ([N] 0)
int turno_de = 1;

process Persona[i=1 to N] {
    P(mutex);
    if (turno_de == i) { V(mutex); P(sem_priv[i]); }
    Imprimir(documento);
    turno_de = turno_de + 1;
    V(mutex);

    if (i != N) V(sem_priv[i+1]);
}
```
### d) Modifique la solución de (b) para el caso en que además hay un proceso Coordinador que le indica a cada persona que es su turno de usar la impresora.
```
int llegada = 1;
sem mutex = 1, sem_pers[N] = ([N] 0), sem_coor = 0;

process Persona[i=1 to N] {
    int mi_turno;
    P(mutex);
    mi_turno = llegada;
    llegada = llegada + 1;
    V(mutex);
    P(sem_pers[mi_turno]);
    Imprimir(documento);
    V(sem_coor);
}

process Coordinador {
    for j = 1 to N {
        V(sem_pers[j]);
        P(sem_coor);
    }
}
```
### e) Modificar la solución (d) para el caso en que sean 5 impresoras. El coordinador le indica a la persona cuando puede usar una impresora, y cual debe usar.
```
cola Impresoras;
int llegada = 1;
sem mutex = 1, sem_pers[N] = ([N] 0), cant_impresoras = 5, mutex_cola = 1;

process Persona[i=1 to N] {
    Impresora impresora;
    int mi_turno;
    
    P(mutex);
    mi_turno = llegada;
    llegada = llegada + 1;
    V(mutex);

    P(sem_pers[mi_turno]);
    P(mutex_cola);
    impresora = Impresoras.pop();
    V(mutex_cola);
    impresora.Imprimir(documento);
    

    P(mutex_cola);
    Impresoras.push(impresora);
    V(mutex_cola);
    V(cant_impresoras);
}

process Coordinador {
    for j = 1 to N {
        V(sem_pers[j]);
        P(cant_impresoras);
    }
}
```
