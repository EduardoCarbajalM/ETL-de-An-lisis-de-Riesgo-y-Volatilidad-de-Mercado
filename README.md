# 📈 Financial Volatility Monitor: Crypto ETL Pipeline

Un pipeline ETL (Extract, Transform, Load) automatizado construido en Python para la extracción, limpieza y análisis estadístico de activos financieros en tiempo real. 

Este proyecto sortea protecciones anti-scraping para recopilar datos de mercado en vivo, utiliza transformaciones matemáticas vectorizadas para limpiar la información y aplica modelos estadísticos para cazar anomalías (outliers), empaquetando el resultado en una arquitectura columnar optimizada para Big Data.

## 🛠️ Stack Tecnológico
* **Lenguaje:** Python 3.x
* **Extracción:** `requests`, `BeautifulSoup4` (Web Scraping dinámico con inyección de User-Agent)
* **Transformación:** `pandas`, `numpy` (Limpieza de strings y vectorización condicional)
* **Análisis Estadístico:** `scipy.stats` (Z-Score y Regresión Lineal)
* **Visualización:** `matplotlib` (Scatter plots con formato condicional)
* **Almacenamiento:** `pyarrow` (Apache Parquet particionado con compresión Snappy)

## 🏗️ Arquitectura del Pipeline

### 1. Extract (Extracción Segura)
El script se conecta al mercado de divisas de Yahoo Finance. Se implementa una suplantación de cabeceras HTTP (`User-Agent`) para evadir el error 403 (Forbidden) del servidor. Mediante selectores CSS directos, se aísla el cuerpo de la tabla financiera y se extraen los 25 activos más transitados del día (Nombre, Precio, Variación a 24h y Volumen).

### 2. Transform (Limpieza y Vectorización)
La data cruda de la web requiere un preprocesamiento intensivo:
* Extracción de precios incrustados mediante `.str.split()`.
* Casteo forzado de caracteres financieros (comas y símbolos) a variables operables numéricamente (`float64`).
* Conversión de abreviaciones de volumen en inglés (Billions 'B', Trillions 'T') a su escala matemática base (`1e9`, `1e12`) utilizando diccionarios enlazados con `.map()`.
* Creación de una variable categórica **Tendencia** calculada en tiempo real con `np.where()`.

### 3. Analyze & Visualize (Inteligencia de Negocio)
Aplicación del módulo `scipy` para evaluar la salud del mercado general:
* Cálculo de **Regresión Lineal** para encontrar el coeficiente de correlación de Pearson (*r*) entre el Precio del activo y su Volumen de transacciones.
* Renderizado de Matplotlib en un gráfico de dispersión, inyectando los metadatos estadísticos en el cuadro general y aplicando colores dinámicos: Verde para activos Alcistas y Rojo para Bajistas.

### 4. Load (Almacenamiento Distribuido)
Los datos estructurados y enriquecidos se exportan utilizando el estándar de la industria (Apache Parquet). Para facilitar consultas masivas en entornos de clúster, el Dataframe se **particiona automáticamente** en el sistema de archivos dependiendo de su columna categórica de `Tendencia`.

## 🚀 Uso en Entorno Local (Fedora/Linux)