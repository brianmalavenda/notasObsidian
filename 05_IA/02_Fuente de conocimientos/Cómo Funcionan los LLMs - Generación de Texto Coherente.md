tipo:: #ia 
tags::
fecha:: 2025-10-06
links::
links_externos::

Vamos a tratar de entender cómo funcionan los algoritmos de inteligencia artificial de LLM basados en la generación de texto coherente, donde si coloco como prompt de entrada "El perro", el algoritmo selecciona "ladra" como la palabra más coherente para continuar la oración.


data: todo el conocimiento de la humanidad - corpus - 
multimodales incorporan imagenes y video al corpus

data - abecedario - "diccionario" - todas las palabras posibles y la semántica entre ellas. Esto es para entrenar al modelo, todavía no son tokens

tokenización es despues del entrenamiento, es la jerarquización de esos conjuntos de palabras o "tokens"
cada token tiene su concepto, una dimension
perro por ejemplo, animal, mamifero

perro puede ser un solo token, tambien por ejemplo satisfaccion pueden ser 4 tokens
sat - is - f - accion

embedins: cercanía conceptual (estadisticas) entre un token y otro

vectores multidimensionales : por ejemplo papa (relaciona tokens, que son coordenadas, entre por ejemplo progenitor, hombre - hombre esta dentro de genero)

cada core hace el calculo de una de las dimensioens del nucleo. La eficiencia de un LLM, como se calcula. Cuantos cores necesito con procesamiento paralelo donde el equilibrio entre energia y procesamiento, latencia etc sean optimos?.

**paper: attention is all you need**

multilayer perfecptron
red neuronal de 
neurona: matriz  de pesos, de donde sale .. de machine learning
...
tengo tres coordenadas que son tokens, necesito averiguar cual es la funcion que los une. Eso se logra con un perceptron (un tipo de red neuronal) multicapa
y = mx + b
m=pendiente

neurona ejemplo
inputs => pesos aleatorios
X= (1,1) => W0 = 2                                                                          0.5
Y= (2,2) => W1 = 0  ---> FUNCION DE PERDIDA (va entre 0 y 1)   0.3  => acuracy = 1,7
Z= (3,1) => W2 = 1                                                                          0.9

1) se multiplican los pesos por los valores de entrada
2) se le aplica a los resultados la funcion SIGMOIDE = 1 / (1 + e^(-x))
3) se suman los resultaods que son todos entre 0 y 1. El resultado es el valor de la pendiente
4) calculo del accurancy = 1,7 (ponele que sea 10%) - no sirve, tenemos que aplicar otra capa de entrenamiento (vuelta) backpropagatiton
5) esos pesos los guardo en una matriz de pesos. Esos pesos a su vez son lso "parametros" del modelo.


## Dataset y Divisiones

### Corpus

El corpus es la colección completa de textos en un idioma (español, en este caso) que incluye libros, artículos de internet, documentos, etc. No es exactamente un diccionario, sino todos los textos de entrenamiento.

### Training Set

Es el 70% del corpus que se utiliza para entrenar el modelo. El modelo ajusta sus parámetros (pesos) basándose en este conjunto de datos.

### Test Set

Es el 30% restante del corpus que permite evaluar el rendimiento del modelo con datos que nunca ha visto durante el entrenamiento. Esto verifica la capacidad de generalización del modelo.

## Arquitectura del LLM

Texto → Tokenización → Embeddings → Capas de Transformers → Softmax → Probabilidades

### Tokens

Son las unidades básicas de procesamiento que pueden ser palabras completas, subpalabras o caracteres individuales. Permiten manejar eficientemente vocabularios extensos.

**Tokenización de "preparación":**

```python
"preparación" → ["pre", "par", "ación"]  # 3 tokens típicos
```

**Tokenización de "el perro":**

```python
"el perro" → ["el", " per", "ro"]  # 3 tokens (nota el espacio antes de "per")
```

**Otros ejemplos:**

- "reparación" → ["re", "par", "ación"]
- "acción" → ["acción"] # token completo
- "canción" → ["can", "ción"]

