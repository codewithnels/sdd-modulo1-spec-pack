# Reglas de negocio (EARS) — AsistQR Familia

**Versión:** 1.0.1 · **Fecha:** 2026-11-14 · **Etapa BMAD:** Discovery (Agente Business Analyst)

Palabras normativas: **DEBE** (obligatorio), **NO DEBE** (prohibido), **PUEDE** (opcional).
Fuente "API" = regla que ya aplica `qr-asist` y la app debe respetar. Fuente "Producto" = decisión del equipo para la app.
Valores del turno vigente: inicio 07:30, tolerancia 15 min (puntual hasta 07:45 inclusive), hora de corte 08:30, salida 14:00, zona `America/Lima`, días escolares lunes a viernes.

---

## Sesión y acceso

**RN-001** | Categoría: Sesión | Prioridad: MUST | Fuente: API
Patrón EARS: Event-driven
CUANDO el apoderado envía un correo y una contraseña válidos, el sistema DEBE abrir la pantalla Inicio con la lista de sus hijos.
✅ **Ejemplo:** `rosa.quispe@mail.com` + contraseña correcta → Inicio con "Valeria".
❌ **Contraejemplo:** Con datos correctos la app vuelve a mostrar el login.

**RN-002** | Categoría: Sesión | Prioridad: MUST | Fuente: Producto
Patrón EARS: Unwanted
SI la API responde `401` al iniciar sesión, el sistema DEBE mostrar "Correo o contraseña incorrectos" sin indicar cuál de los dos falló.
✅ **Ejemplo:** Contraseña errada → "Correo o contraseña incorrectos".
❌ **Contraejemplo:** "La contraseña es incorrecta para rosa.quispe@mail.com".

**RN-003** | Categoría: Sesión | Prioridad: MUST | Fuente: API
Patrón EARS: Unwanted
SI la API responde `429` (más de 20 peticiones por minuto a `/auth/**`, que incluye inicio de sesión y renovación), el sistema DEBE deshabilitar el botón "Ingresar" durante 60 segundos y mostrar la cuenta regresiva.
✅ **Ejemplo:** Intento 21 a las 10:00:30 → botón deshabilitado hasta 10:01:30, texto "Intenta de nuevo en 60 s".
❌ **Contraejemplo:** El botón sigue activo y el usuario recibe errores sin explicación.

**RN-004** | Categoría: Sesión | Prioridad: MUST | Fuente: API
Patrón EARS: Event-driven
CUANDO el token de acceso vence (15 minutos de vida), el sistema DEBE renovarlo con el token de renovación sin pedir datos al apoderado.
✅ **Ejemplo:** Token emitido 09:00, consulta a las 09:16 → la app renueva y la consulta responde.
❌ **Contraejemplo:** A las 09:16 la app manda al login.

**RN-005** | Categoría: Sesión | Prioridad: MUST | Fuente: API
Patrón EARS: Unwanted
SI la API rechaza el token de renovación (vencido a los 7 días o revocado), el sistema DEBE borrar los tokens del equipo y mostrar el login con el mensaje "Tu sesión expiró. Ingresa de nuevo."
✅ **Ejemplo:** Último uso el 2026-11-01, se abre el 2026-11-09 → login con el mensaje.
❌ **Contraejemplo:** La app muestra una pantalla en blanco o reintenta sin fin.

**RN-006** | Categoría: Seguridad | Prioridad: MUST | Fuente: Producto
Patrón EARS: Event-driven
CUANDO el apoderado cierra sesión, el sistema DEBE borrar los tres tokens guardados (acceso, renovación y push) y dar de baja el token de push en la API.
✅ **Ejemplo:** Tras cerrar sesión, una marca de Valeria no genera aviso en ese equipo.
❌ **Contraejemplo:** El equipo sigue recibiendo avisos después de cerrar sesión.

## Hijos y estado del día

**RN-007** | Categoría: Datos | Prioridad: MUST | Fuente: API
Patrón EARS: Ubiquitous
El sistema DEBE mostrar únicamente los estudiantes devueltos por `GET /api/v1/guardians/me/students`.
✅ **Ejemplo:** La API devuelve 3 hijos → la app muestra 3 tarjetas.
❌ **Contraejemplo:** La app muestra un estudiante guardado de una sesión anterior de otra cuenta.

