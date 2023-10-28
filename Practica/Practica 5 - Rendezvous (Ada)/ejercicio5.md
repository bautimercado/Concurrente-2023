# 5. En un sistema para acreditar carreras universitarias, hay UN Servidor que atiende pedidos de U Usuarios de a uno a la vez y de acuerdo con el orden en que se hacen los pedidos. Cada usuario trabaja en el documento a presentar, y luego lo envía al servidor; espera la respuesta de este que le indica si está todo bien o hay algún error. Mientras haya algún error, vuelve a trabajar con el documento y a enviarlo al servidor. Cuando el servidor le responde que está todo bien, el usuario se retira. Cuando un usuario envía un pedido espera a lo sumo 2 minutos a que sea recibido por el servidor, pasado ese tiempo espera un minuto y vuelve a intentarlo (usando el mismo documento).

```ada
Procedure Main is
    Task Type Usuario;
    Task Body Usuario
        Documento documento;
        Boolean correcto = false;
    Begin
        -- Trabajando en el documento;
        while (not correcto) loop
            select
                Servidor.enviar_pedido(documento, correcto);
                if (not correcto) then
                    -- Trabajando en el documento;
                end if;
            or delay(120.0)
                delay(60.0);
            end select;
        end loop;
    End Usuario;
    arr_usuarios: array (1..U) of Usuario;

    Task Servidor is
        entry enviar_documento(documento: IN Documento, correcto: OUT boolean);
    End Servidor;
    Task Body Servidor
        Boolean resultado;
    Begin
        loop
            accept enviar_documento(documento: IN Documento, correcto: OUT boolean) do
                resultado = analisis_de_documento(documento);    -- Si está OK da true, si tiene algún error da false
                correcto = resultado;
            end enviar_documento;
        end loop;
    End Servidor;
Begin
    null;
End Main;
```