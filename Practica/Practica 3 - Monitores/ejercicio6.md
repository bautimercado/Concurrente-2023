## 6. Existe una comisión de 50 alumnos que deben realizar tareas de a pares, las cuales son corregidas por un JTP. Cuando los alumnos llegan, forman una fila. Una vez que están todos en fila, el JTP les asigna un número de grupo a cada uno. Para ello, suponga que existe una función AsignarNroGrupo() que retorna un número “aleatorio” del 1 al 25. Cuando un alumno ha recibido su número de grupo, comienza a realizar su tarea. Al terminarla, el alumno le avisa al JTP y espera por su nota. Cuando los dos alumnos del grupo completaron la tarea, el JTP les asigna un puntaje (el primer grupo en terminar tendrá como nota 25, el segundo 24, y así sucesivamente hasta el último que tendrá nota 1). Nota: el JTP no guarda el número de grupo que le asigna a cada alumno.

```cpp
Monitor JTP {
    int cant_alumnos = 0, nota = 25;
    entrega_grupos[25] = ([25] 0);
    notas_grupos[25];
    cond barrera, esperando_nota[25];

    procedure get_grupo(id_grupo: int out) {
        cant_alumnos++;
        if (cant_alumnos == 50)
            signal_all(barrera);
        else
            wait(barrera);
        id_grupo = AsignarNroGrupo();
    }

    procedure entregar(id_grupo: int in; nota_obtenida: int out) {
        entrega_grupos[id_grupo]++;
        if (entrega_grupos[id_grupo] == 2) {
            notas_grupos[id_grupo] = nota;
            nota--;
            signal(esperando_nota[id_grupo]);
        }
        else
            wait(esperando_nota[id_grupo]);
        nota_obtenida = notas_grupos[id_grupo];
    }
}

Process alumno[id=1 to 50] {
    int id_grupo, nota_obtenida;
    JTP.get_grupo(id_grupo);
    //Realizar tarea;
    JTP.entregar(id_grupo, nota_obtenida);
}
```