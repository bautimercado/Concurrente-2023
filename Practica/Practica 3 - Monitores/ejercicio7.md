## 7. En un entrenamiento de fútbol hay 20 jugadores que forman 4 equipos (cada jugador conoce el equipo al cual pertenece llamando a la función DarEquipo()). Cuando un equipo está listo (han llegado los 5 jugadores que lo componen), debe enfrentarse a otro equipo que también esté listo (los dos primeros equipos en juntarse juegan en la cancha 1, y los otros dos equipos juegan en la cancha 2). Una vez que el equipo conoce la cancha en la que juega, sus jugadores se dirigen a ella. Cuando los 10 jugadores del partido llegaron a la cancha comienza el partido, juegan durante 50 minutos, y al terminar todos los jugadores del partido se retiran (no es necesario que se esperen para salir).

```cpp
monitor AdministradorCancha {
    int sig_cancha = 0, equipos = 0;

    procedure obtener_cancha(id_cancha: int out) {
        id_cancha = sig_cancha;
        equipos++;
        if (equipos == 2)
            sig_cancha++;
    }
}

monitor Cancha[id=0 to 1] {
    int cant_jugadores = 0;
    cond esperando_jugadores;

    procedure llega_jugador() {
        cant_jugadores++;
        if (cant_jugadores == 10)
            signal_all(esperando_jugadores);
        else
            wait(esperando_jugadores);
    }
}

monitor Equipo[id=0 to 3] {
    cond esperando_jugadores;
    int cant_jugadores = 0, cancha_aux;

    procedure anotarse(id_cancha: int out) {
        cant_jugadores++;
        if (cant_jugadores == 5)
            AdministradorCancha.obtener_cancha(cancha_aux);  // Se puede llamar a otro monitor desde un monitor?
            signal_all(espernado_partido);
        else
            wait(esperando_partido);
        id_cancha = cancha_aux;
    }
} 

process Jugador[id=0 to 19] {
    int id_cancha;
    int id_equipo = DarEquipo();
    Equipo[id_equipo].anotarse(id_cancha);
    Cancha[id_cancha].llega_jugador(id_equipo);
    delay(50minutos);
}
```