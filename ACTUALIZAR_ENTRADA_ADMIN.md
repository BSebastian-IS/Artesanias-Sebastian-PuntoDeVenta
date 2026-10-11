# Administradores entran con su propio NIP

## Actualizar
1. Detén npm run dev con Ctrl+C. Reemplaza src completa y conserva .env.
2. En Supabase → Edge Functions → staff-pin → Code, reemplaza el código por TODO supabase/functions/staff-pin/index.ts y publica con Deploy function. Deja Verify JWT desactivado.
3. Ejecuta npm run dev.

Esta entrega no añade un SQL nuevo ni cambia create-staff. Debes conservar SQL 09 y 10 ya instalados para logo, NIP y varios puntos de venta.

## Cómo entrar
Entra como administrador y abre Inicio. Pulsa Entrar a la sucursal. La ventana muestra Usuario y NIP de 4 dígitos. Tu cuenta de administrador queda seleccionada: escribe TU NIP y pulsa Abrir punto de venta.

El administrador permanece como administrador. Puede abrir cualquier sucursal de su negocio, incluso si no hay un cajero asignado allí. Conserva Usuarios, Configuración, inventario y demás permisos administrativos. No es necesario cambiarlo a cajero ni asignarle sucursales. Si aún no tiene NIP, asígnalo desde Usuarios → Asignar / cambiar NIP.

Desde una sesión administrativa también puedes elegir un cajero asignado e ingresar el NIP de ese cajero para trabajar con su cuenta. En ese caso se aplican sus permisos de cajero. La ventana identifica el rol junto al nombre.

El cajero solo puede elegir su propia cuenta y abrir los puntos que tenga asignados. El servidor comprueba el rol y el NIP; para administradores también comprueba que la sucursal pertenece al mismo negocio. Un NIP incorrecto mantiene cerrada la entrada.

## Validación
Pruebas locales: administrador entra a dos sucursales sin cajeros usando su propio NIP y conserva su rol; NIP incorrecto bloqueado; cajero entra a sus dos puntos y no a un tercero. Se verificó también el rechazo de una sucursal ajena al negocio en el servidor. Los cambios deben publicarse en tu Supabase para activarlos.