**RN-008** | Categoría: Datos | Prioridad: MUST | Fuente: Producto
Patrón EARS: State-driven
MIENTRAS la cuenta tenga 0 estudiantes vinculados, el sistema DEBE mostrar "Tu cuenta aún no tiene estudiantes vinculados. Comunícate con secretaría." y NO DEBE mostrar el historial ni la bandeja.
✅ **Ejemplo:** Lista vacía → mensaje y botón "Cerrar sesión".
❌ **Contraejemplo:** Inicio vacío sin explicación.

**RN-009** | Categoría: Datos | Prioridad: MUST | Fuente: API
Patrón EARS: Ubiquitous
El sistema DEBE mostrar el estado de asistencia tal como lo entrega la API, con este texto: `ON_TIME` → "Puntual", `LATE` → "Tardanza · N min", `NOT_RECORDED` → "Sin marca aún", `ABSENT` → "No registra entrada", `JUSTIFIED` → "Falta justificada".
✅ **Ejemplo:** `{status: LATE, lateMinutes: 7}` → "Tardanza · 7 min".
❌ **Contraejemplo:** La app calcula "Tardanza · 22 min" restando 07:30 a la hora de entrada.

**RN-010** | Categoría: Datos | Prioridad: MUST | Fuente: Producto
Patrón EARS: Event-driven
CUANDO el apoderado abre la app o la trae a primer plano, el sistema DEBE consultar el estado de hoy de todos sus hijos y mostrarlo en 3 segundos o menos con red 4G.
✅ **Ejemplo:** La app vuelve de segundo plano a las 07:55 → la tarjeta pasa de "Sin marca aún" a "Puntual" con "Entrada 07:41".
❌ **Contraejemplo:** La tarjeta muestra lo de la mañana hasta que el usuario cierra y abre la app.

**RN-011** | Categoría: Datos | Prioridad: MUST | Fuente: Producto
Patrón EARS: Event-driven
CUANDO llega un aviso push con la app abierta, el sistema DEBE actualizar la tarjeta del hijo correspondiente en 2 segundos o menos sin que el apoderado recargue.
✅ **Ejemplo:** Push de salida a las 14:03 → la tarjeta muestra "Salida 14:03".
❌ **Contraejemplo:** El push aparece pero la tarjeta sigue diciendo "Sin salida".

**RN-012** | Categoría: Datos | Prioridad: MUST | Fuente: Producto
Patrón EARS: Unwanted
SI no hay conexión a internet, el sistema DEBE mostrar "Sin conexión. Revisa tu internet." con un botón "Reintentar" y NO DEBE mostrar marcas guardadas de antes.
✅ **Ejemplo:** Modo avión → mensaje y botón.
❌ **Contraejemplo:** Se muestran las marcas de ayer como si fueran de hoy.

**RN-013** | Categoría: Datos | Prioridad: MUST | Fuente: API
Patrón EARS: Ubiquitous
El sistema DEBE mostrar todas las horas en la zona `America/Lima` con formato de 24 horas `HH:mm` y las fechas como `dd/MM/yyyy`.
✅ **Ejemplo:** `checkInAt: 2026-04-14T12:52:14Z` → "07:52".
❌ **Contraejemplo:** "12:52 PM" o "04/14/2026".

## Notificaciones

**RN-014** | Categoría: Notificaciones | Prioridad: MUST | Fuente: API + Producto
Patrón EARS: Event-driven
CUANDO se registra la entrada de un estudiante con estado `ON_TIME`, el sistema DEBE enviar a cada apoderado vinculado con avisos activos el push "{Nombre} ingresó a las {HH:mm}".
✅ **Ejemplo:** Valeria marca 07:41 → "Valeria ingresó a las 07:41".
❌ **Contraejemplo:** No llega aviso, o llega a un apoderado no vinculado.

**RN-015** | Categoría: Notificaciones | Prioridad: MUST | Fuente: API + Producto
Patrón EARS: Event-driven
CUANDO se registra la entrada de un estudiante con estado `LATE`, el sistema DEBE enviar el push "{Nombre} llegó tarde: ingresó a las {HH:mm} ({N} min de tardanza)" en lugar del aviso de entrada normal.
✅ **Ejemplo:** Marca 07:52 → "Valeria llegó tarde: ingresó a las 07:52 (7 min de tardanza)".
❌ **Contraejemplo:** Llegan dos avisos: uno de entrada y otro de tardanza.

