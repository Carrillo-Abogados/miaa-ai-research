# Sustentación — Examen Final Aprendizaje Automático I
## MIAA 2026-1 | Universidad Icesi | Prof. Milton Sarria

---

## TABLA RÁPIDA — LAS 30 RESPUESTAS

### Teóricas (1-15)

| # | R | # | R | # | R |
|---|---|---|---|---|---|
| 1 | **c** | 6 | **d** | 11 | **b** |
| 2 | **e** | 7 | **e** | 12 | **e** |
| 3 | **c** | 8 | **e** | 13 | **d** |
| 4 | **c** | 9 | **e** | 14 | **b** |
| 5 | **a** | 10 | **c** | 15 | **a** |

### Prácticas (16-30)

| # | R | # | R | # | R |
|---|---|---|---|---|---|
| 16 | **c** | 21 | **c** (Clasif.1) | 27 | **Falso** |
| 17 | **d** | 22 | **a** (80%) | 28 | **b** (K=3, 92%) |
| 18 | **e** | 23 | **b** (89%) | 29 | **a** (8 FP) |
| 19 | **a** | 24 | **a** (63%) | 30 | **a** (-0.325, 1.433, -0.120) |
| 20 | **d** | 25 | **Verdadero** | |  |
|    |       | 26 | **Verdadero** | |  |

---

## PARTE TEÓRICA — EXPLICACIÓN PREGUNTA POR PREGUNTA

---

### Pregunta 1 → **c) Regularización**

**Qué pasa:** El error en entrenamiento es bajo pero en prueba es alto.
**Qué significa:** El modelo memorizó los datos de entrenamiento en vez de aprender el patrón general. Eso se llama **overfitting**.
**Por qué regularización:** Ridge y Lasso le ponen un "freno" a los coeficientes del modelo. No los dejan crecer demasiado. Eso obliga al modelo a ser más simple y generalizar mejor.
**Por qué NO las otras:**
- (a) Más complejidad → empeora el overfitting
- (d) Nunca se tocan los datos de prueba, esos son sagrados
- (e) Menos datos de entrenamiento → el modelo aprende peor

---

### Pregunta 2 → **e) Remover outliers**

**Qué pasa:** R² varía brutalmente entre folds (0.45 a 0.89).
**Qué significa:** Algunos folds tienen outliers que distorsionan el modelo, otros no. Por eso los resultados son tan inconsistentes.
**Por qué remover outliers:** Si eliminas los valores atípicos antes de entrenar, todos los folds van a tener datos más homogéneos y los resultados se estabilizan.
**Por qué NO las otras:**
- (b) Cambiar la métrica a MAE solo cambia cómo MIDES, no arregla el modelo
- (c) Más folds no resuelve que los outliers caigan en folds diferentes
- (d) Elegir el mejor fold es cherry-picking, no es válido

---

### Pregunta 3 → **c) No garantiza salidas en [0, 1]**

**El problema central:** La regresión lineal puede predecir -0.3 o 1.5. Esos números NO son probabilidades. Una probabilidad DEBE estar entre 0 y 1.
**Para eso existe la regresión logística:** Usa la función sigmoide que "aplasta" cualquier número al rango [0, 1]. Así sí puedes interpretar la salida como probabilidad.
**Por qué NO las otras:**
- (a) No es tema de precisión, es un error conceptual de diseño
- (b) Sí puede procesar categóricas con encoding
- (d) Técnicamente SÍ puede clasificar (con umbral), el problema es que las salidas no son probabilidades válidas

---

### Pregunta 4 → **c) Overfitting, regularizar**

**Qué pasa:** AUC alto en entrenamiento, bajo en prueba.
**Es exactamente el mismo patrón que la pregunta 1:** El modelo se aprendió de memoria los datos de entrenamiento. En datos nuevos, falla.
**Solución:** Regularizar (penalizar coeficientes) o reducir la cantidad de variables (menos oportunidades de memorizar).

---

### Pregunta 5 → **a) Homocedasticidad**

**Qué pasa:** Los residuos forman un "abanico" — son pequeños al inicio y se abren conforme el valor predicho crece.
**Qué es homocedasticidad:** Es el supuesto de que la varianza de los errores es CONSTANTE para todos los valores de X. Homo = igual, cedasticidad = dispersión.
**Abanico = heterocedasticidad:** La dispersión de los errores CAMBIA (crece). Se viola el supuesto.
**Cómo se ve:**
```
Correcto (homo):     •  •  •  •  •     (dispersión constante)
Lo que hay (hetero):  • •• ••••• •••••  (dispersión creciente = abanico)
```

