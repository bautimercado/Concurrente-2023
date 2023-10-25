# Semáforos

## En un restorán trabajan C cocineros y M mozos. De forma repetida, los cocineros preparan un plato y lo dejan listo en la bandeja de platos terminados, mientras que los mozos toman los platos de esta bandeja para repartirlos entre los comensales. Tanto los cocineros como los mozos trabajan de a un plato por vez. Modele el funcionamiento del restorán considerando que la bandeja de platos listos puede almacenar hasta P platos. No es necesario modelar a los comensales ni que los procesos terminen.

```cpp
Plato bandeja[P];
int primero_libre = 0, primero_ocupado = 0;
sem mutex_mozos = 1, mutex_cocineros = 1;
sem vacio = P, lleno = 0;

process Mozos[id=1 to M] {
    Plato plato;
    while (true) {
        P(lleno);
        P(mutex_mozos);
        plato = bandeja[primero_ocupado];
        primero_ocupado = (primero_ocupado + 1) mod P;
        V(mutex_mozos);
        V(vacio);
        //Llevar plato a comensal;
    }
}

process Cocinero[id=1 to C] {
    Plato plato;
    while (true) {
        //Preparando plato;
        P(vacio);
        P(mutex_cocineros);
        bandeja[primero_libre] = plato;
        primero_libre = (primero_libre + 1) mod P;
        V(mutex_cocineros);
        V(lleno);
    }
}
```

## 