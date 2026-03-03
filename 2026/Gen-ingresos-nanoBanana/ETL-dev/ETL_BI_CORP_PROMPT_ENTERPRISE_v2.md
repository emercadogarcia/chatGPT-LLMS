# PROMPT ETL BI CORP -- ENTERPRISE v2

## Robusto · Parametrizable · Transaccional · Auditable

Diseña un proceso **ETL empresarial en Postgres**, completamente
parametrizado, transaccional, auditable y preparado para producción
financiera, alineado a buenas prácticas de arquitectura y gobierno de
datos.

------------------------------------------------------------------------

## 1️⃣ Origen y Disparo

-   Recibir archivos **.xls o .xlsx** desde Gmail.
-   Procesar únicamente correos cuyo asunto sea exactamente:\
    **"\[BI CORP\] - DATOS TABLERO FINANCIERO"**
-   Permitir ejecución:
    -   Automática (trigger)
    -   Manual (bajo demanda)

------------------------------------------------------------------------

## 2️⃣ Validaciones Iniciales (Pre-Ingesta)

Antes de procesar, validar:

-   Extensión permitida (.xls, .xlsx)
-   Tamaño máximo configurable (default 10MB)
-   Nombre de archivo contra tabla `CONFIG_ETL_ARCHIVOS` usando `regex`
-   Archivo no procesado previamente mediante:
    -   Hash SHA-256
    -   Control por nombre + fecha
-   Control de concurrencia (evitar doble ejecución)
-   Existencia de configuración activa para el archivo

Si alguna validación falla:

-   Registrar en `ETL_LOG_PROCESO`
-   Mover archivo a `/rejected`
-   Finalizar proceso sin romper el workflow general

------------------------------------------------------------------------

## 3️⃣ Configuración Dinámica (Sin Hardcode)

Toda la lógica debe resolverse desde base de datos.

La tabla `CONFIG_ETL_ARCHIVOS` debe definir:

-   `patron_regex`
-   `tabla_staging`
-   `tabla_final`
-   `clave_unica` (simple o compuesta)
-   `columnas_obligatorias`
-   `reglas_validacion`
-   `modo_carga` (append / upsert)
-   `activo`

No se permite lógica fija en código.

------------------------------------------------------------------------

## 4️⃣ Conversión y Normalización

-   Convertir Excel a CSV UTF-8 sin BOM.

-   Guardar archivo normalizado en `/landing`.

-   Aplicar normalización:

    -   Trim de espacios
    -   Eliminación de caracteres invisibles
    -   Normalización numérica regional (1.234,56 → 1234.56)
    -   Fechas a formato ISO 8601
    -   Estandarización de nulos
    -   Normalización de encabezados (UPPERCASE + snake_case)

-   Validar consistencia de columnas vs layout configurado.

------------------------------------------------------------------------

## 5️⃣ Carga en STAGING

-   Cargar primero en tabla STAGING asociada.
-   No insertar directamente en tabla final.

La tabla STAGING debe incluir:

-   `id_proceso`
-   `hash_archivo`
-   `raw_data`
-   `validado` (boolean)
-   `error_msg`
-   `created_at`

------------------------------------------------------------------------

## 6️⃣ Validaciones de Calidad (Data Quality Gate)

Ejecutar validaciones SQL antes del merge:

-   Estructura esperada
-   Tipos de datos
-   Longitudes máximas
-   Claves duplicadas internas
-   Validación contra tablas maestras
-   Identificación de registros NUEVOS vs ACTUALIZACIÓN
-   Conteo de registros leídos vs válidos
-   Reglas de negocio definidas dinámicamente

Si el porcentaje de error supera umbral configurable → abortar proceso.

------------------------------------------------------------------------

## 7️⃣ Proceso Transaccional Obligatorio

Implementar control transaccional en Postgres:

``` sql
BEGIN;

-- Validaciones finales

INSERT INTO tabla_final (...)
SELECT ...
FROM tabla_staging
ON CONFLICT (clave_unica)
DO UPDATE SET ...;

-- Registro en ETL_LOG_PROCESO

COMMIT;
```

En caso de error:

``` sql
ROLLBACK;
```

Debe garantizar atomicidad completa.

------------------------------------------------------------------------

## 8️⃣ Lógica de UPSERT Inteligente

-   Determinar clave única desde configuración.

-   Implementar `INSERT ... ON CONFLICT DO UPDATE`.

-   Permitir actualización selectiva de columnas.

-   Registrar métricas:

    -   Registros insertados
    -   Registros actualizados
    -   Registros rechazados

------------------------------------------------------------------------

## 9️⃣ Logging y Auditoría

### ETL_LOG_PROCESO

Debe registrar:

-   archivo
-   hash
-   tabla_destino
-   fecha_inicio
-   fecha_fin
-   registros_leidos
-   registros_insertados
-   registros_actualizados
-   registros_error
-   estado
-   mensaje_error

### ETL_LOG_DETALLE

Debe registrar:

-   id_proceso
-   fila
-   error_detectado
-   campo_afectado

Debe permitir auditoría completa y trazabilidad histórica.

------------------------------------------------------------------------

## 🔟 Gestión de Archivos

Estructura de carpetas:

-   `/landing` → recibido
-   `/processing` → en proceso
-   `/processed` → exitoso
-   `/rejected` → error

Mover archivos según resultado.\
Nunca eliminar automáticamente.

------------------------------------------------------------------------

## 1️⃣1️⃣ Requerimientos Técnicos

-   Motor destino: Postgres
-   Volumen promedio por archivo: 3MB
-   Soporte para carga diaria y bajo demanda
-   Rollback automático obligatorio
-   Validación de registros NUEVOS vs ACTUALIZACIÓN

Archivos soportados:

-   ETL-FLUJO-CAJA
-   ETL-CARTERA_CxC
-   ETL-CARTERA-VENC-PROV-CXP
-   ETL-MAYOR-DET-PROVEEDOR
-   ETL-GASTOS
-   HECHOS_SUMAS_SALDOS
-   ETL-MAESTRO-PROVEEDORES
-   ETL-CentroCosto

Cada archivo debe tener su propia clave única y reglas configuradas
dinámicamente.

------------------------------------------------------------------------

## 1️⃣2️⃣ Gobierno y Buenas Prácticas

El diseño debe incluir:

-   Arquitectura técnica por capas:
    -   Ingesta
    -   Staging
    -   Validación
    -   Integración
    -   Logging
-   Modelo de datos soporte
-   Estrategia anti reproceso (hash + histórico)
-   Control de concurrencia
-   Versionado de layout
-   Manejo estructurado de excepciones
-   Métricas de calidad de datos
-   Diseño escalable y desacoplado
-   Cero hardcode

------------------------------------------------------------------------

## 1️⃣3️⃣ Entregables Esperados

Generar:

-   Diagrama de arquitectura
-   DDL completo de tablas soporte
-   Ejemplo SQL MERGE en Postgres
-   Ejemplo estructura STAGING
-   Ejemplo función de validación
-   Estrategia detallada de rollback
-   Flujo técnico paso a paso

------------------------------------------------------------------------

Diseñar la solución como si fuera a ser auditada por control interno y
utilizada en un entorno financiero productivo.\
Priorizar robustez, trazabilidad, escalabilidad y mantenibilidad.
