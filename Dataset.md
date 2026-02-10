tipo::
tags::
fecha:: 2026-02-04
links:: [[Machine Learning]]
links_externos:: [repo_estudio](https://github.com/microsoft/ML-For-Beginners/blob/main/1-Introduction/README.md)


## Recopilación y preparación de datos

Hay varios pasos en el proceso de preparación de datos. Es posible que necesites recopilar datos y normalizarlos si provienen de diversas fuentes. Puede mejorar la calidad y cantidad de los datos a través de varios métodos, como convertir cadenas en números (como hacemos en Agrupamiento). También puedes generar nuevos datos, basados en el original (como lo hacemos en Clasificación). Puede limpiar y editar los datos (como lo haremos antes del Aplicación web lección). Por último, es posible que también necesites aleatorizarlo y mezclarlo, dependiendo de tus técnicas de entrenamiento.

## Seleccionar variable de función

 Hay muchos tipos de métodos de regresión y cuál elijas dependerá de la respuesta que estés buscando. Si desea predecir la altura probable de una persona de una edad determinada, utilizaría la regresión lineal, ya que está buscando una valor numérico. Si estás interesado en descubrir si un tipo de cocina debe considerarse vegana o no, estás buscando una asignación de categoría entonces usarías regresión logística. 

## Visualización de datos

Un aspecto importante del conjunto de herramientas del científico de datos es la capacidad de visualizar datos utilizando varias bibliotecas excelentes, como Seaborn o MatPlotLib. Representar sus datos visualmente puede permitirle descubrir correlaciones ocultas que puede aprovechar. Sus visualizaciones también podrían ayudarlo a descubrir sesgos o datos desequilibrados (como descubrimos en Clasificación).

## División del conjunto de datos 

Antes del entrenamiento, debes dividir tu conjunto de datos en dos o más partes de tamaño desigual que aún representen bien los datos.

1. **Formación**. Esta parte del conjunto de datos se ajusta a su modelo para entrenarlo. Este conjunto constituye la mayor parte del conjunto de datos original.
2. **Testing**. Un conjunto de datos de prueba es un grupo independiente de datos, a menudo recopilados a partir de los datos originales, que se utilizan para confirmar el rendimiento del modelo construido.
3. **Validando**. Un conjunto de validación es un grupo independiente más pequeño de ejemplos que se utiliza para ajustar los hiperparámetros o la arquitectura del modelo para mejorarlo. Dependiendo del tamaño de sus datos y de la pregunta que esté haciendo, es posible que no necesite crear este tercer conjunto (como señalamos en Previsión de series temporales).

En el entrenamiento del modelo peude ser que la etapa de formación sea con datos que no esten limpio 100%. Esto es algo a medir. Con el total de datos, el resultado. Con datos más "puros" o "limpios", cuanto se vuelve más ágil el modelo? Hay que achicar el set de datos en ese caso. Queda siempre una pregunta de partida para cada entranamiento de los modelos de menos datos pero más "limpios" o más datos pero contaminados?

```python
X, y = datasets.load_diabetes(return_X_y=True)
```

|Variable|Contiene|Dimensión|¿Para qué sirve?|
|:--|:--|:--|:--|
|**X**|Las características de entrada (features)|(442, 10)|Todo lo que el modelo usará para "aprender" y predecir|
|**y**|El valor objetivo (target)|(442,)|Lo que queremos que el modelo aprenda a predecir|

**La notación matemática estándar:**
- **X** = Matriz de características (en mayúscula porque es una matriz 2D)    
- **y** = Vector objetivo (en minúscula porque es un vector 1D)

```python
X_train, X_test, y_train, y_test = model_selection.train_test_split(
    X, y, 
    test_size=0.33  # 33% para test, 67% para entrenamiento
)
```

|Característica|Descripción|
|:--|:--|
|**Aleatoriedad**|Mezcla los datos aleatoriamente antes de dividir (evita sesgos de orden)|
|**Proporción**|33% para probar, 67% para entrenar (configurable con `test_size`)|
|**Consistencia**|Mantiene la correspondencia entre X e y (mismo índice en ambos)|

## Normalización



---
#### Referencias

