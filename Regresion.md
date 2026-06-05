tipo::
tags::
fecha:: 2026-02-05
links:: [[Machine Learning]]
links_externos::

## Regresión lineal

La regresión es una técnica de Machine Learning que predice un valor numérico continuo.
Imagina que quieres predecir:
El precio de una casa basándote en su tamaño, ubicación, años de antigüedad...
La temperatura de mañana según datos históricos meteorológicos
El progreso de la diabetes de un paciente según ciertos indicadores médicos (¡este es tu caso!)
La idea clave: A diferencia de la clasificación (que separa en categorías como "sí/no", "gato/perro"), la regresión te da un número. Por ejemplo: "Este paciente tendrá un nivel de progresión de diabetes de 150.5".
En tu ejemplo, estás haciendo regresión lineal: buscas encontrar una línea (o hiperplano) que mejor se ajuste a tus datos para hacer predicciones numéricas.

En una regresion lineal se aplica esta función 
```sh
β=(XTX)−1XTy
# XT = X(matriz) y T(transpuesta)
```
No hay ajustes de "pesos" como en un modelo que utiliza redes neuronales, sino que se aplica a los datos ya una función determinada. Esto es una única operación matemática. No hay iteración, no hay épocas. El resultado es óptimo en un solo paso.

Analogía:
* Red neuronal = Ir probando caminos hasta llegar al destino (varias épocas)
* Regresión lineal = Usar GPS con coordenadas exactas (llegas directo)

1. CARGAR DATOS
   X, y = load_diabetes(return_X_y=True)
   └─ X: 442×10 (características)
   └─ y: 442 (objetivo a predecir)

2. DIVIDIR (para evaluar honestamente)
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.33)
   └─ Entrenamiento: 295 pacientes (aprendizaje)
   └─ Test: 147 pacientes (evaluación final)

3. CREAR MODELO (contenedor de la ecuación)
   model = LinearRegression()
   └─ Inicializa el modelo vacío

4. ENTRENAR (encontrar mejores β)
   model.fit(X_train, y_train)
   └─ Calcula: y = β₀ + β₁·edad + β₂·sexo + ... + β₁₀·s6

5. PREDECIR (usar el modelo aprendido)
   predicciones = model.predict(X_test)
   └─ Aplica la ecuación a pacientes nuevos

## Los Tres Supuestos Básicos de la Regresión Lineal
Para que tu modelo sea válido, debes verificar estos supuestos. Si fallan, tus conclusiones pueden ser incorrectas.

### Supuesto 1: Linealidad
**La relación entre X e Y debe ser lineal**

```python
# DIAGNÓSTICO: Gráfico de residuos vs predichos
residuos = y_test - y_pred
plt.scatter(y_pred, residuos, alpha=0.6)
plt.axhline(y=0, color='red', linestyle='--')
plt.xlabel('Valores Predichos')
plt.ylabel('Residuos')
# Si hay patrón (curva) → Problema de no linealidad
```
### Supuesto 2: Homocedasticidad

**La varianza de los errores debe ser constante**

```python
# DIAGNÓSTICO: Mismo gráfico anterior
# Si los puntos forman un "embudo" (más dispersión a la derecha) → Heterocedasticidad
# Solución: Transformar Y (log, raíz cuadrada) o usar regresión ponderada
```

### Supuesto 3: Independencia de errores

**Los errores no deben estar correlacionados entre sí**

```python
# DIAGNÓSTICO: Gráfico de residuos vs orden de observación
plt.plot(residuos)
plt.xlabel('Orden de observación')
plt.ylabel('Residuos')
# Si hay patrones cíclicos → Autocorrelación (común en series temporales)
```

### Ejemplo

Comenzando con el modelo sobre el dataset de pacientes diabéticos, verificamos si cumple las condiciones para el posterior tratamiento.

![[LLM_2.png]]

#### ¿Qué es `model.score()`? ¿Es "accuracy"?

**No es accuracy.** En regresión lineal, `model.score()` devuelve el **coeficiente de determinación R²** (R-squared).
#### Fórmula de R²

R2=1−∑(yreal​−yˉ​)2∑(yreal​−ypredicho​)2​

| Método                 | Código                     | Cuándo usar                             | Pros/Cons                                                |
| ---------------------- | -------------------------- | --------------------------------------- | -------------------------------------------------------- |
| **Semilla fija**       | `random_state=42`          | Desarrollo, debugging, reproducibilidad | ✅ Siempre mismo resultado<br>❌ Puede ser "suerte"        |
| **Validación cruzada** | `cross_val_score`          | Evaluación final, publicación           | ✅ Usa todos los datos<br>❌ Más lento (5x entrenamientos) |
| **Múltiples semillas** | Bucle con `random_state=i` | Estudiar robustez                       | ✅ Ve la variabilidad real<br>❌ Más complejo              |

## Distribución normal y desviación estandar

```python
X_train, X_test, y_train, y_test = model_selection.train_test_split(X, y, test_size=0.4)
model = linear_model.LinearRegression()
model.fit(X_train, y_train)
cv_scores = model_selection.cross_val_score(model, X, y, cv=5)
print(f"R² (CV): {cv_scores.mean():.3f} ± {cv_scores.std():.3f}")

# cv_scores.std() => mide qué tan dispersos están los valores - promedio de las diferencias

print(f"R² = {cv_scores.mean():.3f} ± {cv_scores.std():.3f}")
print(f"Intervalo de confianza (95%): [{cv_scores.mean() - 2*cv_scores.std():.3f}, {cv_scores.mean() + 2*cv_scores.std():.3f}]")

```

![[LLM_1.png]]

**¿Por qué ±2×std = 95% de confianza?**
Esto viene de la **regla empírica** (o regla del 68-95-99.7) de la distribución normal:
Si tus scores siguen una campana de Gauss:

| Confianza | Factor z  | Uso típico                          |
| --------- | --------- | ----------------------------------- |
| 68%       | 1.0       | Rápido, "un sigma"                  |
| **90%**   | **1.645** | Estándar en ciencias sociales       |
| **95%**   | **2.0**   | **Estándar en ciencia** (más común) |
| 99%       | 2.576     | Alta certeza                        |
| 99.7%     | 3.0       | "Casi seguro"                       |

El factor z es mi cv_scores.std() => *cv_scores.std() x 2,576 == 99% de confianza*

### Overfitting

Subajuste y sobreajuste Son problemas comunes que degradan la calidad del modelo, ya que éste no encaja lo suficientemente bien o encaja demasiado bien. Esto hace que el modelo haga predicciones demasiado alineadas o demasiado vagamente alineadas con sus datos de entrenamiento. Un modelo de sobreajuste predice demasiado bien los datos de entrenamiento porque ha aprendido demasiado bien los detalles y el ruido de los datos. 

![[LLM_0.png]]

## Predicción

Este es el momento en el que puedes utilizar datos completamente nuevos para probar la precisión de tu modelo. En una configuración de ML "aplicada", donde se crean activos web para usar el modelo en producción, este proceso puede implicar recopilar información del usuario (presionar un botón, por ejemplo) para establecer una variable y enviarla al modelo para inferencia o evaluación.



---
#### Referencias

