## 1. Existen N personas que deben ser chequeadas por un detector de metales antes de poder ingresar al avión.


### a. Analice el problema y defina qué procesos, recursos y semáforos serán necesarios/convenientes, además de las posibles sincronizaciones requeridas para resolver el problema.
- En esta solución, bastaría con tener un único semáforo (ya que el recurso compartida es uno solo, el detector de metales) y N procesos Persona que competirán por el detector.
- Está el semáforo `detector`, cada proceso `Persona` intenta acceder al detector si es que está libre.

### b. Implemente una solución que modele el acceso de las personas a un detector (es decir, si el detector está libre la persona lo puede utilizar; en caso contrario, debe esperar).
```
sem detector = 1;

Process Persona[i = 1 to N] {
    P(detector);
    avanzar al detector;
    V(detector);
}
```

### c. Modifique su solución para el caso que haya tres detectores.
```
sem detector = 3;

Process Persona[i = 1 to N] {
    P(detector);
    avanzar al detector;
    V(detector);
}
```
