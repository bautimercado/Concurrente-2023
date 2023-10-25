# 2. Se quiere modelar el funcionamiento de un banco, al cual llegan clientes que deben realizar un pago y retirar un comprobante. Existe un único empleado en el banco, el cual atiende de acuerdo con el orden de llegada. Los clientes llegan y si esperan más de 10 minutos se retiran sin realizar el pago.

```ada
Procedure Main is
    Task Empleado is
        ENTRY pagar(monto: IN float, comprobante: OUT Comprobante);
    End Empleado;
    Task Body Empleado
        Comprobante comprobante;
    Begin
        loop
            accept pagar(monto: IN float, comprobante: OUT Comprobante)
                comprobante = generar_comprobante(monto);
        end loop;
    End Empleado;

    Task Type Cliente is
    End Cliente;
    Task Body Cliente
        Comprobante comprobante;
        float monto;
    Begin
        monto = calcular_monto_a_pagar();
        select
            Empleado.pagar(monto, comprobante);
        or delay 600
            null;
        end select;
    End Cliente;
    arr_clientes: array (1..N) of Cliente;

Begin
    null;
End Main;
```