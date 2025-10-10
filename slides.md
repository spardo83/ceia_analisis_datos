# Slide 1 · Objetivo general
- Contexto: explorar el dataset  de `Airbnb` para entender oferta, calidad del dato y drivers de ocupacion.
- Meta: preparar un conjunto de features limpio que permita modelar el nivel de ocupacion (`occupancy_level`).

# Slide 2 · Ingesta y saneamiento inicial
- Lectura robusta: uso de `na_values` para capturar variantes vacias y duplicar el dataframe como base de trabajo.
- Limpieza de `price`: remocion de simbolos monetarios, conversion a numerico y verificacion de valores no positivos.
- Fechas clave (`last_scraped`, `host_since`, etc.): conversion a `datetime` para habilitar calculos temporales.
- Normalizacion de flags binarios: mapear `t/f` a categorias legibles (`yes/no`) y evitar valores nulos silenciosos.

# Slide 3 · Radiografia del dataset
- `df.info()` y resumen de tipos: dimension, tipos de columna y densidad de valores no nulos.
- Tablas descriptivas numericas y categoricas: detectar escalas, dispersion y categorias dominantes.
- Duplicados y nulos en `id`: validar integridad antes de avanzar con analisis.

# Slide 4 · Valores faltantes
- Tabla y grafico (`missing_table`, `top_missing`): identificar variables criticas con mayor porcentaje de NaNs.
- `missingno.matrix`: visualizar patrones conjuntos de faltantes que sugieren mecanismos MAR/MNAR.
- Analisis especifico: cruce de `reviews_per_month` faltante con `number_of_reviews` para interpretar causas.

# Slide 5 · Distribuciones clave
- Histogramas de `price` (completo y con recorte 99%): detectar sesgo positivo y posibles outliers de alto precio.
- Visualizaciones de disponibilidad (`availability_*`) y tasa estimada de ocupacion para comprender la demanda.

# Slide 6 · Feature engineering temporal
- Imputacion de `reviews_per_month` en cero cuando la ausencia implica falta de reseñas.
- Calculo de `host_tenure_days` y `days_since_last_review` usando `last_scraped` como referencia temporal.
- Densidad de reseñas (`reviews_per_year`): normaliza volumen de reseñas respecto de la antiguedad del host.

# Slide 7 · Analisis de skewness en precios
- Nueva celda calcula skewness de `price` y de `log1p(price)` para cuantificar la asimetria observada.
- Razonamiento: decidir transformaciones (log, winsorizacion) o estrategias de outlier handling basadas en evidencia.

# Slide 8 · Tratamiento de precios atípicos
- Conteo de precios iguales a cero o negativos; definicion de la mediana positiva como referencia de imputacion.
- Creacion de `price_imputed` reemplazando valores no positivos para evitar distorsiones y asegurar consistencia.
- Verificacion inmediata con `MinMaxScaler` para chequear que el rango escalado quede entre 0 y 1.
- Base para futuros analisis de outliers o decisiones de winsorizacion segun el impacto en el modelo.

# Slide 9 · Codificacion de amenities
- Limpieza flexible del JSON Array: parseo robusto con `json.loads`, fallback a `ast.literal_eval`.
- Normalizacion a minusculas y deduplicacion para obtener listas consistentes por propiedad.
- Seleccion Top-20 amenities y creacion de indicadores binarios (`amenity_*`) para enriquecer el feature set.
- Tabla de frecuencia/porcentaje expone cobertura relativa y ayuda a priorizar futuras ingenierias.

# Slide 10 · Dataset final para modelado
- Construccion de `feature_cols`: incluye variables numericas base + indicadores de amenities.
- Incorporacion de `price_imputed` como variable principal de precio para el entrenamiento.
- Ensamble de `model_df` con target `occupancy_level`, preservando integridad de registros.
- Division train/test (`train_test_split`) asegurando que se excluyen filas sin target.

# Slide 11 · Pipeline de preprocesamiento
- `ColumnTransformer` aplica `StandardScaler` a numericas base y `MinMaxScaler` exclusivo para `price_imputed`.
- Incorporacion de columnas de amenities como numericas para preservar su escala discreta (0/1).
- `OneHotEncoder` en categoricas con `handle_unknown='ignore'` para robustez al predecir en nuevos datos.
- Resultado: matrices `X_train_prepared` y `X_test_prepared` listas para alimentar modelos supervisados.
