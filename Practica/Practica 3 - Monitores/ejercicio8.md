## 8. Se debe simular una maratón con C corredores donde en la llegada hay UNA máquina expendedoras de agua con capacidad para 20 botellas. Además, existe un repositor encargado de reponer las botellas de la máquina. Cuando los C corredores han llegado al inicio comienza la carrera. Cuando un corredor termina la carrera se dirigen a la máquina expendedora, espera su turno (respetando el orden de llegada), saca una botella y se retira. Si encuentra la máquina sin botellas, le avisa al repositor para que cargue nuevamente la máquina con 20 botellas; espera a que se haga la recarga; saca una botella y se retira. Nota: mientras se reponen las botellas se debe permitir que otros corredores se encolen.

```cpp

monitor Maquina {
    int cant_botellas = 20, dormidos = 0;
    cond esperando_botellas, caller, repositor;
    bool maquina_libre = true, repositor_llamado = false;

    procedure hacer_fila_y_tomar() {
        if (cant_botellas == 0) {
            if (not repositor_llamado) {
                repositor_llamado = true;
                signal(repositor);
                dormidos++;  // Para que no le roben el lugar
                await(caller);
                dormidos--;
            }
            else {
                dormidos++;
                await(esperando_botellas);
            }
        }
        elif (dormidos > 0) { dormidos++; await(esperando_botellas); }  // En el caso de que entre un proceso sin que haya entrado antes el proceso que llamó al repositor.
        cant_botellas--;
        if (dormidos > 0) { dormidos--; signal(esperando_botellas); }
    }

    procedure llenar_dispenser() {
        if (not repositor_llamado) -> await(repositor);
        cant_botellas = 20;
        signal(caller);
        repositor_llamado = false;
    }
}

monitor Carrera {
    cond esperando_corredores;
    int cant_corredores = 0;

    procedure llegada() {
        cant_corredores++;
        if (cant_corredores == C)
            signal_all(esperando_corredores);
        else
            wait(esperando_corredores)
    }
}

process Corredor[id=1 to C] {
    Carrera.llegada();
    Maquina.hacer_fila_y_tomar();
}

process Repositor {
    while (true) {
        Maquina.llenar_dispenser();
    }
}
```
