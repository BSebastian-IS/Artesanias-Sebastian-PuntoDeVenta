# Acceso con nombre y NIP de 4 dígitos

## Actualizar
1. Detén npm run dev con Ctrl+C. Reemplaza la carpeta src completa y conserva tu .env.
2. En Supabase → SQL Editor, pega supabase/08_nip_4_sin_correo.sql y ejecuta Run. Requiere 03_cajeros_por_sucursal.sql; incluye las funciones de 07, por lo que no hace falta ejecutar 07 de nuevo. No ejecutes 01 en un negocio existente.
3. Actualiza y publica AMBAS Edge Functions con sus archivos completos:
   - staff-pin: supabase/functions/staff-pin/index.ts. Desactiva Verify JWT; la función valida la sesión administrativa o el NIP por su cuenta.
   - create-staff: supabase/functions/create-staff/index.ts. Conserva su configuración actual; valida la sesión y permisos administrativos.
4. Ejecuta npm run dev.

## Tu primer NIP de administrador
En la pantalla inicial elige Administrador → Acceso inicial o recuperación con correo. Entra con tu correo y contraseña actuales. Ve a Usuarios y en TU cuenta pulsa Asignar / cambiar NIP. Escribe 4 dígitos, repítelos y guarda. Después puedes entrar con el nombre exacto de usuario mostrado en Usuarios y ese NIP, eligiendo Administrador.

## Crear cajeros y otros administradores
En Usuarios → Agregar usuario escribe nombre de usuario y NIP de 4 dígitos, selecciona rol y, si es cajero, su sucursal. No pide correo ni contraseña. El NIP se asigna al crear la persona. Los nuevos administradores también usan nombre y NIP y pueden entrar a todas las sucursales.

Usa un nombre distinto por persona. El acceso por nombre no distingue mayúsculas ni espacios al principio/final, pero sí acentos y espacios interiores. Si dos nombres coinciden en los negocios del proyecto, el acceso se bloquea; usa un nombre distintivo y reasigna su NIP. No uses solo NIP para identificar personas. Se admite 0123 y otros NIP con ceros iniciales.

Los cajeros eligen Cajero con NIP, escriben nombre y NIP y solo acceden a su sucursal asignada. No escriben correo, UUID ni eligen otra sucursal.

## Personas que ya existían
No vuelvas a crearlas: conserva sus cuentas. Asigna un NIP NUEVO de 4 dígitos desde Usuarios (los anteriores de 6 dejan de servir para entrar con esta versión). Cambiar sucursal o rol revoca el NIP: asigna otro después. Al editar nombre también cambia el nombre utilizado para entrar.

## Acceso y protección
El NIP se guarda como hash PBKDF2 con sal, nunca como texto recuperable. Cada persona tiene máximo 5 intentos en 15 minutos; el administrador puede cambiar el NIP para reiniciar los intentos. Ambos roles reciben sesiones reales de Supabase Auth y mantienen los permisos de sucursal del SQL 03. Las cuentas nuevas usan una identidad interna de Auth que el programa genera automáticamente; no necesitan un correo real. Las claves administrativas siguen solo en las funciones, nunca en .env del navegador.

El correo del administrador original queda como acceso inicial/recuperación. Los usuarios nuevos se recuperan cambiándoles el NIP desde Usuarios. Cambiar/quitar NIP no cierra sesiones ya abiertas; quitar el acceso o cambiar la sucursal sí cambia los permisos aplicados por la base de datos.

Si crear usuario informa que ya se creó pero falta el NIP, no lo crees otra vez: actualiza Usuarios y asígnale el NIP allí.

## Comprobar en tu proyecto
Entra con un administrador usando nombre y NIP: deben verse todas las sucursales. Cierra sesión y entra con un cajero: debe verse solamente su sucursal, sin Usuarios ni selector de otras tiendas. Las pruebas locales validaron SQL, permisos, intentos, creación sin correo y ambos accesos; las funciones deben publicarse en tu Supabase para activarlo.
