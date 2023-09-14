## 3. Un sistema operativo mantiene 5 instancias de un recurso almacenadas en una cola. Además, existen P procesos que necesitan usar una instancia del recurso. Para eso, deben sacar la instancia de la cola antes de usarla. Una vez usada, la instancia debe ser encolada nuevamente.
```
cola recursos;
sem mutex = 5, uso_cola = 1;

Process proceso[i = 1 to P] {
    int recurso;
    P(mutex);
    P(uso_cola);
    Pop(recursos, recurso);
    V(uso_cola);
    usar recurso;
    P(uso_cola);
    Push(recursos, recurso);
    V(uso_cola);
    V(mutex);
}
```