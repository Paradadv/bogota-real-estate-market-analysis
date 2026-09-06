# Bogotá Real Estate Market Analysis

## Descripción

Este proyecto analiza la evolución del mercado inmobiliario de Bogotá utilizando datos mensuales relacionados con ventas, disponibilidad, lanzamientos, área y valor de vivienda.

El análisis cubre el periodo comprendido entre junio de 2004 y junio de 2019 e incluye:

- limpieza y validación de datos;
- análisis exploratorio;
- análisis por segmentos VIS, No VIS y VIP;
- construcción de indicadores derivados;
- análisis de series de tiempo;
- comparación de modelos de pronóstico;
- generación de un forecast a 12 meses.

El proyecto fue desarrollado como parte de un portafolio de Ciencia de Datos aplicado a mercados y finanzas.

---

## Objetivo

El objetivo principal es entender la dinámica histórica del mercado de vivienda en Bogotá y evaluar qué tan bien distintos modelos de series de tiempo pueden pronosticar las unidades vendidas.

Las principales preguntas que busca responder el proyecto son:

- ¿Cómo han evolucionado las ventas de vivienda en Bogotá?
- ¿Qué diferencias existen entre los segmentos VIS y No VIS?
- ¿Existe estacionalidad en las ventas?
- ¿Cuáles han sido los periodos de mayor y menor actividad?
- ¿Qué modelo de series de tiempo ofrece mejores resultados?
- ¿Cuál sería el comportamiento esperado de las ventas durante los siguientes 12 meses?

---

## Dataset

**Fuente:** Datos Abiertos Bogotá.

El dataset contiene:

- 181 observaciones mensuales;
- 37 variables originales;
- datos desde junio de 2004 hasta junio de 2019.

Las variables incluyen información sobre:

- unidades vendidas;
- unidades disponibles;
- unidades lanzadas;
- área vendida;
- área disponible;
- área lanzada;
- valor vendido;
- valor disponible;
- valor lanzado.

La información se encuentra segmentada en:

- VIP;
- VIS;
- No VIS;
- Total.

---

## Calidad y limpieza de datos

Durante la revisión inicial se encontró que la serie temporal estaba completa:

- 181 meses;
- sin meses faltantes;
- sin fechas duplicadas.

Se identificaron tres valores faltantes en las siguientes variables:

- `UNIDADES_LANZADAS_VIP`
- `AREA_LANZADAS_VIP`
- `VALOR_LANZADAS_VIP`

Estos valores correspondían a:

- febrero de 2012;
- mayo de 2012;
- diciembre de 2012.

Para estos meses se verificó que:

```text
TOTAL = VIS + NO_VIS
```

Por esta razón, los valores VIP faltantes fueron imputados con cero.

---

## Consideración metodológica sobre el segmento VIP

Durante la validación de los datos se comprobó que:

```text
UNIDADES_VENDIDAS_VIP =
UNIDADES_VENDIDAS_TOTAL
- UNIDADES_VENDIDAS_VIS
- UNIDADES_VENDIDAS_NO_VIS
```

Esta relación se cumple para las 181 observaciones del dataset.

Esto indica que el segmento VIP se comporta como una categoría residual dentro de los datos.

En algunos meses la suma de VIS y No VIS supera ligeramente el total reportado, generando valores negativos en VIP.

Por esta razón, los indicadores derivados del segmento VIP no se utilizan como parte principal de las conclusiones del análisis.

El análisis se concentra principalmente en:

- mercado total;
- VIS;
- No VIS.

---

## Feature Engineering

A partir de las variables originales se construyeron nuevos indicadores para enriquecer el análisis.

### Valor promedio por vivienda

```text
Valor vendido / Unidades vendidas
```

### Valor promedio por metro cuadrado

```text
Valor vendido / Área vendida
```

### Tasa de absorción

```text
Unidades vendidas / Unidades disponibles
```

Este indicador permite aproximar la relación entre ventas y oferta disponible.

### Participación por segmento

Por ejemplo, para el segmento VIS:

