🛍️ Punto de venta · Artesanías Sebastián
Sistema para registrar ventas, administrar productos y consultar cajas de varias sucursales. Está hecho con JavaScript, HTML, CSS, Vite y Supabase, con interfaz en español y moneda en pesos mexicanos (MXN).
Este README corresponde al ZIP adjunto POS_Sebastian_VisualStudioCode (22).zip. El número que Windows añade al archivo es un nombre de descarga, no una versión del programa.
✨ Funciones principales
- Acceso con nombre de usuario y NIP de cuatro dígitos.
- Administradores con acceso a todas las sucursales.
- Cajeros asignados a uno o varios puntos de venta.
- Catálogo compartido, fotografías y búsqueda por nombre o código.
- Carrito, descuento, efectivo, tarjeta y transferencia.
- Inventario por sucursal, entradas, salidas e importación mediante CSV.
- Preparación de envíos de mercancía a sucursales y ticket de revisión.
- Apertura de caja, movimientos de efectivo y corte.
- Historial, reimpresión de tickets y reportes.
- Logo de la empresa y configuración del ticket.
- Botón Salir sin cerrar sesión y botón Seguir vendiendo cuando el usuario tiene una caja abierta.
🚀 Abrir en Visual Studio Code
Requisitos
- Node.js 22.12 o superior, según package.json.
- Visual Studio Code y un navegador.
- Internet para utilizar Supabase.
- La configuración de tu proyecto Supabase en .env.
Instalación
1. Extrae el ZIP.
2. En Visual Studio Code selecciona Archivo → Abrir carpeta.
3. Abre pos-sebastian, la carpeta que contiene package.json.
4. Selecciona Terminal → Nueva terminal.
5. Ejecuta:
npm ci
Después:
npm run dev
Abre la dirección que indique la terminal, normalmente http://localhost:5173. No ejecutes el sistema haciendo doble clic en index.html.
Si PowerShell bloquea npm.ps1: usa npm.cmd ci y npm.cmd run dev, o selecciona una terminal de tipo Command Prompt.
🔗 Conexión a Supabase
El ZIP usa variables de entorno y no incluye tu archivo .env personal.
Copia .env.example y renombra la copia como .env. En PowerShell:
Copy-Item .env.example .env
Completa el archivo:
VITE_SUPABASE_URL=https://TU-PROYECTO.supabase.co
VITE_SUPABASE_PUBLISHABLE_KEY=TU-CLAVE-PUBLICA
La URL y la clave pública deben pertenecer al mismo proyecto donde están tus tablas y usuarios. No coloques una clave service_role, una clave secreta ni la contraseña de la base de datos en estas variables: Vite las incluye en el navegador.
Cuando cambies .env, detén el servidor con Ctrl + C y ejecuta otra vez npm run dev.
Si ya estás usando el sistema
Conserva el proyecto Supabase actual, tu .env, tus usuarios y tus datos. Para actualizar la interfaz no necesitas volver a crear las tablas ni ejecutar todos los SQL.
Instalación de una base nueva
La carpeta supabase contiene el esquema y las actualizaciones. 01_esquema.sql es la instalación inicial; los demás archivos incorporan funciones posteriores. Revisa las guías ACTUALIZAR_*.md y sus requisitos antes de ejecutarlos.
La pantalla actual ya no ofrece crear la primera cuenta. En una instalación nueva debes preparar la cuenta administrativa inicial y el negocio antes de usar el acceso por NIP. El administrador original puede acceder por Recuperación de cuenta con su correo y contraseña para asignarse un NIP.
Las Edge Functions create-staff y staff-pin deben publicarse con sus respectivos supabase/functions/.../index.ts. Sigue las guías de NIP y asignación de sucursales incluidas. staff-pin usa su propia validación y requiere la configuración indicada en esas guías.
No ejecutes 01_esquema.sql sobre tu negocio existente para actualizarlo. Para numeración de tickets, 14_ticket_por_sesion_caja.sql sustituye la solución anterior de 13_numero_ticket.sql: no ejecutes 13 después de 14.

👤 Usuarios y acceso
En la pantalla inicial selecciona Administrador o Cajero con NIP, escribe el nombre de usuario y el NIP de cuatro dígitos.
Usuario	Acceso
Administrador	Todas las sucursales y apartados administrativos
Cajero	Los puntos de venta que el administrador le asignó


