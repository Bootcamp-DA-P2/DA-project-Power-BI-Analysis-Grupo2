# 🏡 Dashboard de Business Intelligence - Airbnb

## 🧠 Descripción del proyecto

Este proyecto forma parte de una iniciativa de Business Intelligence para una startup del sector inmobiliario.

La empresa ha sido contratada por Airbnb para analizar datos de distintas ciudades y desarrollar un sistema de reporting interactivo basado en dashboards. El objetivo es transformar datos en bruto en información útil para la toma de decisiones mediante visualizaciones claras y análisis accionables.

---

## 🎯 Objetivos del proyecto

- Cargar, limpiar y transformar datos mediante Power Query
- Construir un modelo de datos relacional optimizado
- Crear medidas avanzadas con DAX
- Diseñar dashboards interactivos en Power BI
- Extraer insights de negocio accionables
- Permitir análisis comparativo entre ciudades, barrios y tipos de alojamiento

---

## ⚙️ Tecnologías utilizadas

- Microsoft Power BI Desktop
- Power Query (M Language)
- DAX (Data Analysis Expressions)
- Git y GitHub (control de versiones)
- GitHub Projects (gestión del flujo de trabajo y tareas del equipo)

---

## 🧹 Proceso ETL (Extracción, Transformación y Carga)

### 🔍 Limpieza y transformación de datos

Se han realizado las siguientes transformaciones en Power Query:

- Carga inicial de múltiples archivos y combinación de tablas en una única estructura de trabajo. Posteriormente, se añadió una columna `source` con el nombre de los archivos originales, que fue renombrada a `city` para estandarizar la procedencia de los datos. Además, se eliminó la extensión `_airbnb.csv` mediante reemplazo de texto, dejando únicamente el nombre de la ciudad.
- Eliminación de columnas con información considerada no relevante para el análisis, como `name` y `host_name`, ya que no aportaban valor analítico y aumentaban la cardinalidad del modelo.
- Eliminación de la columna `neighbourhood_group` por contener un alto porcentaje de valores en blanco y no ser utilizada en el análisis.
- Eliminación de la columna `calculated_host_listings_count` debido a la alta presencia de valores nulos y a que esta métrica puede ser derivada mediante cálculos en DAX.
- Renombrado de campos para estandarización del modelo (`id` → `listing_id`, `source` → `city`).
- Revisión y eliminación de duplicados en `listing_id` y `host_id` para garantizar la integridad del modelo.
- Corrección de formatos numéricos en latitud y longitud y ajuste de tipos de datos (`text`, `number`, `date`).
- Aplicación de funciones de limpieza de texto (`Trim` y `Clean`) y normalización de mayúsculas/minúsculas para evitar duplicidades en categorías como `neighbourhood`.
- Revisión de calidad de datos mediante herramientas de perfilado (calidad, distribución y perfil de columnas).
- Tratamiento de valores nulos: se aplicó limpieza en campos críticos como `listing_id` y `price`, mientras que en el resto de variables se conservaron los nulos para mantener la distinción entre ausencia de información y valor cero.

---

### 🧠 Decisiones de limpieza y optimización del modelo

- Estructuración del modelo en esquema en estrella, separando tablas de hechos y dimensiones.
- Creación de claves únicas (`id`) en todas las tablas necesarias para garantizar integridad relacional.
- Definición de relaciones uno a muchos (1:*) entre dimensiones y tabla de hechos `fact_listings`, con filtro unidireccional.
- Creación de jerarquías en dimensiones para facilitar el análisis:

  - `dim_location`: jerarquía de `city → neighbourhood`, incluyendo además una columna calculada de `hemisferio`.

  - `dim_date`: construcción de tabla calendario a partir de `last_review`, con columnas derivadas (`year`, `month`, `mes`, `numero de mes`) y jerarquía temporal completa. La columna `year_last_review` se generó directamente en Power Query a partir de `last_review` mediante una transformación básica de fecha. Al tratarse de un atributo estático, se incorporó durante la fase ETL para mantener centralizadas las transformaciones temporales dentro de la dimensión de fechas.

  - `dim_host`: estructura basada en `host_id`, con posibilidad de incorporar `calculated_host_listings_count` si se desea extender el análisis.

- `dim_room_type` contiene la categorización de tipo de alojamiento.

---

### 🗂️ Modelado de datos

El modelo se compone de:

- `fact_listings` (tabla de hechos)
- `dim_location` (ubicación)
- `dim_room_type` (tipo de alojamiento)
- `dim_host` (anfitrión)
- `dim_date` (tabla de fechas)

Relaciones establecidas:

- Relación uno a muchos (1:*) desde dimensiones hacia tabla de hechos
- Dirección de filtro unidireccional para mantener consistencia del modelo

---

