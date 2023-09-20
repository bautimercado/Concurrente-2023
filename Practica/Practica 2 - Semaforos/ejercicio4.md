## 4. Suponga que existe una BD que puede ser accedida por 6 usuarios como máximo al mismo tiempo. Además, los usuarios se clasifican como usuarios de prioridad alta y usuarios de prioridad baja. Por último, la BD tiene la siguiente restricción:
### - No puede haber más de 4 usuarios con prioridad alta al mismo tiempo usando la BD.
### - No puede haber más de 5 usuarios con prioridad baja al mismo tiempo usando la BD.
### Indique si la solución presentada es la más adecuada. Justifique la respuesta.
```
Var
    sem: semaphoro := 6;
    alta: semaphoro := 4;
    baja: semaphoro := 5;

Process Usuario-Alta [I:1..L]:: {
    P(sem);
    P(alta);
    //usa la BD
    V(sem);
    V(alta);
}

Process Usuario-Baja [I:1..K]:: {
    P(sem);
    P(baja);
    //usa la BD
    V(sem);
    V(baja);
}
```
- Esta solución no es adecuada. Supongamos que llegan 5 procesos usuarios con prioridad alta, los primeros 4 pasan el semáforo `sem` y el semáforo `alta`, el 5to y el 6to también pasan `sem` pero no pasan `alta` (restricción de 4 usuarios con prioridad alta). El problema de esto radica en que si llega un proceso usuario de prioridad baja, no podría acceder a la BD, ya que efectivamente hay 6 procesos en ella (pero hay dos que no la usan).
- Lo mismo podría pasar en el caso de que llegan 6 procesos de prioridad baja, por la restricción uno no podría usar la BD, y llega un proceso de prioridad alta.
- La solución sería poner primero los semáforos de `baja` y `alta` y después el `sem`. 