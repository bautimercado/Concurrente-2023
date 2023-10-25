# 3. Se dispone de un sistema compuesto por 1 central y 2 procesos periféricos, que se comunican continuamente. Se requiere modelar su funcionamiento considerando las siguientes condiciones:

- La central siempre comienza su ejecución tomando una señal del proceso 1; luego toma aleatoriamente señales de cualquiera de los dos indefinidamente. Al recibir una señal de proceso 2, recibe señales del mismo proceso durante 3 minutos.
- Los procesos periféricos envían señales continuamente a la central. La señal del proceso 1 será considerada vieja (se deshecha) si en 2 minutos no fue recibida. Si la señal del proceso 2 no puede ser recibida inmediatamente, entonces espera 1 minuto y vuelve a mandarla (no se deshecha).

```ada
Process Main is
    Task Proceso1;
    Task Body Proceso1 is
        Señal s;
    Begin
        loop
            s = generar_señal();
            select
                Central.aceptar_p1(s);
            or delay(120)
                null;
            end select;
        end loop;
    End Proceso1;

    Task Proceso2;
    Task Body Proceso2 is
        Señal s;
    Begin
        s = generar_señal();
        loop
            select
                Central.aceptar_p2(s);
                s = generar_señal();  -- Acá o en el or??
            or delay(60);
                null;
            end select;
        end loop;
    End Proceso2;

    Task Cental is
        entry aceptar_p1(s: in Señal);
        entry aceptar_p2(s: in Señal);
        entry timeout();
    End Cental;
    Task Body Cental is
        Señal señal;
        Boolean solo_p2 = false;
    Begin
        accept aceptar_p1(s: in Señal) do
            señal = s;
        end aceptar_p1;
        -- Usar señal de p1
        loop
            select
                where (not solo_p2) => accept aceptar_p1(s: in Señal) do
                    señal = s;
                end aceptar_p1;
                -- Usar señal de p1
            or
                accept aceptar_p2(s: in Señal) do
                    señal = s;
                end aceptar_p2;
                Timer.contar();
                solo_p2 = true;
                -- Usar señal de p2;
            or
                accept timeout() do
                    solo_p2 = false;
                end timeout;
        end loop;
    End Cental;

    Task Timer is
        entry contar();
    End Timer;
    Task Body Timer is
        loop
            accept contar() do
                delay(180);
            end contar;
            Central.timeout();
        end loop;
    End Timer;

Begin
    null;
End Main;
```