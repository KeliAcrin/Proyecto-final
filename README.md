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

## Dataset
**Nombre del dataset:** Dataset Sintético de Tiempos de Respuesta para Despacho Crítico de Unidades Médicas Urbanas (SVA-4x4).
**Fuente oficial o confiable:** Basado en matrices de distancias e índices de congestionamiento vial promedio extraídos de plataformas de geolocalización urbana y protocolos estándar de Despacho Asistido por Computadora (CAD) para servicios de rescate.
**Institución responsable:** Simulación analítica con fines educativos para QMexico Summer School 2026.
**URL de la fuente:** https://github.com/QMexico-Summer-School-2026/ambulancias-matching-dataset
**URL raw del CSV usado en data/:** https://github.com/KeliAcrin/Proyecto-final/blob/data/dataset_real_4x4.csv
**Fecha de consulta:** 30 de junio de 2026.
**Dominio del problema:** Optimización de Operaciones de Salud Pública, Logística de Transporte de Emergencia y Cómputo Cuántico Aplicado.

## Modelado
**Conjunto A:** Unidades de Ambulancia de Soporte Vital Avanzado (SVA) disponibles en sus respectivas bases operativas metropolitanas: $\{A_1, A_2, A_3, A_4\}$.
**Criterio para elegir exactamente 4 elementos de A:** Se seleccionaron las 4 unidades médicas críticas activas, estacionarias y con personal completo dentro del centro geográfico de la ciudad al momento de dispararse una alerta múltiple.
**Conjunto B:** Reportes incidentes concurrentes catalogados estrictamente con "Código Rojo" (prioridad absoluta de riesgo de vida): $\{I_1, I_2, I_3, I_4\}$.
**Criterio para elegir exactamente 4 elementos de B:** Los 4 reportes médicos más críticos que ingresaron a la línea de emergencias centralizada dentro de una misma ventana de tiempo de 2 minutos.
**Definición de $x_{ij} = 1$:** La ambulancia $i$ es despachada y asignada de forma unívoca para atender el incidente crítico $j$.
**Interpretación de $x_{ij} = 0$:** La ambulancia $i$ no es asignada al incidente crítico $j$.

## Matriz de score
**Columnas usadas:** `I1_Infarto`, `I2_Accidente`, `I3_ACV`, `I4_Caida`.
**Fórmula exacta de $S_{ij}$:** $$S_{ij} = 20 - T_{ij}$$
Donde $T_{ij}$ representa el tiempo estimado de traslado (ETA) en minutos de la ambulancia $i$ hacia el siniestro $j$.
**Normalización aplicada:** Desplazamiento lineal inverso con respecto a un umbral máximo operativo de 20 minutos. Esto transforma el problema de minimización de tiempos en una maximización de puntajes positivos (scores), donde una respuesta más rápida produce un score significativamente más alto, adaptándose perfectamente a la estructura del código base.
**Matriz S 4x4:**
$$
\begin{pmatrix}
14 & 6 & 11 & 2 \\
8 & 15 & 9 & 12 \\
5 & 9 & 13 & 7 \\
11 & 12 & 6 & 16
\end{pmatrix}
$$
## Restricciones
**Restricción por filas:** Cada unidad de ambulancia debe ser enviada a exactamente una emergencia médica para no dividir recursos críticos:
$$\sum_{j=1}^{4} x_{ij} = 1 \quad \forall i \in \{1, 2, 3, 4\}$$
**Restricción por columnas:** Cada paciente o siniestro crítico debe recibir exactamente una sola ambulancia asignada para evitar la sobrecobertura o desatención de otros sectores:
$$\sum_{i=1}^{4} x_{ij} = 1 \quad \forall j \in \{1, 2, 3, 4\}$$
**Otras restricciones, si existen:** No existen restricciones adicionales; se asume un modelo de emparejamiento perfecto y balanceado.
**Justificación de por qué el problema es matching bipartito:** Contamos con dos conjuntos disjuntos e independientes de la misma cardinalidad ($n=4$ elementos en cada lado). Las conexiones permitidas se realizan estrictamente de un conjunto hacia el otro de manera biyectiva (relación uno a uno), buscando emparejar el origen óptimo con su destino ideal bajo una métrica de costo global.
**Justificación de por qué es razonable modelarlo como QUBO:** Las restricciones de igualdad dadas por el matching bipartito pueden penalizarse cuadráticamente mediante parámetros de Lagrange ($\lambda_A$ y $\lambda_B$). Al expandir estos binomios, las restricciones pasan a formar parte de la ecuación de energía como interacciones lineales y cuadráticas entre variables binarias. Esto elimina las restricciones explícitas y genera un Hamiltoniano cuadrático sin restricciones, idóneo para mapearse directamente en los operadores cuánticos de fase del algoritmo QAOA.

