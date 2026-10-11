# Envíos de mercancía a sucursal

## Instalar

1. Detén el servidor en Visual Studio Code con Ctrl+C.
2. Reemplaza la carpeta src con la del paquete. Conserva tu .env.
3. En Supabase → SQL Editor → New query, pega todo supabase/06_envios_sucursal.sql y pulsa Run. Tu proyecto ya debe tener 03_cajeros_por_sucursal.sql. No ejecutes otra vez 01_esquema.sql.
4. Inicia el punto de venta con npm run dev y pulsa Actualizar.

## Preparar e imprimir

1. Entra como administrador y selecciona la sucursal de destino en la parte superior.
2. Abre Envíos a sucursal.
3. Busca por nombre o código, selecciona el producto, escribe las piezas y pulsa Agregar al envío. Repite para cada producto. Agregar el mismo producto suma sus piezas en el borrador.
4. Escribe observaciones y revisa destino y cantidades.
5. Pulsa Guardar envío y preparar ticket. En ese momento se suman las cantidades al inventario de destino y se abre la impresión. No se descuentan de otra sucursal.
6. El comprobante muestra folio, fecha, preparador, destino, productos, piezas enviadas y espacios para recibidas, diferencias y firma. Entrégalo junto con la mercancía.

El historial permite Imprimir ticket otra vez sin volver a sumar existencias. El cajero verá el apartado con los envíos de su sucursal y podrá imprimir; solo administradores pueden preparar envíos. Cancelar la impresión no cancela el envío.

Si se interrumpe la conexión al guardar, conserva el borrador pendiente y pulsa Confirmar envío: se reintenta con la misma solicitud, sin duplicar piezas. Los borradores se conservan en este navegador por usuario y sucursal. No borres los datos del navegador durante un envío pendiente. Si hay un error de validación, corrige el borrador y vuelve a guardar.

Las diferencias se anotan en papel y se comunican al administrador. La recepción no modifica automáticamente el inventario: el administrador registra el ajuste en Inventario → Entrada / salida e incluye el folio ENV en el motivo. Todavía no hay confirmación digital de recibido, edición o cancelación de envíos guardados.

No necesitas volver a publicar create-staff. Para una instalación nueva, ejecuta 06 después de los SQL anteriores.
