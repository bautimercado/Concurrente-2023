## 11. En un vacunatorio hay un empleado de salud para vacunar a 50 personas. El empleado de salud atiende a las personas de acuerdo con el orden de llegada y de a 5 personas a la vez. Es decir, que cuando está libre debe esperar a que haya al menos 5 personas esperando, luego vacuna a las 5 primeras personas, y al terminar las deja ir para esperar por otras 5. Cuando ha atendido a las 50 personas el empleado de salud se retira. Nota: todos los procesos deben terminar su ejecución; asegurarse de no realizar Busy Waiting; suponga que el empleado tienen una función VacunarPersona() que simula que el empleado está vacunando a UNA persona.

```cpp
Cola personas_esperando;
sem mutex_persona = 1, sem_personas = 5, esperando_personas = 0, personas_vacunandose = 0;
int cant_esperando = 0;

process Empleado {
    int id_persona;
    for i = 1 to 10 {
        P(esperando_personas);
        //Atiende a las 5 personas:
        for j = 1 to 5 {
            id_persona = personas_esperando.pop();
            //VacunarPersona(id_persona);
        }
        //Avisa a las 5 personas que pueden retirarse:
        for j = 1 to 5 {
            V(personas_vacunandose);
        }
    }
}

process Persona[id=1 to 50] {
    P(sem_personas);
    
    P(mutex_persona);
    personas_esperando.push(id);
    cant_esperando++; 
    if (cant_esperando == 5) {
        V(esperando_personas);
        cant_esperando = 0;
    }
    V(mutex_persona);
    P(personas_vacunandose);
}
```
