# Clasificación de Tumores Mamarios — Comparación de Modelos con Optimización de Umbral Clínico

**Lenguaje:** R | **Tema:** Clasificación Supervisada | **Dataset:** Wisconsin Breast Cancer (UCI / Kaggle)

---

## Problema

Predecir si un tumor mamario es **maligno (M) o benigno (B)** a partir de características extraídas de imágenes digitalizadas de núcleos celulares.

Se trata de un problema de clasificación binaria donde los **falsos negativos tienen mayor costo clínico** que los falsos positivos: no detectar un tumor maligno es más grave que indicar estudios adicionales innecesarios. La selección del modelo y la optimización del umbral de clasificación reflejan esta asimetría.

---

## Dataset

- **Fuente:** [Wisconsin Breast Cancer Dataset — Kaggle](https://www.kaggle.com/datasets/uciml/breast-cancer-wisconsin-data) | [UCI ML Repository](https://archive.ics.uci.edu/dataset/17/breast+cancer+wisconsin+diagnostic)
- **Observaciones:** 569 tumores (357 benignos / 212 malignos)
- **Variables:** 30 variables numéricas — media, error estándar y valor extremo de 10 características del núcleo celular (radio, textura, perímetro, área, suavidad, compacidad, concavidad, puntos cóncavos, simetría, dimensión fractal)
- **Balance de clases:** ~63% benignos / ~37% malignos (desbalance moderado)
- **Valores faltantes:** ninguno

---

## Metodología

### Análisis Exploratorio de Datos (EDA)
- Matriz de correlación con clustering jerárquico para identificar grupos de predictores altamente correlacionados
- Densidades empíricas por clase para evaluar la separabilidad entre grupos de variables
- Relaciones bivariadas entre variables representativas por grupo

**Hallazgo principal:** las variables de tamaño (radio, área, perímetro) y las morfológicas (concavidad, puntos cóncavos) muestran la mayor separación entre clases. Las fronteras de decisión parecen aproximadamente lineales en algunas combinaciones de variables.

### Estrategia de Evaluación
- **Split train/test (80/20)** realizado *antes* del EDA para evitar data leakage
- Split estratificado con `caret::createDataPartition()` para preservar las proporciones de clase
- Selección de modelos mediante **validación cruzada 10-fold** sobre el conjunto de entrenamiento, optimizando AUC
- Evaluación final sobre el conjunto de prueba reservado

### Modelos Comparados

| Modelo | Tipo |
|---|---|
| Regresión Logística | Discriminativo, frontera lineal |
| LDA | Generativo, frontera lineal |
| QDA | Generativo, frontera cuadrática |
| KNN | No paramétrico |
| Random Forest | Ensamble, no paramétrico |

### Optimización del Umbral de Clasificación
El umbral por defecto (0.5) fue ajustado para **maximizar la especificidad sujeto a una sensibilidad mínima de 0.95** — reflejando la prioridad clínica de minimizar tumores malignos no detectados.

---

## Resultados

KNN y Random Forest obtuvieron AUC = 0.994 en validación cruzada.

Tras la optimización del umbral en el conjunto de prueba:

| Modelo | AUC | Sensibilidad | Especificidad |
|---|---|---|---|
| Random Forest | > 0.98 | 0.976 | 0.958 |
| KNN | > 0.98 | 0.952 | > 0.96 |

**Se seleccionó Random Forest** como modelo final por su mayor sensibilidad tras el ajuste del umbral, que es la métrica clínicamente relevante en este contexto.

---

## Decisiones Técnicas Clave

- **Prevención de data leakage:** el split train/test se realizó antes de cualquier exploración
- **Métrica de selección:** AUC en lugar de accuracy, dado el desbalance moderado de clases y el costo asimétrico de los errores
- **Ajuste de umbral:** basado en criterio de dominio clínico, no solo en desempeño estadístico
- **Multicolinealidad:** reconocida y aceptada dado que el objetivo es predictivo, no inferencial

---

## Estructura del Repositorio

```
├── TP_final_Taller_de_datos.Rmd   # Análisis completo en R Markdown
├── data/
│   └── Dataset.csv                # Dataset Wisconsin Breast Cancer
└── README.md
```

Para reproducir el análisis: abrir `TP_final_Taller_de_datos.Rmd` en RStudio y knitear a HTML o PDF.

**Paquetes necesarios:** `tidyverse`, `caret`, `corrplot`, `pROC`, `GGally`

---

## Autora

[Carla Zanellato](https://github.com/carlazanellato) — Ingeniera Química | Especialización en Estadística Matemática (UBA FCEyN) | En transición hacia Data Science

*An English version of this README is available upon request.*
