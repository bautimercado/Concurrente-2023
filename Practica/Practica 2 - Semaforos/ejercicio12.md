## 12. Simular la atención en una Terminal de Micros que posee 3 puestos para hisopar a 150 pasajeros. En cada puesto hay una Enfermera que atiende a los pasajeros de acuerdo con el orden de llegada al mismo. Cuando llega un pasajero se dirige al puesto que tenga menos gente esperando. Espera a que la enfermera correspondiente lo llame para hisoparlo, y luego se retira. Nota: sólo deben usar procesos Pasajero y Enfermera. Además, suponer que existe una función Hisopar() que simula la atención del pasajero por parte de la enfermera correspondiente.

- En este caso debería proteger también la cantidad de pasajeros por puesto en el mismo mutex_pasajero

```cpp
int cant_pasajeros_puesto[3] = ([3] 0), hisopados = 150
cola pasajeros_esperando_en_puesto[3];  //Un array con 3 colas.
sem mutex_colas[3] = ([3] 0), mutex_pasajero = 1, enfermera_esperando[3] = ([3] 0), atendidos[150] = ([150] 0), mutex_enfermeras = 1;

process Enfermera[id=1 to 3] {
    int id_pasajero;
    while hisopados > 0 {
        P(enfermera_esperando[id]);
        if (not pasajeros_esperando_en_puesto[id].isEmpty()) {
            P(mutex_colas[id]);
            id_pasajero = pasajeros_esperando_en_puesto[id].pop();
            V(mutex_colas[id]);
            //Hisopar(id_pasajero);

            P(mutex_colas[id]);
            cant_pasajeros_puesto[id]--;
            V(mutex_colas[id]);
            V(atendidos[id_pasajero]);

            P(mutex_enfermeras);    
            hisopados--;
            if (hisopados == 0)
                for i=1 to 3 -> V(enfermera_esperando[i]);   // Para que las enfermeras dormidas se despierten si terminaron
            V(mutex_enfermeras);
        }
    }
}

process Pasajero[id=1 to 150] {
    int puesto;
    P(mutex_pasajero);
    if (cant_pasajeros_puesto[1] < cant_pasajeros_puesto[2] and cant_pasajeros_puesto[1] < cant_pasajeros_puesto[3])
        puesto = 1;
    elif (cant_pasajeros_puesto[2] < cant_pasajeros_puesto[1] and cant_pasajeros_puesto[2] < cant_pasajeros_puesto[3])
        puesto = 2;
    else
        puesto = 3;
    V(mutex_pasajero);
    
    P(mutex_colas[puesto]);
    cant_pasajeros_puesto[puesto]++;
    pasajeros_esperando_en_puesto[puesto].push(id);
    V(mutex_colas[puesto]);
    V(enfermera_esperando);
    P(atendidos[id]);
}
```