```text
Unidades vendidas VIS / Unidades vendidas totales
```

### Crecimiento interanual

Se calculó la variación porcentual respecto al mismo mes del año anterior:

```python
pct_change(12)
```

Esto permite analizar cambios en las ventas reduciendo parte del efecto estacional mensual.

---

## Análisis exploratorio

### Ventas históricas

Las ventas mensuales presentan una alta variabilidad y distintos ciclos de expansión y contracción.

Se observan periodos de mayor actividad alrededor de:

- 2006;
- 2009;
- 2016.

También se identifican periodos de contracción importantes a lo largo de la serie.

---

## Resultados anuales

Para realizar comparaciones anuales se utilizaron únicamente años con 12 meses completos de observaciones.

### Mejor año completo

**2006**

Unidades vendidas:

```text
48.451
```

### Peor año completo

**2017**

Unidades vendidas:

```text
24.729
```

La diferencia entre ambos niveles representa una reducción aproximada del 49 %.

El año 2019 fue excluido de esta comparación debido a que el dataset únicamente contiene información hasta junio de ese año.

---

## Meses extremos

### Mes con mayor número de ventas

**Septiembre de 2009**

```text
5.182 unidades
```

### Mes con menor número de ventas

**Diciembre de 2008**

```text
1.373 unidades
```

---

## Estacionalidad

El análisis por mes del año muestra un patrón estacional visible.

En promedio:

- agosto presenta uno de los mayores niveles de ventas;
- septiembre también registra altos niveles de actividad;
- diciembre presenta el menor volumen promedio.

Esto sugiere que el mes del año contiene información relevante para explicar y pronosticar las ventas de vivienda.

---

## Tendencia

Se utilizó una media móvil de 12 meses para reducir el ruido mensual y visualizar mejor la tendencia de largo plazo.

La serie muestra:

- expansión hasta aproximadamente 2006–2007;
- caída durante 2008–2009;
- recuperación posterior;
- nuevos ciclos de contracción y recuperación.

La tendencia no es lineal, por lo que una regresión lineal simple no sería suficiente para representar adecuadamente el comportamiento de la serie.

---

## Descomposición temporal

La serie fue descompuesta en tres componentes:

- tendencia;
- estacionalidad;
- residuo.

La descomposición confirma la existencia de un componente estacional anual.

También se observan shocks importantes que no son explicados completamente por la tendencia y la estacionalidad.

Esto sugiere que existen eventos extraordinarios que afectan el comportamiento mensual del mercado.

---

# Forecasting

Para evaluar los modelos se utilizó una separación temporal entre entrenamiento y prueba.

## Entrenamiento

Periodo:

```text
Junio de 2004 - Diciembre de 2017
```

## Prueba

Periodo:

```text
Enero de 2018 - Junio de 2019
```

Esta separación permite simular un escenario realista en el que los modelos únicamente conocen información del pasado al momento de realizar el pronóstico.

---

## Modelos evaluados

Se compararon tres enfoques de forecasting:

1. Naive estacional
2. Holt-Winters
3. SARIMA

Las métricas utilizadas fueron:

- MAE;
- RMSE;
- MAPE.

---

## Resultados de los modelos

| Modelo | MAE | RMSE | MAPE |
|---|---:|---:|---:|
| Naive estacional | 513.89 | 661.63 | 18.85 % |
| Holt-Winters | **395.08** | **491.87** | **15.40 %** |
| SARIMA | 402.92 | 497.38 | 15.84 % |

---

## Mejor modelo

El mejor desempeño fue obtenido por:

### Holt-Winters

Resultados:

```text
MAE: 395.08
RMSE: 491.87
MAPE: 15.40 %
```

Holt-Winters obtuvo mejores resultados que el baseline estacional y un rendimiento ligeramente superior a SARIMA.

Esto sugiere que la combinación de tendencia y estacionalidad capturada por Holt-Winters representa adecuadamente la dinámica de esta serie.

---

## Pronóstico final

Después de seleccionar Holt-Winters como mejor modelo, se volvió a entrenar utilizando toda la serie disponible.

