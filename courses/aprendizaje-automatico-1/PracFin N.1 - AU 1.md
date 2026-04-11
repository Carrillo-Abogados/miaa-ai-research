#### Información

Para las siguientes preguntas debes emplear los datos que se encuentran en el siguiente directorio: "C:\Noveno Semestre - 2026-1\Maestría en IA Aplicada\Aprendizaje Automatico 1\EXAMFINAL-riesgo_cardiovascular.data"

```
import numpy as np
import pandas as pd

data=pd.read_csv('riesgo_cardiovascular.data')
```

**Contexto:**  
En un estudio sobre riesgo cardiovascular se recolectaron datos de 500 personas con cinco variables:

1. Riesgo cardiovascular (0-100)
    
2. Horas de ejercicio semanal
    
3. Horas de sueño diarias
    
4. Tazas de café consumidas
    
5. Minutos en redes sociales
    

La variable objetivo es **Riesgo cardiovascular**, se espera que las demás variables tengan algún tipo de influencia en la variable objetivo. Debemos descubrir si en efecto existe tal relación.

---

Pregunta 16
Debes calcular la matriz de correlación empleando el coeficiente de correlación de **Pearson** para analizar la existencia de relaciones lineales entre variables.

Según la matriz de correlación ¿Cuál es la conclusión correcta?   
a.

El café diario (r = 0.094) es un factor protector contra el riesgo cardiovascular.  

b.

Ninguna de las opciones es correcta

c.

La correlación lineal no detecta relaciones no lineales; se debe usar un análisis de regresión cuadrática o modelos no lineales  

d.

Las horas sueño (r = 0.0089) tienen mayor impacto en el riesgo que el ejercicio (r = -0.012).  

e.

No hay relación alguna entre ejercicio y riesgo cardiovascular, pues la correlación es -0.012 (próxima a cero).

---

Pregunta 17
**Contexto**: Al calcular la matriz de correlación lineal (Pearson), ninguna variable muestra una correlación significativa (|r| < 0.1) con el riesgo cardiovascular. Sin embargo, se sospecha que podría haber relaciones no lineales ocultas.

**Para identificar visualmente patrones no lineales y relaciones entre variables, ¿cuál es el siguiente paso más adecuado?**
a.

**Entrenar un modelo de regresión lineal** directamente, pues el modelo puede capturar patrones complejos en los datos sin necesidad de EDA.  

b.

Ninguna de las opciones es correcta

c.

**Aplicar una transformación logarítmica** a todas las variables y recalcular la correlación de Pearson.  

d.

**Generar un pairplot** (matriz de gráficos de dispersión y histogramas) para visualizar distribuciones y relaciones bivariadas.  

e.

**Descartar todas las variables**, ya que la correlación lineal no fue significativa.

---

Pregunta 18
Usando los datos proporcionados, realiza un `pairplot` para analizar visualmente las relaciones entre las variables:

**'Horas_Sueno', 'Tazas_Cafe', 'Horas_Ejercicio','Minutos_Redes'**

Y la variable objetivo: '**Riesgo_Cardiovascular**'

A partir del grafico realizado, podemos concluir que: 
a.

Existe una relación lineal entre _`Minutos_redes` y `Riesgo_Cardiovascular`_  

b.

Existe una relación lineal oculta entre _`Horas_Sueno` y `Riesgo_Cardiovascular`_  

c.

Existe una relación lineal entre _`Horas_Ejercicio` y `Riesgo_Cardiovascular`_  

d.

Existe una relación parabólica oculta entre _`Tazas_Cafe` y `Riesgo_Cardiovascular`_  

e.

Existe una relación parabólica oculta entre _`Horas_Ejercicio` y `Riesgo_Cardiovascular`_

---

Pregunta 19
Realizar el siguiente experimento:  

- Seleccione únicamente la variable **Horas_Ejercicio** como variable predictora (X) y use la variable **Riesgo_Cardiovascular** como variable objetivo (y)
    
- Separar los datos en entrenamiento y prueba usando: test_size=0.3, random_state=42
    
- **Ajuste dos modelos** utilizando `sklearn`:
    
    - **Modelo 1:** Regresión lineal simple (grado 1).
        
    - **Modelo 2:**
      Regresión polinomial (grado 2):
      ```
      from sklearn.preprocessing import PolynomialFeatures
      poly = PolynomialFeatures(degree=2)
      X_poly = poly.fit_transform(X_train)
      ```

- **Compare ambos modelos** mediante:
    
    - El **Error Cuadrático Medio (MSE)** en datos de prueba.
        

  
A partir de los resultados obtenidos podemos concluir que:  
a.

El modelo polinomial (grado 2) obtiene un MSE menor que el lineal, ya que captura la relación parabólica observada en los datos. La regresión lineal simple es insuficiente para modelar patrones no lineales.  

b.

El modelo polinomial (grado 2) tiene un MSE menor porque es más complejo, lo que demuestra que siempre debemos usar polinomios de alto grado para minimizar el error, sin importar la relación real en los datos.  

c.

La regresión lineal simple tiene un MSE más alto porque los datos no tienen ninguna relación con la variable independiente, lo que confirma que el ejercicio no influye en el riesgo cardiovascular.  

d.

El modelo polinomial es mejor porque su MSE es menor en los datos de prueba, lo que garantiza que funcionará igual de bien en cualquier otro conjunto de datos médico.

---

Pregunta 20
Al comparar un **modelo de regresión lineal simple** (grado 1) y un **modelo de regresión polinomial** (grado 2) para predecir el riesgo cardiovascular en función de las horas de ejercicio, se obtuvieron los siguientes errores cuadráticos medios (MSE) en datos de prueba:

- **MSE Lineal:** 42.64
    
- **MSE Polinomial:** 6.52
    

¿Cuál de las siguientes conclusiones es la más adecuada basada en estos resultados?  
a.

Ambos modelos son igualmente válidos, pues el MSE depende de la escala de los datos y no refleja diferencias reales en su capacidad predictiva.  

b.

El modelo lineal es superior porque, aunque tiene un MSE más alto, es más interpretable y generalizable.  

c.

El MSE polinomial es sospechosamente bajo, lo que sugiere sobreajuste (overfitting); por tanto, el modelo lineal es más confiable."  

d.

El modelo polinomial (grado 2) es claramente mejor, ya que su MSE es 6.5 veces menor que el del modelo lineal, lo que indica que captura la relación no lineal en los datos.

---

