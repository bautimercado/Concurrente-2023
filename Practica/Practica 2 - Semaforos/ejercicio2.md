## 2. Un sistema de control cuenta con 4 procesos que realizan chequeos en forma colaborativa. Para ello, reciben el historial de fallos del día anterior (por simplicidad, de tamaño N). De cada fallo, se conoce su número de identificación (ID) y su nivel de gravedad (0=bajo, 1=intermedio, 2=alto, 3=crítico). Resuelva considerando las siguientes situaciones:

### a) Se debe imprimir en pantalla los ID de todos los errores críticos (no importa el orden).
```
fallos = set of (int, int);
sem mutex = 1;

Process verificador[i = 1 to 4] {
    int id, nivel;
    P(mutex);
    while (fallos != Ø) {
        tomar_primero(fallos, id, nivel);
        V(mutex);
        if (nivel == 3) print(id);
        P(mutex);
    }
    V(mutex);
} 

```
### b) Se debe calcular la cantidad de fallos por nivel de gravedad, debiendo quedar los resultados en un vector global.
```
fallos = set of (int, int);
sem mutex = 1;
int cant_fallos_por_nivel[3] = ([n] 0)

Process verificador[i =  1 to 4] {
    int id, nivel;
    P(mutex);
    while (fallos != Ø) {
        tomar_primero(fallos, id, nivel);
        cant_fallos_por_nivel[nivel] = cant_fallos_por_nivel[nivel] + 1;
        V(mutex);
        P(mutex);
    }
    V(mutex);
}
```
### c) Ídem b) pero cada proceso debe ocuparse de contar los fallos de un nivel de gravedad determinado.
```
fallos = set of (int, int);
sem mutex = 1
int cant_fallos_por_nivel[3] = ([n] 0)


Process verificador[i = 0 to 3] {
    int id, nivel;
    P(mutex);
    while (fallos != Ø) {
        tomar_primero(fallos, id, nivel);
        if (nivel == i) cant_fallos_por_nivel[i] = cant_fallos_por_nivel[i] + 1;
        else insertar(fallos, id, nivel);      
        V(mutex);
        P(mutex);
    }
    V(mutex);
}
```