**RN-016** | Categoría: Notificaciones | Prioridad: MUST | Fuente: API + Producto
Patrón EARS: Event-driven
CUANDO se registra la salida de un estudiante, el sistema DEBE enviar el push "{Nombre} salió a las {HH:mm}".
✅ **Ejemplo:** Marca de salida 14:05 → "Valeria salió a las 14:05".
❌ **Contraejemplo:** La salida se notifica como entrada.

**RN-017** | Categoría: Notificaciones | Prioridad: MUST | Fuente: API + Producto
Patrón EARS: Event-driven
CUANDO corre el proceso de corte después de las 08:30 de un día escolar (se ejecuta cada 5 minutos, así que el aviso sale entre 08:30 y 08:35) y el estudiante no tiene entrada, el sistema DEBE enviar el push "{Nombre} no registra entrada hoy ({dd/MM})".
✅ **Ejemplo:** Lunes 2026-11-16, sin marca a las 08:30 → "Valeria no registra entrada hoy (16/11)".
❌ **Contraejemplo:** Se envía el aviso un sábado o el texto dice "Valeria faltó".

**RN-018** | Categoría: Notificaciones | Prioridad: MUST | Fuente: Producto
Patrón EARS: Ubiquitous
El sistema DEBE entregar el push en 60 segundos o menos desde que la API recibe la marca, en el 95 % de los casos.
✅ **Ejemplo:** Marca recibida 07:41:05 → push en el celular 07:41:40.
❌ **Contraejemplo:** El push llega a las 07:45 con la puerta en línea.

**RN-019** | Categoría: Notificaciones | Prioridad: MUST | Fuente: Producto
Patrón EARS: Unwanted
SI la marca llega a la API más de 10 minutos después de la hora del escaneo (puerta sin internet), el aviso DEBE mostrar la hora real del escaneo y agregar "(registrado sin conexión)".
✅ **Ejemplo:** Escaneo 07:40, sincronizado 08:05 → "Valeria ingresó a las 07:40 (registrado sin conexión)".
❌ **Contraejemplo:** "Valeria ingresó a las 08:05".

**RN-020** | Categoría: Notificaciones | Prioridad: MUST | Fuente: API
Patrón EARS: State-driven
MIENTRAS el vínculo apoderado-estudiante tenga `receivesNotifications = false`, el sistema NO DEBE enviar push de ese estudiante a ese apoderado, pero DEBE seguir mostrando sus datos en la app.
✅ **Ejemplo:** El padre sin avisos activos abre la app y ve "Puntual" con "Entrada 07:41" sin haber recibido push.
❌ **Contraejemplo:** El padre deja de ver al hijo en la app.

**RN-021** | Categoría: Privacidad | Prioridad: MUST | Fuente: Producto
Patrón EARS: Ubiquitous
El texto visible de un push DEBE contener solo el nombre de pila del estudiante, el tipo de evento y la hora, y NO DEBE contener apellidos, DNI, grado, sección ni foto.
✅ **Ejemplo:** "Valeria ingresó a las 07:41".
❌ **Contraejemplo:** "Valeria Quispe (2.° B, DNI 7xxxxxxx) ingresó".

**RN-022** | Categoría: Notificaciones | Prioridad: MUST | Fuente: Producto
Patrón EARS: Ubiquitous
La bandeja de avisos DEBE listar los avisos de los últimos 30 días del más reciente al más antiguo, en páginas de 20.
✅ **Ejemplo:** Hoy 2026-11-14 → se ven avisos desde el 2026-10-16 (30 días contando hoy).
❌ **Contraejemplo:** Aparece un aviso del 2026-09-01.

**RN-023** | Categoría: Notificaciones | Prioridad: MUST | Fuente: Producto
Patrón EARS: Event-driven
CUANDO el apoderado abre un aviso no leído, el sistema DEBE marcarlo como leído y bajar en 1 el contador de no leídos.
✅ **Ejemplo:** Contador 3 → abre un aviso → contador 2.
❌ **Contraejemplo:** El contador sigue en 3.

