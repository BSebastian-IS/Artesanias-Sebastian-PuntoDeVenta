# Cajeros con una sola sucursal

Esta versión permite asignar una sucursal a cada cajero. El administrador conserva acceso a todas. Al entrar, el cajero ve automáticamente su sucursal, sin selector para cambiarla.

## 1. Actualiza los archivos

1. Detén la terminal anterior con Ctrl + C.
2. Extrae el ZIP actualizado en una carpeta nueva y abre `pos-sebastian` en Visual Studio Code.
3. Copia tu `.env` anterior a esta carpeta, junto a `package.json`. Conserva la URL y la clave pública de tu mismo proyecto Supabase.
4. En Terminal → Nueva terminal ejecuta `npm ci`.

## 2. Actualiza tu base de datos

Como ya tienes las tablas, NO ejecutes de nuevo `01_esquema.sql`.

1. Abre `supabase/03_cajeros_por_sucursal.sql` en Visual Studio Code y copia TODO el contenido.
2. En tu proyecto Supabase → SQL Editor → New query, pega el contenido y pulsa Run.
3. Debe aparecer `Success. No rows returned`.

El script necesita que ya estén instalados `01_esquema.sql` y `02_usuarios_por_correo.sql`. No elimina ventas, usuarios ni tablas; puede ejecutarse nuevamente. No ejecutes el 02 después del 03.

**Los cajeros existentes no reciben una sucursal automáticamente. Hasta que un administrador les asigne una, no podrán consultar ni operar sucursales.** Su correo y contraseña se conservan.

Para una instalación completamente nueva, ejecuta 01, después 02 y finalmente 03.

## 3. Actualiza la función create-staff

Debes actualizarla aunque ya la hayas publicado: ahora recibe y valida la sucursal.

1. En Supabase → Edge Functions → create-staff → Code, abre `index.ts`.
2. En Visual Studio Code abre el nuevo `supabase/functions/create-staff/index.ts`.
3. Copia todo y reemplaza el contenido del editor de Supabase.
4. Guarda y pulsa Deploy updates (o Deploy).
5. Conserva desactivada la opción Verify JWT / Enforce JWT verification. La función verifica la sesión con Auth y exige administrador del negocio dentro del código.

No agregues claves secretas al `.env`: esta función usa las variables del servidor de Supabase.

## 4. Asigna sucursales

1. Ejecuta `npm run dev` y abre el enlace Local que muestre la terminal.
2. Inicia sesión con tu cuenta de administrador.
3. En Usuarios → Editar, selecciona el rol Cajero y su Sucursal asignada. Guarda.
4. Repite para cada cajero.
5. Al agregar personas nuevas, también selecciona su sucursal. Para Administrador, el selector desaparece porque tiene acceso a todas.

Si la persona tiene una caja abierta, ciérrala en la sucursal correspondiente antes de cambiar su rol o sucursal. El sistema lo exige para evitar dejar una caja inaccesible.

## 5. Comprueba el acceso

Abre una ventana de incógnito e inicia sesión con un cajero. Debe mostrar su sucursal y solo Inicio, Punto de venta, Historial de ventas y Caja y cortes. No debe aparecer el selector para cambiar sucursal ni Usuarios.

Cada cajero ve inventario, ventas, detalles de ventas, cajas y movimientos de su sucursal. El catálogo de productos, las categorías y los clientes siguen siendo compartidos por el negocio. Las cajas conservan la restricción de propietario: un cajero no puede manejar una caja abierta por otra persona; el administrador sí.

La autorización se aplica en Supabase mediante RLS y en las operaciones de escritura del RPC, además de la interfaz. Las claves administrativas del servidor y el SQL Editor siguen teniendo acceso privilegiado, como corresponde al administrador del proyecto.

## Mensajes comunes

- **Sucursal pendiente de asignar**: el administrador debe editar tu acceso y seleccionar una sucursal.
- **Cierra las cajas abiertas…**: cierra primero la caja de esa persona antes de reasignarla.
- **No se pudo crear la cuenta**: verifica que actualizaste el SQL 03 y el código de create-staff.
- **La cuenta fue creada, pero falta asociarla…**: usa Usuarios → Agregar usuario → Usar cuenta existente, con su correo y la sucursal. No cambies su contraseña.

## Validación de esta entrega

Pruebas locales de PostgreSQL con PGlite: aislamiento entre sucursales y negocios, restricciones de lectura directa por API, acceso por tablas relacionadas, bloqueo de escrituras directas, sucursal ajena, cajero sin asignación, caja ajena, reasignación con caja abierta y venta permitida en su sucursal. La función create-staff se prueba con Auth simulado. También se revisó la interfaz en Chromium y la compilación de producción.

Estas pruebas no despliegan cambios en tu proyecto remoto: debes aplicar los pasos 2 y 3 en tu Supabase.
