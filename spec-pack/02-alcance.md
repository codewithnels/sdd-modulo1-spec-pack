# Alcance — AsistQR Familia

**Versión:** 1.0.1 · **Fecha:** 2026-11-14 · **Etapa BMAD:** Discovery (Agente PM)

## 1. In-scope (priorizado con MoSCoW)

| ID | Funcionalidad | Descripción | MoSCoW |
|---|---|---|---|
| F-01 | Inicio de sesión | El apoderado entra con correo y contraseña; la sesión se mantiene hasta 7 días sin volver a escribirlos. | **Must** |
| F-02 | Mis hijos | Lista de todos los estudiantes vinculados a la cuenta y cambio rápido entre ellos. | **Must** |
| F-03 | Marcas de hoy | Estado del día de cada hijo (puntual, tardanza, sin marca, falta, justificada) con hora de entrada y salida, actualizado al llegar un aviso. | **Must** |
| F-04 | Notificaciones push | Avisos de entrada, salida, tardanza y falta (no registra entrada) para cada hijo; tocar un aviso abre el historial del hijo en ese día. | **Must** |
| F-05 | Historial del mes | Calendario o lista del mes con el estado de cada día escolar y un resumen de conteos. | **Must** |
| F-06 | Bandeja de avisos | Lista de los avisos recibidos en los últimos 30 días, con leídos y no leídos. | **Must** |
| F-07 | Justificar una falta | Enviar motivo y sustento (foto o PDF) para un día sin entrada. | Should |
| F-08 | Mis justificaciones | Ver el estado de cada justificación enviada: pendiente, aprobada o rechazada. | Should |
| F-09 | Reporte mensual PDF | Descargar y compartir el reporte de asistencia del mes de un hijo. | Should |
| F-10 | Cambiar contraseña | Cambiar la contraseña desde la app. | Should |
| F-11 | Silenciar avisos por tipo | Apagar en el equipo algún tipo de aviso, por ejemplo la salida. | Could |
| F-12 | Tema oscuro | Respetar el tema claro u oscuro del sistema. | Could |

Nota: la antigua F-13 (abrir el día desde un aviso) se integró en F-04 en la versión 1.0.1, porque el flujo principal de avisos la necesita.

## 2. Out-of-scope (Won't en esta versión)

| Funcionalidad | Razón |
|---|---|
| Perfil del personal (marcar con QR del kiosco y GPS, horas trabajadas) | Es otro actor y otro flujo; esta versión es solo para familias. |
| Alertas de riesgo de ausentismo (modelo predictivo) | Decisión del colegio: un puntaje de riesgo no se anuncia automáticamente a la familia; el tutor decide cómo abordarlo. |
| Auto-registro de cuentas | La secretaría crea la cuenta y vincula a los hijos para garantizar el vínculo legal apoderado-estudiante. |
| Recuperar contraseña olvidada desde la app | La API no tiene ese flujo; en esta versión la secretaría restablece la contraseña. Pasa a la siguiente versión. |
| Inicio de sesión por SMS o redes sociales | Costo de SMS y la API solo soporta correo y contraseña. |
| Chat o mensajería con el tutor | Requiere moderación y otro módulo del backend. |
| Ver o descargar datos sin conexión | La constitución (PRINCIPIO-004) prohíbe guardar datos personales en el equipo. |
| Mostrar la foto del estudiante | Depende del consentimiento de imagen; se evalúa en otra versión. |
| Pagos, notas o comunicados del colegio | Fuera del propósito de asistencia. |

## 3. MVP

Con solo los Must, un apoderado **inicia sesión con su correo, ve a todos sus hijos y el estado de hoy de cada uno, recibe un aviso en su celular cuando su hijo entra, sale, llega tarde o no registra entrada, revisa el historial del mes y vuelve a leer cualquier aviso en la bandeja**. Todavía no puede justificar faltas ni descargar el PDF.

## 4. Supuestos

1. Cada apoderado tiene correo electrónico propio y un celular con Google Play Services (Android) o iOS.
2. La secretaría crea la cuenta de cada apoderado y la vincula a sus hijos antes del lanzamiento.
3. El colegio usa un solo turno (mañana: 07:30–14:00, tolerancia 15 min, corte 08:30) en esta versión.
4. Los días escolares son de lunes a viernes.
5. El tutor revisa las justificaciones pendientes al menos una vez por día escolar.

## 5. Restricciones

- Técnicas: Flutter 3; consumir solo la API `qr-asist`; tokens en almacenamiento seguro; Android 8.0+ e iOS 15+.
- Legales: Ley N.° 29733 y su reglamento (DS 016-2024-JUS); datos de menores.
- De recursos: un desarrollador con apoyo de agentes de IA; costo de operación cercano a US$ 0 al mes.

## 6. Dependencias

| Dependencia | Estado |
|---|---|
| API `qr-asist`: login, renovación, hijos, historial, justificaciones, archivos, reporte PDF | Existe |
| API `qr-asist`: registrar token de push del equipo | **Falta** (Módulo 2) |
| API `qr-asist`: bandeja de avisos guardados (listar, marcar leído) | **Falta** (Módulo 2) |
| API `qr-asist`: listar las justificaciones del propio apoderado | **Falta** (Módulo 2) |
| API `qr-asist`: envío real por FCM (hoy el módulo de notificaciones solo escribe en logs) | **Falta** (Módulo 2) |
| Firebase Cloud Messaging (proyecto de Firebase del colegio) | Por crear |
| Cuentas de Google Play Console y Apple Developer | Por crear |
