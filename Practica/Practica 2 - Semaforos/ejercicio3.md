## 3. Un sistema operativo mantiene 5 instancias de un recurso almacenadas en una cola. Además, existen P procesos que necesitan usar una instancia del recurso. Para eso, deben sacar la instancia de la cola antes de usarla. Una vez usada, la instancia debe ser encolada nuevamente.
```
cola recursos;
sem mutex = 5;

Process proceso[i = 1 to P] {
    int recurso;
    P(mutex);
    Pop(recursos, recurso);
    usar recurso;
    Push(recursos, recurso);
    V(mutex);
}
```