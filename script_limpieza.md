# Script de limpieza

``` 
let
// Paso 1: Fuente de datos original
Origen = Table.FromRows(Json.Document(Binary.Decompress(Binary.FromText("XZDdSsMwGIZvJfR4laQ/Wg9XN1B0MFzFgzJGln5qoE1Kmoi3s2vwEnpjfokOFs++vOHh/WnbRHaHT1CWJ4tE6eFo4DAa3TlhNSqCW3jXRvrf0YCQXnwD8cH/oP2iTRhq5ImPVo9kazRhJQp3ehid5ULO3wqfLKP0ilK8MpoVKWUpLQOcobTRbgLyoHg/nzCBFN6FCwETWuvJQ9U/uApw7p0bED3vNNmAmE/qF94+v6zrJR63ZQyyLIBFcFXSakOKR+KbxnmLMoqbpTQPoK9Glg4zup4bmEjdoLJ0XVgGW8YUo4G6Rmm3W5H1lwWjcKGmvgzJ8gjLz9vcoPQKR8EHcr/yNpeTVGXM+Gb7Hw==", BinaryEncoding.Base64), Compression.Deflate)), let _t = ((type nullable text) meta [Serialized.Text = true]) in type table [Columna1 = _t, Columna2 = _t, Columna3 = _t, Columna4 = _t, Columna5 = _t]),

// Paso 2: Promover la primera fila como encabezados
PromoverEncabezados = Table.PromoteHeaders(Origen, [PromoteAllScalars=true]),

// Paso 3: Eliminar espacios en blanco al inicio y al final
// de la columna nombre_producto usando Text.Trim
LimpiarEspacios = Table.TransformColumns(
    PromoverEncabezados,
    {{"nombre_producto", Text.Trim, type text}}
),

// Paso 4: Estandarizar la columna categoria a Title Case
// para unificar "computación", "COMPUTACIÓN" y "Computación"
EstandarizarCategoria = Table.TransformColumns(
    LimpiarEspacios,
    {{"categoria", Text.Proper, type text}}
),

// Paso 5: Filtrar y eliminar registros de prueba
// Excluir filas donde categoria sea exactamente "Prueba"
EliminarPruebas = Table.SelectRows(
    EstandarizarCategoria,
    each [categoria] <> "Prueba"
),

// Paso 6: Definir tipos de datos correctos
TiparColumnas = Table.TransformColumnTypes(
    EliminarPruebas,
        {
            {"id_venta", Int64.Type},
            {"nombre_producto", type text},
            {"categoria", type text},
            {"precio", type number},
            {"fecha_venta", type date}
        }
)
in
TiparColumnas 
```
