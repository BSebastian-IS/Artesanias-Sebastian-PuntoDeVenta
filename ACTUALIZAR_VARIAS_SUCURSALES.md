# Cajeros con varios puntos de venta y edición de administradores

## Actualizar tu instalación
1. Detén npm run dev con Ctrl+C. Reemplaza src completa por la del ZIP y conserva .env.
2. Si ya ejecutaste SQL 09 para el logo y entrada con NIP, no lo repitas. En Supabase → SQL Editor ejecuta TODO supabase/10_cajeros_varias_sucursales.sql. Si no tienes SQL 09, ejecuta primero 09_logo_y_acceso_punto_venta.sql y después 10. No ejecutes 01 de nuevo.
3. Actualiza y publica ambas Edge Functions con sus archivos completos:
   - create-staff: supabase/functions/create-staff/index.ts.
   - staff-pin: supabase/functions/staff-pin/index.ts. Deja Verify JWT desactivado.
4. Ejecuta npm run dev.

## Asignar uno o varios puntos de venta
Usuarios → Agregar usuario o Editar → selecciona Cajero. En Puntos de venta asignados marca una o varias casillas. Guarda. El cajero ve solamente esas sucursales en Inicio y usa el mismo NIP para entrar a cualquiera de ellas. Al abrir una sucursal solo aparecen los cajeros asignados allí.

Las asignaciones anteriores se conservan como una sola sucursal. Las nuevas restricciones se aplican en Supabase, incluyendo lectura de inventario, ventas, caja y sus operaciones. No basta con cambiar una selección en el navegador para entrar en una sucursal no asignada. La sucursal elegida se conserva al entrar aunque sea distinta de la primera de la lista.

Cierra las cajas abiertas de esa persona antes de modificar sus sucursales o su rol. Cuando cambian esas asignaciones, el NIP se revoca: después de guardar pulsa Asignar / cambiar NIP y asigna uno de 4 dígitos. Si solo cambias el nombre, su NIP permanece y debe entrar con el nombre nuevo.

## Editar administradores
En Usuarios todos tienen botón Editar, incluida Tu cuenta. Puedes cambiar el nombre y el rol de otros administradores. Si conviertes otro administrador en cajero, asígnale al menos una sucursal, guarda y asígnale NIP.

En tu propia cuenta puedes cambiar el nombre; el rol se conserva como Administrador para no dejarte sin acceso. Para cambiar o quitar el NIP usa los botones correspondientes en la fila. Los administradores siguen teniendo acceso a todas las sucursales.

Los nombres deben ser diferentes dentro del negocio. Estos cambios no requieren correo ni UUID. Los NIP continúan guardándose como hash y no se muestran en texto.

## Apartados retirados
Se retiraron Proveedores y Recepción de mercancía del menú, tal como aparecen en tu captura. Las entradas/salidas de Inventario y Envíos a sucursal continúan disponibles.

## Validación
Pruebas SQL con asignaciones anteriores y migración repetida: un cajero puede ver y vender en dos sucursales, la tercera y sucursales de otro negocio se bloquean. Se comprobó caja, edición de nombre propio, rechazo de cambiar el propio rol, revocación del NIP y permisos administrativos. En pantalla se probaron las casillas, edición de administradores, cambio entre las dos sucursales con el mismo NIP y el menú actualizado.

Las comprobaciones son locales. Para activar los cambios en tu proyecto ejecuta SQL 10 y publica las dos funciones.
