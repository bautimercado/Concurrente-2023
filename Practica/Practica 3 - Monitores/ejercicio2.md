## 2. Existen N procesos que deben leer información de una base de datos, la cual es administrada por un motor que admite una cantidad limitada de consultas simultáneas.

### a) Analice el problema y defina qué procesos, recursos y monitores serán necesarios/convenientes, además de las posibles sincronizaciones requeridas para resolver el problema.

- Necesitariamos procesos lectores que querrán acceder a la base de datos, después necesitariamos un monitor el cuál tendrá la cantidad de consultas que haya y una cola (variable condición) para los lectores que esperan.
  - Un lector solicitará el monitor y se restara la cantidad de consultas admitidas.
  - Cuando el lector sale de la BD, se incrementa esa cantidad y se despierta a algún proceso que haya estado esperando.

### b) Implemente el acceso a la base por parte de los procesos, sabiendo que el motor de base de datos puede atender a lo sumo 5 consultas de lectura simultáneas.

```cpp
Monitor AccesoBD() {
    int cant_consultas = 5;
    cond cola;

    procedure acceder() {
        while (cant_consultas == 0) { wait(cola); }
        cant_consultas--;
    }

    procedure salir() {
        cant_consultas++;
        signal(cola);
    }
}

Process Lector[i=1 to L] {
    AccesoBD.acceder();
    //Leer registro;
    AccesoBD.salir();
}
```