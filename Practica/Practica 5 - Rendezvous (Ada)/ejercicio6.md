# 6. En una playa hay 5 equipos de 4 personas cada uno (en total son 20 personas donde cada una conoce previamente a que equipo pertenece). Cuando las personas van llegando esperan con los de su equipo hasta que el mismo esté completo (hayan llegado los 4 integrantes), a partir de ese momento el equipo comienza a jugar. El juego consiste en que cada integrante del grupo junta 15 monedas de a una en una playa (las monedas pueden ser de 1, 2 o 5 pesos) y se suman los montos de las 60 monedas conseguidas en el grupo. Al finalizar cada persona debe conocer el grupo que más dinero junto. Nota: maximizar la concurrencia. Suponga que para simular la búsqueda de una moneda por parte de una persona existe una función Moneda() que retorna el valor de la moneda encontrada.

```ada
Procedure Main is
    Task Administrador is
        entry llega_persona1();
        entry llega_persona2();
        entry llega_persona3();
        entry llega_persona4();
        entry llega_persona5();
        entry recibir_monedas1(monto: in double);
        entry recibir_monedas2(monto: in double);
        entry recibir_monedas3(monto: in double);
        entry recibir_monedas4(monto: in double);
        entry recibir_monedas5(monto: in double);
    End Administrador;
    Task Body Administrador
        integer cant_grupos_terminaron = 0;
        integer grupo_ganador;
        arr_montos: array (1..5) of double;
        arr_integrantes_terminaron: array (1..5) of integer;
        -- Los arrays comienzan inicializados en 0
    Begin
        while (cant_grupos_terminaron < 5) loop
            select
                when (llega_persona1'count == 4) => accept llega_persona1() do
                    for i in 1..3 loop
                        accept llega_persona1();
                    end loop;
                end llega_persona1;

                or when (llega_persona2'count == 4) => accept llega_persona2() do
                    for i in 1..3 loop
                        accept llega_persona2();
                    end loop;
                end llega_persona2;

                or when (llega_persona3'count == 4) => accept llega_persona3() do
                    for i in 1..3 loop
                        accept llega_persona3();
                    end loop;
                end llega_persona3;

                or when (llega_persona4'count == 4) => accept llega_persona4() do
                    for i in 1..3 loop
                        accept llega_persona4();
                    end loop;
                end llega_persona4;

                or when (llega_persona5'count == 4) => accept llega_persona5() do
                    for i in 1..3 loop
                        accept llega_persona5();
                    end loop;
                end llega_persona5;

                or accept recibir_monedas1(monto: in double) do
                    arr_montos(1) += monto;
                    arr_integrantes_terminaron(1)++;
                    if (arr_integrantes_terminaron(1) == 4) then
                        cant_grupos_terminaron++;
                    end if;
                end recibir_monedas1;

                or accept recibir_monedas2(monto: in double) do
                    arr_montos(2) += monto;
                    arr_integrantes_terminaron(2)++;
                    if (arr_integrantes_terminaron(2) == 4) then
                        cant_grupos_terminaron++;
                    end if;
                end recibir_monedas2;

                or accept recibir_monedas3(monto: in double) do
                    arr_montos(3) += monto;
                    arr_integrantes_terminaron(3)++;
                    if (arr_integrantes_terminaron(3) == 4) then
                        cant_grupos_terminaron++;
                    end if;
                end recibir_monedas3;

                or accept recibir_monedas4(monto: in double) do
                    arr_montos(4) += monto;
                    arr_integrantes_terminaron(4)++;
                    if (arr_integrantes_terminaron(4) == 4) then
                        cant_grupos_terminaron++;
                    end if;
                end recibir_monedas4;

                or accept recibir_monedas5(monto: in double) do
                    arr_montos(5) += monto;
                    arr_integrantes_terminaron(5)++;
                    if (arr_integrantes_terminaron(5) == 4) then
                        cant_grupos_terminaron++;
                    end if;
                end recibir_monedas5;
            end select;
        end loop;
        grupo_ganador = obtener_maximo(arr_montos(*)); -- Función que obtiene el número de grupo que más dinero juntó
        for i in 1..20 loop
            Persona(i).saber_ganador(grupo_ganador);
        end loop;
    End Administrador;

    Task Type Persona is
        entry asignar_equipo(id_equipo: in integer);
        entry saber_ganador(id_equipo: in integer);
    End Persona;
    Task Body Persona
        integer equipo, ganador;
        integer cant_monedas = 0;
        double monto = 0;
    Begin
        accept asignar_equipo(id_equipo: in integer) do
            equipo = id_equipo;
        end asignar_equipo;
        
        if (equipo == 1) then
            Administrador.llega_persona1();
        elsif (equipo == 2) then
            Administrador.llega_persona2();
        elsif (equipo == 3) then
            Administrador.llega_persona3();
        elsif (equipo == 4) then
            Administrador.llega_persona4();
        elsif (equipo == 5) then
            Administrador.llega_persona5();
        end if;
        
        while cant_monedas < 15 loop
            monto += Moneda();
            cant_monedas++;
        end loop;

        if (equipo == 1) then
            Administrador.recibir_monedas1(monto);
        elsif (equipo == 2) then
            Administrador.recibir_monedas2(monto);
        elsif (equipo == 3) then
            Administrador.recibir_monedas3(monto);
        elsif (equipo == 4) then
            Administrador.recibir_monedas4(monto);
        elsif (equipo == 5) then
            Administrador.recibir_monedas5(monto);
        end if;

        accept saber_ganador(id_equipo: in integer) do
            ganador = id_equipo;
        end saber_ganador;
    End Persona;
    arr_personas: array (1..20) of Persona;
Begin
    for i in 1..20 loop
        Persona(i).asignar_equipo(random(5));
    end loop;
End Main;
```