En Usuarios, el administrador puede crear personal, editarlo, asignar sucursales y cambiar su NIP. Para los usuarios creados por el sistema no se pide correo ni UUID: se genera una identidad interna de autenticación.
Si un cajero olvida su NIP, un administrador debe cambiarlo en Usuarios. Recuperación de cuenta permite al administrador original entrar con su correo y contraseña actuales; no es un envío automático de una contraseña nueva.
Al volver a abrir o recargar la página se muestra la pantalla de acceso. La caja abierta permanece guardada en Supabase.
🧾 Cómo realizar una venta
1. Inicia sesión y elige una sucursal desde Inicio.
2. Confirma el NIP al entrar al punto de venta.
3. En Caja y cortes, abre una caja e indica el fondo inicial si no hay una abierta.
4. En Punto de venta, busca el producto y selecciónalo.
5. Ajusta la cantidad con + y −.
6. Elige cliente, descuento y forma de pago.
7. Para efectivo, captura lo recibido y revisa el cambio.
8. Pulsa Cobrar y revisa el ticket.
Cerrar la ventana de impresión no cancela la venta: el cobro ya se guardó. Puedes reimprimir desde Historial de ventas.
Si aparece un cobro pendiente, usa Confirmar / reintentar. El sistema conserva la referencia para evitar duplicarlo. No borres los datos del navegador mientras haya un cobro sin confirmar.
Tarjeta y transferencia son registros de la forma de pago; el programa no realiza un cargo bancario.
📦 Inventario y venta por sucursal
El catálogo de productos es compartido. Las cantidades de inventario y las cajas pertenecen a cada sucursal.
En tu configuración actual, Ambulante y Manzanillo venden sin bloqueo por existencias y sin descontar inventario. En su tabla se muestra Existencia 0 y Mínimo 0. Estos ceros son visuales: no borran las cantidades guardadas ni cambian el mínimo compartido del producto.
Este comportamiento depende de branches.stock_control en Supabase. El ZIP por sí solo no configura una base nueva: tus dos sucursales actuales ya tienen ese modo activado. Las otras sucursales conservan el control de existencias.
Importar desde Excel
Guarda la lista como CSV UTF-8 y revisa los encabezados antes de importarla. Un archivo .xlsx no se importa directamente.
- Productos → Importar CSV: carga o actualiza el catálogo.
- Inventario → Importar inventario: establece cantidades en la sucursal seleccionada.
Ejemplo de catálogo:
sku,name,price,cost,min_stock,category
LL-001,Llavero Manzanillo,30,15,5,Llaveros
IM-001,Imán de madera,25,12,5,Souvenirs
La importación de inventario reemplaza las cantidades de los productos incluidos; no es una suma de mercancía. Usa la exportación como plantilla y consulta ACTUALIZAR_IMPORTACION_INVENTARIO.md antes de cargar archivos grandes.
Envíos para rellenar una sucursal
En Envíos a sucursal, el administrador prepara los productos y cantidades. Guardar el envío suma las piezas al inventario del destino y permite imprimir un ticket para revisar lo recibido, anotar diferencias y firmar.
Este flujo registra mercancía que llega desde fuera; no descuenta de otra sucursal. La existencia se suma al guardar, no después de una confirmación digital de recepción.
💵 Caja, salida y tickets
- Salir sin cerrar sesión: regresa a Inicio y conserva la sesión y la caja. Si hay productos sin cobrar, solicita confirmar que se vacíe el carrito.
- Seguir vendiendo: aparece donde tu usuario tiene una caja abierta; confirma el NIP y vuelve a ese punto de venta.
- Corte de caja: registra el efectivo contado y cierra la caja.
- Cerrar sesión · Fin del día: está disponible cuando corresponde cerrar la sesión; el sistema pide realizar el corte si detecta una caja abierta.
El efectivo esperado se calcula así:
Fondo inicial + ventas en efectivo + entradas de efectivo − retiros
La numeración comienza en 000001 para una nueva serie de sesión o una nueva caja, según la actualización 14. Entrar de nuevo por el formulario principal inicia una nueva serie; Seguir vendiendo conserva la existente. Reimprimir conserva el número original. Los números pueden repetirse entre sesiones y cajas; las ventas tienen un identificador único para distinguirlas.
🖨️ Logo e impresión
El logo de acceso está incluido en:
public/logo-artesanias-sebastian.jpg
Aparece desde la primera visita, sin iniciar sesión. El logo configurado dentro del negocio y usado por los tickets se administra por separado en Configuración.
En Configuración puedes ajustar el ticket y probar su impresión. Para tu Xprinter de 80 mm, selecciona la impresora instalada en Windows, el papel correspondiente y revisa la vista previa. El sistema abre el diálogo de impresión del navegador; no incluye impresión silenciosa automática.
🗂️ Estructura del proyecto
Archivo o carpeta	Para qué sirve
src/main.js	Inicio de sesión, navegación, eventos y acciones
src/modules/inicio.js	Sucursales, indicadores y Seguir vendiendo
src/modules/ventas.js	Productos, carrito y cobro
src/modules/productos.js	Catálogo, fotos y búsqueda
src/modules/inventario.js	Existencias y movimientos
src/modules/envios.js	Preparación y consulta de envíos
src/modules/caja.js	Aperturas, movimientos y cortes
src/modules/historial.js	Consulta y reimpresión de ventas
src/modules/reportes.js	Reportes por fechas
src/modules/usuarios.js	Personal, roles, sucursales y NIP
src/modules/configuracion.js	Datos del negocio, logo y ticket
src/modules/tickets.js	Comprobantes imprimibles
src/modules/clientes.js	Directorio de clientes
src/modules/categorias.js	Categorías de productos
src/modules/sucursales.js	Administración de sucursales
src/services/database.js	Consultas y operaciones en Supabase
src/lib/supabase.js	Cliente y variables de conexión
src/styles/main.css	Colores, tamaños y diseño adaptable
public/	Logo y archivos estáticos
supabase/	SQL y código de Edge Functions
tests/	Pruebas automáticas
.env.example	Plantilla de conexión sin credenciales
ACTUALIZAR_*.md	Guías para cambios específicos