---

### Pregunta 6 → **d) No captura relación no lineal**

**Qué pasa:** Los residuos forman una U invertida al graficarlos contra el predictor.
**Qué significa:** El modelo lineal traza una LÍNEA RECTA, pero la relación real entre distancia y tiempo de entrega tiene una curva. La línea recta no puede seguir esa curva, entonces los errores forman un patrón sistemático.
**Solución:** Usar un modelo que capture curvas (polinómico, por ejemplo).
**No es overfitting (e):** Al contrario, es UNDERFITTING — el modelo es demasiado simple para los datos.

---

### Pregunta 7 → **e) Odds se multiplican por exp(0.35)**

**Esto es CLAVE y el error más común del mundo:**

En regresión logística, el coeficiente β = 0.35 NO significa "la probabilidad sube 35%".

**Lo que realmente pasa:**
1. El modelo predice **log-odds** (logaritmo de la razón de probabilidades)
2. β = 0.35 significa: por cada día más de hospitalización, el **log-odds** sube 0.35
3. Para convertir a odds: exp(0.35) = 1.42
4. Significa: los odds de readmisión se **multiplican por 1.42** por cada día extra

**Si te preguntan "¿por qué no es 35%?":** Porque la relación entre coeficiente y probabilidad NO es directa en logística. Pasa por una función sigmoide que es NO lineal. El coeficiente actúa en escala logarítmica, no en escala de probabilidad.

---

### Pregunta 8 → **e) Recall para la clase spam**

**El problema del desbalance:** 5% spam, 95% no-spam. Si un modelo SIEMPRE dice "no es spam" (sin analizar nada), tendría 95% de accuracy. Eso es inútil.
**Por qué recall:** Recall responde la pregunta: "De todos los correos que SÍ eran spam, ¿cuántos detecté?" Eso es exactamente lo que nos importa. Un spam que se te escapa (falso negativo) es el peor error aquí.
**Accuracy es engañosa** con clases desbalanceadas porque premia predecir la clase mayoritaria.

---

### Pregunta 9 → **e) Regresión polinómica**

**Qué pasa:** RMSE alto + residuos en forma de U.
**Es la misma lógica que la pregunta 6:** Los residuos con patrón U significan que hay una curva que el modelo lineal no ve. Necesitas un modelo con términos x² para capturar esa curva.
**Por qué NO Ridge (a):** Ridge regulariza, pero el modelo sigue siendo lineal. No puede capturar curvas.

---

### Pregunta 10 → **c) Normalización/estandarización**

**Por qué KNN necesita normalizar:** KNN decide basándose en DISTANCIAS. Si tienes edad (0-100) e ingreso (0-1,000,000), el ingreso domina completamente el cálculo de distancia. La edad se vuelve irrelevante.
**Normalizar:** Pone todas las variables en la misma escala. Así cada variable contribuye de forma justa a la distancia.
**StandardScaler:** Resta la media y divide entre la desviación estándar. Resultado: media=0, desviación=1.

---

### Pregunta 11 → **b) Maldición de la dimensionalidad**

**Qué pasa:** 3000 variables. KNN funciona bien en entrenamiento, mal en prueba.
**El concepto:** En espacios de muchas dimensiones, TODOS los puntos están aproximadamente a la misma distancia entre sí. Si todos están igual de lejos, buscar "el más cercano" no tiene sentido. KNN pierde su ventaja.
**Por qué no es simplemente overfitting (c):** El overfitting es la CONSECUENCIA. La maldición de la dimensionalidad es la CAUSA raíz. La pregunta pide el fenómeno principal responsable.

---

### Pregunta 12 → **e) Mejor que aleatorio, margen de mejora**

**Escala de AUC:**
```
0.50 = azar (tirar una moneda)
0.60-0.70 = pobre
0.70-0.80 = aceptable
0.80-0.90 = bueno
0.90-1.00 = excelente
```
AUC = 0.65 está por encima de 0.5 (no es azar), pero es un clasificador pobre. Tiene margen para mejorar.

---

### Pregunta 13 → **d) Normalizar (StandardScaler)**

