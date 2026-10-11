# Fotos de productos

## Instala la actualización

1. Detén el POS con Ctrl + C y extrae el ZIP nuevo en una carpeta nueva.
2. Copia el `.env` del proyecto que ya utilizas a la nueva carpeta `pos-sebastian`, junto a `package.json`.
3. Abre esa carpeta en Visual Studio Code y ejecuta `npm ci`.
4. Copia TODO el contenido de `supabase/04_fotos_productos.sql`.
5. En tu proyecto Supabase → SQL Editor → New query, pega el contenido y pulsa Run.
6. Debe aparecer `Success. No rows returned`.
7. Ejecuta `npm run dev` y abre el enlace Local.

No ejecutes otra vez los scripts 01, 02 o 03 sobre el proyecto que ya está configurado. La actualización 04 agrega una columna y configura el bucket y sus permisos. No borra tus productos, ventas, inventario ni usuarios. No necesitas modificar create-staff ni tu clave pública.

Para instalaciones nuevas, ejecuta 01 → 02 → 03 → 04.

## Agrega una foto

1. Inicia sesión como administrador.
2. Entra a Productos.
3. Busca tu producto y pulsa Foto. También puedes entrar en Editar → Cambiar foto.
4. Pulsa Seleccionar foto y elige una imagen JPG, PNG o WebP de hasta 5 MB.
5. Revisa la vista previa y pulsa Guardar.
6. Ve a Punto de venta: aparecerá la foto en la tarjeta del producto.

Para un producto nuevo, primero guarda sus datos; después pulsa Foto. Si estás editando datos y pulsas Cambiar foto, guarda primero tus cambios de nombre/precio: el formulario de foto es una operación separada.

Para reemplazar la imagen, selecciona otra y guarda. Para quitarla, marca Quitar foto actual sin seleccionar un archivo nuevo y guarda.

## Cómo se guardan

Las fotos se convierten a WebP y se reducen a un máximo de 1,280 píxeles de lado para ahorrar espacio. Se guardan en el bucket privado `product-photos`, en carpetas de negocio/producto. Las imágenes se comparten entre todas las sucursales porque el catálogo es compartido. Las existencias continúan siendo independientes por sucursal.

Los administradores del negocio suben y cambian fotos; los cajeros con sucursal asignada solo ven las fotos actuales de su catálogo. Las URL de lectura son firmadas por una hora: si dejas la pantalla mucho tiempo y una foto desaparece, pulsa Actualizar para renovarlas.

Las fotos anteriores se conservan en Storage al reemplazarlas o quitarlas para evitar perder una imagen durante cambios simultáneos o fallos de red. Quitar foto la desconecta del producto, no elimina el archivo guardado. Puedes revisar archivos antiguos en Supabase → Storage → product-photos; no elimines el archivo que aún está asociado a un producto.

En demostración, las fotos se guardan solo en este navegador. El almacenamiento local es limitado; para un catálogo con muchas imágenes, usa la conexión real a Supabase.

## Errores

- No se pudo subir la foto: confirma que ejecutaste el SQL 04 en el mismo proyecto de tu `.env`, que eres administrador y que el archivo tiene menos de 5 MB.
- No se pudo confirmar la foto: pulsa Actualizar antes de repetir; un fallo de conexión puede ocurrir después de guardar.
- Imagen no legible: prueba exportarla como JPG o PNG. HEIC, SVG y GIF no están admitidos.

## Validación

Se comprobó la autorización del bucket y RPC con PostgreSQL local (PGlite), incluyendo una política de Storage amplia para verificar que no permita acceder a fotos de otro negocio. También se probó que cajeros no suban, borren o cambien fotos, que el catálogo conserve la foto al editar/importar y que un fallo de red ambiguo no borre archivos. Las llamadas de subida y URL firmadas se verificaron con respuestas simuladas; la interfaz se probó en Chromium.

Esta entrega no configura tu proyecto remoto automáticamente: aplica el SQL 04 antes de usar fotos en Supabase.