## Historial

**RN-024** | Categoría: Datos | Prioridad: MUST | Fuente: API
Patrón EARS: Ubiquitous
El historial mensual DEBE mostrar solo días escolares (lunes a viernes) desde el primer día del mes hasta hoy, y el resumen DEBE contar puntuales, tardanzas, faltas y justificadas.
✅ **Ejemplo:** Noviembre consultado el viernes 13/11 → días del 02/11 al 13/11 (10 días escolares).
❌ **Contraejemplo:** Se muestran sábados o días futuros.

**RN-025** | Categoría: Datos | Prioridad: MUST | Fuente: Producto
Patrón EARS: Unwanted
SI el apoderado intenta ver un mes posterior al actual, el sistema NO DEBE permitirlo (la flecha "mes siguiente" se deshabilita en el mes actual).
✅ **Ejemplo:** En noviembre 2026 la flecha a diciembre está deshabilitada.
❌ **Contraejemplo:** Se muestra diciembre vacío.

**RN-040** | Categoría: Datos | Prioridad: MUST | Fuente: Producto (supuesto PA-11)
Patrón EARS: Unwanted
SI el apoderado intenta ver un mes anterior al inicio del año escolar en curso (marzo 2026), el sistema NO DEBE permitirlo (la flecha "mes anterior" se deshabilita en marzo).
✅ **Ejemplo:** En marzo 2026 la flecha a febrero está deshabilitada.
❌ **Contraejemplo:** Se muestra diciembre 2025 vacío.

## Justificaciones

**RN-026** | Categoría: Justificaciones | Prioridad: SHOULD | Fuente: API
Patrón EARS: Unwanted
SI el día tiene una marca de entrada, el sistema NO DEBE ofrecer "Justificar" para ese día.
✅ **Ejemplo:** 12/11 con "Tardanza · 7 min" → no aparece el botón.
❌ **Contraejemplo:** El botón aparece y la API responde error.

**RN-027** | Categoría: Justificaciones | Prioridad: SHOULD | Fuente: API
Patrón EARS: Unwanted
SI la fecha elegida es posterior a hoy, el sistema NO DEBE permitir enviar la justificación.
✅ **Ejemplo:** Hoy 14/11 → el calendario no deja elegir el 17/11.
❌ **Contraejemplo:** Se envía una justificación para el 17/11.

**RN-028** | Categoría: Justificaciones | Prioridad: SHOULD | Fuente: API + Producto
Patrón EARS: Ubiquitous
El motivo de una justificación DEBE tener entre 10 y 500 caracteres.
✅ **Ejemplo:** "Cita médica en EsSalud a las 08:00" (34 caracteres) → se acepta.
❌ **Contraejemplo:** "Enfermo" (7 caracteres) → se acepta sin aviso.

**RN-029** | Categoría: Justificaciones | Prioridad: SHOULD | Fuente: API
Patrón EARS: Optional
DONDE el apoderado adjunte un sustento, el archivo DEBE ser JPG, PNG, WEBP o PDF de 5 MB como máximo.
✅ **Ejemplo:** Foto JPG de 2.1 MB → se adjunta.
❌ **Contraejemplo:** Video MP4 de 18 MB → se sube.

**RN-030** | Categoría: Justificaciones | Prioridad: SHOULD | Fuente: API
Patrón EARS: Unwanted
SI ya existe una justificación pendiente para ese estudiante y ese día, el sistema DEBE mostrar "Ya enviaste una justificación para este día y está en revisión." y NO DEBE enviar otra.
✅ **Ejemplo:** Segundo intento para el 10/11 → mensaje, sin envío.
❌ **Contraejemplo:** Quedan dos justificaciones pendientes para el mismo día.

**RN-031** | Categoría: Justificaciones | Prioridad: SHOULD | Fuente: API
Patrón EARS: Event-driven
CUANDO el tutor aprueba una justificación, el día DEBE mostrarse como "Falta justificada" y la hora de las marcas NO DEBE cambiar.
✅ **Ejemplo:** 10/11 "No registra entrada" → aprobada → "Falta justificada".
❌ **Contraejemplo:** El día aparece como "Puntual".

## Reporte

