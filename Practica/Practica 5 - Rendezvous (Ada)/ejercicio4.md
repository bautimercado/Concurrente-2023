# 4. En una clínica existe un médico de guardia que recibe continuamente peticiones de atención de las E enfermeras que trabajan en su piso y de las P personas que llegan a la clínica ser atendidos.

- Cuando una persona necesita que la atiendan espera a lo sumo 5 minutos a que el médico lo haga, si pasado ese tiempo no lo hace, espera 10 minutos y vuelve a requerir la atención del médico. Si no es atendida tres veces, se enoja y se retira de la clínica.
- Cuando una enfermera requiere la atención del médico, si este no lo atiende inmediatamente le hace una nota y se la deja en el consultorio para que esta resuelva su pedido en el momento que pueda (el pedido puede ser que el médico le firme algún papel). Cuando la petición ha sido recibida por el médico o la nota ha sido dejada en el escritorio, continúa trabajando y haciendo más peticiones.
- El médico atiende los pedidos dándole prioridad a los enfermos que llegan para ser atendidos. Cuando atiende un pedido, recibe la solicitud y la procesa durante un cierto tiempo. Cuando está libre aprovecha a procesar las notas dejadas por las enfermeras.

```ada
Process Main is
    Task Type Persona;
    Task Body Persona
        int nivel_de_enojo = 1;
        bool atendido = false;
    Begin
        while (nivel_de_enojo < 3 and not atendido) loop
            select
                Medico.atender_persona();
                atendido = true;
            or delay(300.0);
                delay(600.0);
                nivel_de_enojo++;
            end select;
        end loop;
    End Persona;
    arr_personas: array [1..P] of Persona;

    Task Type Enfermera;
    Task Body Enfermera;
        Pedido pedido;
    Begin
        loop
            -- Trabajando;
            select
                Medico.atender_enfermera();
            else
                pedido = generar_pedido();
                EscritorioMedico.dejar_pedido(pedido);
            end select;
        end loop;
    End Enfermera;
    arr_enfermeras: array [1..E] of Enfermera;

    Task EscritorioMedico is
        entry dejar_pedido(pedido: IN Pedido);
        entry obtener_pedido(pedido: OUT Pedido);
    End EscritorioMedico;
    Task Body EscritorioMedico
        queue pedidos;
    Begin
        loop
            select
                accept dejar_pedido(pedido: IN Pedido) do
                    pedidos.push(pedido);
                end dejar_pedido;
            or
                when (not pedidos.empty()) => accept obtener_pedido(pedido: OUT Pedido) do
                    pedido = pedidos.pop();
                end obtener_pedido;
            end select;
        end loop;
    End EscritorioMedico;

    Task Medico is
        entry atender_persona();
        entry atender_enfermera();
    End Medico;
    Task Body Medico
        Pedido pedido;
    Begin
        loop
            select
                accept atender_persona() do
                    -- Atendiendo a una persona por cierto tiempo;
                end atender_persona;
            or
                when (atender_persona'count == 0) => accept atender_enfermera() do
                    -- Atendiendo enfermera por cierto tiempo;
                end atender_enfermera;
            else
                select
                    EscritorioMedico.obtener_pedido(pedido);
                    -- Atender pedido pendiente;
                else
                    null;
                end select;
            end select;
        end loop;
    End Medico;
Begin
    null;
End Main;
```
