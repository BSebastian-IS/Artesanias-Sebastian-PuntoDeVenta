# 🛍️ Punto de venta Sebastián

Proyecto para **Visual Studio Code**, hecho con JavaScript, HTML, CSS, Vite y Supabase. Interfaz en español, moneda MXN y archivos separados por módulo. No necesitas Next.js ni un servidor propio para la base de datos.

## 1. Abrir y ejecutar en Windows

1. Instala Node.js **22.12 o superior** (se recomienda la versión LTS), desde https://nodejs.org/.
2. Descomprime el ZIP. En Visual Studio Code: **Archivo → Abrir carpeta → pos-sebastian**. Abre la carpeta que contiene `package.json`.
3. Abre **Terminal → Nueva terminal** y ejecuta:

```powershell
npm ci
npm run dev
```

4. Abre la dirección que aparece en la terminal, normalmente **http://localhost:5173**.
5. Presiona **Probar demostración**. Incluye productos de ejemplo, tres sucursales y existencias. Los precios son ejemplos, no un catálogo real.

Si PowerShell bloquea `npm.ps1`, utiliza `npm.cmd ci` y `npm.cmd run dev` o selecciona una terminal de tipo Command Prompt en VS Code. No abras `index.html` con doble clic: usa `npm run dev`.

## 2. Conectar a Supabase paso a paso

**El proyecto está preparado para conectarse; no contiene credenciales ni está conectado a tu cuenta.** Usa un proyecto NUEVO de Supabase. El SQL crea tablas propias y no se debe ejecutar encima de un POS existente sin adaptar los nombres.

1. Crea tu proyecto en https://supabase.com/dashboard.
2. En **SQL Editor → New query**, pega todo el archivo `supabase/01_esquema.sql` y pulsa **Run**. Ejecuta este archivo una sola vez. Después ejecuta `supabase/02_usuarios_por_correo.sql`, luego `supabase/03_cajeros_por_sucursal.sql`, `supabase/04_fotos_productos.sql` y `supabase/05_importar_inventario.sql` y `supabase/06_envios_sucursal.sql` y `supabase/07_acceso_nip.sql`. Publica la función siguiendo `ACTUALIZAR_SUCURSALES.md`. Si algo falla, la transacción evita una instalación a medias.
3. Desde el diálogo **Connect** o la configuración de API de tu proyecto, copia la **Project URL** y la **Publishable key**. El cliente también acepta una clave `anon` heredada si tu proyecto aún la utiliza.
4. Copia `.env.example` y cambia el nombre de la copia a `.env`. En PowerShell puedes usar:

```powershell
Copy-Item .env.example .env
```

5. Completa los valores dentro del archivo:

```dotenv
VITE_SUPABASE_URL=https://TU-PROYECTO.supabase.co
VITE_SUPABASE_PUBLISHABLE_KEY=TU-CLAVE-PUBLICA
```

**Nunca coloques aquí `service_role`, una secret key o la contraseña de Postgres.** Las variables `VITE_` son públicas y se envían al navegador. La autorización depende de Supabase Auth y las reglas de la base.

6. Detén el servidor con **Ctrl+C** y ejecuta otra vez `npm run dev`.
7. Crea la primera cuenta con **Crear cuenta de administrador**. Si se requiere confirmación por correo, confirma tu correo y luego inicia sesión. También puedes crearla manualmente desde Supabase → Authentication → Users, usando una contraseña y marcando la confirmación del correo.
8. Si usas registro con correo, configura **Authentication → URL Configuration → Site URL** con `http://localhost:5173` (y la dirección real al publicar).
9. Al entrar, pulsa **Crear mi negocio**. Esto crea tu acceso administrador, categorías iniciales y sucursales Manzanillo, Barra de Navidad y Tecomán.
10. Agrega tus productos desde **Productos**. Agrega existencias desde **Inventario → Entrada / salida**. El negocio real inicia sin productos ni ventas de demostración.

La conexión depende de que la Data API y Authentication estén activadas, y de que `public` sea un esquema expuesto. `pos_private` debe permanecer sin exponer en la Data API. El SQL configura permisos, RLS y el RPC `pos_action`.

## 3. Primera venta

