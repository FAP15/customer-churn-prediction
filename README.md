# Predicción de abandono de clientes (Customer Churn) con SQL y Machine Learning

## 1. Project Overview

El abandono de clientes (*Customer Churn*) es un problema relevante para las empresas que ofrecen servicios mediante suscripciones, ya que la pérdida de clientes puede afectar directamente sus ingresos y su crecimiento.

El objetivo de este proyecto es analizar el comportamiento de los clientes de una empresa ficticia de servicios por suscripción y desarrollar modelos de Machine Learning capaces de identificar a aquellos que presentan mayor probabilidad de abandonar el servicio.

Para ello, se construyó una base de datos relacional en MySQL, se realizaron consultas SQL para explorar el comportamiento de los clientes y se utilizó Python para preparar los datos, entrenar modelos predictivos y evaluar sus resultados.

El proyecto contempla un enfoque integral de análisis de datos, desde la extracción y exploración de información hasta la construcción y evaluación de modelos de clasificación.

## 2. Objectives

**Objetivo general**

Desarrollar un modelo de Machine Learning que permita predecir el abandono de clientes durante un período futuro de tres meses, utilizando información histórica sobre suscripciones, pagos, utilización del servicio y atención al cliente.

**Objetivos específicos**

- Diseñar y construir una base de datos relacional en MySQL para almacenar información de clientes y sus actividades.
- Realizar consultas SQL para analizar el comportamiento de los clientes e identificar patrones asociados al abandono.
- Construir un dataset de Machine Learning a partir de información histórica, evitando utilizar datos posteriores a la fecha de corte.
- Explorar y preparar las variables predictoras para su utilización en modelos de clasificación.
- Entrenar y comparar dos algoritmos: Regresión Logística y Random Forest.
- Evaluar el rendimiento de los modelos mediante métricas de clasificación y matrices de confusión.
- Identificar las variables que presentan mayor relevancia en las predicciones.

## 3. Technologies

Las principales herramientas y tecnologías utilizadas en el desarrollo del proyecto fueron:

**Base de datos**
- MySQL: creación, almacenamiento y consulta de los datos.
- MySQL Workbench: administración de la base de datos y ejecución de consultas SQL.

**Lenguaje de programación**
- Python: procesamiento de datos, análisis exploratorio y desarrollo de modelos predictivos.

**Librerías de Python**
- Pandas: manipulación y análisis de datos.
- SQLAlchemy: conexión e interacción entre Python y MySQL.
- PyMySQL: conector para la comunicación con MySQL.
- Scikit-learn: preprocesamiento de variables, entrenamiento y evaluación de modelos de Machine Learning.

**Algoritmos utilizados**
- Regresión Logística (*Logistic Regression*).
- Random Forest Classifier.

## 4. Dataset

Para este proyecto se utilizaron datos sintéticos que representan la actividad de clientes de una empresa ficticia que ofrece servicios mediante suscripciones.

Los datos fueron organizados en una base de datos relacional denominada `cliente_churn_db`, compuesta por cinco tablas:

| Tabla | Descripción |
|---|---|
| `customers` | Información general de los clientes, como edad, región, fecha de registro y canal de adquisición. |
| `subscriptions` | Información de las suscripciones, incluyendo tipo de plan, contrato, fechas de inicio y finalización, y estado. |
| `payments` | Registro de pagos, importes, fechas y estados de las transacciones. |
| `service_usage` | Información sobre la utilización del servicio, medida mediante cantidad de sesiones y horas de uso. |
| `support_tickets` | Registro de consultas o solicitudes realizadas por los clientes al servicio de soporte. |

### Dataset para Machine Learning

A partir de las tablas anteriores se construyó un dataset específico para el entrenamiento de los modelos predictivos.

Se estableció como fecha de corte el **30 de junio de 2026**, utilizando únicamente información histórica disponible hasta ese momento para construir las variables predictoras.

La variable objetivo, denominada `churn`, identifica si un cliente canceló su suscripción durante el período comprendido entre el **1 de julio y el 30 de septiembre de 2026**:

