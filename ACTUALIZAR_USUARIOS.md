> Esta versión incluye sucursales por cajero. Sigue primero **ACTUALIZAR_SUCURSALES.md**, que contiene el orden completo de instalación.

# Agregar personas sin UUID

Ahora se pide nombre, correo, contraseña inicial y rol. La persona entra con su correo y contraseña.

## 1. Reemplazar el proyecto

Detén el servidor con Ctrl+C. Descomprime el ZIP en una carpeta nueva, copia tu `.env` de la carpeta anterior (junto a `package.json`) y abre la carpeta nueva en Visual Studio Code. Ejecuta `npm ci`. Tus datos de Supabase se conservan.

## 2. Actualizar las funciones SQL

En Supabase → SQL Editor, pega TODO `supabase/02_usuarios_por_correo.sql` y pulsa Run. No borra datos y se puede volver a ejecutar. **No ejecutes de nuevo 01_esquema.sql si tus tablas ya existen.**

## 3. Publicar create-staff una sola vez

1. En Supabase abre **Edge Functions**.
2. Pulsa **Deploy a new function / Create function**, mediante el editor del Dashboard.
3. Nombra la función exactamente **create-staff**.
4. Copia TODO `supabase/functions/create-staff/index.ts` en el archivo `index.ts` del editor de Supabase. No hace falta copiar handler.js: ya está incluido.
5. Pulsa **Deploy**.
6. En los detalles/configuración de la función, desactiva **Verify JWT / Enforce JWT verification** si está activado. El código verifica el JWT con `auth.getUser` y comprueba que el usuario sea administrador del negocio antes de crear cuentas. Esto evita problemas con la comprobación antigua de JWT y claves de firma nuevas.

La función usa variables automáticas de Supabase: SUPABASE_URL, SUPABASE_ANON_KEY y SUPABASE_SERVICE_ROLE_KEY. Si tu entorno no las proporciona, configúralas solo como secretos de Edge Functions. **No pongas service_role o claves secretas en el .env del POS ni en código del navegador.**

Si ya tienes Supabase CLI configurado, puedes ejecutar desde esta carpeta: `supabase functions deploy create-staff --project-ref TU_PROJECT_ID --no-verify-jwt`. La configuración está en supabase/config.toml.

## 4. Usar el formulario

Ejecuta `npm run dev`. Entra con tu administrador, sin usar la demo. En **Usuarios → Agregar usuario**, elige **Crear cuenta nueva** y captura nombre, correo, contraseña inicial de 8 a 128 caracteres y rol. Pulsa Guardar. La cuenta se crea confirmada y se asocia al negocio. No se envía invitación automática por correo.

En otra ventana privada, abre el POS e inicia sesión con esa nueva cuenta. Un cajero puede abrir su caja y vender.

Si el correo ya está registrado, elige **Usar cuenta existente** y captura nombre, correo y rol. La contraseña actual se conserva. Esta opción funciona con el SQL del paso 2; no requiere crear la cuenta otra vez.

Editar acceso cambia nombre y rol, sin modificar correo ni contraseña. Quitar acceso elimina la pertenencia al negocio; no borra la cuenta.

## Errores y recuperación

- Función no publicada: revisa nombre create-staff, Deploy y configuración de JWT.
- Falta add_member_by_email: ejecuta el SQL del paso 2.
- Correo existente: selecciona Usar cuenta existente.
- Cuenta creada pero falta asociarla: usa Usar cuenta existente con ese mismo correo.
- Solo administradores: entra con un administrador del negocio.
- Contraseña rechazada: cumple también la política configurada en Authentication de tu proyecto.

Authentication y el alta del miembro son dos operaciones. Si la segunda falla o se pierde la respuesta, la cuenta puede quedar creada sin acceso; la opción existente completa el alta sin duplicar. No borres la cuenta para reintentar.

No se ha publicado la función en tu proyecto desde esta conversación. Las pruebas locales verifican el SQL y la autorización del handler; realiza una prueba remota después de completar los pasos 2 y 3.