Los archivos de Proveedores, Recepción y Demostración permanecen en el código, pero esas opciones ya no se muestran en el menú o en la pantalla de acceso actual.
🛠️ Comandos útiles
Comando	Uso
npm ci	Instala las versiones del archivo de bloqueo
npm run dev	Inicia el desarrollo local
npm run build	Genera la compilación en dist
npm run preview	Sirve la compilación local para revisarla
npm test	Ejecuta las pruebas del proyecto


Para actualizar tu proyecto, respalda tu carpeta, conserva .env y copia los archivos indicados en la guía del cambio. Si cambian package.json o package-lock.json, ejecuta npm ci otra vez.
🌐 Usarlo en varias computadoras
La versión publicada se utiliza en https://artesanias-sebastian.com, con el reenvío de GoDaddy desactivado y los DNS apuntando al alojamiento del sistema.
Cada computadora necesita navegador e internet. No necesita Visual Studio Code para usar la versión publicada. Cada persona entra con su usuario y NIP, y los datos se guardan en el mismo proyecto Supabase.
Modificar archivos en tu computadora con npm run dev no actualiza automáticamente la página publicada: debes compilar y publicar la nueva versión por el flujo de alojamiento correspondiente.
❓ Problemas frecuentes
Problema	Qué revisar
No conecta a Supabase	URL y clave pública en .env; reinicia Vite
No aparece un cambio	Guarda el archivo y recarga con Ctrl + Shift + R
No aparece el logo de acceso	Copia public/ a la raíz y conserva el nombre del JPG
No permite cobrar	Caja abierta, carrito, pago recibido y permisos de sucursal
Producto bloqueado por stock	Existencias y modo de control de esa sucursal
NIP rechazado	Rol elegido, nombre correcto, cuatro dígitos y asignación
Cuenta sin sucursal	Un administrador debe asignarla en Usuarios
Demasiadas redirecciones	Revisa el reenvío del dominio y sus registros DNS


📌 Alcance
El sistema registra ventas por piezas enteras, una forma de pago por venta y tickets internos. No incluye CFDI, cobro bancario integrado, pagos mixtos, devoluciones parciales ni venta sin internet con sincronización posterior.
Las pruebas automatizadas comprueban reglas de venta, caja y permisos en un entorno local. No sustituyen una prueba real de inicio de sesión, venta, corte e impresión con tus equipos y tu Supabase.