- `1`: el cliente canceló su suscripción durante el período objetivo.
- `0`: el cliente no canceló su suscripción durante ese período.

Se consideraron únicamente los clientes que tenían una suscripción vigente en la fecha de corte.

**Características del dataset resultante**

- 3.947 registros.
- 13 variables predictoras.
- 1 variable objetivo (`churn`).
- Sin valores faltantes.
- Sin registros duplicados.
- Un identificador único por cliente.

**Variables predictoras**

Las 13 variables utilizadas se organizaron en dos grupos:

**Variables numéricas**
- `age`: edad del cliente.
- `antiguedad_dias`: antigüedad de la suscripción en días.
- `total_pagos`: cantidad total de pagos registrados.
- `pagos_exitosos`: cantidad de pagos exitosos.
- `pagos_fallidos`: cantidad de pagos fallidos.
- `gasto_total`: gasto acumulado en pagos exitosos.
- `total_sesiones`: cantidad total de sesiones de uso.
- `total_horas`: cantidad total de horas de utilización.
- `cantidad_tickets`: cantidad de solicitudes realizadas al soporte.

**Variables categóricas**
- `region`: región geográfica del cliente.
- `acquisition_channel`: canal de adquisición.
- `plan_type`: tipo de plan contratado.
- `contract_type`: modalidad del contrato.

**Distribución de la variable objetivo**

| Categoría | Registros | Proporción |
|---|---:|---:|
| No abandonaron (`0`) | 3.581 | 90,73 % |
| Abandonaron (`1`) | 366 | 9,27 % |
| **Total** | **3.947** | **100 %** |

Esta distribución evidencia un desbalance entre las clases, ya que la cantidad de clientes que permanecieron en el servicio es considerablemente superior a la cantidad de clientes que lo abandonaron. Esta característica fue tenida en cuenta durante el entrenamiento y la evaluación de los modelos.
## 5. SQL Analysis

Una vez construida la base de datos, se realizaron consultas SQL para explorar el comportamiento de los clientes e identificar posibles patrones relacionados con el abandono del servicio.

El análisis se centró principalmente en las siguientes dimensiones:

### Abandono según el tipo de plan

Se analizó la cantidad de clientes activos, cancelados y pausados para cada tipo de suscripción.

| Plan | Total de clientes | Activos | Cancelados | Tasa de abandono |
|---|---:|---:|---:|---:|
| Basic | 2.130 | 1.178 | 854 | 42,03 % |
| Standard | 1.875 | 1.522 | 263 | 14,73 % |
| Premium | 995 | 800 | 133 | 14,26 % |

El plan Basic presentó una tasa de abandono considerablemente superior a la de los planes Standard y Premium. Esto permitió identificar diferencias importantes en el comportamiento de los clientes según el tipo de suscripción.

### Abandono según la antigüedad

Se agruparon los clientes según el tiempo transcurrido desde el inicio de su suscripción.

| Antigüedad | Total de clientes | Cancelados | Tasa de abandono |
|---|---:|---:|---:|
| Menos de 3 meses | 324 | 250 | 78,62 % |
| Entre 3 y 5 meses | 832 | 374 | 46,40 % |
| Entre 6 y 11 meses | 1.418 | 404 | 29,64 % |
| 12 meses o más | 2.426 | 222 | 9,81 % |

Se observó una relación inversa entre la antigüedad y la tasa de abandono: los clientes con menor tiempo de permanencia presentaron tasas de cancelación más elevadas.

### Abandono según la cantidad de tickets de soporte

También se estudió la relación entre la cantidad de solicitudes realizadas al servicio de soporte y el abandono.

| Cantidad de tickets | Total de clientes | Cancelados | Tasa de abandono |
|---|---:|---:|---:|
| Sin tickets | 2.500 | 637 | 26,97 % |
| 1 ticket | 1.315 | 309 | 24,56 % |
| Entre 2 y 3 tickets | 1.026 | 254 | 25,94 % |
| 4 o más tickets | 159 | 50 | 33,11 % |

