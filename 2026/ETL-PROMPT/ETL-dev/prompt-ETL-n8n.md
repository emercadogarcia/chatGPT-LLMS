# PROMPT PARA GENERAR CODIGO ETL - N8N
**Diseña un proceso ETL robusto y auditable para N8N que haga lo siguiente:**
* Reciba archivos XLS o XLSX desde correo recibido gmail con el asunto: "[BI CORP] - DATOS TABLERO FINANCIERO"
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

**Los nombres de los archivos a recibir en el correo seran:**
1. ETL-FLUJO-CAJA
2. ETL-CARTERA_CxC
3. ETL-CARTERA-VENC-PROV-CXP
4. ETL-MAYOR-DET-PROVEEDOR
5. ETL-GASTOS
6. HECHOS_SUMAS_SALDOS
7. ETL-MAESTRO-PROVEEDORES
8. ETL-CentroCosto


# PROMPT ETL BI CORP – VERSION ENTERPRISE

Diseña un proceso ETL empresarial, robusto, auditable y transaccional que:
Reciba archivos XLS o XLSX desde Gmail cuyo asunto sea exactamente:
"[BI CORP] - DATOS TABLERO FINANCIERO"

Descargue adjuntos y valide:
* Extensión permitida (.xls, .xlsx)
* Tamaño máximo 10MB
* Nombre contra tabla CONFIG_ETL_ARCHIVOS usando patrón regex
* Que el archivo no haya sido procesado previamente (hash)

Resuelva dinámicamente:
* Tabla staging
* Tabla final
* Clave única
* Reglas de negocio
* Desde base de datos (sin hardcode).
* Convierta automáticamente el Excel a CSV codificado en UTF-8 sin BOM.

Normalice datos:
* Trim espacios
* Normalización numérica regional
* Fechas ISO 8601
* Control de nulos
* Cargue primero en tabla STAGING asociada.

Ejecute validaciones:
* Estructura esperada
* Tipos de datos
* Claves duplicadas
* Validación contra tablas maestras
* Determinar si registro es NUEVO o ACTUALIZACIÓN

Ejecute proceso transaccional:
BEGIN;
MERGE (INSERT/UPDATE usando ON CONFLICT);
LOG proceso;
COMMIT;
ROLLBACK si error;

Genere:
* Log de proceso
* Log detallado por error
* Métricas de calidad

Mueva archivo a:
* /processed si éxito
* /rejected si error
* Sea escalable, desacoplado y parametrizable.

Genere:
* Arquitectura técnica
* Modelo de datos soporte
* Ejemplo SQL MERGE en Postgres
* Ejemplo estructura STAGING
* Ejemplo manejo de errores

El diseño debe considerar:
* Cargas diarias y bajo demanda
* Volumen promedio 10MB
* Rollback automático
* Validación de registros nuevos vs actualización




---
# PROMPT: DISEÑO DE MOTOR ETL UNIVERSAL (N8N + POSTGRES) - ZERO IA

## ROL
Actúa como un Senior Data Engineer y Arquitecto de Soluciones n8n/PostgreSQL. Tu objetivo es diseñar un flujo de trabajo dinámico (Metadata-Driven) que procese múltiples archivos financieros para la organización [BI CORP].

## CONTEXTO Y FUENTES
El flujo debe ser capaz de procesar 8 tipos de archivos distintos con estructuras heterogéneas mediante una única ruta de nodos (workflow maestro):
1. ETL-FLUJO-CAJA
2. ETL-CARTERA_CxC
3. ETL-CARTERA-VENC-PROV-CXP
4. ETL-MAYOR-DET-PROVEEDOR
5. ETL-GASTOS
6. HECHOS_SUMAS_SALDOS
7. ETL-MAESTRO-PROVEEDORES
8. ETL_CentroCosto

## REQUERIMIENTOS TÉCNICOS (SIN IA)
El diseño debe basarse exclusivamente en lógica programática (JavaScript en Code Nodes) y nodos nativos de n8n, siguiendo el modelo de ejecución: Gmail -> JS Validation -> Binary to Text -> JS Parser -> JS Dynamic Cleaning -> Postgres Batch Insert/Upsert.

### 1. Gobernanza de Datos (PostgreSQL)
Provee el script DDL para crear una tabla de control llamada `ETL_CONFIG` que incluya:
- `archivo_id` (PK)
- `patron_nombre` (Regex para identificar el archivo)
- `tabla_destino` (Nombre de la tabla final)
- `mapping_columnas` (JSON que mapea: Nombre CSV -> Nombre DB)
- `columnas_clave` (Array de columnas para lógica de UPSERT/ON CONFLICT)

### 2. Lógica del Workflow en n8n
Diseña la lógica de los siguientes nodos clave:
- **Identificador de Archivo:** Un Code Node que analice el `fileName` del adjunto de Gmail y consulte la tabla `ETL_CONFIG` para traer los metadatos del proceso.
- **Parseador Universal:** Un Code Node (JS) que detecte automáticamente el delimitador (`,` o `;`), elimine el BOM de archivos UTF-8 y convierta el texto en un array de objetos JSON.
- **Limpiador de Datos Dinámico:** Un Code Node que, sin usar nombres de columnas fijos, itere sobre cualquier objeto y:
    - Aplique `.trim()` a todos los valores.
    - Convierta formatos de moneda regional (ej: `1.234,56` o `(100.00)`) a decimales estándar (`1234.56` o `-100.00`).
    - Normalice fechas al estándar ISO 8601.
- **Carga Transaccional:** Uso del nodo Postgres para ejecutar un `UPSERT` dinámico basado en las `columnas_clave` definidas en la configuración.

### 3. Robustez y Auditoría
- **Atomicidad:** Implementar BEGIN / COMMIT / ROLLBACK para asegurar que si un lote de 100 registros falla, no se ensucie la base de datos.
- **Control de Duplicados:** Calcular un Hash MD5 del archivo original para evitar el doble procesamiento.
- **Logging:** Registrar cada ejecución en una tabla `ETL_LOGS` con: fecha, archivo, registros procesados y status (Success/Error).

## ENTREGABLES
1. **Explicación de la Arquitectura:** Flujo detallado de nodos.
2. **Scripts SQL:** DDL de tablas de configuración, logs y ejemplo de Upsert.
3. **Código JavaScript (Code Nodes):** Scripts listos para copiar en n8n para el parseo y la limpieza dinámica.
4. **Estrategia de Errores:** Cómo manejar registros rechazados y mover archivos a carpetas `/rejected`.

## RESTRICCIONES
- NO utilizar nodos de OpenAI, LangChain o cualquier servicio de IA.
- El flujo debe soportar archivos de hasta 10MB (aprox. 50,000 filas).
- Todo debe ser escalable: si se añade un noveno archivo, solo se debe actualizar la tabla de configuración SQL, sin tocar el workflow de n8n.