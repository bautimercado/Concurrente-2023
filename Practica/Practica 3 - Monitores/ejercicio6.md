## 6. Existe una comisión de 50 alumnos que deben realizar tareas de a pares, las cuales son corregidas por un JTP. Cuando los alumnos llegan, forman una fila. Una vez que están todos en fila, el JTP les asigna un número de grupo a cada uno. Para ello, suponga que existe una función AsignarNroGrupo() que retorna un número “aleatorio” del 1 al 25. Cuando un alumno ha recibido su número de grupo, comienza a realizar su tarea. Al terminarla, el alumno le avisa al JTP y espera por su nota. Cuando los dos alumnos del grupo completaron la tarea, el JTP les asigna un puntaje (el primer grupo en terminar tendrá como nota 25, el segundo 24, y así sucesivamente hasta el último que tendrá nota 1). Nota: el JTP no guarda el número de grupo que le asigna a cada alumno.

```cpp
Monitor comision {
    cond alumnos, jtp;
    int cant_alumnos = 0, nota = 25;
    cola<int> ids_alumnos, nro_grupos;
    grupo_alumno[50] = ([50] 0), notas_grupo[25] = ([25] 0), alumnos_grupo[25] = ([25] 0);

    procedure formar_fila(id: int in) {
        cant_alumnos++;
        ids_alumnos.push(id);
        if (cant_alumnos == 50) { signal(jtp); }
        wait(alumnos);
    }

    procedure esperar_y_asignar_grupos() {
        int id;
        if (cant_alumnos != 50) { wait(jtp); }
        for i = 1 to 50 {
            id = ids_alumnos.pop();
            grupo_alumno[id] = AsignarNroGrupo();
        }
    }

    procedure despertar_alumnos() {
        signal_all(alumnos);
    }

    procedure obtener_mi_grupo(grupo: int out; id: int in) {
        grupo = grupo_alumno[id];
    }

    procedure terminar_tarea(grupo: int in) {
        nro_grupos.push(grupo);
        signal(jtp);
        wait(alumnos);
    }

    procedure alumno_termino() {
        int grupo;
        if (nro_grupos.empty()) { wait(jtp); }
        grupo = nro_grupos.pop();
        alumnos_grupo[grupo]++;
        if (alumnos_grupo[grupo] == 2) {
            notas_grupo[grupo] = nota;
            nota--;
        }
    }
}

Process JTP {
    comision.esperar_y_asignar_grupos();
    comision.despertar_alumnos();
    for i = 1 to 50 {
        comision.alumno_termino();
    }
    comision.despertar_alumnos();
}

Process alumno[id=1 to 50] {
    int grupo;
    comision.formar_fila(id);
    comision.obtener_mi_grupo(grupo, id);
    //realizar tarea
    comision.terminar_tarea(grupo);
}
```