Posteriormente se generó un pronóstico de 12 meses.

Periodo pronosticado:

```text
Julio de 2019 - Junio de 2020
```

Este forecast debe interpretarse como un ejercicio retrospectivo de modelado y no como una proyección actual del mercado inmobiliario de Bogotá.

Los resultados del pronóstico se encuentran disponibles en:

```text
data/processed/pronostico_12_meses.csv
```

---

## Tecnologías utilizadas

- Python
- Pandas
- NumPy
- Matplotlib
- Scikit-learn
- Statsmodels
- Jupyter Notebook
- Git
- GitHub

---

## Estructura del proyecto

```text
bogota-real-estate-market-analysis/
│
├── data/
│   ├── raw/
│   │   └── mercado_inmobiliario_bogota.csv
│   │
│   └── processed/
│       ├── mercado_inmobiliario_bogota_limpio.csv
│       ├── comparacion_modelos.csv
│       └── pronostico_12_meses.csv
│
├── notebooks/
│   ├── 01_exploracion_inicial.ipynb
│   └── 02_forecasting.ipynb
│
├── src/
│
├── dashboard/
│
├── images/
│
├── README.md
├── requirements.txt
└── .gitignore
```

---

## Cómo ejecutar el proyecto

### 1. Clonar el repositorio

```bash
git clone https://github.com/Paradadv/bogota-real-estate-market-analysis.git
```

### 2. Entrar a la carpeta del proyecto

```bash
cd bogota-real-estate-market-analysis
```

### 3. Crear un entorno virtual

```bash
python -m venv .venv
```

### 4. Activar el entorno virtual en Windows

```powershell
.\.venv\Scripts\Activate.ps1
```

### 5. Instalar las dependencias

```bash
pip install -r requirements.txt
```

### 6. Ejecutar los notebooks

Ejecutar en el siguiente orden:

```text
1. notebooks/01_exploracion_inicial.ipynb
2. notebooks/02_forecasting.ipynb
```

---

## Limitaciones

El proyecto presenta varias limitaciones que deben ser consideradas al interpretar los resultados:

- el dataset finaliza en junio de 2019;
- 2019 es un año incompleto;
- existen particularidades metodológicas en algunas variables del segmento VIP;
- el análisis utiliza información agregada para toda Bogotá;
- no se incluyen variables macroeconómicas externas;
- el forecasting utiliza principalmente la dinámica histórica de la propia serie;
- los resultados no representan una predicción actual del mercado inmobiliario.

Variables externas como tasas de interés, inflación, desempleo o ingreso podrían mejorar futuros modelos.

---

## Posibles extensiones

Este proyecto puede ampliarse en varias direcciones:

- incorporar tasas de interés;
- incorporar inflación;
- incluir datos de desempleo;
- analizar VIS y No VIS de forma independiente;
- optimizar los parámetros de SARIMA;
- utilizar validación temporal walk-forward;
- comparar modelos de machine learning como XGBoost;
- construir un dashboard interactivo en Power BI;
- automatizar la actualización de los datos;
- construir un pipeline ETL;
- incorporar nuevas fuentes de información inmobiliaria.

---

## Conclusiones

El análisis muestra que el mercado inmobiliario de Bogotá presenta una dinámica compleja caracterizada por:

- ciclos de expansión y contracción;
- una estacionalidad anual identificable;
- diferencias importantes entre los segmentos VIS y No VIS;
- shocks mensuales que generan variaciones extraordinarias.

Entre los años completos analizados, 2006 presentó el mayor volumen de ventas, mientras que 2017 registró el menor.

En términos predictivos, Holt-Winters obtuvo el mejor desempeño entre los modelos evaluados, alcanzando un MAPE aproximado del 15.40 % sobre el conjunto de prueba.

Los resultados muestran que incorporar tendencia y estacionalidad permite mejorar considerablemente el pronóstico frente a un baseline estacional simple.

---

## Autor

**David**

Estudiante de Ciencia de Datos y Finanzas.