### 📊 Transformaciones en tabla de hechos (`fact_listings`)

En la tabla de hechos se han creado las siguientes columnas y métricas:

- `rango_precio` (segmentación de precios)
- `price_eur` (normalización de precios por moneda local)
- `porcentaje_ocupacion`

Y mediante DAX se han desarrollado medidas avanzadas:

- `% disponibilidad anual`
- `% premium listings`
- `actividad alquiler`
- `actividad alquiler hemisferio`
- `disponibilidad media`
- `highest price eur`
- `ingresos anuales estimados`
- `media reviews`
- `median price eur`
- `ocupacion promedio %`
- `precio medio eur`
- `premium listings`
- `revenue`
- `total alojamientos`
- `total anfitriones`

---

## 📊 Dashboards desarrollados

### 🟦 Executive Dashboard

Panel orientado a proporcionar una visión global del negocio mediante indicadores clave y segmentación interactiva.

**KPIs principales:**
- Revenue total
- Total de alojamientos
- Total de anfitriones

**Visualizaciones:**
- Matriz jerárquica `city → neighbourhood` con análisis de revenue por ubicación.

**Filtros globales sincronizados:**
- Fecha (`date`)
- Ciudad (`city`)
- Barrio (`neighbourhood`)
- Tipo de alojamiento (`room_type`)

---

### 🟧 Price Analysis Dashboard

Panel enfocado en el análisis y comparación de precios entre ciudades, barrios y tipos de alojamiento.

**KPIs principales:**
- Median Price EUR
- Highest Price EUR
- Premium Listings (%)

**Visualizaciones:**
- Median Price EUR by City
- Listings by Price Range
- Top 5 Most Expensive Neighbourhoods (Median Price EUR)
- Top 5 Cheapest Neighbourhoods (Median Price EUR)
- Median Price EUR by Room Type

---

### 🟨 Temporal Analysis Dashboard

Panel orientado al análisis temporal de precios, ocupación y disponibilidad.

**Visualizaciones:**
- Rental Activity by Hemisphere, Month and City
- Average Price EUR by Month and City
- Matrix of Occupancy Rate (%) by City
- Annual Availability (%) by Year and Room Type

**Objetivo:**
- Identificar patrones estacionales
- Comparar comportamientos entre ciudades y hemisferios
- Analizar tendencias de ocupación y disponibilidad

---

### 🟩 Geographical Analysis Dashboard

Panel geográfico destinado a visualizar la distribución espacial de los alojamientos.

**Visualizaciones:**
- Mapa de alojamientos utilizando coordenadas (`latitude`, `longitude`)
- Total de alojamientos por ubicación

**Objetivo:**
- Analizar la concentración geográfica de los listings
- Identificar zonas con mayor presencia de alojamientos Airbnb

## 🧠 Insights de negocio

- Existen diferencias significativas de precios entre ciudades
- Los alojamientos tipo “Entire home/apt” concentran los precios más altos
- Los mercados premium están geográficamente concentrados
- La distribución de precios está sesgada, por lo que la mediana es una métrica más representativa que la media

---

## 🔗 Interactividad del dashboard

- Navegación entre páginas mediante botones personalizados
- Filtros globales sincronizados entre páginas
- Segmentación por ciudad, fecha y rango de precio
- Jerarquías geográficas (ciudad → barrio)

---

## 🚀 Conclusión

Este proyecto demuestra la capacidad de transformar datos complejos en información visual e intuitiva mediante herramientas de Business Intelligence.

El dashboard final permite analizar el comportamiento del mercado de Airbnb desde una perspectiva geográfica, económica y estructural.

---

## 📌 Herramientas utilizadas

Power BI • Power Query • DAX • GitHub • Modelado de datos • Visualización de datos

---

## 📋 Gestión del proyecto

Para la organización y seguimiento del trabajo se utilizó **GitHub Projects** como herramienta Kanban, permitiendo planificar tareas, distribuir responsabilidades entre los miembros del equipo y monitorizar el progreso del proyecto durante todas sus fases de desarrollo.

A través del tablero se gestionaron actividades relacionadas con:

- Limpieza y transformación de datos (ETL)
- Diseño y optimización del modelo de datos
- Desarrollo de medidas DAX
- Construcción de dashboards y visualizaciones
- Documentación del proyecto
- Revisión y validación de entregables

El flujo de trabajo se estructuró mediante estados de seguimiento para facilitar la planificación y coordinación del equipo.

### 🔗 Tablero Kanban

[Ver GitHub Project Board](https://github.com/orgs/Bootcamp-DA-P2/projects/24)

## 👥 Equipo de desarrollo

Proyecto realizado por:

- **Sonia Navarro Romero**
- **Yasira Blanco Moreno**
- **Elena Suárez Serrano**