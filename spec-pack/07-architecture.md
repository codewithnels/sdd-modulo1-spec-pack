# Documento de arquitectura — AsistQR Familia

**Versión:** 1.0.1 · **Fecha:** 2026-11-14 · **Etapa BMAD:** Planning (Agente Architect)

## 1. Overview del sistema

AsistQR Familia es una app móvil Flutter (Android e iOS) que usan los apoderados para ver en tiempo real la asistencia de sus hijos. No tiene servidor propio: es un cliente de la API `qr-asist` (Java 21, Spring Boot, PostgreSQL 16), que ya registra las marcas de la puerta, calcula los estados de cada día y emite eventos de entrada, salida y "no registra entrada". El "tiempo real" se logra con notificaciones push de Firebase Cloud Messaging (FCM) más una consulta a la API cada vez que llega un aviso o la app vuelve a primer plano. La app no guarda datos personales en el equipo, solo los tokens cifrados.

```
 Tablet de puerta ──marca──▶ API qr-asist ──evento──▶ Módulo notificaciones (API)
   (Angular PWA)              │   ▲                          │ guarda en bandeja
                              │   │ REST/JSON + JWT          │ envía push
                              ▼   │                          ▼
                         PostgreSQL 16              Firebase Cloud Messaging
                                  ▲                          │
                                  │ HTTPS                    │ push
                                  └──────── App Flutter ◀────┘
                                          AsistQR Familia
```

**Principios de diseño:** la API es la fuente de verdad (PRINCIPIO-002); el push solo avisa y luego la app consulta (no se confía en el contenido del push para mostrar estados); ningún dato personal se escribe en disco (PRINCIPIO-004); capas separadas para poder probar sin red.

## 2. Componentes principales

La app sigue una estructura por funcionalidad (`lib/features/<módulo>/{data,domain,presentation}`) más un núcleo compartido.

| ID | Componente | Responsabilidad | Tecnología | HUs |
|---|---|---|---|---|
| CMP-01 | **Núcleo (`core`)** | Cliente HTTP con interceptores (token, renovación, errores ProblemDetail), detección de conexión, router, tema, textos `es_PE`, formato de hora `America/Lima`. | `dio`, `go_router`, `intl`, `connectivity_plus`* | Transversal (HU-001 a HU-013) |
| CMP-02 | **Sesión (`auth`)** | Login, guardar y renovar tokens, cerrar sesión, cambiar contraseña. | `flutter_secure_storage`, `flutter_riverpod` | HU-001, HU-002, HU-003, HU-013 |
| CMP-03 | **Asistencia (`attendance`)** | Lista de hijos, estado de hoy, historial mensual y resumen. | `flutter_riverpod`, `dio` | HU-004, HU-005, HU-006 |
| CMP-04 | **Avisos (`notifications`)** | Registrar el token de push, recibir push en primer y segundo plano, refrescar la asistencia al llegar uno, bandeja con leídos y no leídos, abrir el día desde un aviso. | `firebase_messaging`, `flutter_local_notifications`* | HU-007, HU-008, HU-009 |
| CMP-05 | **Justificaciones (`justifications`)** | Formulario, elegir foto o PDF, subir el sustento, enviar y listar justificaciones. | `image_picker`*, `file_picker`* | HU-010, HU-011 |
| CMP-06 | **Reportes (`reports`)** | Descargar el PDF mensual a un directorio temporal, abrir la hoja de compartir y borrar el archivo al cerrarla (RN-039). | `share_plus`*, `path_provider`* | HU-012 |
| EXT-01 | **API `qr-asist`** (externo) | Autenticación, hijos, asistencia, justificaciones, archivos, reportes y, desde el Módulo 2, push y bandeja. | Java 21, Spring Boot | Todas |
| EXT-02 | **Firebase Cloud Messaging** (externo) | Entregar los push a Android e iOS (vía APNs). | FCM HTTP v1 | HU-007, HU-008 |

\* Paquetes de apoyo aprobados en la constitución 1.0.1 (PRINCIPIO-001, ADR-005).

## 3. Modelo de datos

La app no tiene base de datos. Estos son los modelos en memoria (Dart, inmutables) que reflejan la API:

