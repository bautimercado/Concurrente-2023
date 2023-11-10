## 7. Hay un sistema de reconocimiento de huellas dactilares de la policía que tiene 8 Servidores para realizar el reconocimiento, cada uno de ellos trabajando con una Base de Datos propia; a su vez hay un Especialista que utiliza indefinidamente. El sistema funciona de la siguiente manera: el Especialista toma una imagen de una huella (TEST) y se la envía a los servidores para que cada uno de ellos le devuelva el código y el valor de similitud de la huella que más se asemeja a TEST en su BD; al final del procesamiento, el especialista debe conocer el código de la huella con mayor valor de similitud entre las devueltas por los 8 servidores. Cuando ha terminado de procesar una huella comienza nuevamente todo el ciclo. Nota: suponga que existe una función Buscar(test, código, valor) que utiliza cada Servidor donde recibe como parámetro de entrada la huella test, y devuelve como parámetros de salida el código y el valor de similitud de la huella más parecida a test en la BD correspondiente. Maximizar la concurrencia y no generar demora innecesaria.

```ada
Procedure Main is
    Task Type Servidor;
    End Servidor
    Task Body Servidor
        Huella test;
        integer codigo;
        double valor;
    Begin
        loop
            Especialista.pedido_huella(test);
            Buscar(test, codigo, valor);
            Especialista.recibir_huella(codigo, valor);
            Especialista.barrera();
        end loop;
    End Servidor;
    arr_sevidores: array (1..8) of Servidor;

    Task Especialista is
        entry pedido_huella(una_huella: out Huella);
        entry recibir_resultado(codigo: in integer, valor: in double);
        entry barrera();
    End Especialista;
    Task Body Especialista
        Huella huella;
        integer codigo_aux, codigo;
        double valor_max = -9999.99, valor_aux;
    Begin
        -- huella = obtener_huella();
        loop
            for i in 1..16 loop
                select
                    accept pedido_huella(una_huella: out Huella) do
                        una_huella = huella;
                    end pedido_huella;
                or
                    accept recibir_resultado(codigo: in integer, valor: in double) do
                        codigo_aux = codigo;
                        valor_aux = valor;
                    end recibir_resultado;
                    if (valor_aux > valor_max) then
                        valor_max = valor_aux;
                        codigo = codigo_aux;
                    end if;
                end select;
            end loop;
            -- Hacer algo con el resultado;
            -- huella = obtener_huella();
            for i in 1..8 loop
                accept barrera();
            end loop;
        end loop;
    End Especialista;
Begin
    null;
End Main;
```