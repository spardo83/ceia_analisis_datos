# TP · Análisis y Preparación de Datos (Airbnb Listings)

Este proyecto explora un dataset de listados de Airbnb, diagnostica calidad de datos y prepara un conjunto de features para modelar el nivel de ocupación (clasificación/estimación de alta ocupación). El trabajo principal vive en el cuaderno `tp.ipynb` y se acompaña de un resumen de presentación en `slides.md`.

## Objetivos
- Entender estructura y calidad del dataset `listings_big.csv`.
- Realizar limpieza, imputaciones y estandarizaciones necesarias (precios, fechas, flags, etc.).
- Diseñar features relevantes (tiempo de host, reviews por año, amenities, transformaciones de precio, etc.).
- Armar un pipeline reproducible de preprocesamiento (imputación, escalado, codificación) listo para modelado.

## Estructura
- `tp.ipynb`: cuaderno principal con EDA, ingeniería de variables y pipeline.
- `listings_big.csv`: dataset base utilizado en el análisis.
- `slides.md`: guía/resumen de las diapositivas del proyecto.
- `presentation.md`: guion breve para orador (resumen para explicar el proyecto).
- `pyproject.toml` y `uv.lock`: definición del entorno y dependencias.

## Dataset
- Fuente: Inside Airbnb (conjunto consolidado de listados y métricas de disponibilidad y reseñas).
- Dimensión aproximada: ~35k filas, ~79 columnas (puede variar por ciudad/fecha).
- Campos destacados: `price`, `availability_*`, `number_of_reviews*`, `host_since`, `amenities`, `room_type`, `property_type`, coordenadas, y métricas derivadas (ej. `estimated_occupancy_l365d`).

## Principales pasos del cuaderno
- Ingesta robusta con captura de `na_values` y tipificación de fechas clave.
- Limpieza de `price` (símbolos monetarios → numérico) e imputación de valores no positivos.
- Normalización de flags binarios (`t/f` → `yes/no`).
- Radiografía del dataset: `df.info()`, tablas descriptivas, duplicados, faltantes y patrones conjuntos (`missingno`).
- Feature engineering temporal: `host_tenure_days`, `days_since_last_review`, `reviews_per_year`.
- Transformaciones y análisis de asimetría de `price` (posible `log1p`, winsorización).
- Codificación de `amenities`: parseo robusto, normalización y selección Top-N para indicadores binarios.
- Conjunto final para modelado con `ColumnTransformer`: imputación + `StandardScaler` en numéricas y `OneHotEncoder` en categóricas, preservando indicadores de amenities.

## Requisitos
- Python >= 3.11
- Dependencias clave (ver `pyproject.toml`): `pandas`, `numpy`, `scikit-learn`, `scipy`, `matplotlib`, `seaborn`, `missingno`, `xgboost`, `optuna`, `torch`/`torchvision` (para posibles extensiones), `jupyter`, `ipykernel`.

## Puesta en marcha
Opción A · uv (recomendado si lo usás en tu entorno):
1) Instalar `uv` (ver documentación oficial).
2) En el directorio del proyecto:
   - `uv sync`
   - `uv run jupyter lab` (o `uv run jupyter notebook`)

Opción B · pip/venv:
1) Crear entorno virtual (ej. `python -m venv .venv` y activar).
2) `pip install -r <(uv export --format requirements.txt)` si usás `uv` para exportar, o instalar manualmente según `pyproject.toml`.
3) `pip install jupyter ipykernel` (si no estuvieran).
4) Abrir el cuaderno: `jupyter lab` y cargar `tp.ipynb`.

Nota: `listings_big.csv` es un archivo grande; algunas celdas de perfilado/visualización pueden demorar.

## Cómo reproducir el análisis
- Abrir `tp.ipynb` y ejecutar de arriba hacia abajo.
- Verificar rutas: el cuaderno asume `listings_big.csv` en el mismo directorio.
- Para adaptar a otro dataset, mantener el flujo de saneamiento/codificación y ajustar nombres de columnas.

## Resultados esperados
- Diagnóstico de calidad de datos y tratamiento de faltantes.
- Conjunto de features numéricas/categóricas listo para modelado supervisado de ocupación.
- Pipeline reproducible para entrenamiento/validación con `scikit-learn`.

## Presentaciones
- `slides.md` contiene un esquema de 10 diapositivas con el flujo del proyecto.
- `presentation.md` ofrece un guion breve para orador (tiempos, mensajes clave y call to action).

## Licencia y uso
Este repositorio es para fines educativos/analíticos. La redistribución de datos de terceros puede estar sujeta a sus términos originales (Inside Airbnb). Verificar permisos antes de publicar.
