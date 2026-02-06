
tipo::
tags::
fecha:: 2026-02-05
links::[[Machine Learning]]
links_externos::

## ¿Qué es el ajuste de hiperparámetros?

[link](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-tune-hyperparameters?view=azureml-api-2&WT.mc_id=academic-77952-leestott)

**Hiperparámetros** son configuraciones ajustables que controlan el entrenamiento del modelo. Para las redes neuronales, por ejemplo, se elige el número de capas ocultas y el número de nodos por capa. El rendimiento del modelo depende en gran medida de estos valores.
**Ajuste de hiperparámetros** (o **optimización de hiperparámetros**) es el proceso de encontrar la configuración de hiperparámetros que produzca el mejor rendimiento. Este proceso suele ser computacionalmente costoso y manual. Azure Machine Learning le permite automatizar el ajuste de hiperparámetros y ejecutar experimentos en paralelo para optimizarlos de manera eficiente.

1. Hiperparámetros discretos
2. Hiperparámetros continuos

## Muestreo del espacio de hiperparámetros

Especifique el método de muestreo para el espacio de hiperparámetros. Azure Machine Learning admite:

- Muestreo aleatorio
- Muestreo de cuadrícula
- Muestreo bayesiano


---
#### Referencias

