# Evaluación y Validación de Modelos de Machine Learning para Riesgo Crediticio

Este repositorio contiene un proyecto de evaluación y validación de modelos de aprendizaje automático aplicado a un problema financiero de clasificación binaria. El objetivo principal es predecir si una solicitud de crédito podría caer en incumplimiento a 90 días (`default_90d = 1`) a partir de variables financieras, demográficas y crediticias.

El trabajo compara el desempeño de cuatro modelos supervisados de machine learning frente a dos baselines: un modelo dummy de clase mayoritaria y una regla financiera tradicional. Además, se incorpora un enfoque de evaluación orientado a negocio mediante métricas estadísticas, ajuste de umbral, validación cruzada, análisis A/B simulado, curvas ROC/PR/KS, matriz de confusión, análisis de lift, calibración e importancia de variables.

---

## Objetivo SMART

El objetivo del proyecto es seleccionar, validar y justificar un modelo de aprendizaje automático que identifique clientes con alto riesgo de incumplimiento crediticio, comparando cuatro modelos supervisados contra un baseline financiero.

Los criterios operativos definidos fueron:

- Recall mínimo: **70%**
- Precision mínima: **40%**
- ROC-AUC mínimo: **0.80**
- Mejora de utilidad incremental frente al baseline por cada 1,000 solicitudes

---

## Problema de negocio

En el contexto de originación crediticia, una institución financiera necesita anticipar qué clientes presentan mayor probabilidad de incumplimiento. Una mala clasificación puede tener impactos relevantes:

- Un **falso negativo** representa un cliente riesgoso que no fue detectado y podría generar pérdida financiera.
- Un **falso positivo** representa un cliente sano enviado a revisión, lo cual implica costo operativo o pérdida de oportunidad comercial.

Por esta razón, el proyecto no se limita a evaluar accuracy, sino que incorpora métricas más adecuadas para problemas financieros desbalanceados, como recall, precision, F2, ROC-AUC, PR-AUC, Brier Score, matriz de confusión y utilidad incremental.

---

## Dataset

Se utilizó un dataset financiero sintético y realista con:

- **8,000 registros**
- **17 variables predictoras**
- **Tasa de default:** 22.53%
- Variable objetivo: `default_90d`

Principales variables utilizadas:

- `edad`
- `ingresos_anuales`
- `antiguedad_laboral_meses`
- `score_buro`
- `ratio_deuda_ingreso`
- `uso_linea_credito`
- `atrasos_12m`
- `monto_credito`
- `ratio_credito_ingreso`
- `tasa_interes`
- `plazo_meses`
- `cuentas_abiertas`
- `prior_default`
- `desempleo_macro`
- `tipo_empleo`
- `destino_credito`
- `canal`

El dataset se dividió de forma estratificada en:

| Conjunto | Registros | Default rate |
|---|---:|---:|
| Train | 4,800 | 22.54% |
| Dev | 1,600 | 22.50% |
| Test | 1,600 | 22.50% |

---

## Modelos evaluados

Se entrenaron y compararon los siguientes modelos:

1. **Regresión Logística**
2. **Random Forest**
3. **Extra Trees**
4. **AdaBoost**

Además, se utilizaron dos baselines:

- **Dummy Classifier:** predice siempre la clase mayoritaria.
- **Regla financiera:** criterio basado en variables tradicionales de riesgo como score de buró, ratio deuda-ingreso, atrasos, uso de línea de crédito y default previo.

---

## Metodología

El flujo de trabajo del proyecto fue el siguiente:

1. Definición del problema financiero y objetivo SMART.
2. Generación y preparación del dataset.
3. Análisis exploratorio de datos.
4. Separación estratificada en train, dev y test.
5. Entrenamiento de cuatro modelos supervisados.
6. Evaluación inicial con umbral estándar de 0.50.
7. Ajuste de umbral operativo con enfoque de negocio.
8. Comparación contra baselines.
9. Validación cruzada.
10. Estimación de intervalos mediante bootstrap.
11. Análisis A/B simulado.
12. Análisis de matriz de confusión.
13. Evaluación de curvas ROC, Precision-Recall y KS.
14. Análisis de deciles, cumulative gains y lift.
15. Interpretación de variables mediante importancia por permutación.
16. Selección y justificación final del modelo.

---

## Resultados principales

El modelo seleccionado fue:

## **AdaBoost con umbral operativo de 0.32**

Métricas finales en el conjunto test:

