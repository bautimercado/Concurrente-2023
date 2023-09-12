## 2. Un sistema de control cuenta con 4 procesos que realizan chequeos en forma colaborativa. Para ello, reciben el historial de fallos del día anterior (por simplicidad, de tamaño N). De cada fallo, se conoce su número de identificación (ID) y su nivel de gravedad (0=bajo, 1=intermedio, 2=alto, 3=crítico). Resuelva considerando las siguientes situaciones:

### a) Se debe imprimir en pantalla los ID de todos los errores críticos (no importa el orden).
```
fallos = set of (int, int);
sem mutex = 1;

Process verificador[i = 1 to 4] {
    int id, nivel;
    while (fallos != Ø) {
        P(mutex);
        tomar_primero(fallos, id, nivel);
        if (nivel == 3) print(id);
        V(mutex);
    }
} 

```
### b) Se debe calcular la cantidad de fallos por nivel de gravedad, debiendo quedar los resultados en un vector global.
```
fallos = set of (int, int);
sem mutex = 1;
int cant_fallos_por_nivel[3] = ([n] 0)

Process verificador[i =  1 to 4] {
    int id, nivel;
    while (fallos != Ø) {
        P(mutex);
        tomar_primero(fallos, id, nivel);
        cant_fallos_por_nivel[nivel] = cant_fallos_por_nivel[nivel] + 1;
        V(mutex);
    }
}
```
### c) Ídem b) pero cada proceso debe ocuparse de contar los fallos de un nivel de gravedad determinado.
```
fallos = set of (int, int);
sem mutex = 1
int cant_fallos_por_nivel[3] = ([n] 0)

// Se necesita un semáforo para los dos procesos?
Process verificador_critico[i =  1 to 2] {
    int id, nivel;
    while (fallos != Ø) {
        P(mutex);

        tomar_primero(fallos, id, nivel);
        if (nivel == 3) cant_fallos_por_nivel[3] = cant_fallos_por_nivel[3] + 1;
        else insertar(fallos, id, nivel);
        
        V(mutex);
    }
}

Process verificador_alto {
    int id, nivel;
    while (fallos != Ø) {
        P(mutex);

        tomar_primero(fallos, id, nivel);
        if (nivel == 2) cant_fallos_por_nivel[2] = cant_fallos_por_nivel[2] + 1;
        else insertar(fallos, id, nivel);
        
        V(mutex);
    }
}

Process verificador_intermedio {
    int id, nivel;
    while (fallos != Ø) {
        P(mutex);

        tomar_primero(fallos, id, nivel);
        if (nivel == 1) cant_fallos_por_nivel[1] = cant_fallos_por_nivel[1] + 1;
        else insertar(fallos, id, nivel);
        
        V(mutex);
    }
}
```
__// Está bien la solución? Hay alguna otra forma de hacerlo sin tener que definir varios procesos?__