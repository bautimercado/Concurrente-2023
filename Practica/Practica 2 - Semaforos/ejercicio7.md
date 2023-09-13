## 7. Suponga que se tiene un curso con 50 alumnos. Cada alumno debe realizar una tarea y existen 10 enunciados posibles. Una vez que todos los alumnos eligieron su tarea, comienzan a realizarla. Cada vez que un alumno termina su tarea, le avisa al profesor y se queda esperando el puntaje del grupo, el cual está dado por todos aquellos que comparten el mismo enunciado. Cuando un grupo terminó, el profesor les otorga un puntaje que representa el orden en que se terminó esa tarea de las 10 posibles.
- Nota: Para elegir la tarea suponga que existe una función elegir que le asigna una tarea a un alumno (esta función asignará 10 tareas diferentes entre 50 alumnos, es decir, que 5 alumnos tendrán la tarea 1, otros 5 la tarea 2 y así sucesivamente para las 10 tareas).
```
sem mutex_tarea = 1, mutex_profesor = 0, barrera = 0, esperando_nota[10] = ([10] 0);
int cant_tareas_entregadas = 0, grupos[10] = ([10] 0), notas[10] = ([10] 0); grupo_termino;

process Alumno[id=1 to 50] {
    int mi_tarea;
    P(mutex_tarea);
    mi_tarea = obtener_tarea();
    cant_tareas_entregadas = cant_tareas_entregadas + 1;
    if (cant_tareas_entregadas == 50) {
        for i = 1 to 50 { V(barrera); }
        V(mutex_profesor);    // Necesario?
    }
    V(mutex_tarea);
    
    P(barrera);
    realizar tarea;
    P(mutex_tarea);   // Una vez que el alumno terminó su tarea
    grupos[mi_tarea] = grupos[mi_tarea] + 1;
    
    // Si los 5 alumnos de una tarea terminaron, se despierta al prof y se espera a que ponga la nota (si libero la SC podría terminar otro alumno y pisar el valor de grupo_termina)
    if (grupos[mi_tarea] == 5) { grupo_termino = mi_tarea; V(mutex_profesor); P(esperando_nota[mi_tarea]); }
    V(mutex_tarea);

    if (notas[mi_tarea] == 0) P(esperando_nota[mi_tarea]);

}

process Profesor {
    P(mutex_profesor);   // Necesario?
    for j = 10 to 1;
        P(mutex_profesor);
        notas[grupo_termino] = k;
        for k = 1 to 5 { V(esperando_nota[grupo_termino]); } // Para que los 5 alumnos de la tarea terminada despierten.

}
```