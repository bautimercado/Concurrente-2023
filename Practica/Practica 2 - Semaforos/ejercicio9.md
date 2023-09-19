## 9. Resolver el funcionamiento en una fábrica de ventanas con 7 empleados (4 carpinteros, 1 vidriero y 2 armadores) que trabajan de la siguiente manera:
- Los carpinteros continuamente hacen marcos (cada marco es armando por un único carpintero) y los deja en un depósito con capacidad de almacenar 30 marcos.
- El vidriero continuamente hace vidrios y los deja en otro depósito con capacidad para 50 vidrios.
- Los armadores continuamente toman un marco y un vidrio (en ese orden) de los depósitos correspondientes y arman la ventana (cada ventana es armada por un único armador).
```
cola marcos, vidrios;
sem espacio_marcos = 30, mutex_cola_marcos = 1, espacio_vidrios = 50, mutex_cola_vidrios = 1, marcos_disponibles = 0, vidrios_disponibles = 0;

process Carpintero[id = 1 to 4] {
    while (true) {
        //Construyendo marco;
        P(espacio_marcos);
        P(mutex_cola_marcos);
        marcos.push(marco);
        V(marcos_disponibles);
        V(mutex_cola_marcos);
    }
}

process Vidriero {
    while (true) {
        //Construyendo vidrio;
        P(espacio_vidrios);
        P(mutex_cola_vidrios);
        vidrios.push(vidrio);
        V(vidrios_disponibles);
        V(mutex_cola_vidrios);
    }
}

process Armadores[id = 1 to 2] {
    marco, vidrio;
    while (true) {
        P(marcos_disponibles);
        P(mutex_cola_marcos);
        marco = marcos.pop();
        V(mutex_cola_marcos);
        V(espacio_marcos);

        P(vidrios_disponibles);
        P(mutex_cola_vidrios);
        vidrio = vidrios.pop();
        V(mutex_cola_vidrios);
        V(espacio_vidrios);

        //Construir ventana(marco, ventana);
    }
}   
``` 