| Entidad | Atributos clave | Origen |
|---|---|---|
| `Session` | `accessToken`, `accessTokenExpiresAt`, `refreshToken`, `refreshTokenExpiresAt` | `POST /auth/login`, `/auth/refresh`. **Única entidad persistida**, en almacenamiento seguro. |
| `Child` | `personId` (UUID), `fullName`, `grade`, `section`; derivado: `firstName` (primera palabra de `fullName`) | `GET /guardians/me/students` |
| `AttendanceDay` | `date`, `status` (`ON_TIME`, `LATE`, `NOT_RECORDED`, `ABSENT`, `JUSTIFIED`), `lateMinutes`, `checkInAt?`, `checkOutAt?` | `GET /attendance/students/{id}?month=` |
| `MonthSummary` | `onTime`, `late`, `absent`, `justified` (conteo de `AttendanceDay` por estado; no recalcula estados) | Derivado en la app |
| `InboxNotification` | `id`, `studentPersonId`, `type` (`CHECK_IN`, `LATE`, `CHECK_OUT`, `ABSENT`), `occurredAt`, `sentAt`, `offline` (bool), `title`, `body`, `readAt?` | `GET /notifications/me` (nuevo) |
| `Justification` | `id`, `studentPersonId`, `date`, `reason`, `attachmentUrl?`, `status` (`PENDING`, `APPROVED`, `REJECTED`), `createdAt` | `POST /justifications`, `GET /justifications/mine` (nuevo) |

**Relaciones:** un apoderado tiene 1..N `Child`; un `Child` tiene 0..N `AttendanceDay` por mes; un `Child` tiene 0..N `InboxNotification` y 0..N `Justification`; una `Justification` corresponde a un solo `AttendanceDay` sin entrada.

**Tablas nuevas en `qr-asist` (Módulo 2):**

| Tabla | Columnas |
|---|---|
| `push_device` | `id` uuid, `user_id` uuid FK, `token` varchar(512) único, `platform` (`ANDROID`/`IOS`), `created_at`, `last_seen_at` |
| `notification` | `id` uuid, `recipient_person_id` uuid FK, `student_person_id` uuid FK, `type` varchar(20), `occurred_at` timestamptz, `offline` boolean, `title` varchar(120), `body` varchar(200), `sent_at` timestamptz, `read_at` timestamptz null |

**Seguridad de datos:** los textos de `notification` siguen RN-021 (sin apellidos ni DNI). Los registros de `notification` de más de 30 días se eliminan con una tarea mensual. El token de push se borra al cerrar sesión (RN-006) o cuando FCM responde `UNREGISTERED`.

## 4. API Contracts

Base: `https://<dominio>/api/v1`. Autenticación: `Authorization: Bearer <accessToken>`. Errores en formato ProblemDetail (RFC 9457): `{ "type": "...", "title": "...", "status": 422, "detail": "..." }`.

### 4.1 Existentes en `qr-asist`

| # | Método y ruta | Request | Response | Códigos | HU |
|---|---|---|---|---|---|
| API-01 | `POST /auth/login` | `{ "email": "rosa.quispe@mail.com", "password": "********" }` | `{ "tokenType": "Bearer", "accessToken": "...", "accessTokenExpiresAt": "2026-11-14T13:15:00Z", "refreshToken": "...", "refreshTokenExpiresAt": "2026-11-21T13:00:00Z" }` | 200, 400, 401, 429 | HU-001 |
| API-02 | `POST /auth/refresh` | `{ "refreshToken": "..." }` | Igual a API-01 | 200, 401 | HU-002 |
| API-03 | `GET /guardians/me/students` | — | `[ { "personId": "uuid", "fullName": "Valeria Quispe", "grade": "2", "section": "B" } ]` | 200, 403 | HU-004 |
| API-04 | `GET /attendance/students/{personId}?month=2026-11` | — | `[ { "date": "2026-11-12", "status": "LATE", "lateMinutes": 7, "checkInAt": "2026-11-12T12:52:14Z", "checkOutAt": "2026-11-12T19:05:00Z" } ]` | 200, 403 | HU-005, HU-006, HU-011 |
| API-05 | `POST /files?ownerPersonId={studentId}` (multipart, campo `file`) | Imagen JPG/PNG/WEBP o PDF, máx. 5 MB | `{ "id": "uuid", "url": "/api/v1/files/uuid" }` | 201, 403, 413, 422 | HU-010 |
| API-06 | `POST /justifications` | `{ "studentPersonId": "uuid", "date": "2026-11-10", "reason": "Cita médica en EsSalud a las 08:00", "attachmentUrl": "/api/v1/files/uuid" }` | `{ "id": "uuid" }` | 201, 400, 403, 409 (ya hay una pendiente), 422 (día futuro o con entrada) | HU-010 |
| API-07 | `GET /reports/students/{personId}/attendance?month=2026-10` | — | `application/pdf` | 200, 403 | HU-012 |
| API-08 | `POST /users/me/password` | `{ "currentPassword": "...", "newPassword": "12 a 128 caracteres" }` | — | 204, 400, 401 | HU-013 |

