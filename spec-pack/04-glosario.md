# Glosario (lenguaje ubicuo) — AsistQR Familia

**Versión:** 1.0.1 · **Fecha:** 2026-11-14

| Término | Definición | Equivalente en la API |
|---|---|---|
| **Apoderado** | Padre, madre o tutor legal vinculado a uno o más estudiantes. Es el único usuario de la app. | `GUARDIAN` |
| **Estudiante / hijo** | Persona matriculada en el colegio y vinculada a un apoderado. En la interfaz se dice "hijo" o "hija". | `STUDENT` |
| **Vínculo** | Relación registrada por secretaría entre un apoderado y un estudiante, con su parentesco y si recibe avisos. | guardian–student link |
| **Recibe avisos** | Marca del vínculo que indica si ese apoderado recibe push de ese estudiante. | `receivesNotifications` |
| **Carnet QR** | Credencial impresa con un código QR firmado que el estudiante pasa por la tablet de la puerta. | credential `CARD` |
| **Marca** | Registro de un escaneo del carnet en la puerta: entrada o salida, con su hora. | attendance event |
| **Entrada** | Primera marca del día; define si el día es puntual o tardanza. | `CHECK_IN` |
| **Salida** | Marca al retirarse del colegio. | `CHECK_OUT` |
| **Turno** | Horario del colegio: inicio 07:30, tolerancia 15 min, corte 08:30, salida 14:00. | shift schedule |
| **Tolerancia** | Minutos después del inicio en los que la entrada todavía es puntual (hasta 07:45). | `tolerance_minutes` |
| **Hora de corte** | Hora (08:30) en la que un estudiante sin entrada pasa a "No registra entrada" y se avisa al apoderado. | `cutoff_at` |
| **Puntual** | Día con entrada dentro de la tolerancia. | `ON_TIME` |
| **Tardanza** | Día con entrada después de la tolerancia; se informa con los minutos que calcula la API. | `LATE`, `lateMinutes` |
| **Sin marca aún** | Día escolar en curso sin entrada, antes de la hora de corte. | `NOT_RECORDED` |
| **No registra entrada** | Día escolar sin entrada después de la hora de corte. Se usa en lugar de "faltó" porque el estudiante pudo entrar sin marcar. | `ABSENT` |
| **Falta justificada** | Día sin entrada cuya justificación fue aprobada por el tutor. | `JUSTIFIED` |
| **Día escolar** | Lunes a viernes; los sábados y domingos no generan estado ni avisos. Los feriados todavía no se excluyen (PA-09). | `school-days` |
| **Justificación** | Solicitud del apoderado con motivo y sustento opcional para un día sin entrada. Estados: pendiente, aprobada, rechazada. | justification `PENDING` / `APPROVED` / `REJECTED` |
| **Sustento** | Archivo (foto o PDF, máximo 5 MB) que respalda una justificación, por ejemplo un certificado médico. | stored file |
| **Tutor** | Docente a cargo del aula que revisa y decide las justificaciones en el panel web. No usa la app. | `TEACHER` |
| **Aviso / notificación push** | Mensaje que llega al celular cuando ocurre una marca o un "No registra entrada". | push FCM |
| **Bandeja de avisos** | Lista dentro de la app con los avisos de los últimos 30 días, leídos y no leídos. | notification inbox (por construir) |
| **Registrado sin conexión** | Marca que la tablet de la puerta guardó sin internet y envió después; se muestra con su hora real. | gate offline batch |
| **Token de acceso** | Credencial de sesión de 15 minutos que la app envía en cada consulta. | `accessToken` |
| **Token de renovación** | Credencial de 7 días que permite pedir un nuevo token de acceso sin volver a ingresar. | `refreshToken` |
| **Token de push** | Identificador del celular en Firebase Cloud Messaging para recibir avisos. | FCM registration token |
| **Tipo de aviso** | Clase de push: entrada (`CHECK_IN`), tardanza (`LATE`), salida (`CHECK_OUT`) o no registra entrada (`ABSENT`). | `InboxNotification.type` |
| **Panel web** | Aplicación Angular del colegio donde secretaría, tutores y dirección trabajan. No es parte de esta app. | Angular PWA |
| **ProblemDetail** | Formato estándar de errores de la API (RFC 9457) con `type`, `title`, `status` y `detail`. | `ProblemDetail` |
| **Módulo 2** | Siguiente módulo del diploma, donde se completa el backend que esta app necesita. | — |
| **Reporte mensual** | PDF con todos los días del mes de un estudiante, sus estados y los conteos. | attendance report |

## Términos de interfaz (agregados en la fase de prototipado)

| Término | Definición |
|---|---|
| **Tarjeta del hijo** | Bloque en Inicio con el nombre, grado y sección, y el estado de hoy de un estudiante. |
| **Chip de estado** | Etiqueta con color, ícono y texto que muestra el estado de un día (ej.: "Tardanza · 7 min"). |
| **Selector de hijo** | Pestañas o menú desplegable para cambiar de estudiante en Historial y Justificar. |
| **Contador de no leídos** | Número en el ícono de la campana con los avisos sin abrir. |
| **Estado vacío** | Pantalla que se muestra cuando no hay datos (sin hijos, sin avisos, sin justificaciones). |
| **En revisión** | Etiqueta de un día sin entrada que tiene una justificación pendiente (RN-035). |
| **Hoy no hay clases** | Mensaje de la tarjeta en sábado o domingo (RN-034). |
| **Hoja de compartir** | Menú del sistema operativo para enviar un archivo a WhatsApp, correo, etc. |
| **Lista de días elegibles** | Selector de fecha de P-005 que solo muestra días sin entrada y no futuros. |