Los resultados muestran que los clientes con cuatro o más tickets presentaron una tasa de abandono superior a la de los demás grupos. Sin embargo, las diferencias no permiten establecer por sí solas una relación causal entre las solicitudes de soporte y la cancelación.

### Análisis del gasto y la utilización del servicio

Se analizaron los pagos exitosos para comparar el gasto promedio según el plan contratado.

| Plan | Gasto promedio por pago |
|---|---:|
| Premium | 39,99 |
| Standard | 25,00 |
| Basic | 15,00 |

Además, se realizaron consultas para identificar a los clientes con mayor utilización del servicio, considerando la cantidad de sesiones y las horas acumuladas.

Se comparó también el gasto promedio acumulado entre clientes activos y cancelados:

- Clientes activos: 315,39.
- Clientes cancelados: 138,40.

### Evolución mensual

Finalmente, se analizó la evolución mensual de los pagos, los ingresos, las sesiones y las horas de utilización del servicio.

Este análisis permitió observar el comportamiento de estas métricas a lo largo del período disponible y complementar el estudio transversal de los clientes con una perspectiva temporal.

## 6. Machine Learning

A partir de los datos almacenados en MySQL, se construyó un dataset específico para desarrollar modelos de clasificación capaces de predecir el abandono de clientes.

### Construcción del dataset

Se estableció el 30 de junio de 2026 como fecha de corte y se definió como período objetivo los tres meses siguientes, desde el 1 de julio hasta el 30 de septiembre de 2026.

Se seleccionaron los 3.947 clientes que tenían una suscripción vigente en la fecha de corte.

La variable objetivo `churn` se definió de la siguiente manera:

- `1`: el cliente canceló durante el período objetivo.
- `0`: el cliente no canceló durante ese período.

Para evitar la filtración de información (*data leakage*), las variables predictoras se construyeron utilizando exclusivamente información disponible hasta la fecha de corte.

### Preparación de los datos

Se separaron las variables predictoras (`X`) de la variable objetivo (`y`). El identificador `customer_id` se conservó para identificar los registros, pero no se utilizó como característica predictiva.

Posteriormente, se dividieron los datos en conjuntos de entrenamiento y prueba:

- Entrenamiento: 80 % (3.157 registros).
- Prueba: 20 % (790 registros).

Se utilizó `stratify=y` para mantener aproximadamente la misma proporción de clases en ambos conjuntos.

### Preprocesamiento

Se implementó un proceso de preprocesamiento mediante `ColumnTransformer` y `Pipeline` de Scikit-learn.

**Variables numéricas:**
- Imputación de valores faltantes mediante la mediana.
- Estandarización mediante `StandardScaler`.

**Variables categóricas:**
- Imputación de valores faltantes mediante la categoría más frecuente.
- Codificación mediante `OneHotEncoder`, utilizando `handle_unknown="ignore"`.

Las transformaciones se integraron en los pipelines de los modelos para ajustar el preprocesamiento exclusivamente con los datos de entrenamiento.

### Entrenamiento de modelos

Se entrenaron dos algoritmos de clasificación:

**1. Regresión Logística (Logistic Regression)**

Se utilizó como modelo de referencia por tratarse de un algoritmo de clasificación interpretable. Se configuró con `class_weight="balanced"` para compensar el desbalance existente entre las clases.

**2. Random Forest Classifier**

Se utilizó un modelo basado en un conjunto de árboles de decisión, configurado con 200 estimadores (`n_estimators=200`) y `class_weight="balanced"`.

Ambos modelos se entrenaron utilizando los mismos conjuntos de entrenamiento y prueba, permitiendo comparar sus resultados bajo las mismas condiciones.

## 7. Model Evaluation

Para evaluar el rendimiento de los modelos se utilizaron las siguientes métricas:

- **Accuracy:** proporción de predicciones correctas sobre el total de registros.
- **Precision:** proporción de predicciones positivas que realmente corresponden a clientes que abandonaron.
- **Recall:** proporción de clientes que abandonaron y fueron correctamente identificados.
- **F1-score:** media armónica entre Precision y Recall.

