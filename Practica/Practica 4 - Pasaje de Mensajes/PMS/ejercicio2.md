# 2. En un laboratorio de genética veterinaria hay 3 empleados. El primero de ellos continuamente prepara las muestras de ADN; cada vez que termina, se la envía al segundo empleado y vuelve a su trabajo. El segundo empleado toma cada muestra de ADN preparada, arma el set de análisis que se deben realizar con ella y espera el resultado para archivarlo. Por último, el tercer empleado se encarga de realizar el análisis y devolverle el resultado al segundo empleado.

```cpp
process PrimerEmpleado {
    Muestra muestra;
    while (true) {
        //Preparando muestra
        BufferPrimeroSegundo!envio_muestra(muestra);
    }
}

process BufferPrimeroSegundo {
    cola muestras;
    while (true) {
        do ❏ PrimerEmpleado?envio_muestra(muestra) -> muestras.push(muestra);
            ❏ not muestras.empty(); SegundoEmpleado?segundo_listo() -> SegundoEmpleado!chan_muestra(muestras.pop());
    }
}

process SegundoEmpleado {
    Muestra muestra;
    Analisis set_analisis;
    Resultado resultado;
    while (true) {
        BufferPrimeroSegundo!segundo_listo();
        BufferPrimeroSegundo?chan_muestra(muestra);
        // Armando set de análisis
        TercerEmpleado!muestras(set_analisis);
        TercerEmpleado?chan_resultado(resultado);
        }
}

process TercerEmpleado {
    Resultado resultado;
    cola muestras;
    while (true) {
        SegundoEmpleado?muestras(muestras);
        //Analizando muestras y generando resultado;
        SegundoEmpleado!chan_resultado(resultado);
    }
}
```