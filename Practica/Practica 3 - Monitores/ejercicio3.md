## 3. Existen N personas que deben fotocopiar un documento. La fotocopiadora sólo puede ser usada por una persona a la vez. Analice el problema y defina qué procesos, recursos y monitores serán necesarios/convenientes, además de las posibles sincronizaciones requeridas para resolver el problema. Luego, resuelva considerando las siguientes situaciones:

- Necesitaríamos N procesos persona, un monitor Impresora, que tendrá variables que representen si la impresora está libre, una cola de espera para las personas que están esperando y dos procedimientos que simbolizen el bloqueo y liberación del recurso.

### a) Implemente una solución suponiendo no importa el orden de uso. Existe una función Fotocopiar() que simula el uso de la fotocopiadora.
```cpp
Monitor Impresora {
    bool libre = true;
    cond cola;

    procedure ocupar_impresora() {
        if (not libre) { wait(cola); }
        libre = false;
    }

    procedure liberar_impresora() {
        libre = true;
        signal(cola);
    }
}

Process Persona[i=1 to N] {
    Impresora.ocupar_impresora();
    Fotocopiar();
    Impresora.liberar_impresora();
}
```
### b) Modifique la solución de (a) para el caso en que se deba respetar el orden de llegada.

```cpp
Monitor Impresora {
    bool libre = true;
    int espera = 0;
    cond cola;

    procedure ocupar_impresora() {
        if (not libre) { espera++; wait(cola); }
        else { libre = false; }
    }

    procedure liberar_impresora() {
        if (espera > 0) { espera--; signal(cola);}
        else { libre = true; }
    }
}

Process Persona[i=1 to N] {
    Impresora.ocupar_impresora();
    Fotocopiar();
    Impresora.liberar_impresora();
}
```

### c) Modifique la solución de (b) para el caso en que se deba dar prioridad de acuerdo con la edad de cada persona (cuando la fotocopiadora está libre la debe usar la persona de mayor edad entre las que estén esperando para usarla).

```cpp
Monitor Impresora {
    bool libre = true;
    cond espera[N];
    cola durmiendo;

    procedure ocupar_impresora(id, edad: in int) {
        if (not libre) {
            durmiendo.insertar_ordenado(id, edad);
            wait(espera[id]);
        }
        else
            libre = false;
    }

    procedure liberar_impresora() {
        int id;
        if (durmiendo.empty()) libre = true;
        else {
            id = durmiendo.pop();
            signal(espera[id]);
        }
    }
}

Process Persona[id=1 to N] {
    int edad = ...;
    Impresora.ocupar_impresora(id, edad);
    Fotocopiar();
    Impresora.liberar_impresora();
}
```

### d) Modifique la solución de (a) para el caso en que se deba respetar estrictamente el orden dado por el identificador del proceso (la persona X no puede usar la fotocopiadora hasta que no haya terminado de usarla la persona X-1).

```cpp
Monitor Impresora {
    bool libre = true;
    cond espera[N];
    cola durmiendo;

    procedure ocupar_impresora(id: in int) {
        if (not libre) { 
            durmiendo.insertar_ordenado(id);
            wait(espera[id]); 
        }
        libre = false;
    }

    procedure liberar_impresora() {
        int id;
        if (durmiendo.empty())
            libre = true;
        else {
            id = durmiendo.pop();
            signal(espera[id]);
        }
    }
}

Process Persona[id=1 to N] {
    Impresora.ocupar_impresora(id);
    Fotocopiar();
    Impresora.liberar_impresora();
}
```

### e) Modifique la solución de (b) para el caso en que además haya un Empleado que le indica a cada persona cuando debe usar la fotocopiadora.

```cpp
Monitor Impresora {
    bool libre = true;
    int cant_dormidos = 0;
    cond personas, esperar_persona, esperar_liberacion;

    procedure pedir_impresora() {
        cant_dormidos++;
        signal(esperar_persona);
        wait(personas);
    }

    procedure dar_impresora() {
        if (cant_dormidos == 0)
            wait(esperar_persona);
        if (not libre)
            wait(esperar_liberacion);
        cant_dormidos--;
        signal(personas);
        libre = false;
    }

    procedure devolver_impresora() {
        libre = true;
        signal(esperar_liberacion);
    }
}

Process Persona[id=1 to N] {
    Impresora.pedir_impresora();
    Fotocopiar();
    Impresora.devolver_impresora();
}

Process Empleado {
    int cant_personas = N;
    while (cant_personas > 0) {
        Impresora.dar_impresora();
        cant_personas++;
    }
}
```

### f) Modificar la solución (e) para el caso en que sean 10 fotocopiadoras. El empleado le indica a la persona cuál fotocopiadora usar y cuándo hacerlo.

- A qué se refiere con cuando hacerlo??

```cpp
Monitor Impresora {
    cola fotocopiadoras<Fotocopiadora>;  //Con 10 instancias de fotocopiadoras
    cola dormidos<int>;
    Fotocopiadora fotocopiadora_persona[N] = ([N] nil)
    cond personas[N], esperar_persona, esperar_fotocopiadora;

    procedure pedir_impresora(fotocopiadora: Fotocopiadora out; id: int in) {
        dormidos.push(id);
        signal(esperar_persona);
        wait(personas[id]);
        fotocopiadora = fotocopiadora_persona[id];
    }

    procedure dar_impresora() {
        int id;
        Fotocopiadora fotocopiadora;
        if (dormidos.empty())
            wait(esperar_persona);
        if (fotocopiadoras.empty())
            wait(esperar_fotocopiadora);
        id = dormidos.pop();
        signal(personas[id]);
        fotocopiadoras.pop();
        fotocopiadora_persona[id] = fotocopiadora;
    }

    procedure devolver_impresora(fotocopiadora: Fotocopiadora in) {
        signal(esperar_fotocopiadora);
        fotocopiadoras.push(fotocopiadora);
    }
}

Process Persona[id=1 to N] {
    Fotocopiadora fotocopiadora;
    Impresora.pedir_impresora(fotocopiadora, id);
    Fotocopiar(fotocopiadora);
    Impresora.devolver_impresora(fotocopiadora);
}

Process Empleado {
    int cant_personas = N;
    while (cant_personas > 0) {
        Impresora.dar_impresora();
        cant_personas++;
    }
}
```