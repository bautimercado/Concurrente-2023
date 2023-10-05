## 8. Se debe simular una maratón con C corredores donde en la llegada hay UNA máquina expendedoras de agua con capacidad para 20 botellas. Además, existe un repositor encargado de reponer las botellas de la máquina. Cuando los C corredores han llegado al inicio comienza la carrera. Cuando un corredor termina la carrera se dirigen a la máquina expendedora, espera su turno (respetando el orden de llegada), saca una botella y se retira. Si encuentra la máquina sin botellas, le avisa al repositor para que cargue nuevamente la máquina con 20 botellas; espera a que se haga la recarga; saca una botella y se retira. Nota: mientras se reponen las botellas se debe permitir que otros corredores se encolen.

```cpp
monitor Repositor {

    procedure reponer_botellas(cant_botellas: int out) {
        //Poniendo botellas en máquina;
        cant_botellas = 20;
    }
}

monitor Maquina {
    int cant_botellas = 20, dormidos = 0;
    cond esperando_botellas;
    bool maquina_libre = true, repositor_trabajando = false;

    procedure hacer_fila() {
        if (cant_botellas == 0) {
            if (not repositor_trabajando) {  // Para que lo llame uno solo.
                repositor_trabajando = true;
                Repositor.reponer_botellas(cant_botellas);
            }
            else {
                dormidos++;
                wait(esperando);
            }
        }
        elif (not maquina_libre)
            dormidos++;
            wait(esperando);
        else
            maquina_libre = false;
    }

    procedure salir() {
        cant_botellas--;
        if (dormidos > 0) {
            dormidos--;
            signal(esperna);
        }
        else
            maquina_libre = true;
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
    Maquina.hacer_fila();
    //tomando botella;
    Maquina.salir();
}
```