1. Selecciona una sucursal en la parte superior.
2. Abre **Caja y cortes → Abrir caja** e indica el fondo inicial.
3. En **Punto de venta**, busca por nombre o código y haz clic en los productos.
4. Ajusta las piezas con + y −, selecciona cliente, descuento y forma de pago.
5. Si es efectivo, captura lo recibido; el cambio se actualiza automáticamente. Pulsa **Cobrar**.
6. La venta, las partidas y el descuento de inventario se guardan en una sola transacción. Aparece el diálogo de impresión. Puedes cerrar el diálogo de impresión: la venta ya quedó guardada.
7. En **Historial de ventas** puedes reimprimir el ticket. Para un lector USB que funciona como teclado, enfoca el buscador y escanea; después selecciona el producto mostrado.
8. En **Caja y cortes** registra entradas o retiros y al finalizar pulsa **Realizar corte**, captura el efectivo contado e imprime el corte.

Efectivo esperado = fondo inicial + ventas pagadas en efectivo + entradas − retiros. Tarjetas y transferencias se registran sin aumentar el efectivo de caja.

Si una respuesta de cobro se pierde, el navegador conserva un folio pendiente. Usa **Confirmar / reintentar** para consultar o completar la misma venta sin duplicarla. No borres los datos del navegador mientras exista un cobro pendiente. Si falla por un error explícito de base de datos, la transacción se revierte y permite corregir el carrito.

La cancelación está disponible para administradores mientras la caja de la venta siga abierta. Devuelve piezas al inventario y elimina el ingreso de esa venta del corte. La devolución física del dinero o reverso bancario se hace fuera del sistema.

## 4. Apartados y archivos

| Apartado | Archivo principal | Función |
|---|---|---|
| Inicio | `src/modules/inicio.js` | Indicadores de hoy y stock bajo |
| Punto de venta | `src/modules/ventas.js` | Cuadrícula con precios, carrito y cobro |
| Historial | `src/modules/historial.js` | Ventas, cancelación y exportación |
| Productos | `src/modules/productos.js` | Alta, edición, archivo y CSV |
| Envíos a sucursal | `src/modules/envios.js` | Reposición con varios productos, ticket de revisión y suma inmediata al destino |
| Inventario | `src/modules/inventario.js` | Existencias por sucursal y movimientos |
| Clientes | `src/modules/clientes.js` | Alta y edición del directorio |
| Proveedores | `src/modules/proveedores.js` | Alta y edición del directorio |
| Recepción de mercancía | `src/modules/compras.js` | Entradas por proveedor y referencia |
| Categorías | `src/modules/categorias.js` | Clasificación del catálogo |
| Caja | `src/modules/caja.js` | Apertura, entradas, retiros y cortes |
| Reportes | `src/modules/reportes.js` | Fechas, pagos, productos y margen estimado |
| Sucursales | `src/modules/sucursales.js` | Alta y edición de tiendas |
| Usuarios | `src/modules/usuarios.js` | Miembros, roles y sucursal asignada |
| Configuración | `src/modules/configuracion.js` | Datos del negocio e impuesto |
| Tickets | `src/modules/tickets.js` | Impresión de ventas y cortes |
| Conexión | `src/lib/supabase.js` | Cliente Supabase y variables de entorno |
| Base de datos | `src/services/database.js` | Consultas paginadas y llamadas RPC |
| Demostración | `src/services/demo.js` | Datos locales de prueba |
| Formularios | `src/lib/forms.js` | Formularios compartidos |
| Utilidades | `src/lib/utils.js` | Moneda, fechas, cálculos y CSV |
| Aplicación | `src/main.js` | Navegación, acciones y estado |
| Diseño | `src/styles/main.css` | Interfaz adaptable e impresión |
| SQL | `supabase/01_esquema.sql` | Tablas, índices, RLS y operaciones |

## 5. Usuarios y permisos

Para agregar personas, usa **Usuarios → Agregar usuario** y captura nombre, correo, contraseña inicial, rol y sucursal del cajero. No se pide UUID. Instala las actualizaciones 02 y 03 en ese orden y publica `create-staff` siguiendo **ACTUALIZAR_SUCURSALES.md**. La actualización no borra datos existentes.

