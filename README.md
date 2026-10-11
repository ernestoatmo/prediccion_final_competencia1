# prediccion_final_competencia1
Predicción final de la competencia 1 - Cómo correr

Esta carpeta contiene todo el código necesario para replicar de forma exacta la predicción final enviada para la competencia. La estrategia predictiva consiste en un **ensamble de 20 modelos LightGBM**, promediando las probabilidades obtenidas bajo distintas semillas, utilizando hiperparámetros optimizados previamente mediante búsqueda bayesiana.

El archivo final seleccionado como entrega es el de **11000 registros** (`prediccion_202108_2_corte_11000.csv`).

## Estructura de Archivos

* `01_optimizacion_bayesiana.ipynb`: Script de Feature Engineering (Rankings con cero fijo, Lags y Deltas) y búsqueda de hiperparámetros mediante la librería Optuna.
* `02_corrida_ensamble.ipynb`: Script final que genera el ensamble de 20 modelos, calcula el promedio de probabilidades y exporta los archivos de submission.

## Instrucciones de Replicación

Ambos notebooks están diseñados para ejecutarse de forma secuencial en **Google Colab**. Requieren acceso a Google Drive para simular la estructura de *buckets* utilizada en la materia y descargarán automáticamente el dataset crudo (`competencia_01_crudo.csv`) si no se encuentra en el entorno.

### Paso 1: Optimización de Hiperparámetros (Opcional para la replicación directa)
El archivo `01_optimizacion_bayesiana.ipynb` contiene el proceso de *tuning*. Se utilizó el algoritmo TPE de Optuna (20 trials) maximizando el AUC. 
Los mejores hiperparámetros identificados por el estudio fueron:
* `n_estimators`: 1273
* `num_leaves`: 721
* `min_sum_hessian_in_leaf`: 0.003055877921643489

### Paso 2: Ejecución del Ensamble y Predicción Final (Replicación Exacta)
Para replicar el archivo entregado, se debe ejecutar directamente el notebook `02_corrida_ensamble.ipynb`. 

**Nota sobre los parámetros:** A partir de los resultados de la optimización bayesiana del Paso 1, se realizó un leve ajuste manual de los hiperparámetros para la corrida del ensamble, buscando mayor comodidad con hiperparámetros redondeados. Los parámetros exactos configurados en este notebook son:
* `n_estimators`: 1250
* `num_leaves`: 700
* `min_sum_hessian_in_leaf`: 0.003

El notebook entrenará iterativamente 20 modelos variando la semilla aleatoria de LightGBM (generadas a partir de la semilla base `331613`). Al finalizar, promediará las probabilidades y exportará múltiples cortes.

### Paso 3: Archivo de Entrega
Una vez finalizada la ejecución del Paso 2, el directorio generará un bloque de archivos CSV. El archivo que representa la predicción final oficial de esta entrega es (nótese que se generan archivos entre 10000 y 12000 cortes, con intervalos de 125 cortes):
**`prediccion_202108_2_corte_11000.csv`**

