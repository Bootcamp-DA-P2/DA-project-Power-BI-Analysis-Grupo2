## DOCUMENTACIÓN DEL PROCESO ETL
1.	Pasos de transformación documentados

* El primer paso ha sido eliminar columnas con información que hemos considerado innecesaria, tales como “name” (una descripción del alojamiento) o “host_name” (dado que ya contamos con un “ID” único para la identificación).
* Igualmente, se han eliminado algunas filas con estados irrelevantes para reducir el tamaño del modelo y que sea más manejable. 
* Renombramos las columnas de fuente por “city” y “id” por “listing_id”.
* Remplazamos los puntos por comas en las columnas latitud y longitud, para seguidamente cambiar correctamente los tipos de datos (número, fecha), evitando que los decimales se corrompan. 

2. Decisiones de limpieza y optimización del modelo

* Aplicamos las funciones Recortar (Trim) y Limpiar (Clean) en los campos de texto, para eliminar espacios en blanco accidentales, y normalizamos mayúsculas y minúsculas, para evitar duplicados en las categorías (por ejemplo, “neighbourhood”).
* Utilizamos herramientas como Calidad de columnas o Distribución de columnas para identificar visualmente la cantidad de errores y nulos antes y después de eliminarlos. 
* Agrupamos valores, creamos consultas combinadas y eliminamos las columnas redundantes una vez vinculadas a sus IDs correspondientes. Esto facilita la búsqueda y visualización de datos, reduce el peso del modelo y mejora el rendimiento de las medidas DAX. Por ejemplo, para la dimensión “dim_location”, agrupamos los valores únicos city y neighbourhood y generamos una clave única (“location_id”).
* A partir de la tabla original, separamos los atributos descriptivos para dar forma a las tablas de dimensiones independientes (dim_room_type, dim_host, dim_location), manteniendo únicamente las columnas de métricas y llaves externas (FK) en la tabla de hechos “fact_listings”. 
* Extraemos los valores únicos de la columna de fecha “last_review” para construir una dimensión de tiempo dedicada (dim_date), garantizando el filtrado temporal eficiente sin depender de la tabla de hechos. En la visualización de datos, encontramos un problema con Tokyo, si bien se debe a la falta inicial de datos. 
* Establecemos relaciones uno a muchos (1:*) desde las dimensiones hacia la tabla de hechos, configurando la dirección del filtro cruzado como único. Aseguramos que los filtros fluyan correctamente desde las dimensiones para segmentar las métricas de fact_listings.
* Durante la limpieza y preparación, identificamos la necesidad de segmentar los alojamientos, por lo que creamos columnas calculadas (Rango_Precio) y medidas DAX (Ocupación promedio) para mantener el modelo ordenado. 
