# Evaluación 2 - Visualización de Datos de Transporte

## Problema
El presente proyecto aborda el análisis exploratorio, procesamiento y visualización de las operaciones de transporte aéreo en Chile.
* **Pregunta Principal:** ¿Cómo se distribuye y evoluciona el volumen de operaciones aéreas en Chile según el tipo de tráfico, rutas y operadores?
* **P1:** ¿Cuáles son las aerolíneas y rutas que concentran el mayor volumen de tráfico?
* **P2:** ¿Cómo evoluciona mensualmente el flujo de vuelos y qué dispersión presenta?
* **P3:** ¿Cómo interactúan capacidad operativa, volumen y temporada simultáneamente?
* **Hipótesis (H1):** El tráfico presenta alta concentración operativa dominada por pocas rutas y operadores, con estacionalidad marcada en verano e invierno.

## Fuente de datos
* **Dataset:** Operaciones de Aeronaves
* **Institución:** Junta de Aeronáutica Civil (JAC) - Portal de Datos Abiertos del Estado de Chile (`https://datos.gob.cl`).
* **Formatos utilizados:** CSV y Apache Parquet.

## Adquisición
Los datos originales fueron descargados directamente desde el catálogo oficial y almacenados sin alteraciones manuales en el directorio `data/raw/`, respetando la regla de preservación. Por restricciones de tamaño en GitHub (>100MB), los crudos se mantienen locales mediante `.gitignore`.

## Configuración del entorno
El proyecto utiliza Python con un entorno virtual aislado (`.venv`). Las dependencias exactas (`pandas`, `numpy`, `matplotlib`, `seaborn`, `jupyter`, `pyarrow`) se encuentran registradas en `requirements.txt`.

## Estructura del proyecto
```text
evaluacion_02_transportes/
├── data/
│   ├── raw/
│   └── processed/
├── notebooks/
│   └── evaluacion02.ipynb
├── src/
├── output/
│   └── figures/
├── docs/
├── README.md
├── requirements.txt
└── .gitignore
```

## Perfilamiento
Se realizó una inspección sobre más de 11 millones de registros utilizando métodos de Pandas (`head()`, `info()`, `describe()`, `nunique()`). Se comparó el rendimiento entre CSV y Parquet, determinando que Parquet optimiza los tiempos de carga, RAM y esquema de datos.

## Calidad de datos
Se auditaron valores nulos y registros duplicados. Se detectaron 1.122 filas idénticas, zonas horarias incrustadas en fechas y variables cuantitativas (Peso al despegue) inferidas como texto, documentando cada hallazgo en una matriz de calidad.

## Preparación
Mediante código reproducible, se eliminaron los duplicados exactos, se removió la zona horaria de `dt_operacion` para evitar conflictos gráficos, se normalizaron cadenas de texto y se forzó la conversión numérica del peso, exportando el resultado limpio a `data/processed/`.

## Variables derivadas
Se construyeron 4 variables analíticas:
1. `anio`, `mes_num`, `trimestre` y `periodo_ym`: Desglose jerárquico temporal.
2. `temporada`: Clasificación cualitativa (ALTA vs REGULAR).
3. `ruta`: Concatenación espacial `Origen <-> Destino`.
4. `categoria_peso`: Discretización binaria de aeronaves.

## Dimensionalidad
Las variables fueron clasificadas en Dimensiones (Temporales, Geográficas, Corporativas, Operativas) y Medidas cuantitativas (`total_vuelos`, `pmd`). Se identificó la jerarquía temporal y espacial de las operaciones aeronáuticas.

## Transformaciones analíticas
Se aplicaron operaciones con `groupby()`, `agg()` y `pivot_table()`, generando agregaciones simples para ránkings y agregaciones multidimensionales para evaluar la diversidad de rutas y pesos operativos mensuales.

## Diseño de visualizaciones
Previo a la programación, se estructuró una Matriz de Diseño vinculando cada pregunta analítica con sus dimensiones, medidas y la justificación perceptual de la representación gráfica elegida.

## Matplotlib
Utilizando la interfaz orientada a objetos (`fig, ax = plt.subplots()`), se implementaron:
1. **Gráfico de comparación (`01...`):** Barras horizontales del Top 10 de aerolíneas.
2. **Gráfico de evolución (`02...`):** Serie de tiempo de líneas por tipo de operación.

## Seaborn
Se implementaron visualizaciones estadísticas (pre-agrupadas para rendimiento):
1. **Gráfico de distribución (`03...`):** Panel combinado de histograma y gráfico de violín en escala Log10.
2. **Gráfico de segmentación (`04...`):** Boxplot horizontal de las 8 rutas principales.

## Visualización multidimensional
En `05_multidimensional_relplot.png` se integraron 5 variables: `total_vuelos` (X), `peso_promedio` (Y), `rutas_distintas` (Tamaño), `temporada` (Color) y `tipo_operacion` (Faceta). Se justificó la carga cognitiva, limitando la cantidad de entidades visualizadas.

## Benchmark visual
En `06_benchmark_visual.png` se contrastó un Gráfico de Sectores frente a un Gráfico de Barras Horizontales Ordenadas, demostrando que las barras superan al gráfico circular en precisión perceptual e integridad al no recalcular artificialmente el total de la muestra.

## Validación
Las visualizaciones principales fueron auditadas bajo 12 criterios técnicos y de diseño (audiencia, escalas, color, legibilidad, sesgos y limitaciones).

## Hallazgos
1. **Concentración:** Un número minúsculo de operadores concentra la inmensa mayoría de las frecuencias.
2. **Estacionalidad:** Patrón cíclico indexado al calendario vacacional escolar y turístico nacional.
3. **Dispersión de red:** Chile tiene un modelo radial hipercentralizado, con pocas rutas troncales de miles de vuelos y miles de rutas con vuelos marginales.

## Limitaciones
* Los datos miden frecuencias operativas (vuelos), no pasajes vendidos, y el análisis agregado impide identificar cuellos de botella diarios u horarios precisos.

## Consideraciones éticas
Se documentó el impacto de eliminar duplicados crudos y forzar nulos numéricos. Se advirtió que omitir a los operadores pequeños del gráfico multidimensional mejora la legibilidad gráfica, pero invisibiliza la existencia de la aviación menor o de emergencia ante el público general.

## Conclusiones
El flujo reproducible implementado demostró que procesar más de 11 millones de registros requiere eficiencia (Parquet), validación estricta de tipos de datos, y conocimiento profundo de la teoría de percepción visual (Matplotlib/Seaborn) para no distorsionar estadísticamente la realidad operativa del país.

## Instrucciones de ejecución
1. Clonar repositorio.
2. Activar entorno virtual (`python -m venv .venv` y `.venv\Scripts\activate`).
3. Ejecutar `pip install -r requirements.txt`.
4. Correr `notebooks/evaluacion02.ipynb` secuencialmente.

## Autor
* **Nombre:** Vicente Liapiz
* **Asignatura:** Visualización de Datos - Evaluación N.º 2