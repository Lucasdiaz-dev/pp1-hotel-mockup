# Mockup Hotel Litoral – v1.4

HTML + CSS estático, sin backend y sin JavaScript. Está alineado con la especificación de casos de uso v1.1 y el modelo de dominio v1.1.
- Las páginas de administrador son solo para escritorio.
- El lado cliente/visitante es para escritorio y mobile (breakpoints 900/560px).
- Los comentarios `<!-- -->` indican a qué template y bloque Twig corresponde cada parte.

## Mapa de pantallas

| Página | Rol | Casos de uso |
|---|---|---|
| index.html | Visitante / Cliente | CU003 Consultar habitaciones disponibles (cards con portada y ★ estrellas, filtros por tipo, solo activas) |
| habitacion.html | Visitante / Cliente | CU004 Ver detalles (galería desde la portada) · inicio de CU005 |
| login.html | Visitante | CU002 Iniciar sesión (email + contraseña, único para ambos roles; banner “continuar con tu reserva”, CU005 FA03) |
| registro.html | Visitante | CU001 Registrarse |
| confirmar.html | Cliente | CU005 Generar reserva (noches × precio = total fijado) |
| reserva-pendiente.html | Cliente | CU005 paso 13: la reserva quedó Pendiente (pantalla posterior a confirmar) |
| mis-reservas.html | Cliente | CU006 Ver reserva (secciones Próximas y Canceladas) · CU013 Cancelar reserva |
| perfil.html | Cliente | CU007 Actualizar perfil |
| admin-habitaciones.html | Administrador | CU010 Gestionar habitaciones |
| admin-habitacion-form.html | Administrador | CU011 Alta / CU012 Modificar habitación (tipo, activa, visor de imágenes, portada, quitar) |
| admin-reservas.html | Administrador | CU008 Consultar reservas por período · CU014 Confirmar reserva · CU015 Confirmar / Revertir cancelación · CU016 Cancelar reserva (Administrador) |
| admin-usuarios.html | Administrador | CU009 Administrar usuarios |

## Notas
- El footer tiene enlaces para recorrer las tres vistas: visitante, cliente y administrador.
- Las fotos son de Unsplash (`img/CREDITOS.txt`). En el proyecto van en `public/img/habitaciones/` como `.webp`.
- Las páginas se generan con `../mockup_src/gen.py`. Si se edita el HTML a mano, no hay que volver a correr el script.

## Validaciones por pantalla

Estas validaciones y mensajes internos no se muestran en el mockup: están en comentarios HTML y acá.

- **index.html (CU003)**:
  - La fecha de ingreso debe ser anterior a la de egreso.
  - Huéspedes: dropdown de 1 a la capacidad máxima entre las habitaciones activas.
  - Sin resultados: "No existen habitaciones disponibles en ese período."
- **habitacion.html (CU004/CU005)**:
  - Huéspedes: dropdown de 1 a la capacidadMaxima de la habitación.
  - La fecha de ingreso no puede ser anterior a hoy, y la de egreso debe ser posterior a la de ingreso.
  - Error: "La cantidad de huéspedes supera la capacidad máxima."
  - Si no hay sesión, "Reservar" lleva a login y después vuelve a la reserva.
- **login.html (CU002)**:
  - Error genérico: "Email o contraseña incorrectos."
  - El banner "Iniciá sesión para continuar con tu reserva" solo aparece si se viene de Reservar.
- **registro.html (CU001)**: todos los campos son obligatorios, incluido el teléfono. Error: "Ya existe una cuenta registrada con ese email."
- **perfil.html (CU007)**: el email debe ser único y el teléfono es obligatorio. Error: "Ya existe otra cuenta con ese email."
- **mis-reservas.html (CU006/CU013)**:
  - "Próximas" agrupa Pendiente, Confirmada y Cancelación pendiente, ordenadas por fecha de ingreso. "Canceladas" va aparte.
  - Cada reserva es una tarjeta: datos a la izquierda, foto a la derecha con el estado en una etiqueta blanca. En mobile la foto va arriba.
  - "Cancelar" solo aparece en una reserva Pendiente o Confirmada, y hasta 24 h antes del check-in (fecha de ingreso a las 14:00).
  - Una Pendiente pasa a Cancelada. Una Confirmada pasa a Cancelación pendiente.
  - Los mensajes se muestran como texto plano bajo el título, sin cajas de color: "La reserva #12 fue cancelada." / "Tu cancelación quedó pendiente de confirmación del hotel." / "No tenés reservas registradas."
  - El mockup toma como hoy el 27/10/2026, así que la #9 (ingreso 28/10) ya no se puede cancelar.
- **admin-habitaciones.html (CU010)**: las habitaciones inactivas no se muestran en la búsqueda pública ni pueden reservarse.
- **admin-habitacion-form.html (CU011/CU012)**:
  - Estrellas: de 1 a 5, obligatorio.
  - Errores de imágenes: "Debe asignar al menos una imagen." y "Debe marcar una portada."
  - "Quitar" solo desasigna: el archivo no se borra y sigue disponible en el visor.
- **reserva-pendiente.html (CU005)**: "Confirmar reserva" lleva a esta pantalla en lugar de mostrar un aviso. "Entendido" vuelve a Mis reservas.
- **admin-reservas.html (CU008/CU014/CU015/CU016)**:
  - Se muestra el teléfono del cliente, que el administrador usa para coordinar el pago (fuera del sistema).
  - Pendiente: Confirmar o Rechazar (Rechazar pasa a Cancelada y libera la habitación).
  - Confirmada: Cancelar (CU016, por un inconveniente del hotel; pasa a Cancelada, sin restricción de 24 h).
  - Cancelación pendiente: Confirmar cancelación o Revertir (vuelve a Confirmada si el cliente desiste).
  - Cancelada: sin acciones.
  - La fecha desde no puede ser posterior a la fecha hasta.
  - Sin resultados: "No existen reservas en el período seleccionado."
- **Avisos y errores**: siempre texto plano o tarjeta neutra (blanca con borde), nunca cajas con fondo de color.
- **Estrellas**: se muestran solo como ★ repetidas N veces, sin el número.

## Galería ampliada (habitacion.html)

Al hacer clic en una foto se abre un lightbox sin JavaScript, con el patrón `:target`:
- Cada miniatura enlaza a `#foto-N`, y el `<div id="foto-N" class="lightbox">` se muestra con `.lightbox:target`.
- "Cerrar" (y el fondo oscuro) enlazan a `#`. "Anterior" y "Siguiente" enlazan a la foto vecina.

En Symfony/Twig se puede repetir este mismo patrón con un `{% for imagen in habitacion.imagenes %}` que genere los ids `foto-{{ loop.index }}`, o reemplazarlo por un JS chico.
