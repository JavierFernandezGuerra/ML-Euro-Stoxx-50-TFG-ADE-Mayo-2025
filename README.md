# 🎓 ML & Euro Stoxx 50 — Trabajo Fin de Grado

Autor: Javier Fernández Guerra | Tutor: Alfonso Santos Ramón | Fecha: Mayo 2025

Grado: Administración y Dirección de Empresas, Mención en Análisis de Datos

Calificación: Matrícula de Honor 

---

## Resumen

Proyecto práctico que aplica **algoritmos de clasificación supervisada basados en árboles** para la **selección de acciones en el índice Euro Stoxx 50**.
El trabajo replica y adapta la metodología de **Caparrini et al. (2024)** (originalmente sobre el S&P 500), evaluando la capacidad de **Decision Tree**, **Random Forest** y **XGBoost** para generar carteras que superen al benchmark europeo.

El código implementa un **pipeline reproducible**: descarga/preprocesado de datos, cálculo de métricas financieras, etiquetado temporal, entrenamiento, optimización de hiperparámetros (incluido un algoritmo genético) y **backtesting sin look-ahead bias**.

> Este código corresponde al desarrollo del TFG presentado en la UAM. Documento completo disponible en: [Repositorio UAM](https://repositorio.uam.es/handle/10486/721076).

---

## Tecnologías y librerías principales

* **Python 3.x**, **pandas**, **numpy** — manipulación y análisis de datos.
* **eikon (LSEG / Refinitiv)** — descarga de series históricas de precios.
* **scikit-learn** — implementación de DecisionTree y RandomForest, validación y escalado.
* **xgboost** — modelo de boosting para clasificación.
* **matplotlib / seaborn** — visualización de métricas y capital acumulado.

---

## Objetivos del proyecto

1. Evaluar si los clasificadores basados en árboles pueden mejorar la selección de acciones en el **Euro Stoxx 50**.
2. Calcular métricas financieras relevantes (p. ej. **beta móvil a 3 años**, ratios y retornos logarítmicos).
3. Construir un **dataset de características (features)** y etiquetas de rentabilidad futura a 12 meses.
4. Entrenar, optimizar y validar modelos (Decision Tree, Random Forest, XGBoost).
5. Simular carteras con rebalanceo temporal y comparar su **precisión y rentabilidad acumulada** frente al benchmark.

---

## Metodología

### 1. Obtención y preprocesado de datos

* Descarga de precios mensuales del índice `.STOXX50E` y de los 50 componentes (RICs) mediante Eikon o archivos locales en caché.
* Conversión de precios a retornos simples y logarítmicos.
* Alineación temporal de series y limpieza de valores faltantes.

### 2. Cálculo de métricas financieras

* **Beta 3 años** por ventana móvil mediante:

  * Covarianza/varianza.
  * Regresión lineal móvil (rolling window de 36 meses).
* Se generan ratios y factores complementarios (momentum, volatilidad, valor, etc.).

### 3. Construcción del dataset de ML

* Selección de empresas con historial completo desde 1998.
* Generación de la variable objetivo `Label` según la **rentabilidad futura a 12 meses** (clasificación binaria: top/bottom).
* Normalización y almacenamiento del dataset final (`df_final`).

### 4. Entrenamiento y optimización

* **Función principal:** `ejecutar_pipeline_modelo(df_final, df_prices_long, df_eurostoxx, modelo=...)`

  * Entrena el clasificador seleccionado.
  * **Optimización de hiperparámetros** mediante búsqueda en grid o **algoritmo genético**.
  * Validación temporal (*TimeSeriesSplit*) para evitar *look-ahead bias*.
  * Cálculo de precisiones y métricas por rebalanceo.

### 5. Backtesting y formación de carteras

* Rebalanceos periódicos (anuales o semestrales).
* Cálculo de retornos simulados mediante `calcular_retorno_estrategia()` y `rebalanceo_backtest()`.
* Generación de curvas de **capital acumulado** y comparación con el índice Euro Stoxx 50.

---

## Métricas y evaluación

* **Accuracy temporal** (por fecha de rebalanceo).
* **Capital acumulado** de las carteras simuladas vs. **benchmark**.
* **Métricas de rendimiento**: retorno anualizado, volatilidad, Sharpe ratio, drawdown máximo.
* **Importancia de variables** y su evolución temporal (feature importance media y dinámica).

---

## Funciones clave

| Función                                | Descripción                                                |
| -------------------------------------- | ---------------------------------------------------------- |
| `ejecutar_pipeline_modelo(...)`        | Entrena, valida y ejecuta el backtest completo.            |
| `rebalanceo_backtest(...)`             | Lógica de rebalanceo de carteras.                          |
| `calcular_retorno_estrategia(...)`     | Convierte predicciones del modelo en evolución de capital. |
| `calcular_metricas_performance(...)`   | Calcula retorno, volatilidad y drawdown.                   |
| `guardar_objeto()` / `cargar_objeto()` | Cacheo de resultados y reproducibilidad.                   |

---

## Cómo reproducir

1. **Instalar dependencias:**

   ```bash
   pip install pandas numpy scikit-learn xgboost eikon matplotlib seaborn
   ```
2. **Configurar Eikon:**

   ```python
   import eikon as ek
   ek.set_app_key("TU_CLAVE_EIKON")
   ```
3. **Ejecutar el pipeline:**

   ```python
   ejecutar_pipeline_modelo(df_final, df_prices_long, df_eurostoxx, modelo="xgboost")
   ```

---

## Resultados principales

* Los modelos de ML alcanzan **precisiones superiores al azar** y **superan al benchmark** en rendimiento acumulado.
* La **importancia de los factores** cambia a lo largo del tiempo, confirmando su **dinamismo**.
* Los resultados son **coherentes con los hallazgos de Caparrini et al. (2024)** en el S&P 500.

---

## Licencia

Licencia MIT — uso académico con cita al autor.

