# 3. En un examen final hay N alumnos y P profesores. Cada alumno resuelve su examen, lo entrega y espera a que alguno de los profesores lo corrija y le indique la nota. Los profesores corrigen los exámenes respetando el orden en que los alumnos van entregando.

## a) Considerando que P=1.

```cpp
process Alumno[id:1..N] {
    double nota;
    // Haciendo examen;
    Buffer!examen(id);
    Profesor?nota(nota);
}

process Buffer {
    int id_alumno;
    cola resoluciones;
    while (true) {
        do ❏ Alumno[*]?examen(id_alumno) ->
            resoluciones.push(id_alumno)
        ❏ not resoluciones.empty(); Profesor?listo() ->
            Profesor!corregir(resoluciones.pop())
    }
}

process Profesor {
    double nota;
    int id_alumno;
    while (true) {
        Buffer!listo();
        Buffer?corregir(id_alumno);
        // nota = Corrigiendo examen;
        Alumno[id_alumno]!nota(nota);
    }
}
```

## b) Considerando que P>1.

```cpp
process Alumno[id:1..N] {
    double nota;
    // Haciendo examen;
    Buffer!examen(id);
    Profesor?nota(nota);
}

process Buffer {
    int id_alumno, id_profesor;
    cola resoluciones;
    while (true) {
        do Alumno[*]?examen(id_alumno) ->
            resoluciones.push(id_alumno)
        ❏ not resoluciones.empty(); Profesor[*]?listo(id_profesor) ->
            Profesor[id_profesor]!corregir(resoluciones.pop())
    }
}

process Profesor[id:1..P] {
    double nota;
    int id_alumno;
    while (true) {
        Buffer!listo(id);
        Buffer?corregir(id_alumno);
        // nota = Corrigiendo examen;
        Alumno[id_alumno]!nota(nota);
    }
}
```

## c) Ídem b) pero considerando que los alumnos no comienzan a realizar su examen hasta que todos hayan llegado al aula.

Nota: maximizar la concurrencia y no generar demora innecesaria.

```cpp
process Alumno[id:1..N] {
    double nota;
    Coordinador!llegada();
    Coordinador!empezar();
    // Haciendo examen;
    Coordinador!examen(id);
    Coordinador?nota(nota);
}

process Coordinador {
    int id_alumno, id_profesor;
    cola resoluciones;
    for i = 1 to N
        Alumno[*]?llegada();
    for i = 1 to N
        Alumno[i]*?empezar();
    while (true) {
        do Alumno[*]?examen(id_alumno) ->
            resoluciones.push(id_alumno)
        ❏ not resoluciones.empty(); Profesor[*]?listo(id_profesor) ->
            Profesor[id_profesor]!corregir(resoluciones.pop())
    }
}

process Profesor[id:1..P] {
    double nota;
    int id_alumno;
    while (true) {
        Coordinador!listo(id);
        Coordinador?corregir(id_alumno);
        // nota = Corrigiendo examen;
        Alumno[id_alumno]!nota(nota);
    }
}
```