### Comparación de modelos

Los resultados obtenidos sobre el conjunto de prueba fueron los siguientes:

| Métrica | Regresión Logística | Random Forest |
|---|---:|---:|
| Accuracy | 0,7747 | 0,8810 |
| Precision | 0,2535 | 0,3220 |
| Recall | 0,7397 | 0,2603 |
| F1-score | 0,3776 | 0,2879 |

### Matrices de confusión

**Regresión Logística**

| | Predicción: No abandono | Predicción: Abandono |
|---|---:|---:|
| Real: No abandono | 558 | 159 |
| Real: Abandono | 19 | 54 |

**Random Forest**

| | Predicción: No abandono | Predicción: Abandono |
|---|---:|---:|
| Real: No abandono | 677 | 40 |
| Real: Abandono | 54 | 19 |

### Interpretación de los resultados

La Regresión Logística obtuvo un Recall de 0,7397, identificando aproximadamente el 74 % de los clientes que realmente abandonaron el servicio. Sin embargo, su Precision fue de 0,2535, lo que indica que una proporción importante de sus predicciones positivas fueron falsas alarmas.

Random Forest obtuvo mejores resultados en Accuracy y Precision, pero presentó un Recall considerablemente inferior, identificando aproximadamente el 26 % de los abandonos reales.

Es importante considerar que el dataset presenta un fuerte desbalance de clases: aproximadamente el 90,73 % de los clientes pertenecen a la clase que no abandonó el servicio.

Por este motivo, la Accuracy debe interpretarse con precaución. Un clasificador que predijera siempre la clase mayoritaria alcanzaría una Accuracy aproximada del 90,73 %, superior a la obtenida por ambos modelos.

En este contexto, la Regresión Logística presentó un mejor equilibrio entre Recall y F1-score, por lo que resultó más adecuada para detectar clientes con riesgo de abandono dentro de los modelos evaluados.

### Análisis de umbrales de clasificación

También se exploraron distintos umbrales de clasificación para la Regresión Logística, utilizando las probabilidades de abandono obtenidas mediante `predict_proba()`.

| Umbral | Precision | Recall | F1-score |
|---|---:|---:|---:|
| 0,5 | 0,2535 | 0,7397 | 0,3776 |
| 0,4 | 0,2169 | 0,8767 | 0,3478 |
| 0,3 | 0,1896 | 0,9452 | 0,3158 |

Al reducir el umbral, el modelo identificó una mayor proporción de los clientes que abandonaron, aunque también aumentó la cantidad de falsas alarmas.

El umbral de 0,5 obtuvo el mayor F1-score entre los valores explorados, mientras que los umbrales inferiores priorizaron la detección de abandonos.

Este análisis es exploratorio: los umbrales se evaluaron sobre el conjunto de prueba. Para una selección definitiva, sería conveniente utilizar un conjunto de validación independiente o validación cruzada y reservar el conjunto de prueba para la evaluación final.

## 8. Feature Importance

Con el objetivo de comprender qué variables tuvieron mayor relevancia en las predicciones, se analizaron los coeficientes de la Regresión Logística y las importancias de variables de Random Forest.

### Regresión Logística

Se analizaron los coeficientes del modelo, considerando su magnitud y signo.

Entre los coeficientes de mayor magnitud se encontraron:

| Variable | Coeficiente |
|---|---:|
| gasto_total | 6,7908 |
| total_sesiones | -5,2317 |
| pagos_exitosos | -3,3947 |
| total_pagos | 1,2886 |
| plan_type_Basic | 0,8104 |
| pagos_fallidos | 0,7917 |
| plan_type_Premium | -0,7673 |
| total_horas | -0,7221 |
| antiguedad_dias | -0,7169 |

Los coeficientes positivos indican una asociación positiva con las probabilidades de abandono estimadas por el modelo, mientras que los negativos indican una asociación negativa, manteniendo constantes las demás variables del modelo.

