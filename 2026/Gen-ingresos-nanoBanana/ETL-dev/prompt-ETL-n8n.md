# PROMPT PARA GENERAR CODIGO ETL - N8N
**Diseña un proceso ETL robusto y auditable que:**
* Reciba archivos XLS o XLSX
* Valide extensión, tamaño y nombre contra una tabla de configuración
* Convierta automáticamente el archivo a CSV codificado en UTF-8
* Normalice y limpie los datos
* Valide estructura, tipos y reglas de negocio
* Cargue primero en tabla staging
* Inserte en tabla final solo registros válidos
* Genere logging detallado con métricas de calidad
* Mueva archivos a carpetas processed o rejected según resultado
* Sea escalable y parametrizable (sin hardcode)
* Implemente control de errores y trazabilidad completa
* Genera arquitectura, modelo de tablas de soporte y ejemplo técnico.

**Para afinarlo necesito:**
¿Motor de base destino?     R. Postgres.
¿Volumen promedio por archivo?  R. promedio de 3 MB.
¿Carga diaria o bajo demanda?   R. Ambos casos.
¿Se requiere rollback automático?   R. Si.
¿Se debe validar contra otras tablas?   R. Se debe validar los datos que se van cargar si son NUEVOS o se va ACTUALIZAR, se debe hacer un analisis de la clave unica que pueda tener el archivo fuente.