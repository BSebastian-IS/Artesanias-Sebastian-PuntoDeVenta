# NIP al abrir un punto de venta y logo de empresa

## Instalar sobre tu sistema actual
1. Detén npm run dev con Ctrl+C.
2. Reemplaza la carpeta src completa con la del ZIP. Conserva tu archivo .env y no borres tus datos de Supabase.
3. En Supabase → SQL Editor, copia TODO supabase/09_logo_y_acceso_punto_venta.sql y ejecuta Run. Requiere 03_cajeros_por_sucursal.sql. Incluye la actualización de las funciones de NIP: no necesitas repetir 07 u 08. Crea el bucket privado business-logos y sus permisos automáticamente. No ejecutes 01 de nuevo.
4. En Supabase → Edge Functions → staff-pin → Code, reemplaza el código por TODO supabase/functions/staff-pin/index.ts y pulsa Deploy function. Deja Verify JWT desactivado en staff-pin. Esta función comprueba el NIP, rol, negocio y sucursal antes de emitir una sesión real.
5. Si todavía no actualizaste create-staff para crear usuarios sin correo, publícala también con supabase/functions/create-staff/index.ts, tal como explica ACTUALIZAR_NIP.md. Si ya la actualizaste con la versión anterior, no cambió en esta entrega.
6. Ejecuta npm run dev.

## Abrir una sucursal con el cajero
En Inicio pulsa Entrar a la sucursal. Aparecerá una ventana con la sucursal, los usuarios autorizados y el NIP de 4 dígitos. Selecciona tu cuenta e ingresa su NIP. No pide correo. Los administradores pueden usar su propio NIP, incluso sin cajeros asignados; consulta ACTUALIZAR_ENTRADA_ADMIN.md.

El administrador puede preparar la entrada de cualquier sucursal, pero al abrir el punto de venta con un NIP de cajero la sesión pasa a ese cajero. El menú muestra su nombre y las ventas se registran con su cuenta. Ya no tiene permisos administrativos ni acceso a otras sucursales. Para administrar de nuevo, cierra sesión y entra como administrador.

La entrada desde el menú Punto de venta o Comenzar una venta también pide el NIP cuando aún no se confirmó la entrada en esa sesión. Volver a pulsar Entrar en Inicio vuelve a pedirlo. No se almacena el NIP escrito. Si hay un cobro pendiente, primero se debe confirmar. Cambiar de cajero vacía el carrito con confirmación.

## Subir tu logo
Como administrador: Configuración → Logo de la empresa → Seleccionar logo. Elige PNG, JPG o WebP de hasta 5 MB, revisa la vista previa y pulsa Guardar logo. Un PNG con fondo transparente conserva su transparencia. Se guarda una versión WebP proporcionada de hasta 512 píxeles; no se estira ni recorta. El logo aparece en el menú lateral y en Inicio para todo el personal autorizado del negocio.

Para reemplazarlo, selecciona otro archivo y guarda. Para retirarlo pulsa Quitar logo. El cajero puede verlo, pero no editarlo. El almacenamiento es privado y usa URLs firmadas; si lo dejas abierto más de una hora y deja de verse, pulsa Actualizar.

## Comprobaciones
Pruebas locales: SQL ejecutado dos veces, RLS de logo por negocio y rol, rechazo de subida/edición de cajeros y usuarios ajenos, PIN/rol/sucursal verificados, 39 pruebas automáticas correctas y compilación correcta. En pantalla se probó subir el logo, recargar, filtrar cajeros por sucursal, bloquear NIP incorrecto y abrir con el correcto como cajero sin privilegios administrativos.

No se publicó nada en tu proyecto automáticamente. Después de ejecutar SQL 09 y publicar staff-pin, prueba lo mismo con una cuenta de tu Supabase.