| Métrica | Valor |
|---|---:|
| ROC-AUC | 0.8907 |
| PR-AUC | 0.7201 |
| Precision | 0.4814 |
| Recall | 0.9000 |
| F1 | 0.6273 |
| F2 | 0.7667 |
| Brier Score | 0.1284 |
| Tasa de alertas | 42.06% |
| True Negatives | 891 |
| False Positives | 349 |
| False Negatives | 36 |
| True Positives | 324 |

El modelo cumplió el objetivo SMART al superar los criterios mínimos de recall, precision y ROC-AUC.

---

## Comparación con baseline financiero

El baseline de regla financiera obtuvo:

- Recall: **62.50%**
- Precision: **65.03%**
- ROC-AUC: **0.8720**
- Utilidad incremental por 1,000 solicitudes: aproximadamente **677,781**

El modelo AdaBoost con umbral ajustado obtuvo:

- Recall: **90.00%**
- Precision: **48.14%**
- ROC-AUC: **0.8907**
- Utilidad incremental por 1,000 solicitudes: aproximadamente **888,625**

La mejora de utilidad incremental frente a la regla financiera fue de aproximadamente:

## **210,843.75 por cada 1,000 solicitudes**

---

## Interpretación de la matriz de confusión

Con el umbral estándar de 0.50, AdaBoost detectaba menos defaults:

| Resultado | Casos |
|---|---:|
| TN | 1,188 |
| FP | 52 |
| FN | 191 |
| TP | 169 |

Después de ajustar el umbral a 0.32:

| Resultado | Casos |
|---|---:|
| TN | 891 |
| FP | 349 |
| FN | 36 |
| TP | 324 |

El ajuste de umbral incrementó la cantidad de defaults detectados y redujo significativamente los falsos negativos, lo cual es clave en un problema de riesgo crediticio. A cambio, aumentó la tasa de falsos positivos y la cantidad de solicitudes enviadas a revisión.

---

## Análisis A/B simulado

Se simuló una comparación entre una política de control basada en reglas financieras y una política basada en el modelo AdaBoost.

| Grupo | Tasa de alertas | Recall | Default rate en aprobados | Utilidad incremental / 1,000 |
|---|---:|---:|---:|---:|
| Control - Reglas | 21.03% | 60.66% | 11.21% | 657,749 |
| Modelo - AdaBoost | 42.82% | 90.96% | 3.56% | 895,108 |

La prueba z de dos proporciones arrojó:

- z = **4.5769**
- p-value = **0.0000**

Esto indica una diferencia estadísticamente significativa en la reducción del default rate de aprobados.

---

## Visualizaciones incluidas

El notebook genera visualizaciones robustas para analizar el desempeño del modelo:

- Distribución de la variable objetivo
- Distribución de variables por clase
- Default rate por segmentos categóricos
- Matriz de correlación
- Comparación de métricas por modelo
- Ranking por utilidad incremental
- Curvas ROC
- Curvas Precision-Recall
- Curva KS
- Matrices de confusión
- Optimización de umbral
- Trade-off entre falsos positivos y falsos negativos
- Calibración del modelo
- Cumulative gains
- Lift por decil
- Validación cruzada
- Intervalos bootstrap
- Importancia por permutación
- Tablero visual ejecutivo

---

## Interpretabilidad

La importancia por permutación mostró que las variables más relevantes para el modelo AdaBoost fueron:

| Variable | Importancia media |
|---|---:|
| tasa_interes | 0.33349 |
| ratio_deuda_ingreso | 0.08133 |
| ratio_credito_ingreso | 0.06831 |
| uso_linea_credito | 0.01657 |
| atrasos_12m | 0.00568 |

Esto indica que el modelo concentra su capacidad predictiva principalmente en variables relacionadas con costo del crédito, endeudamiento, capacidad de pago y comportamiento reciente.

---

## Estructura sugerida del repositorio

```text
.
├── notebook_modelo_riesgo_crediticio.ipynb
├── reporte_tecnico_evaluacion_modelo_financiero.docx
├── README.md
├── requirements.txt
└── assets/
    ├── fig01_eda.png
    ├── fig02_segmentos.png
    ├── fig03_correlacion.png
    ├── fig04_comparacion.png
    ├── fig05_roc_pr_ks.png
    ├── fig06_matriz_confusion.png
    ├── fig07_umbral.png
    ├── fig08_calibracion_lift.png
    ├── fig09_cv.png
    ├── fig10_bootstrap.png
    ├── fig11_interpretabilidad.png
    ├── fig12_ab.png
    └── fig13_dashboard.png
