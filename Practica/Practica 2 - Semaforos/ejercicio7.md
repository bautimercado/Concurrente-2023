## 7. Suponga que se tiene un curso con 50 alumnos. Cada alumno debe realizar una tarea y existen 10 enunciados posibles. Una vez que todos los alumnos eligieron su tarea, comienzan a realizarla. Cada vez que un alumno termina su tarea, le avisa al profesor y se queda esperando el puntaje del grupo, el cual está dado por todos aquellos que comparten el mismo enunciado. Cuando un grupo terminó, el profesor les otorga un puntaje que representa el orden en que se terminó esa tarea de las 10 posibles.

" Nota: Para elegir la tarea suponga que existe una función elegir que le asigna una tarea a un alumno (esta función asignará 10 tareas diferentes entre 50 alumnos, es decir, que 5 alumnos tendrán la tarea 1, otros 5 la tarea 2 y así sucesivamente para las 10 tareas).

```
sem mutex_cant_alumnos = 1, profesor = 0, mutex_grupo[10] = ([10] 0), mutex_cola = 1;
sem barrera = 0, esperando_nota[10] = ([10] 0);
int cant_alumnos = 0, grupo_alumnos[10] = ([10] 0), nota_grupo[10] = ([10] 0);
Cola c_grupos[10];

process Alumno[id=1 to 50] {
    int id_grupo;

    id_grupo = obtener_tarea();
    P(mutex_cant_alumnos);
    cant_alumnos = cant_alumnos + 1;
    V(mutex_cant_alumnos);

    if (cant_alumnos == 50) {
        for i = 1 to 50 { V(barrera); }
    }
    
    P(barrera);
    //realizar tarea;
    P(mutex_grupo[id_grupo]);
    grupo_alumnos[id_grupo] = grupos[id_grupo] + 1;
    if (grupo_alumnos[id_grupo] == 5) {
        P(mutex_cola);
        c_grupos.push(id_grupo);
        V(mutex_cola);
        V(profesor);
    }
    else
        V(mutex_grupo[id_grupo]);
    P(esperando_nota[id_grupo]);
}

process Profesor {
    int id_grupo;
    for j = 10 to 1;
        P(profesor);
        P(mutex_cola);
        id_grupo = c_grupo.pop();
        V(mutex_cola);
        nota_grupo[id_grupo] = j;

        for k = 1 to 5 { V(esperando_nota[id_grupo]); }
}
```