**Mismo concepto que la pregunta 10.** Edad (0-100) vs ingreso (0-1M). Sin normalizar, KNN solo mira el ingreso porque las distancias están dominadas por esa variable.
**Es el paso CRÍTICO antes de KNN.** No opcional. Obligatorio.

---

### Pregunta 14 → **b) AUC-ROC**

**1% fraude, 99% legítimo. Accuracy = 99%. ¿Es bueno?** NO. Un modelo que dice "todo es legítimo" (sin analizar nada) también tiene 99%. Es inútil.
**AUC-ROC** evalúa qué tan bien el modelo SEPARA las dos clases a través de TODOS los umbrales posibles. No se deja engañar por el desbalance.

---

### Pregunta 15 → **a) Precision**

**Los números:**
```
TP=80, FP=150, FN=20, TN=750

Precision = 80 / (80+150) = 80/230 = 34.8%  ← MUY BAJA
Recall    = 80 / (80+20)  = 80/100 = 80.0%
Accuracy  = (80+750)/1000 = 83.0%
Especif.  = 750 / (750+150) = 83.3%
```

Precision está en 34.8% — es la peor. Hay 150 falsos positivos que la destrozan. De cada 230 veces que el modelo dice "enfermo", solo 80 realmente lo están.

---

## PARTE PRÁCTICA 1 — RIESGO CARDIOVASCULAR (P16-P20)

---

### Pregunta 16 → **c) Pearson no detecta relaciones no lineales**

**Los datos dicen:** Todas las correlaciones son casi cero (< 0.04). Pearson dice "no hay relación lineal".
**Pero SÍ hay relación.** Es parabólica (forma de U). Pearson no puede verla porque solo busca líneas rectas.
**Así se ve:**
```
Lineal (Pearson la detecta):       /     o     \
Parabólica (Pearson NO la ve):    U  (sube, baja, sube)
```
El coeficiente r de Pearson de una parábola perfecta puede ser 0, porque la subida y la bajada se cancelan.

---

### Pregunta 17 → **d) Pairplot**

**Después de que Pearson no muestra nada, ¿qué haces?** MIRAS los datos. Un pairplot genera gráficos de dispersión de todas las combinaciones de variables. Ahí puedes VER patrones que Pearson no captura: curvas, agrupaciones, formas extrañas.

---

### Pregunta 18 → **e) Relación parabólica Horas_Ejercicio vs Riesgo**

**En el pairplot se ve claro:** Horas_Ejercicio vs Riesgo_Cardiovascular forma una U. Poco ejercicio = riesgo alto. Ejercicio moderado = riesgo bajo. Mucho ejercicio = riesgo vuelve a subir.
Ninguna otra variable muestra un patrón así de claro.

---

### Pregunta 19 → **a) Polinomial captura la parábola**

**El experimento:**
- Modelo lineal (línea recta): MSE = 42.64 → error alto, no captura la U
- Modelo polinomial grado 2 (parábola): MSE = 6.52 → error 6.5 veces menor

**Por qué (a) y no (b):** La opción (b) dice "siempre usar polinomios altos". Falso. Solo usas polinomio cuando los datos realmente tienen esa forma. No es una regla universal.

---

### Pregunta 20 → **d) Polinomial claramente mejor, 6.5x menor MSE**

**¿Es overfitting (opción c)?** No. Grado 2 es un modelo MUY simple (solo agrega un término x²). Overfitting sería grado 15 o 20. Aquí la relación es genuinamente cuadrática, el modelo simplemente la captura correctamente.

---

## PARTE PRÁCTICA 2 — PREDICCIONES Y SCORES (P21-P26)

---

### La Matriz de Confusión — Cómo leerla

```
              Pred=0    Pred=1
Real=0 (166)  TN=156    FP=10      ← Los que NO son positivos
Real=1 (134)  FN=50     TP=84      ← Los que SÍ son positivos
```

**Las 4 celdas:**
- **TP (84):** Era positivo, dije positivo. ACIERTO.
- **TN (156):** Era negativo, dije negativo. ACIERTO.
- **FP (10):** Era negativo, dije positivo. ME EQUIVOQUÉ (falsa alarma).
- **FN (50):** Era positivo, dije negativo. ME EQUIVOQUÉ (se me escapó).

---

### Pregunta 21 → **c) Clasificador 1 (AUC = 0.83)**

