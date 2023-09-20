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
```
Cola c;
sem mutex = 1, sem_persona[N] = ([N] 0);
bool libre = true;

process Persona[id=1 to N] {
    int id_extraido;
    P(mutex);
    if (libre) {
        libre = false;
        V(mutex);
    }
    else {
        c.push(id);
        V(mutex);
        P(sem_persona[id]);
    }
    Imprimir(documento);
    P(mutex);
    if (c.empty())
        libre = true;
    else {
        id_extraido = c.pop();
        V(sem_persona[id_extraido]);
    }
    V(mutex);

    
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
Cola c;
sem mutex = 1, sem_persona[N] = ([N] 0), sem_coordinador = 0, persona_termina = 0;

process Persona[id=1 to N] {
    P(mutex);
    c.push(id);
    V(mutex);
    V(sem_coordinador);
    P(sem_pers[id]);
    Imprimir(documento);
    V(persona_termina);
}

process Coordinador {
    int id_extraido;
    while (true) {
        P(sem_coordinador);
        P(mutex);
        id_extraido = c.pop();
        V(mutex);
        V(sem_pers[id_extraido]);
        P(persona_termina);
    }
}
```
### e) Modificar la solución (d) para el caso en que sean 5 impresoras. El coordinador le indica a la persona cuando puede usar una impresora, y cual debe usar.
```
Cola c, impresoras;
sem mutex_c = 1, mutex_impresoras = 1,sem_pers[N] = ([N] 0), cant_impresoras = 5, coordinador = 0;
Impresora persona_impresora[N];

process Persona[id=1 to N] {
    Impresora impresora;
    P(mutex_c);
    c.push(id);
    V(mutex_c);

    V(coordinador);
    P(sem_pers[id]);
    impresora = persona_impresora[id];
    Imprimir(impresora, documento);

    P(mutex_impresoras);
    impresoras.push(impresora);
    V(mutex_impresoras);

    V(cant_impresoras);
}

process Coordinador {
    int id_extraido;
    Impresora impresora;
    while (true) {
        P(coordinador);
        P(cant_impresoras);

        P(mutex_c);
        id_extraido = c.pop();
        V(mutex_c);

        P(mutex_impresoras);
        impresora = impresoras.pop();
        V(mutex_impresoras);
    
        persona_impresora[id_extraido] = impresora;
        V(sem_pers[id_extraido]);
    }
}
```
