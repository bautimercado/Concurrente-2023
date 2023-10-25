# 1. Suponga que N clientes llegan a la cola de un banco y que serán atendidos por sus empleados. Analice el problema y defina qué procesos, recursos y comunicaciones serán necesarios/convenientes para resolver el problema. Luego, resuelva considerando las siguientes situaciones:

- Estarían los procesos cliente y procesos empleado.
- Sería necesario un canal donde los clientes van encolando su llegada a la fila del banco (deberían enviar sus IDs).
- Los empleados van consultando dicho canal y le mandan un mensaje al cliente que sacaron del canal.

## a. Existe un único empleado, el cual atiende por orden de llegada.

```cpp
chan cola_banco(int);
chan privs[N](int);

process Cliente[id:1..N] {
    int temp;
    send cola_banco(id);
    receive privs(temp);
    //Accede a banco
}

process Empleado {
    int id;
    while (true) {
        receive cola_banco(id);
        send priv[id](1);
    }
}
```

## b. Ídem a) pero considerando que hay 2 empleados para atender, ¿qué debe modificarse en la solución anterior?

- No es necesario cambiar nada, ya que las operaciones de receive y send se hacen de manera atómica, por lo tanto no se podrían pisar.

## c. Ídem b) pero considerando que, si no hay clientes para atender, los empleados realizan tareas administrativas durante 15 minutos. ¿Se puede resolver sin usar procesos adicionales? ¿Qué consecuencias implicaría?

- El no resolverlo con procesos adicionales implicaría usar canales distintos para que el empleado vea si hay clientes que quieran pasar al banco (lo cuál no es lo que pide el enunciado).
- La idea de tener canales distintos viene porque al intentar hacer empty los dos empleados (supongamos que hay un solo mensaje en el canal) a los dos les va a dar false, van a entrar a recibir el mensaje y uno se queda demorado.

```cpp
//Resolución con proceso Coordinador
chan cola_banco(int);
chan privs[N](int);
chan pedido(int);
chan atender[2](int);

process Coordinador {
    int id_empleado, id_cliente, solicitud;
    while (true) {
        receive pedido(id_empleado);
        if (empty(cola_banco))
            send atender[id_empleado](0);
        else {
            receive cola_blanco(id_cliente);
            send atender[id_empleado](id_cliente);
        }
    }
}

process Empleado[id=1..2] {
    int id_cliente;
    while (true) {
        send pedido(id);
        receive atender[id](id_cliente);
        if (id_cliente == 0)
            delay(15min);
        else
            send privs[id_cliente](1);
    }
}

process Cliente[id=1..N] {
    int temp;
    send cola_banco(id);
    receive privs[id](temp);
}
```