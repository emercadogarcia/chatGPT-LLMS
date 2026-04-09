# 🔥 Prompt Maestro Optimizado -- Motor ETL Universal (N8N + Postgres) -- Zero IA

Actúa como un **Senior Backend Developer experto en n8n, NodeJS y
PostgreSQL**.

Diseña un **Motor ETL Universal en n8n** capaz de procesar dinámicamente
8 tipos de archivos financieros de \[BI CORP\], utilizando
exclusivamente:

-   Nodos nativos de n8n
-   Code Nodes (JavaScript)
-   Nodo Postgres
-   ❌ Sin uso de nodos de IA

------------------------------------------------------------------------

## 1️⃣ Arquitectura General

Workflow:

Gmail Trigger\
→ Identificación dinámica del archivo\
→ Obtención de configuración desde Postgres\
→ Conversión binario a texto\
→ Parser CSV dinámico\
→ Limpieza universal\
→ Split in Batches\
→ Upsert transaccional\
→ Registro en ETL_LOGS\
→ Movimiento a processed / rejected

Debe funcionar con una única ruta de nodos para los 8 tipos de archivos.

------------------------------------------------------------------------

## 2️⃣ Configuración en Postgres

### Tabla ETL_CONFIG

``` sql
CREATE TABLE etl_config (
    id serial primary key,
    archivo_patron varchar(100) not null,
    tabla_destino varchar(100) not null,
    columnas_mapping jsonb not null,
    columnas_clave text[] not null,
    activo boolean default true,
    created_at timestamp default current_timestamp
);
```

### Ejemplo columnas_mapping

``` json
{
  "MONTO": "importe",
  "FECHA": "fecha",
  "PROVEEDOR": "codigo_proveedor"
}
```

------------------------------------------------------------------------

## 3️⃣ Identificación Dinámica del Archivo

Validar asunto:

`[BI CORP] - DATOS TABLERO FINANCIERO`

Consulta:

``` sql
SELECT * FROM etl_config
WHERE activo = true
AND $fileName ILIKE '%' || archivo_patron || '%'
LIMIT 1;
```

Si no existe configuración → mover a rejected.

------------------------------------------------------------------------

## 4️⃣ Parser CSV Universal

Debe:

-   Eliminar BOM
-   Detectar delimitador automáticamente (`,` o `;`)
-   Normalizar saltos de línea
-   Convertir a JSON dinámico
-   Soportar archivos de hasta 10MB

------------------------------------------------------------------------

## 5️⃣ Limpieza Universal

Para cada fila:

-   trim()
-   Eliminar dobles espacios
-   Normalizar números:
    -   1.234,56 → 1234.56
    -   (100.00) → -100.00
-   Convertir fechas a ISO 8601
-   Convertir vacíos a NULL

Sin asumir nombres de columnas.

------------------------------------------------------------------------

## 6️⃣ Aplicación de Mapping Dinámico

-   Leer columnas_mapping desde ETL_CONFIG
-   Transformar keys CSV → keys DB
-   Ignorar columnas no mapeadas
-   Validar columnas_clave obligatorias

------------------------------------------------------------------------

## 7️⃣ Carga Transaccional (Upsert)

Estructura dinámica:

``` sql
INSERT INTO {{tabla_destino}} (col1, col2, col3)
VALUES ($1, $2, $3)
ON CONFLICT (clave1, clave2)
DO UPDATE SET
col2 = EXCLUDED.col2,
col3 = EXCLUDED.col3;
```

Debe generarse dinámicamente desde configuración.

------------------------------------------------------------------------

## 8️⃣ Control de Transacción por Lote

Cada Split in Batches:

-   BEGIN
-   UPSERT
-   Si falla → ROLLBACK
-   Registrar error
-   Continuar siguiente lote

------------------------------------------------------------------------

## 9️⃣ Logging

``` sql
CREATE TABLE etl_logs (
    id serial primary key,
    archivo varchar(255),
    tabla_destino varchar(100),
    filas_leidas integer,
    filas_insertadas integer,
    filas_actualizadas integer,
    filas_error integer,
    estado varchar(50),
    mensaje text,
    created_at timestamp default current_timestamp
);
```

------------------------------------------------------------------------

## 🔟 Movimiento de Archivos

-   Éxito total → /processed
-   Error crítico → /rejected

------------------------------------------------------------------------

## 1️⃣1️⃣ Restricciones Técnicas

-   Prohibido uso de IA
-   Un único workflow
-   Cero hardcode
-   Optimizado para 10MB
-   Compatible con Postgres estándar
-   Protección contra SQL Injection

------------------------------------------------------------------------

## 1️⃣2️⃣ Entregables Esperados

-   SQL completo de tablas
-   Código JavaScript para:
    -   Identificación
    -   Parser CSV
    -   Limpieza universal
    -   Mapping dinámico
    -   Generador SQL
-   Ejemplo real para ETL-GASTOS
-   Diagrama lógico
-   Explicación de rollback real en n8n

------------------------------------------------------------------------

### Resultado Esperado

Un ETL Engine reutilizable, parametrizable y 100% dinámico basado en
configuración.
