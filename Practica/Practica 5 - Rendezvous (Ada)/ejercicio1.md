# 1. Se requiere modelar un puente de un único sentido que soporta hasta 5 unidades de peso. El peso de los vehículos depende del tipo: cada auto pesa 1 unidad, cada camioneta pesa 2 unidades y cada camión 3 unidades. Suponga que hay una cantidad innumerable de vehículos (A autos, B camionetas y C camiones). Analice el problema y defina qué tareas, recursos y sincronizaciones serán necesarios/convenientes para resolver el problema.

- Debería tener una tarea que es el puente, una tarea para cada tipo de vehículo. Dentro de puente debería tener dos entrys (ingreso y salida) para cada vehículo, para el entry de ingreso se comprueba la condición de las unidades de peso y se incrementa el peso actual, para el caso de la salida se resta el peso actual.

## a. Realice la solución suponiendo que todos los vehículos tienen la misma prioridad.

```ada
Procedure Main is
    Task type auto;
    Task Body auto is
    Begin
        puente.ingresar_auto();
        // Pasando por el puente;
        puente.salir_auto();
    End auto;
    arr_autos: array (1..N) of auto;

    Task type camioneta;
    Task Body camioneta is
    Begin
        puente.ingresar_camioneta();
        // Pasando por el puente;
        puente.salir_camioneta();
    End camioneta;
    arr_camionetas: array (1..N) of camioneta;
    
    Task type camion;
    Task Body camion is
    Begin
        puente.ingresar_camion();
        // Pasando por el puente;
        puente.salir_camion();
    End camion;
    arr_camion: array(1..N) of camion;

    Task puente is
        ENTRY ingresar_auto();
        ENTRY ingresar_camioneta();
        ENTRY ingresar_camion();
        ENTRY salir_auto();
        ENTRY salir_camioneta();
        ENTRY salir_camion();
    End puente;
    Task Body puente is
        int peso_actual = 0;
    Begin
        loop
            select
                when ((peso_actual + 1) <= 5) => Accept ingresar_auto();
                    peso_actual++;
                or when ((peso_actual + 2) <= 5) => Accept ingresar_camioneta();
                    peso_actual += 2;
                or when ((peso_actual + 3) <= 5) => Accept ingresar_camioneta();
                or Accept salir_auto()
                    peso_actual--;
                or Accept salir_camioneta()
                    peso_actual -= 2;
                or Accept salir_camion()
                    peso_actual -= 3;
            End select;
        end loop;
    End puente;
Begin
    null;
End Main;
```

## b. Modifique la solución para que tengan mayor prioridad los camiones que el resto de los vehículos.

```ada
Procedure Main is
    Task type auto;
    Task Body auto is
    Begin
        puente.ingresar_auto();
        // Pasando por el puente;
        puente.salir_auto();
    End auto;
    arrAutos: array (1..N) of auto;

    Task type camioneta;
    Task Body camioneta is
    Begin
        puente.ingresar_camioneta();
        // Pasando por el puente;
        puente.salir_camioneta();
    End camioneta;
    arrCamionetas: array (1..N) of camioneta;
    
    Task type camion;
    Task Body camion is
    Begin
        puente.ingresar_camion();
        // Pasando por el puente;
        puente.salir_camion();
    End camion;
    arrCamion: array(1..N) of camion;

    Task puente is
        ENTRY ingresar_auto();
        ENTRY ingresar_camioneta();
        ENTRY ingresar_camion();
        ENTRY salir_auto();
        ENTRY salir_camioneta();
        ENTRY salir_camion();
    End puente;
    Task Body puente is
        int peso_actual = 0;
    Begin
        loop
            select
                when ((peso_actual + 1) <= 5 and ingresar_camion'count == 0) => Accept ingresar_auto()
                    peso_actual++;
                or when ((peso_actual + 2) <= 5 and ingresar_camion'count == 0) => Accept ingresar_camioneta()
                    peso_actual += 2;
                or when ((peso_actual + 3) <= 5) => Accept ingresar_camion()
                    peso_actual += 3;
                or Accept salir_auto()
                    peso_actual--;
                or Accept salir_camioneta()
                    peso_actual -= 2;
                or Accept salir_camion()
                    peso_actual -= 3;
            End select;
        end loop;
    End puente;
Begin
    null;
End Main;
```