AUC calculado = 0.8287 ≈ 0.83. Miras las 4 gráficas:
- Clasificador 1: AUC = 0.83 ← ESTE
- Clasificador 2: AUC = 0.96
- Clasificador 3: AUC = 0.95
- Clasificador 4: AUC = 0.94

---

### Pregunta 22 → **a) Accuracy = 80%**

**Accuracy = aciertos totales / total**
= (TP + TN) / (TP + TN + FP + FN)
= (84 + 156) / (84 + 156 + 10 + 50)
= 240 / 300
= **0.80 = 80%**

---

### Pregunta 23 → **b) Precision = 89%**

**Precision = de los que DIJE positivo, cuántos realmente lo eran**
= TP / (TP + FP)
= 84 / (84 + 10)
= 84 / 94
= **0.8936 ≈ 89%**

Precision alta = pocas falsas alarmas. Cuando el modelo dice "positivo", casi siempre tiene razón.

---

### Pregunta 24 → **a) Recall = 63%**

**Recall = de los que ERAN positivo, cuántos detecté**
= TP / (TP + FN)
= 84 / (84 + 50)
= 84 / 134
= **0.6269 ≈ 63%**

Recall bajo = se le escapan muchos positivos. De 134 positivos reales, solo detectó 84. Se le pasaron 50.

---

### Pregunta 25 → **Verdadero** (FN=50 > FP=10)

50 falsos negativos vs 10 falsos positivos. El modelo tiene 5 veces más errores de "no detectar" que de "falsa alarma". Es un modelo conservador: prefiere decir "no" antes que arriesgarse.

---

### Pregunta 26 → **Verdadero** (Especificidad = 94%)

**Especificidad = de los NEGATIVOS reales, cuántos identifiqué bien**
= TN / (TN + FP) = 156 / (156 + 10) = 156/166 = **94%** > 85%. Sí es verdadero.

---

## PARTE PRÁCTICA 3 — KNN Y LOGREG (P27-P30)

---

### Pregunta 27 → **Falso** (NO son balanceadas)

Clase 0: 589 total (423 train + 166 test)
Clase 1: 411 total (277 train + 134 test)

589 ≠ 411. La clase 0 tiene un 43% más de registros que la clase 1.

---

### Pregunta 28 → **b) K=3, accuracy = 92%**

```
K= 1 → 86%  (muy sensible a ruido, overfitting)
K= 3 → 92%  ← MEJOR entre las opciones del examen
K= 5 → 91%
K= 7 → 92%
K=11 → 93%
```

K=3 da 92.33%. Entre las opciones (K=3/90%, K=3/92%, K=7/95%, K=5/80%, K=5/70%), la correcta es K=3 con 92%.

---

### Pregunta 29 → **a) 8 falsos positivos**

Con K=7:
```
TN=158, FP=8, FN=17, TP=117
```
Falsos positivos = 8. Son casos donde el modelo dijo "clase 1" pero realmente eran "clase 0".

---

### Pregunta 30 → **a) Coeficientes: -0.325, 1.433, Intercepto: -0.120**

Resultado exacto del modelo:
```
feat1: β₁ = -0.3254  ≈ -0.325
feat2: β₂ =  1.4332  ≈  1.433
Intercepto = -0.1210 ≈ -0.120
```

**Cómo leer estos coeficientes:**
- **feat1 negativo (-0.325):** Cuando feat1 sube, la probabilidad de ser clase 1 BAJA
- **feat2 positivo (1.433):** Cuando feat2 sube, la probabilidad de ser clase 1 SUBE
- **feat2 pesa más** que feat1 (1.433 vs 0.325): feat2 es la variable más influyente

---

## GUÍA PARA LA SUSTENTACIÓN ORAL

### Si te preguntan sobre REGULARIZACIÓN:

**"¿Qué es?"** → Es una penalización que se agrega a la función de costo del modelo para evitar que los coeficientes crezcan demasiado. Coeficientes grandes = modelo que se ajusta demasiado a los datos de entrenamiento.

**"Ridge vs LASSO"** →
- Ridge (L2): Penaliza la SUMA DE CUADRADOS de los coeficientes. Los reduce pero nunca a cero exacto.
- LASSO (L1): Penaliza la SUMA DE VALORES ABSOLUTOS. Puede hacer coeficientes exactamente cero, eliminando variables automáticamente.
- Elastic-Net: Mezcla de ambos. l1_ratio=0 es Ridge, l1_ratio=1 es LASSO.