Si la cuenta ya existe, selecciona **Usar cuenta existente**. Su contraseña no cambia. Editar permite cambiar nombre y rol; no correo ni contraseña. Quitar acceso no borra la cuenta de Authentication.

- Administrador: administra el negocio, su personal y cualquier caja.
- Cajero: vende y maneja la caja que abrió. No administra catálogo, inventario manual ni miembros.
- Cada cajero tiene una sola sucursal. Supabase limita inventario, cajas, ventas y movimientos a esa sucursal; sin asignación queda bloqueado. El catálogo, categorías y clientes son compartidos. Los costos del catálogo siguen siendo legibles por API; ocultarlos requeriría vistas/API específicas. Administradores acceden a todas las sucursales.
- No puedes cambiar tu propio rol ni eliminar tu propio acceso.
- La demo guarda registros locales sin contraseña; no crea cuentas reales.

## 6. Importación de más de 1,000 productos

En **Productos**, exporta el catálogo y úsalo de plantilla. CSV UTF-8, separado por comas:

```csv
sku,name,price,cost,min_stock,category
LL-001,Llavero Manzanillo,30,15,5,Llaveros
SO-001,Imán de madera,25,12,5,Souvenirs
```

**Importar CSV** valida el archivo y guarda cada fila. Un código existente se actualiza; las categorías nuevas se crean. Se puede importar más de 1,000 filas; el proceso tarda según la conexión. No cierres la pestaña durante la importación. Si una fila falla, se indica cuántas quedaron guardadas; puedes corregir y reimportar. La importación completa no es una transacción única. El archivo importa catálogo, no existencias; usa Inventario → Importar inventario para establecer las existencias por sucursal (consulta `ACTUALIZAR_IMPORTACION_INVENTARIO.md`). Las consultas recuperan páginas de 1,000 registros.

## 7. Alcance y validación

Esta primera versión funcional trabaja con piezas enteras, una forma de pago por venta y operación en línea con Supabase. La demo usa almacenamiento local y no sincroniza con Supabase. No procesa pagos bancarios: tarjeta y transferencia son registros manuales.

Incluye recepción simple de mercancía, sin órdenes de compra, pagos a proveedores o cuentas por pagar. No incluye CFDI, crédito/apartados, pagos mixtos, devoluciones parciales, báscula, impresión silenciosa ni venta desconectada sincronizable. El impuesto configurado se añade al precio después del descuento; por defecto es 0%. Los tickets son comprobantes internos.

El catálogo es compartido entre sucursales; el stock y las cajas son independientes. El margen del reporte se estima con el costo guardado al vender; no sustituye un estado financiero. Las fechas de reportes usan hora de Ciudad de México.

Comprobaciones:

```powershell
npm test
npm run build
```

Las pruebas ejecutan el SQL en Postgres embebido (PGlite) con un esquema de Auth simulado y verifican venta atómica, reintentos, inventario insuficiente, cancelaciones, caja, aislamiento entre negocios y permisos. También se verificó la interfaz en un navegador: demostración, apertura de caja, cobro, ticket, actualización de stock, alta de producto, navegación por los apartados, corte y vista móvil. No se ha probado contra tu proyecto remoto porque no se han proporcionado sus credenciales. Antes de usarlo con clientes reales, conecta tu proyecto y realiza una venta y un corte de prueba con tus datos.

Para crear la compilación: `npm run build`. `npm run preview` permite revisarla localmente. Para acceso externo se requiere publicar `dist/` en un hosting con HTTPS; el proyecto no está publicado. Después de configurar `.env`, vuelve a compilar para incluir la conexión.

## Documentación oficial

- Cliente JavaScript: https://supabase.com/docs/reference/javascript/initializing
- RPC: https://supabase.com/docs/reference/javascript/rpc
- Acceso por filas: https://supabase.com/docs/guides/database/postgres/row-level-security
- Autenticación con contraseña: https://supabase.com/docs/guides/auth/passwords
- Vite: https://vite.dev/guide/


## Fotos de productos