### 4.2 Nuevos, a construir en `qr-asist` (Módulo 2)

| # | Método y ruta | Request | Response | Códigos | HU |
|---|---|---|---|---|---|
| API-09 | `PUT /push-devices` | `{ "token": "fcm-token", "platform": "ANDROID" }` | — | 204, 400 | HU-007, HU-008 |
| API-10 | `DELETE /push-devices/{token}` | — | — | 204, 404 | HU-003 |
| API-11 | `GET /notifications/me?page=0&size=20` | — | `{ "items": [ { "id": "uuid", "studentPersonId": "uuid", "type": "LATE", "occurredAt": "2026-11-12T12:52:14Z", "sentAt": "2026-11-12T12:52:40Z", "offline": false, "title": "Tardanza", "body": "Valeria llegó tarde: ingresó a las 07:52 (7 min de tardanza)", "readAt": null } ], "page": 0, "size": 20, "total": 25, "totalPages": 2, "unread": 3 }` | 200 | HU-009 |
| API-12 | `POST /notifications/{id}/read` | — | — | 204, 404 | HU-009 |
| API-13 | `GET /justifications/mine?page=0&size=20` | — | `PageResponse` de `{ "id", "studentPersonId", "date", "reason", "attachmentUrl", "status", "createdAt" }` | 200 | HU-011 |

### 4.3 Contenido del push (FCM)

```json
{
  "notification": { "title": "Tardanza", "body": "Valeria llegó tarde: ingresó a las 07:52 (7 min de tardanza)" },
  "data": {
    "notificationId": "uuid",
    "studentPersonId": "uuid",
    "type": "LATE",
    "occurredAt": "2026-11-12T12:52:14Z",
    "offline": "false"
  }
}
```

Al recibirlo, la app no muestra el estado a partir de `data`: llama a API-04 del hijo indicado y actualiza la tarjeta (RN-011).

**Cambios necesarios en el backend para emitir este push:** (1) adaptador `FcmNotificationSender` que reemplaza al `LoggingNotificationSender`; (2) el evento `AttendanceEventRecorded` debe llevar el estado del día (`ON_TIME`/`LATE`) y `lateMinutes` para elegir la plantilla de RN-014 o RN-015; (3) marcar `offline = true` cuando `recibido − occurredAt > 10 min` (RN-019); (4) guardar cada aviso en `notification` antes de enviarlo.

## 5. Decisiones técnicas (ADRs)

### ADR-001: Tiempo real con push + consulta, no con WebSocket
- **Contexto:** Los padres quieren saber al instante que su hijo llegó. La app pasa casi todo el día cerrada o en segundo plano, y iOS cierra las conexiones abiertas en segundo plano.
- **Opciones:** (a) WebSocket o SSE; (b) consultar la API cada N minutos; (c) push FCM y consulta al recibirlo o al abrir la app.
- **Decisión:** (c).
- **Consecuencias:** El aviso llega con la app cerrada y sin gastar batería. Depende de FCM: en equipos sin Google Play Services el usuario solo ve los datos al abrir la app (PA-08). La API no necesita mantener conexiones abiertas.

### ADR-002: Sin caché local de datos personales
- **Contexto:** Datos de menores (Ley 29733). Una caché permitiría ver datos sin red, pero dejaría marcas en el equipo.
- **Opciones:** (a) caché cifrada con SQLite; (b) solo memoria.
- **Decisión:** (b) solo memoria; en disco, únicamente los tokens en `flutter_secure_storage`.
- **Consecuencias:** Sin internet, la app muestra "Sin conexión" (RN-012). Menor riesgo si se pierde el celular. Cada apertura hace consultas; con 1 a 3 hijos son de 2 a 4 peticiones pequeñas.

