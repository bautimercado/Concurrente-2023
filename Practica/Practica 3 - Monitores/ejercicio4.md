## 4. Existen N vehículos que deben pasar por un puente de acuerdo con el orden de llegada. Considere que el puente no soporta más de 50000kg y que cada vehículo cuenta con su propio peso (ningún vehículo supera el peso soportado por el puente).

```cpp
Monitor Puente {
    double peso_max = 0, peso_por_vehículo[N] = ([N] 0);
    cond dormidos[N];
    cola ids_procesos;
    int cant_dormidos = 0;

    procedure ingresar_al_puente(peso: double in; id: int in) {
        if (peso_max+peso > 50000) { // Debería dormirse si hay alguien esperando.
            cant_dormidos++;
            ids_procesos.insertar_al_inicio(id);
            peso_por_vehiculo[id] = peso;
            wait(dormidos[id]);
        }
        peso_max = peso_max + peso; // Deberia ser un else
    }
    
    procedure salir_del_puente(peso: double in) {
        int id;
        peso_max = peso_max - peso;
        if (cant_dormidos > 0) {
            id = ids_procesos.pop();  // Deberia ser un .top()
            if (peso_max+peso_por_vehiculo[id] <= 50000)
                signal(dormidos[id]);
                // Hacer pop e incrementar el peso del puente.
            else {
                ids_procesos.insertar_al_inicio(id);
            } // No es necesario con el top()
        }
    }
}

Process Vehiculo[id=1 to N] {
    double peso = ...;
    Puente.ingresar_al_puente(peso);
    //transitando puente;
    Puente.salir_del_puente(peso);
}
```