En Productos → Foto puedes subir, reemplazar o quitar imágenes JPG/PNG/WebP de hasta 5 MB. Se muestran en el catálogo y en la cuadrícula del POS. Sigue `ACTUALIZAR_FOTOS.md` y ejecuta `supabase/04_fotos_productos.sql` después de 01, 02 y 03. Las fotos se guardan en Supabase Storage privado, con permisos por negocio. Solo administradores pueden cambiarlas; cajeros autorizados pueden verlas.

## Inventario por CSV

El botón Inventario → Importar inventario valida códigos, cantidades y la sucursal seleccionada. Reemplaza las existencias actuales de los productos incluidos con las cantidades del archivo. La carga es atómica y está limitada a administradores. El mismo archivo se aplica una sola vez para evitar reponer ventas al reintentar. Incluye inventario.csv con 1,629 productos y 50 piezas para Ambulante. Sigue ACTUALIZAR_IMPORTACION_INVENTARIO.md.

## Envíos de mercancía a sucursal

El administrador prepara varios productos desde Envíos a sucursal. Guardar suma las piezas al inventario del destino seleccionado y genera un ticket para revisar cantidades recibidas, diferencias y firma. No descuenta de otra tienda. El cajero puede consultar y reimprimir los envíos de su sucursal. Los reintentos de un mismo envío no duplican existencias. Instala supabase/06_envios_sucursal.sql siguiendo ACTUALIZAR_ENVIOS.md.

## Acceso con NIP y tarjetas de sucursal

Inicio muestra todas las sucursales para administradores y solo la asignada para cajeros. El administrador entra con correo y contraseña. Usuarios permite asignar, cambiar y quitar NIP de 6 dígitos a cajeros. El cajero entra con correo y NIP, y la sucursal se resuelve desde su membresía en Supabase. Instala 07_acceso_nip.sql y publica staff-pin siguiendo ACTUALIZAR_NIP.md. Las credenciales se derivan con PBKDF2 y se guardan en esquema privado; cinco intentos por quince minutos, sesiones reales de Supabase Auth y permisos RLS existentes.

## Acceso actualizado
Consulta ACTUALIZAR_NIP.md: SQL 08 y las dos funciones staff-pin/create-staff permiten crear personal sin correo y entrar con nombre y NIP de 4 dígitos, incluidos administradores. Las instrucciones anteriores de correo y NIP de 6 dígitos quedan sustituidas por esta guía.

## Logo y entrada al punto de venta
Consulta ACTUALIZAR_LOGO_Y_ENTRADA.md: SQL 09 y nueva staff-pin permiten subir el logo desde Configuración y confirmar el NIP del cajero al abrir una sucursal desde Inicio.

## Varios puntos de venta por cajero
Consulta ACTUALIZAR_VARIAS_SUCURSALES.md: SQL 10 y las nuevas funciones permiten marcar varias sucursales, editar administradores y conservar el mismo NIP en los puntos asignados. Proveedores y Recepción de mercancía se retiraron del menú. Esta guía sustituye las instrucciones anteriores de asignación a una sola sucursal.

## Entrada como administrador
Consulta ACTUALIZAR_ENTRADA_ADMIN.md: los administradores pueden abrir cualquier punto de venta de su negocio con su propio NIP, conservando el rol administrador y sin necesitar un cajero. Actualiza src y staff-pin.

## Personalizar ticket
Consulta ACTUALIZAR_TICKET.md. En Configuración puedes diseñar el ticket con vista previa, papel de 80/58 mm, tamaño de letra, logo, datos opcionales y mensajes. SQL 11 guarda el diseño por negocio; no requiere actualizar funciones.

## Ambulante sin límite de inventario
Consulta `ACTUALIZAR_AMBULANTE.md` y aplica `supabase/12_ambulante_sin_inventario.sql` después del SQL 10. Ambulante puede vender productos activos sin existencias iniciales; sus ventas nuevas no modifican inventario. Las cantidades vendidas se guardan para tickets y reportes. Las otras sucursales mantienen su control de stock.

## Numeración por sesión y caja
La actualización vigente es `supabase/14_ticket_por_sesion_caja.sql`; consulta `ACTUALIZAR_NUMERO_TICKET.md`. Los tickets empiezan en 000001 al iniciar sesión de nuevo o al abrir caja después de un corte. No ejecutes SQL 13 después de SQL 14.
