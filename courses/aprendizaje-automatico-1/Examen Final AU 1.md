---

---
Pregunta 1
Tienes un modelo de regresión lineal simple para predecir precios de casas. Observas que el RMSE en los datos de entrenamiento es bajo, pero en los de prueba es mucho mayor.

**¿Qué estrategia es más adecuada para mejorar el desempeño en los datos de prueba?**  
a.

Aumentar la complejidad del modelo agregando más términos polinomiales.  

b.

Ninguna de las opciones es correcta

c.

Aplicar regularización (Ridge o Lasso).  

d.

Eliminar outliers únicamente del conjunto de prueba.  

e.

Disminuir la cantidad de datos de entrenamiento.

---

Pregunta 2
**Caso**:  
Un especialista en IA entrena un modelo de regresión para predecir el precio de viviendas. Usa validación cruzada (k=10) y obtiene resultados muy variables: los R² van desde 0.45 hasta 0.89 entre folds. El dataset tiene muchos valores atípicos.

¿Cuál sería una **mejora más adecuada** para estabilizar los resultados?
a.

Ninguna de las opciones es correcta

b.

Usar una métrica diferente como MAE  

c.

Aumentar el número de folds a 20  

d.

Escoger el fold con mayor R² para el modelo final  

e.

Remover outliers antes del entrenamiento

---

Pregunta 3
**Caso**:  
Un equipo de análisis propone usar un modelo de regresión lineal para predecir si un cliente comprará un producto (1: compra, 0: no compra). Argumentan que el valor predicho puede interpretarse como una probabilidad.

**¿Cuál es el error conceptual en esta propuesta?**
a.

La regresión lineal siempre tiene menor precisión que la logística  

b.

La regresión lineal no puede procesar variables categóricas  

c.

La regresión lineal no garantiza salidas en el rango [0, 1]  

d.

La regresión lineal no puede clasificar, solo predecir

---

Pregunta 4
Tras entrenar un modelo para diagnosticar cáncer (clase positiva: cáncer), se obtiene una curva ROC con AUC = 0.85. El área bajo la curva (AUC) en el conjunto de prueba es significativamente menor que en entrenamiento.

**Podemos afirmar que:**
a.

El modelo tiene alto bias (subajuste). Se recomienda aumentar la complejidad del modelo. Un modelo más complejo tiene menos posibilidad de sufrir sobreajuste.   

b.

La curva ROC no es útil para problemas médicos. Se recomienda usar otras métricas.   

c.

Hay overfitting; se recomienda regularizar el modelo o reducir características.  

d.

El AUC bajo en el el conjunto de prueba se debe solo al tamaño del dataset. Se recomienda hacer una partición con porcentajes diferentes.

---

Pregunta 5
**Caso**:  
Un analista de marketing ajusta una regresión lineal para modelar el gasto mensual de clientes a partir del ingreso. Al graficar los residuos vs. valores predichos, observa una forma de abanico: los residuos son pequeños para predicciones bajas y se dispersan progresivamente conforme aumenta el ingreso.

**¿Qué supuesto está siendo violado?**
a.

Homocedasticidad  

b.

Linealidad  

c.

Independencia de errores  

d.

Ninguna de las opciones es correcta

e.

Normalidad de los residuos

---

Pregunta 6
**Caso**:  
Una empresa modela el tiempo de entrega de paquetes en función de la distancia entre origen y destino. Al graficar los residuos versus el predictor, el analista observa un patrón curvilíneo en forma de U invertida.

**¿Qué sugiere este patrón respecto al modelo ajustado?**
a.

El modelo viola la independencia de errores  

b.

El modelo tiene varianza constante  

c.

Ninguna de las opciones es correcta

d.

El modelo no captura adecuadamente la relación no lineal  

e.

El modelo está sobreajustado

---

Pregunta 7
**Caso**:  
Un hospital desarrolla un modelo de regresión logística para predecir si un paciente será readmitido (1) o no (0) después del alta. Uno de los predictores es el número de días de hospitalización (`dias_hosp`). El modelo entrega el siguiente coeficiente para `dias_hosp`:

> β = 0.35 (p < 0.01)

**¿Cuál de las siguientes afirmaciones es la interpretación más adecuada de este coeficiente?**
a.

