# Proyecto-final
Por: Jessenia Berenice Rufino Jiménez.
\\
Final Project QMexico Summer School 2026: Formulation of a 4x4 bipartite matching problem as QUBO.
I am also submitting my assignments because I couldn't upload them to the platform.

# Tema:"Optimización de Respuesta en Emergencias y Asignación de Ambulancias de Soporte Vital Avanzado"
Este tema ha sido elegido porque en las urgencias medicas existe el concepto de la "Hora Dorada" haciendo referencia a los primeros 60 minutos criticos transcurrido entre una lesión o crisis médica y el tratamiento definitivo. Cada minuto de retraso en la llegada de una ambulancia reducen las probabilidades de supervivencia de un paciente en estado crítico como por ejemplo los paros cardíacos, accidentes cerebrovasculares, traumas severos, etc.

Lo que me lleva al problema social, actualmente los sistemas de despacho suelen usar algoritmos greedy, asignando la ambulancia libre más cercana al primer incidente que llama. Esto puede dejar desprotegido un incidente más grave que ocurra un minuto después. Al plantearlo como un problema de emparejamiento global 4x4, se puede optimizar el sistema completo para que la suma total de los tiempos de espera de todas las víctimas sea la menor posible, salvando vidas a nivel comunitario.

## Formulación Matemática y QUBO
El problema de asignación se modela con variables binarias $x_{i,j}$ donde toma el valor de 1 si la ambulancia $i$ atiende el incidente $j$. Las restricciones imponen un emparejamiento perfecto 1-a-1. Mediante Qiskit Optimization, estas restricciones se convierten en penalizaciones cuadráticas, generando una formulación QUBO apta para computación cuántica de 16 qubits.

## Interpretación de Resultados
* **Resultado Clásico (CPU):** Encontró la solución óptima global reduciendo el tiempo de espera total a **22 minutos** distribuidos eficientemente (A0->I0, A1->I1, A2->I2, A3->I3).
* **Resultado Cuántico (QAOA):** Utilizando el simulador local con el algoritmo QAOA y el optimizador COBYLA, se logró converger exitosamente al mismo estado fundamental de mínima energía, demostrando la viabilidad de usar algoritmos cuánticos para la toma de decisiones críticas en logística de salud pública.
