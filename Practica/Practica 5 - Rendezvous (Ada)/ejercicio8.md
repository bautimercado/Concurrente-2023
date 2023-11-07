## 8. Una empresa de limpieza se encarga de recolectar residuos en una ciudad por medio de 3 camiones. Hay P personas que hacen continuos reclamos hasta que uno de los camiones pase por su casa. Cada persona hace un reclamo, espera a lo sumo 15 minutos a que llegue un camión y si no vuelve a hacer el reclamo y a esperar a lo sumo 15 minutos a que llegue un camión y así sucesivamente hasta que el camión llegue y recolecte los residuos; en ese momento deja de hacer reclamos y se va. Cuando un camión está libre la empresa lo envía a la casa de la persona que más reclamos ha hecho sin ser atendido. Nota: maximizar la concurrencia.

```ada
Procedure Main is
    Task Type Camion;
    Task Body Camion
        integer id, persona;
    Begin
        loop
            Empresa.pedir_trabajo(persona);
            Persona(persona).recibir_camion();
            Empresa.camion_termina();
        end loop;
    End Camion;
    arr_camion: array (1..3) of Camion;

    Task Empresa is
        entry pedir_camion(intentos: in integer, id_persona: in integer);
        entry camion_termina();
        entry pedir_trabajo(id_persona: out integer);
    End Empresa;
    Task Body Empresa
        integer camiones = 3;
        cola reclamos(intentos: in integer,
                      id_persona: in integer);
    Begin
        loop
            select
                accept camion_termina() do
                    camiones++;
                end camion_termina;
            or
                when (not reclamos.empty()) => accept pedir_trabajo(id_persona: out integer) do
                    id_persona = reclamos.pop();
                end pedir_trabajo;
                camiones--;
            or
                when (not camiones.empty()) => accept pedir_camion(intentos: in integer, id_persona: in integer) do
                    reclamos.insertar_ordenado_por_intentos(intentos, id_persona);
                end pedir_camion;
            end select;
        end loop;
    End Empresa;

    Task Type Persona is
        entry asignar_id(id_persona: in integer);
        entry recibir_camion();
    End Persona;
    Task Body Persona
        boolean termino = false;
        integer intentos = 1, id;
    Begin
        accept asingar_id(id_persona: in integer) do
            id = id_persona;
        end asignar_id;

        while (not termino) loop
            select
                Empresa.pedir_camion(intentos, id);
                accept recibir_camion();
                termino = true;
            or delay(900.0);
                intentos++;
            end select;
        end loop;
    End Persona;
    arr_persona: array (1..P) of Persona;
Begin
    for i in 1..P loop
        Persona(i).asignar_id(i);
    end loop;
End Main;
```