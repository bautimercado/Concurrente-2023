## 10. A una cerealera van T camiones a descargarse trigo y M camiones a descargar maíz. Sólo hay lugar para que 7 camiones a la vez descarguen, pero no pueden ser más de 5 del mismo tipo de cereal. Nota: no usar un proceso extra que actué como coordinador, resolverlo entre los camiones.
```
sem e = 1, mutex_trigo = 0, mutex_maiz = 0;
int cant_camiones = 0, cant_trigo = 0, cant_maiz = 0, d_trigo = 0, d_maiz = 0;
process CamionTrigo[i=0 to T-1] {
    P(e);
    if ((cant_camiones == 7) or (cant_trigo == 5)) { d_trigo++; V(e); P(mutex_trigo); }
    cant_camiones++;
    cant_trigo++;
    V(e);
    
    //descargar trigo;

    P(e);
    cant_camiones--;
    cant_trigo--;
    if (d_trigo > 0) { d_trigo--; V(mutex_trigo); }
    else V(e);

}

process CamionMaiz[i=0 to M-1] {
    P(e);
    if ((cant_camiones == 7) or (cant_maiz == 5)) { d_maiz++; V(e); P(mutex_maiz); }
    cant_camiones++;
    cant_trigo++;
    V(e);

    //descarga maiz;

    P(e);
    cant_camiones--;
    cant_maiz--;
    if (d_maiz > 0) { d_maiz--; V(mutex_maiz) }
    else V(e);
}
```
- Más sencillo sin Passing the Baton:
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