Los tokenizadores (BPE, WordPiece, SentencePiece) utilizan algoritmos estadísticos para encontrar las divisiones más eficientes basándose en la frecuencia de los patrones en el corpus de entrenamiento.

### Embeddings

Los embeddings son representaciones vectoriales de los tokens en un espacio multidimensional (típicamente 512 a 4096 dimensiones). Capturan relaciones semánticas y sintácticas entre palabras.

**Concepto:** Palabras similares tienen embeddings cercanos en el espacio vectorial. Por ejemplo:

- "papá" y "mamá" están cerca en la dimensión "progenitor"
- "papá" y "rey" pueden estar cerca en la dimensión "masculino/autoridad"
- "papá" y "patata" estarán lejos a pesar de la homografía

Cada dimensión representa características abstractas aprendidas durante el entrenamiento.

## Arquitectura de Red Neuronal

### Neurona Artificial

Una neurona en un LLM es una unidad computacional que realiza la siguiente operación:
```python
# Operación básica de una neurona
output = función_activación(∑(input_i × peso_i) + bias)
```

**Componentes:**

- **Inputs**: Valores de entrada (embedding del token, salidas de otras neuronas)
- **Pesos**: Coeficientes que determinan la importancia de cada conexión
- **Bias**: Término constante que ajusta el umbral de activación
- **Función de activación**: Transformación no lineal (ReLU, GELU, Sigmoid)

**Analogía biológica:**

- **Axones** → Conexiones de entrada
- **Dendritas** → Conexiones de salida
- **Sinapsis** → Pesos de las conexiones
- **Potencial de acción** → Función de activación

### Capas de Transformers (Arquitectura Central)

Los LLMs modernos utilizan la arquitectura Transformer que incluye:

1. **Self-Attention**: Mecanismo que permite a cada token "prestar atención" a otros tokens relevantes en la secuencia
2. **Feed-Forward**: Capas fully connected que procesan la información    
3. **Normalización**: Estabiliza el entrenamiento
4. **Residual Connections**: Mejora el flujo de gradientes.


## Proceso de Entrenamiento

### Forward Pass

Proceso de calcular las predicciones del modelo:

```python
# Ejemplo simplificado del forward pass
def forward_pass(prompt_tokens):
    embeddings = embedding_layer(prompt_tokens)
    hidden_states = transformer_layers(embeddings)
    logits = output_layer(hidden_states)
    probabilidades = softmax(logits)
    return probabilidades
```

### Procesamiento de lenguaje natural

#### Función de Activación

**Confusión aclarada:** Hay diferentes funciones para diferentes propósitos:
- **En neuronas individuales**: ReLU, GELU, Sigmoid
- **En capa de salida**: Softmax (para probabilidades)    

**Softmax** convierte logits en probabilidades:
```python
def softmax(logits):
    exp_logits = np.exp(logits - np.max(logits))  # estabilidad numérica
    return exp_logits / np.sum(exp_logits)

# Ejemplo: 
probabilidades = softmax([2.0, 1.0, 0.1])  # Resultado: [0.65, 0.24, 0.11]
```

La Función de activación determina si y cuánto "se activa" la neurona. Por ejemplo es una sismoide, ReLU, etc que es una función que va de 0 a 1. Estas funciones de pérdida tienen la funcion de otorgar una probabilidad a cada uno de estos tokens de combinarse con la entrada (prompt). Cada peso de los tokens multiplica (esto nose si siempre es multiplicación ) el valor de cada vector. Estos vectores que son 3 tokens de mi prompt.
Luego de hacer la multiplicación de los pesos se aplica esta función de pérdida o (Loss Function) => S(x) = 1 / (1 + e^(-x))
Luego el resultado de esta función para cada token que es la probabilidad de la relación entre cada una con la posible palabra siguiente que como idea del ejemplo debería ser "ladra". Entonces para las "50 mil palabras" de mi diccionario le aplico esta función de activación y tendría un accurancy para la palabra "camina", "habla", "vuela", "ladra", donde habla debería tener un accurancy muy chico porque es más lejana, vuela igual y camina y ladra deberían ser las de accurancy más alto.
Estos pesos para cada palabra son almacenados en un dataset en formato tensor. Entonces para la cantidad de parametros de mi dataset se genera una matriz de pesos en formato tensor con la misma cantidad de parametros.

