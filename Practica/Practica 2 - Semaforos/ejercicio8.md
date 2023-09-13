## 8. Una fábrica de piezas metálicas debe producir T piezas por día. Para eso, cuenta con E empleados que se ocupan de producir las piezas de a una por vez (se asume T>E). La fábrica empieza a producir una vez que todos los empleados llegaron. Mientras haya piezas por fabricar, los empleados tomarán una y la realizarán. Cada empleado puede tardar distinto tiempo en fabricar una pieza. Al finalizar el día, se le da un premio al empleado que más piezas fabricó.
```
sem mutex = 1, barrera = 0;
int cant_empleados = 0;
piezas_por_empleado = set of (int, int);
cola piezas;

process Empleado[id= 1 to E] {
    int cant_piezas = 0, pieza;
    P(mutex);
    cant_empleados = cant_empleados + 1;
    if (cant_empleados == E) { for i = 1 to E V(barrera); }
    V(mutex);

    P(barrera);
    P(mutex);
        while (piezas != Ø) {
            Pop(piezas, pieza);
            V(mutex);
            trabajar con pieza;
            cant_piezas = cant_piezas + 1;
            P(mutex);
        }
    V(mutex);

    P(mutex);
    insertar_ordenado(piezas_por_empleado, (cant_piezas, id));
    V(mutex);

    //Implementar lógica del empleado ganador? Otro proceso?

}   
```