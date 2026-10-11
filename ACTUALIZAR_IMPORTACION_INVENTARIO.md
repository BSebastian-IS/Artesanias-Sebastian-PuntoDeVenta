# Importar las 50 piezas iniciales

1. Detén el servidor en Visual Studio Code con Ctrl+C.
2. Reemplaza la carpeta src de tu proyecto por la carpeta src de este paquete. Conserva tu .env.
3. En Supabase abre SQL Editor → New query. Copia todo supabase/05_importar_inventario.sql y pulsa Run. No ejecutes de nuevo 01_esquema.sql.
4. En la terminal de tu proyecto ejecuta npm run dev.
5. Entra como administrador, selecciona Ambulante y abre Inventario → Importar inventario.
6. Elige inventario.csv incluido en este paquete (1,629 productos con 50 piezas). Marca la confirmación y pulsa Importar existencias.
7. Al terminar verás 50 en Existencia. Abre Caja para iniciar tu turno y después entra a Punto de venta.

La carga reemplaza la existencia de cada producto incluido con la cantidad del archivo, aunque ya tenga otra cantidad. Por ejemplo, 20 pasa a 50 y 70 pasa a 50. Comprueba códigos, sucursal y cantidades; las demás sucursales y los productos no incluidos se conservan.

La misma combinación de productos y cantidades se aplica una sola vez por sucursal, incluso si reordenas las filas. Reimportarla después de una venta no restablece las 50 piezas. Si se interrumpe la conexión, puedes volver a cargar el mismo archivo para confirmar el resultado.

Los movimientos quedan registrados como Ajuste por importación CSV. No necesitas volver a publicar create-staff. El CSV de inventario se carga en Inventario; el de precios y costos se carga en Productos.