### ADR-003: El estado de hoy sale del endpoint mensual
- **Contexto:** La API no tiene un endpoint "hoy" para el apoderado.
- **Opciones:** (a) pedir un endpoint nuevo `GET /attendance/students/{id}/today`; (b) usar API-04 con el mes actual y tomar el día de hoy.
- **Decisión:** (b) en v1.0.
- **Consecuencias:** No hay cambios en el backend. La respuesta de un mes completo tiene unos 22 registros (menos de 3 KB). Si el colegio crece o se agrega un turno, se reevalúa (a). La fila de hoy solo existe desde la primera marca o desde el corte; si falta en un día escolar, la app muestra "Sin marca aún" (RN-033), excepción aprobada en PRINCIPIO-002. Si hoy es fin de semana y el mes actual todavía no tiene días escolares (ej.: domingo 01/11/2026), la app pide también el mes anterior para mostrar el último día escolar (RN-034).

### ADR-004: Riverpod para el estado y `go_router` para la navegación
- **Contexto:** Constitución PRINCIPIO-001. Los avisos deben poder abrir una pantalla concreta (HU-009).
- **Opciones:** Bloc, Provider, Riverpod; Navigator 2.0 manual o `go_router`.
- **Decisión:** Riverpod (`AsyncNotifier` por pantalla) y `go_router` con rutas `/login`, `/inicio`, `/historial/:personId?mes=&dia=`, `/avisos`, `/justificar/:personId?fecha=`, `/justificaciones`, `/cuenta`.
- **Consecuencias:** Los providers se pueden reemplazar en pruebas sin red. Un push abre `/historial/{id}?mes=2026-11&dia=12` directamente.

### ADR-005: Paquetes adicionales a la constitución
- **Contexto:** La constitución lista los paquetes base. Elegir fotos, compartir PDF, mostrar push en primer plano y detectar la conexión requieren paquetes extra.
- **Opciones:** escribir canales nativos propios o usar paquetes mantenidos por la comunidad Flutter.
- **Decisión:** Usar `image_picker`, `file_picker`, `share_plus`, `path_provider`, `flutter_local_notifications` y `connectivity_plus` (todos de flutter.dev o Flutter Community, licencias BSD o MIT).
- **Consecuencias:** Aprobados e incorporados a PRINCIPIO-001 en la constitución 1.0.1. Sin ellos no se podrían construir T-007 y T-020 (Must) ni HU-010 y HU-012 (Should).

## 6. Dependencias y restricciones técnicas

| Restricción | Origen |
|---|---|
| Flutter 3 / Dart 3; Android 8.0+ (API 26) e iOS 15+; APK ≤ 30 MB | PRINCIPIO-001, 008 |
| HTTPS con TLS 1.2+; tokens solo en `flutter_secure_storage` | PRINCIPIO-004 |
| Timeout de red de 10 s por petición; 1 reintento automático solo en GET | Contexto 3G |
| Horas siempre convertidas a `America/Lima` | RN-013 |
| Sin claves de Firebase ni URLs de producción en el repositorio: se inyectan con `--dart-define` en el pipeline | PRINCIPIO-009 |
| `flutter analyze` sin advertencias; cobertura ≥ 70 % | PRINCIPIO-010 |
| Dependencia bloqueante del Módulo 2: API-09 a API-13 y el adaptador FCM | `02-alcance.md` |
| Calendario de feriados en la API antes del lanzamiento (B-05) | `05-preguntas-abiertas.md` PA-09 |

## 7. Trazabilidad HU → componente → API

| HU | Componente | API |
|---|---|---|
| HU-001 | CMP-02 | API-01 |
| HU-002 | CMP-01, CMP-02 | API-02 |
| HU-003 | CMP-02, CMP-04 | API-10 |
| HU-004 | CMP-03 | API-03 |
| HU-005 | CMP-03, CMP-04 | API-04 |
| HU-006 | CMP-03 | API-04 |
| HU-007 | CMP-04, EXT-02 | API-09, push |
| HU-008 | CMP-04, EXT-02 | API-09, push |
| HU-009 | CMP-04 | API-11, API-12 |
| HU-010 | CMP-05, CMP-03 | API-05, API-06, API-13 |
| HU-011 | CMP-05, CMP-03 | API-13, API-04 |
| HU-012 | CMP-06 | API-07 |
| HU-013 | CMP-02 | API-08 |