El coeficiente indica que la readmisión es más probable que no ser readmitido  

b.

El aumento de un día en hospitalización no afecta significativamente la readmisión  

c.

Por cada día adicional de hospitalización, la probabilidad de readmisión aumenta en 35%  

d.

Ninguna de las opciones es correcta

e.

Por cada día adicional de hospitalización, la razón de probabilidades (odds) de readmisión se multiplica por exp(0.35)

---

Pregunta 8
Un equipo construye un modelo de clasificación para detectar correos spam usando un dataset fuertemente desbalanceado (5% spam, 95% no spam).

**¿Cuál de las siguientes métricas es la mejor para evaluar el desempeño del modelo?**
a.

Ninguna de las opciones es correcta

b.

Accuracy.  

c.

Mean Absolute Error.  

d.

R² Score.  

e.

Recall para la clase spam.

---

Pregunta 9
En un modelo de regresión lineal para predecir el precio de viviendas, el error (RMSE) es alto y los residuos muestran un patrón no lineal (en forma de U).

**¿Qué acción es más adecuada para mejorar el modelo?**  
a.

Aumentar la regularización L2 (Ridge).  

b.

Aplicar log-transform a la variable objetivo.  

c.

Ninguna de las opciones es correcta

d.

Eliminar outliers del dataset.  

e.

Cambiar a un modelo de regresión polinómica.

---

Pregunta 10
Al aplicar KNN en un dataset numérico observas muy mal desempeño.

**¿Qué preprocesamiento podría mejorar los resultados?**
a.

Eliminar instancias atípicas manualmente.

b.

One-hot encoding.

c.

Normalización o estandarización de características.

d.

Ninguna de las opciones es correcta

---

Pregunta 11
Entrenas KNN en un dataset con 3000 variables. El accuracy en el conjunto de prueba es bajo pese a un buen desempeño en el conjunto de entrenamiento. 

**¿Qué fenómeno es el principal responsable?**
a.

Mal ajuste de la métrica de distancia.  

b.

Efecto de la "maldición de la dimensionalidad".  

c.

Sobreajuste por alta varianza.  

d.

Uso de un `k` demasiado grande.  

e.

Ninguna de las opciones es correcta

---

Pregunta 12
Al entrenar un clasificador binario, la curva ROC muestra AUC = 0.65.

**¿Qué interpretación es correcta?**  
a.

El modelo tiene alta precisión pero bajo recall.  

b.

Ninguna de las opciones es correcta

c.

La métrica AUC no aplica para clasificación binaria.  

d.

El modelo no es mejor que adivinar al azar (AUC = 0.5).  

e.

El modelo es mejor que aleatorio pero con margen de mejora.

---

Pregunta 13
Al aplicar KNN a un dataset con características en escalas diferentes (ej.: edad [0-100] vs. ingreso [0−1M]).

**¿Qué paso es crítico antes de entrenar?**  
a.

Ninguna de las opciones es correcta

b.

Aplicar one-hot encoding.  

c.

Convertir todas las variables a categóricas.  

d.

Normalizar las características (ej.: StandardScaler).  

e.

Seleccionar k óptimo con GridSearchCV.

---

Pregunta 14
Una empresa de seguridad bancaria ha entrenado un modelo de clasificación para detectar transacciones fraudulentas. El dataset contiene 1% de fraudes y 99% de transacciones legítimas. El equipo muestra con orgullo una precisión del 99%.

**¿Cuál métrica sería más adecuada para evaluar el desempeño real del modelo en este caso?**
a.

Ninguna de las opciones es correcta

b.

AUC-ROC  

c.

R²  

d.

Precisión (Accuracy)

e.

MAE (Mean Absolute Error)

---

Pregunta 15

**Caso**:  
Un modelo predictivo de enfermedad cardíaca fue evaluado en 1000 pacientes. La matriz de confusión fue:

|               | Predicción Positiva | Predicción Negativa |
| ------------- | ------------------- | ------------------- |
| Real Positivo | 80                  | 20                  |
| Real Negativo | 150                 | 750                 |

**¿Qué métrica se ve más afectada de forma negativa en este modelo?**
a.

Precisión (Precision)  

b.

Sensibilidad (Recall)  

c.

Accuracy  

d.

Especificidad

---