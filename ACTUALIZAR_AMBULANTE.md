# Ambulante sin límite de inventario
1. Detén el programa con Ctrl+C y reemplaza la carpeta `src` del proyecto. Conserva tu `.env`.
2. En Supabase → SQL Editor → New query, pega todo `supabase/12_ambulante_sin_inventario.sql` y pulsa Run. Requiere haber aplicado el archivo 10 de varias sucursales. No vuelvas a ejecutar los SQL anteriores después del 12.
3. Ejecuta `npm run dev` y entra a Ambulante con tu NIP.

La sucursal cuyo nombre sea Ambulante (sin importar mayúsculas o espacios al principio/final) queda sin control de existencias. También se aplica a nuevas sucursales creadas con ese nombre. Cambiar su nombre después conserva el modo.
No necesitas cargar cantidades iniciales: los productos activos se pueden vender incluso sin registro de inventario. Cada clic agrega una pieza; los botones + y − permiten elegir las piezas vendidas. Se guardan cantidades, totales, tickets, caja y reportes normalmente.
Las ventas nuevas de Ambulante no descuentan ni crean existencias ni movimientos de inventario. Cancelarlas tampoco suma inventario. Las ventas anteriores conservan su devolución de existencias original al cancelarse. Las otras sucursales siguen exigiendo inventario suficiente.
El NIP, las sucursales asignadas, la caja abierta, el cobro correcto y los productos activos siguen siendo obligatorios. No necesitas actualizar funciones Edge. No se modifica tu inventario existente.
