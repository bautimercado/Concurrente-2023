## 10. A una cerealera van T camiones a descargarse trigo y M camiones a descargar maíz. Sólo hay lugar para que 7 camiones a la vez descarguen, pero no pueden ser más de 5 del mismo tipo de cereal. Nota: no usar un proceso extra que actué como coordinador, resolverlo entre los camiones.
```
sem mutex_camiones = 7, mutex_trigo = 5, mutex_maiz = 5;

process CamionTrigo[i=0 to T-1] {
    P(mutex_trigo);
    P(mutex_camiones);
    //Descargar cargamento;
    V(mutex_camiones);
    V(mutex_trigo);
}

process CamionMaiz[i=0 to M-1] {
    P(mutex_maiz);
    P(mutex_camiones);
    //Descargar cargamento;
    V(mutex_camiones);
    V(mutex_maiz);
}

```