### Cálculo de Pérdida (Loss Function)

Mide qué tan incorrectas son las predicciones del modelo:
```python
# Para "El perro [ladra]"
probabilidades = [0.01, 0.85, 0.02, 0.12, ...]  # "ladra" en posición 1
target = [0, 1, 0, 0, ...]  # one-hot encoding

# Función de pérdida común: Cross-Entropy
pérdida = -∑(target_i × log(probabilidades_i))

# Si "ladra" tiene probabilidad 0.85:
pérdida = -log(0.85) ≈ 0.16  # pérdida baja - buena predicción

# Si "ladra" tiene probabilidad 0.10:
pérdida = -log(0.10) ≈ 2.30  # pérdida alta - mala predicción
```

### Backward Pass (Backpropagation)

Calcula cómo cambiar cada peso para reducir la pérdida:

1. **Calcula gradientes**: ∂pérdida/∂peso para cada parámetro
2. **Propaga errores** hacia atrás través de la red    
3. **Identifica** qué pesos contribuyeron más al error

```python
# Conceptualmente:
gradiente = ∂loss/∂peso
```

### Optimización

Ajusta los pesos basándose en los gradientes:

**Algoritmo común: Adam Optimizer**
```python
# Pseudocódigo simplificado
for cada peso en el modelo:
    momento = β1 × momento_anterior + (1-β1) × gradiente
    velocidad = β2 × velocidad_anterior + (1-β2) × gradiente²
    peso = peso - learning_rate × momento / (√velocidad + ε)
```

**Learning Rate**: Controla el tamaño de los ajustes en cada paso.

### Repetición (Épocas de Entrenamiento)

El proceso completo se repite por múltiples épocas:

```python
for época in range(num_épocas):
    for lote in datos_entrenamiento:
        # 1. Forward pass
        predicciones = modelo(lote.inputs)
        
        # 2. Calcular pérdida
        pérdida = loss_function(predicciones, lote.targets)
        
        # 3. Backward pass
        gradientes = calcular_gradientes(pérdida)
        
        # 4. Optimización
        optimizer.ajustar_pesos(gradientes)
    
    # Evaluar en validation set
    accuracy_validación = evaluar_modelo(test_set)
```

## Hiperparámetros y Dimensionamiento

La arquitectura del modelo se determina mediante:

- **Número de parámetros**: Típicamente millones a billones    
- **Capas de transformer**: 12-100+ capas
- **Dimensión de embeddings**: 768-12288 dimensiones
- **Heads de atención**: 12-128 heads
- **Context length**: 2048-128k tokens

Se optimizan mediante experimentación para balancear:

- **Precisión** vs. **Velocidad de inferencia**    
- **Capacidad** vs. **Overfitting**
- **Costo computacional** vs. **Rendimiento**

La cantidad de neuronas de un modelo esta determinada por algun calculo de cercanía donde despues de hacer pruebas e ir verificando cuan certero es al generar los resultados y el tiempo al generar esos resultados puedo determinar que con X neuronas tengo un porcentaje de cercanía al resultado correcto de 0.98 y un tiempo de Y ms. Entonces si hago X-50 por ejemplo y mi modelo tiene un porcentaje de precision de 0.97 y Y+1 ms entonces puedo decir que con X-50 neuronas mi modelo funciona bien.

## Generación de Texto

Una vez entrenado, el modelo genera texto:

```python
def generar_texto(prompt, max_length=50):
    tokens = tokenizer(prompt)
    for _ in range(max_length):
        probabilidades = modelo(tokens)
        siguiente_token = muestrear(probabilidades)  # No siempre el más probable
        tokens.append(siguiente_token)
        if siguiente_token == token_eos:
            break
    return tokenizer.decode(tokens)
```

Esta arquitectura y proceso de entrenamiento permiten a los LLMs generar texto coherente y contextualmente relevante, capturando patrones complejos del lenguaje humano.


---
#### Referencias

