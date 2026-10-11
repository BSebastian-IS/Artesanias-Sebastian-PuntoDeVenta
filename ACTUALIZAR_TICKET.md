# Personalizar el ticket para Xprinter de 80 mm

## Actualizar
1. Detén npm run dev con Ctrl+C. Reemplaza src completa por la del ZIP y conserva .env.
2. Supabase → SQL Editor: pega TODO supabase/11_personalizar_ticket.sql y pulsa Run. No ejecutes 01 de nuevo. SQL 11 solo añade la configuración de ticket al negocio y una función para guardarla; no cambia ventas ni inventario.
3. Ejecuta npm run dev y recarga el punto de venta. No necesitas actualizar Edge Functions para esta mejora.

## Diseñar el ticket
Entra como administrador → Configuración → Personalizar ticket.

Puedes cambiar:
- Papel: 80 mm (predeterminado para tu Xprinter) o 58 mm.
- Letra: pequeña, normal o grande.
- Mostrar logo, dirección, teléfono, quién atendió, cliente y código del producto.
- Texto debajo del nombre del negocio.
- Mensaje de agradecimiento.
- Información adicional, por ejemplo una política de cambios redactada por ti.

La vista previa cambia mientras editas. Usa Guardar diseño del ticket para conservar el diseño en Supabase. Los cajeros y las sucursales del negocio imprimen el mismo diseño guardado. Restablecer diseño recupera los valores predeterminados con confirmación. Para cambiar el logo usa el apartado Logo de la empresa que ya está en Configuración.

La vista previa usa datos de ejemplo. Imprimir prueba utiliza lo que ves en el formulario, incluso antes de guardarlo, y añade PRUEBA · SIN VALOR DE VENTA. No crea ventas ni modifica existencias. No necesitas vender otra vez para probar la impresora.

## Imprimir con tu Xprinter
La impresora debe estar instalada en Windows con su controlador. Pulsa Imprimir prueba y elige la Xprinter en la ventana de impresión. En el controlador y en esa ventana usa papel de 80 mm, escala 100 % y desactiva encabezados y pies de página del navegador. El diseño reserva 4 mm de margen por lado (72 mm de contenido para el rollo de 80 mm). El tamaño real del papel se selecciona en la impresora; no se puede elegir una impresora de Windows automáticamente desde la página.

Cuando vendas, el ticket usa los importes reales registrados. También puedes reimprimir ventas existentes desde Historial. Total, cantidades, pago, recibido y cambio no se editan desde este apartado. Las ventas canceladas conservan su aviso. El ticket continúa indicando que es un comprobante interno y no una factura fiscal.

Las opciones de cabecera, logo y mensajes personalizan tickets de venta. El ancho de papel y tamaño base de letra también se aplican al imprimir cortes y envíos; estos conservan sus datos y espacios de revisión.

## Comprobaciones
43 pruebas locales correctas: permisos administrativos y por negocio, validación de opciones y longitudes, textos escapados, importes y estado de ventas. Compilación y prueba de pantalla correctas: edición/vista previa, persistencia al recargar, impresión de prueba con anchos de 80 y 58 mm y sin cambios en ventas/inventario. La impresión física debe comprobarse con tu Xprinter ya instalada.