## Resultados
**Solución clásica exacta:** Calculada mediante el solucionador clásico (`NumPyMinimumEigensolver`). Encontró el estado de mínima energía correspondiente a un Score Máximo de **58 puntos** (Energía QUBO de $-58.0$). El bitstring óptimo activa los elementos de la diagonal principal: $x_{11}=1, x_{22}=1, x_{33}=1, x_{44}=1$, lo que equivale a un tiempo de respuesta total mínimo para la ciudad de **22 minutos** ($6 + 5 + 7 + 4$).
**Resultado QAOA local:** El circuito cuántico simulado localmente con el optimizador clásico `COBYLA` convergió exitosamente hacia el mismo estado fundamental, identificando el bitstring binario óptimo global con una energía idéntica a la calculada por la CPU.
**Comparación clásico vs QAOA local:** Ambos métodos coinciden al 100% en la asignación óptima. QAOA local demuestra precisión matemática absoluta para resolver este volumen de variables ($16$ qubits virtuales) bajo condiciones ideales de simulación sin ruido.
**Si se usó hardware real o pipeline híbrido, comparación adicional:** No ejecutado en procesadores cuánticos físicos remotos. Se conservó el pipeline de ejecución local exclusiva usando `Aer Sampler` como alcance definitivo para validar la lógica del modelo de asignación.

## Ética y limitaciones
**Riesgos éticos:** Los algoritmos automáticos de despacho corren el riesgo de perpetuar sesgos de infraestructura urbana si las bases de ambulancias históricamente se encuentran alejadas de comunidades marginadas. Adicionalmente, el enfoque puramente utilitarista del modelo (minimizar la suma total de minutos del sistema) podría causar que un paciente individual con un tiempo de traslado extremadamente largo sea retrasado aún más para favorecer la rapidez conjunta de los otros tres incidentes.
**Medidas de mitigación:** Este desarrollo es estrictamente una prueba de concepto con datos analíticos que no vulneran datos personales identificables. En una implementación real, el algoritmo debe funcionar exclusivamente como una herramienta de recomendación consultiva, manteniendo siempre un despachador humano (personal paramédico/médico) con la facultad de anular la decisión automatizada en función de criterios clínicos particulares.
**Limitaciones del modelo:** El modelo es completamente estático y determinista; no modela factores estocásticos en tiempo real como accidentes sobre la marcha, condiciones climáticas súbitas o variaciones imprevistas en el tráfico. Además, está restringido a matrices cuadradas perfectas ($4 \times 4$), perdiendo flexibilidad si hay más incidentes que ambulancias disponibles o viceversa.

## Ejecución
**Instrucciones para abrir el archivo .ipynb en Google Colab:**
1. Ve al sitio oficial de Google Colab (colab.research.google.com).
2. Selecciona la pestaña **GitHub** e ingresa la URL de este repositorio.
3. Haz clic sobre el archivo `proyecto_final.ipynb` para abrirlo directamente en el entorno de nube.

**Instrucciones para ejecutar todas las celdas sin errores:**
1. Asegúrate de instalar las librerías necesarias ejecutando la celda inicial de instalación:
   `!pip install qiskit qiskit-optimization qiskit-aer docplex`
2. Ejecuta todo el entorno secuencialmente desde el menú superior seleccionando **Entorno de ejecución** -> **Ejecutar todas**. Todas las celdas completarán su ejecución e imprimirán las gráficas y tablas comparativas de manera exitosa.