**RN-032** | Categoría: Reportes | Prioridad: SHOULD | Fuente: API
Patrón EARS: Event-driven
CUANDO el apoderado pide el reporte de un mes, el sistema DEBE descargar el PDF de `GET /api/v1/reports/students/{personId}/attendance?month=` y abrir la hoja de compartir del sistema.
✅ **Ejemplo:** "Descargar PDF" de octubre 2026 → archivo `asistencia-valeria-2026-10.pdf` listo para compartir por WhatsApp.
❌ **Contraejemplo:** El PDF se guarda en la galería sin aviso.

## Reglas agregadas en la versión 1.0.1 (auditoría del Spec Finalizer)

**RN-033** | Categoría: Datos | Prioridad: MUST | Fuente: API + Producto
Patrón EARS: State-driven
MIENTRAS sea un día escolar y la respuesta de API-04 no traiga una fila para la fecha de hoy (la API la crea con la primera marca o con el corte), el sistema DEBE mostrar "Sin marca aún" para ese hijo.
✅ **Ejemplo:** Lunes 16/11 a las 07:35, API-04 trae filas hasta el 13/11 → la tarjeta dice "Sin marca aún".
❌ **Contraejemplo:** La tarjeta queda vacía o muestra el estado del viernes como si fuera de hoy.

**RN-034** | Categoría: Datos | Prioridad: MUST | Fuente: Producto
Patrón EARS: State-driven
MIENTRAS sea sábado o domingo, el sistema DEBE mostrar en cada tarjeta "Hoy no hay clases" y debajo el estado del último día escolar con su fecha.
✅ **Ejemplo:** Sábado 14/11 → "Hoy no hay clases · Viernes 13/11: Puntual".
❌ **Contraejemplo:** El sábado muestra "Sin marca aún".

**RN-035** | Categoría: Justificaciones | Prioridad: SHOULD | Fuente: API
Patrón EARS: State-driven
MIENTRAS un día tenga una justificación con estado `PENDING`, el sistema DEBE mostrarlo como "En revisión" en lugar de "No registra entrada" y NO DEBE ofrecer "Justificar" para ese día.
✅ **Ejemplo:** 10/11 con justificación enviada → "En revisión", sin botón.
❌ **Contraejemplo:** El 10/11 sigue con el botón "Justificar" y el apoderado envía otra.

**RN-036** | Categoría: UX | Prioridad: MUST | Fuente: Producto
Patrón EARS: Ubiquitous
El sistema DEBE ordenar a los hijos por grado (número) ascendente, luego por sección (letra) ascendente y, si empatan, por nombre.
✅ **Ejemplo:** "1.° A Lucía", "2.° B Valeria", "5.° A Mateo".
❌ **Contraejemplo:** Orden alfabético por nombre: Lucía, Mateo, Valeria.

**RN-037** | Categoría: Notificaciones | Prioridad: MUST | Fuente: Producto
Patrón EARS: Unwanted
SI el apoderado negó el permiso de notificaciones del sistema, el sistema DEBE mostrar en Inicio el aviso "Activa las notificaciones para saber cuándo tus hijos entran y salen" con un botón "Activar" que abre los ajustes de la app.
✅ **Ejemplo:** Permiso negado → franja visible arriba de las tarjetas.
❌ **Contraejemplo:** La app no avisa nada y el apoderado cree que no hubo marcas.

**RN-038** | Categoría: Seguridad | Prioridad: SHOULD | Fuente: API
Patrón EARS: Ubiquitous
La contraseña nueva DEBE tener entre 12 y 128 caracteres y DEBE coincidir con su repetición antes de enviarse a la API.
✅ **Ejemplo:** "AzulCielo2026!" (14 caracteres) repetida igual → se envía.
❌ **Contraejemplo:** "clave123" (8 caracteres) → se envía y la API la rechaza.

**RN-039** | Categoría: Privacidad | Prioridad: SHOULD | Fuente: Producto
Patrón EARS: Event-driven
CUANDO se cierra la hoja de compartir del PDF o termina (o se cancela) la subida de un sustento, el sistema DEBE borrar el archivo temporal del directorio de caché de la app.
✅ **Ejemplo:** Después de compartir `asistencia-valeria-2026-10.pdf`, el archivo ya no existe en caché.
❌ **Contraejemplo:** Los PDF de todos los meses se acumulan en el equipo.
