# Ticket por sesión y caja (actualización más reciente)
1. Reemplaza src y conserva .env.
2. Supabase → SQL Editor: pega supabase/14_ticket_por_sesion_caja.sql y pulsa Run. Requiere haber aplicado SQL 12; funciona si ya aplicaste 13 o si no lo aplicaste. No ejecutes 13 después del 14.
3. Reinicia npm run dev. Cierra sesión y entra otra vez para iniciar la nueva numeración.
Cada inicio de sesión explícito nuevo empieza Ticket 000001. Hacer corte y abrir una nueva caja también empieza desde 000001. Recargar la página conserva la serie. Las pestañas del mismo navegador comparten la serie; otros equipos tienen su propia sesión.
Reimprimir conserva el número. Cancelar no reutiliza el número. Reintentar un cobro pendiente guarda una sola venta. Salir de la cuenta reinicia la numeración al volver a entrar, pero no hace un corte ni cierra la caja automáticamente.
Los números cortos se repiten entre sesiones y cajas, como se solicitó. Las ventas siguen teniendo UUID único, fecha, cajero y caja para distinguirlas. Los tickets antiguos conservan sus números. La prueba es un ejemplo y no consume número real.