Sin embargo, estos coeficientes deben interpretarse con precaución debido a la presencia de correlaciones elevadas entre algunas variables predictoras.

### Random Forest

Se utilizó el atributo `feature_importances_` para identificar las variables que más contribuyeron a las decisiones del modelo.

Las cinco variables con mayor importancia fueron:

| Variable | Importancia |
|---|---:|
| total_sesiones | 14,49 % |
| total_horas | 14,22 % |
| gasto_total | 12,44 % |
| antiguedad_dias | 9,93 % |
| age | 8,05 % |

Estas cinco variables concentran aproximadamente el 59,13 % de la importancia total del modelo.

### Interpretación

Ambos modelos destacaron la relevancia de variables relacionadas con la utilización del servicio, el gasto acumulado y la antigüedad de los clientes.

En particular, Random Forest asignó mayor importancia a la cantidad de sesiones, las horas de uso y el gasto total.

Es importante aclarar que la importancia de variables de Random Forest representa una contribución relativa basada en la reducción de impureza de los árboles. No indica si una variable aumenta o disminuye el abandono, ni demuestra una relación causal.

Asimismo, las correlaciones elevadas entre algunas variables pueden afectar la distribución de la importancia entre ellas.

## 9. Conclusions

El desarrollo de este proyecto permitió recorrer distintas etapas de un proceso de análisis de datos, desde la construcción y consulta de una base de datos relacional hasta el entrenamiento y evaluación de modelos de Machine Learning.

A través de las consultas SQL se identificaron diferencias importantes en las tasas de abandono según el tipo de plan y la antigüedad de los clientes. También se observaron patrones relacionados con la utilización del servicio, el gasto acumulado y las solicitudes de soporte.

En la etapa predictiva se entrenaron dos modelos de clasificación: Regresión Logística y Random Forest. Aunque Random Forest obtuvo una mayor Accuracy y Precision, la Regresión Logística presentó mejores resultados en Recall y F1-score, mostrando una mayor capacidad para identificar clientes que efectivamente abandonaron el servicio.

El análisis de variables permitió identificar la importancia de características relacionadas con la actividad de los clientes, especialmente la cantidad de sesiones, las horas de uso, el gasto acumulado y la antigüedad.

### Limitaciones

El proyecto presenta algunas limitaciones que deben considerarse al interpretar los resultados:

- **Datos sintéticos:** la información utilizada fue generada artificialmente y no representa necesariamente el comportamiento de clientes reales.
- **Desbalance de clases:** la proporción de clientes que abandonaron fue considerablemente inferior a la de aquellos que permanecieron en el servicio.
- **Validación temporal:** se utilizó una única fecha de corte y una división aleatoria entre entrenamiento y prueba. Por lo tanto, los resultados no permiten determinar cómo se comportarían los modelos ante períodos futuros diferentes.
- **Correlación entre variables:** algunas características presentan correlaciones elevadas, lo que puede afectar la interpretación de los coeficientes y la importancia de las variables.
- **Selección del umbral:** el análisis de distintos umbrales se realizó sobre el conjunto de prueba, por lo que sería conveniente repetirlo utilizando un conjunto de validación independiente.

### Posibles mejoras futuras

Como continuación del proyecto, podrían explorarse las siguientes mejoras:

- Incorporar técnicas de balanceo de clases y comparar sus resultados.
- Evaluar otros algoritmos de clasificación.
- Optimizar los hiperparámetros de los modelos.
- Utilizar validación cruzada y validación temporal.
- Analizar las predicciones mediante curvas ROC y Precision-Recall.
- Incorporar técnicas de interpretabilidad como SHAP.
- Evaluar el modelo con datos reales para analizar su capacidad de generalización.

En conclusión, el proyecto permitió desarrollar un flujo de trabajo completo que integra SQL, Python y Machine Learning para abordar un problema de clasificación aplicado al análisis de clientes. Además de comparar modelos predictivos, permitió identificar limitaciones metodológicas y posibles mejoras para futuras iteraciones.
