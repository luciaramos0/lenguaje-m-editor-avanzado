``` let
    // Paso 1: Fuente de datos original
    Origen = Table.FromRows(
    {
        {"id_venta", "nombre_producto", "categoria", "precio", "fecha_venta"},
        // acá las filas de tu tabla del enunciado, tal cual
    },
    type table [Columna1 = text, Columna2 = text, Columna3 = text, Columna4 = text, Columna5 = text]
    ),

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
    TiparColumnas ```