**"¿Qué es C en sklearn?"** → C = 1/α. Es inversamente proporcional a la fuerza de regularización. C chico = mucha regularización = modelo simple. C grande = poca regularización = modelo complejo.

---

### Si te preguntan sobre KNN:

**"¿Cómo funciona?"** → Para clasificar un punto nuevo, busca los K puntos más cercanos del entrenamiento y asigna la clase que más se repite entre ellos (voto mayoritario).

**"¿Por qué normalizar?"** → Porque KNN calcula distancias. Si una variable va de 0 a 1,000,000 y otra de 0 a 100, la primera domina completamente la distancia. Normalizar pone todas en la misma escala.

**"¿Cómo elegir K?"** → Se prueba con varios valores y se elige el que dé mejor accuracy (o F1) en validación. K=1 memoriza (overfitting). K muy grande suaviza demasiado (underfitting).

**"¿Qué es la maldición de la dimensionalidad?"** → Con muchas variables (ej: 3000), el espacio se vuelve tan grande que todos los puntos quedan igual de lejos. "Vecino más cercano" pierde significado. Solución: reducir dimensiones antes (PCA, selección de variables).

---

### Si te preguntan sobre MÉTRICAS:

**"¿Cuándo no sirve accuracy?"** → Cuando las clases están desbalanceadas. Si 99% es clase 0, un modelo que siempre dice 0 tiene 99% accuracy y es completamente inútil.

**"Precision vs Recall, ¿cuál es más importante?"** → Depende del costo del error:
- Si un falso positivo es caro (ej: acusar inocentes) → priorizas **Precision**
- Si un falso negativo es caro (ej: no detectar cáncer) → priorizas **Recall**

**"¿Qué es AUC-ROC?"** → La curva ROC grafica Recall (TPR) vs Tasa de Falsos Positivos (FPR) para TODOS los umbrales posibles. AUC es el área bajo esa curva. 0.5 = azar. 1.0 = perfecto. Mientras más arriba y a la izquierda va la curva, mejor.

---

### Si te preguntan sobre REGRESIÓN LOGÍSTICA:

**"¿Cómo interpretar coeficientes?"** → exp(β) es el odds ratio. Si β = 0.35, exp(0.35) = 1.42. Significa que por cada unidad que sube esa variable, los odds se multiplican por 1.42 (aumentan 42%). NO es un aumento de 35% en probabilidad.

**"¿Qué es la sigmoide?"** → La función que convierte cualquier número real al rango [0, 1]. σ(z) = 1/(1+e^(-z)). Cuando z es muy negativo → σ ≈ 0. Cuando z es muy positivo → σ ≈ 1. Cuando z = 0 → σ = 0.5.

---

### Si te preguntan sobre RELACIONES NO LINEALES:

**"¿Por qué Pearson da r ≈ 0 si hay relación?"** → Pearson solo mide relaciones LINEALES (línea recta). Una parábola sube y luego baja. La subida y la bajada se cancelan, dando r ≈ 0. La relación existe, pero no es lineal.

**"¿Cómo detectar relaciones no lineales?"** → Visualización (pairplot, scatter plots). Si ves curvas, usas modelos que capturen curvas (polinómicos, etc.).

---

## FÓRMULAS CLAVE

```
CLASIFICACIÓN:
  Accuracy    = (TP + TN) / Total
  Precision   = TP / (TP + FP)         "de los que dije sí, cuántos eran sí"
  Recall      = TP / (TP + FN)         "de los que eran sí, cuántos detecté"
  F1          = 2·P·R / (P + R)        "balance entre precision y recall"
  Specificity = TN / (TN + FP)         "de los que eran no, cuántos dije no"

REGRESIÓN:
  MSE  = (1/n) · Σ(yi - ŷi)²          "promedio del error al cuadrado"
  RMSE = √MSE                          "error en las mismas unidades que y"
  R²   = 1 - (SS_res / SS_tot)         "qué % de varianza explica el modelo"

LOGÍSTICA:
  P(y=1) = 1 / (1 + e^(-(β₀ + β₁x₁ + β₂x₂)))
  Odds Ratio = exp(βᵢ)

DISTANCIAS (KNN):
  Euclidiana: √(Σ(xi - yi)²)           "línea recta entre dos puntos"
  Manhattan:  Σ|xi - yi|                "caminar por cuadras"
```
