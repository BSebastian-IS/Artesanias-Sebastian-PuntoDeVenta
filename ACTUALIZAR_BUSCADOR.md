# Buscador de Productos e Inventario

1. Detén la terminal con Ctrl + C.
2. Extrae el ZIP nuevo.
3. Copia la carpeta `src` del ZIP sobre la carpeta `src` de tu proyecto actual, reemplazando los archivos. Conserva tu `.env`.
4. Ejecuta `npm run dev` y abre el enlace Local.

No necesitas ejecutar SQL ni modificar create-staff para agregar este buscador.

En Productos e Inventario escribe el nombre o código: la lista se filtra mientras escribes. También puedes pulsar Buscar. Limpiar muestra de nuevo todos los productos. La búsqueda no distingue mayúsculas ni acentos y acepta varias palabras. Cada apartado conserva su propia búsqueda.

En Inventario también se filtra el historial de movimientos por los productos encontrados, dentro de la sucursal seleccionada. La exportación CSV conserva su comportamiento: exporta la lista completa, no solo los resultados visibles.
