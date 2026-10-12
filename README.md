# prediccion_final_competencia1
Predicción final de la competencia 1 - Cómo correr

# Replicación de la Predicción Final - Ensamble LightGBM

Esta carpeta contiene todo el código necesario para replicar de forma exacta la predicción final enviada para la competencia. 

El archivo final entregado fue seleccionado de manera analítica (no mediante Public Leaderboard) utilizando un esquema de validación offline para maximizar la curva de ganancia. La estrategia predictiva consiste en un **ensamble de 20 modelos LightGBM**, promediando las probabilidades obtenidas bajo distintas semillas aleatorias.

## Estructura de Archivos

Para facilitar la replicación exacta, el pipeline se dividió en tres notebooks modulares:

* `01_optimizacion_bayesiana.ipynb`: Script de Feature Engineering (Rankings con cero fijo, Lags y Deltas) y búsqueda de hiperparámetros mediante la librería Optuna maximizando el AUC.
* `02_validacion_offline_corte.ipynb`: Script introducido para evitar el *overfitting* al Public Leaderboard. Entrena un ensamble rápido aislando el mes `202106` como conjunto de validación y simula la ganancia financiera real para encontrar la cantidad estricta de envíos que maximiza el retorno.
* `03_corrida_ensamble.ipynb`: Script final que genera el ensamble definitivo de 20 modelos sobre todos los datos disponibles, calcula el promedio de probabilidades y exporta **únicamente el archivo con el corte óptimo** hallado en el paso anterior.

## Instrucciones de Replicación

Todos los notebooks están diseñados para ejecutarse en **Google Colab**. Descargarán automáticamente el dataset crudo (`competencia_01_crudo.csv`) simulando la estructura de buckets.

### Paso 1: Optimización de Hiperparámetros (Origen)
El archivo `01_optimizacion_bayesiana.ipynb` contiene el proceso de *tuning*. Los mejores hiperparámetros identificados fueron:
* `n_estimators`: 1273
* `num_leaves`: 721
* `min_sum_hessian_in_leaf`: 0.003055877921643489

*Nota:* Para las siguientes corridas, estos parámetros se redondearon levemente a (`n_estimators`: 1250, `num_leaves`: 700, `min_sum_hessian_in_leaf`: 0.003).

### Paso 2: Cálculo del Tamaño de Envío Óptimo
Al ejecutar `02_validacion_offline_corte.ipynb`, el modelo evalúa financieramente las predicciones del mes de junio. La salida de este notebook indicará exactamente cuál es el punto de corte que maximiza la ganancia acumulada (por ejemplo, *N* envíos).

### Paso 3: Ejecución del Ensamble Final (Replicación Exacta)
Para replicar el archivo entregado, se debe ejecutar el notebook `03_corrida_ensamble.ipynb`. Este script tiene configurado como parámetro manual la variable `corte_optimo` basada en el resultado exacto del Paso 2.

El notebook entrenará iterativamente los 20 modelos (a partir de la semilla base `331613`), promediará las probabilidades resultantes, y aplicará el corte superior a los clientes con mayor probabilidad, exportando de manera determinística el único archivo de predicción final requerido para replicar el resultado del Kaggle.

### Paso 3: Archivo de Entrega
Una vez finalizada la ejecución del Paso 3, el directorio generará un bloque de archivos CSV. El archivo que representa la predicción final oficial de esta entrega es ###, con el corte óptimo obtenido en el paso 2
**`prediccion_202108_2_corte_11000.csv`**

