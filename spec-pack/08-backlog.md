# Backlog — AsistQR Familia

**Versión:** 1.0.1 · **Fecha:** 2026-11-14 · **Etapa BMAD:** Planning (Agente Backlog Builder + Agente QA de Backlog)

Tamaño: **S** (< 30 min) · **M** (30 min – 1 h) · **L** (1 – 2 h), estimado para un agente de desarrollo.
Pantallas P-XXX según `09-ui-spec.md`. Las dependencias forman un grafo sin ciclos.

## 1. Tareas de la app

| ID | Tarea | HU | Componente | Prioridad | Tamaño | Depende de | Criterio de completitud (sí/no) |
|---|---|---|---|---|---|---|---|
| T-001 | Crear proyecto Flutter con estructura `lib/core` y `lib/features/<módulo>/{data,domain,presentation}` y lints estrictos | Infraestructura | CMP-01 | Must | S | — | ¿`flutter analyze` da 0 advertencias y `flutter test` pasa? |
| T-002 | Pipeline de GitHub Actions: analyze, test con cobertura ≥ 70 % y build de APK | Infraestructura | CMP-01 | Must | M | T-001 | ¿Un PR con una advertencia del analizador falla el pipeline? |
| T-003 | Configuración por entorno con `--dart-define` (`API_BASE_URL`), sin secretos en el repositorio | Infraestructura | CMP-01 | Must | S | T-001 | ¿`git grep` no encuentra URLs de producción ni claves? |
| T-004 | Tema con design tokens, textos en `es_PE` y utilidad `formatLimaTime` | Transversal | CMP-01 | Must | M | T-001 | ¿`2026-04-14T12:52:14Z` se formatea "07:52" en una prueba (RN-013)? |
| T-005 | Router `go_router` con las rutas de ADR-004 y guarda de sesión | Transversal | CMP-01 | Must | M | T-004 | ¿Sin sesión, `/inicio` redirige a `/login`? |
| T-006 | Cliente `dio`: header Bearer, timeout 10 s, 1 reintento en GET y mapeo de ProblemDetail a errores de dominio | Transversal | CMP-01 | Must | M | T-003 | ¿Un 422 de la API llega a la UI como `BusinessRuleError` con su detalle? |
| T-007 | Detección de conexión y vista "Sin conexión" con "Reintentar" | HU-005 | CMP-01 | Must | S | T-006 | ¿En modo avión se ve el mensaje de RN-012 y ninguna marca? |
| T-008 | `SessionStorage` sobre `flutter_secure_storage` | HU-002 | CMP-02 | Must | S | T-001 | ¿Los tokens no aparecen en `SharedPreferences` ni en logs? |
| T-009 | Repositorio de sesión: login (API-01) y renovación (API-02) | HU-001 | CMP-02 | Must | M | T-006, T-008 | ¿Las pruebas cubren 200, 401 y 429? |
| T-010 | Interceptor de renovación con candado: una sola renovación para varias peticiones que fallan con 401 | HU-002 | CMP-02 | Must | L | T-009 | ¿Dos peticiones con token vencido generan 1 sola llamada a API-02? |
| T-011 | Pantalla P-001 Login: validación de correo, mensaje de 401 y cuenta regresiva de 429 | HU-001 | CMP-02 | Must | M | T-005, T-009 | ¿Los 4 criterios de HU-001 pasan en pruebas de widget? |
| T-012 | Cerrar sesión en el equipo: botón "Cerrar sesión" con confirmación en P-007 y borrado de los 3 tokens | HU-003 | CMP-02 | Must | S | T-005, T-008 | ¿Después de confirmar, `SessionStorage` está vacío y se ve P-001? |
| T-013 | Modelos `Child`, `AttendanceDay`, `MonthSummary` con parseo JSON | HU-004, HU-005, HU-006 | CMP-03 | Must | S | T-001 | ¿Se parsean los 5 valores de `status` y un valor desconocido no rompe la app? |
| T-014 | Repositorio de asistencia: hijos (API-03) y mes (API-04) | HU-004, HU-005 | CMP-03 | Must | M | T-006, T-013 | ¿Un 403 se convierte en "No tienes acceso a este estudiante"? |
| T-015 | Pantalla P-002 Inicio: tarjetas ordenadas por grado y sección (RN-036), chip de estado y estado vacío sin hijos | HU-004, HU-005 | CMP-03 | Must | L | T-005, T-014 | ¿Con 3 hijos se ven 3 tarjetas y con 0 el mensaje de RN-008? |
| T-016 | Refrescar el estado al volver a primer plano | HU-005 | CMP-03 | Must | S | T-015 | ¿Al reanudar la app se llama a API-04 una vez por hijo? |
| T-017 | Pantalla P-003 Historial: lista del mes, resumen, flechas de mes (siguiente deshabilitada en el mes actual, anterior deshabilitada en marzo 2026, RN-040) y selector de hijo | HU-006 | CMP-03 | Must | L | T-005, T-014 | ¿Noviembre 2026 consultado el viernes 13/11 muestra 10 días escolares? |
| T-018 | Configurar Firebase en Android e iOS, pedir permiso de notificaciones tras el primer login y mostrar el aviso de RN-037 si se niega | HU-007, HU-008 | CMP-04 | Must | M | T-003 | ¿Llega un push de prueba desde la consola de Firebase y, con el permiso negado, se ve la franja de RN-037? |
| T-019 | Registrar el token de push (API-09) después del login y cuando FCM lo rota | HU-007, HU-008 | CMP-04 | Must | S | T-009, T-018, B-01 | ¿La API tiene el token del equipo después de iniciar sesión? |
| T-020 | Push en primer plano: notificación local y refresco de la tarjeta del hijo en ≤ 2 s | HU-005, HU-007, HU-008 | CMP-04 | Must | M | T-015, T-019 | ¿Un push simulado de salida cambia la tarjeta sin recargar (RN-011)? |
| T-021 | Abrir `/historial/{id}?mes=&dia=` al tocar un push con la app cerrada o en segundo plano | HU-009 | CMP-04 | Must | M | T-017, T-020 | ¿Tocar un push del 12/11 abre noviembre con el 12/11 resaltado? |
| T-022 | Repositorio de bandeja: listar (API-11) y marcar leído (API-12) | HU-009 | CMP-04 | Must | S | T-006, B-02 | ¿La paginación pide `page=1` al llegar al final de la lista? |
| T-023 | Pantalla P-004 Avisos: lista paginada de 20, no leídos resaltados, contador en la campana y estado vacío | HU-009 | CMP-04 | Must | L | T-005, T-022 | ¿Abrir un aviso no leído baja el contador de 3 a 2? |
| T-024 | Repositorio de justificaciones: subir sustento (API-05), enviar (API-06), listar las propias (API-13) | HU-010, HU-011 | CMP-05 | Should | M | T-006, B-03 | ¿Un 409 se muestra con el mensaje de RN-030? |
| T-025 | Pantalla P-005 Justificar: días sin entrada y no futuros, motivo de 10 a 500 caracteres, adjunto JPG/PNG/WEBP/PDF ≤ 5 MB | HU-010 | CMP-05 | Should | L | T-017, T-024 | ¿Los 5 criterios de HU-010 pasan en pruebas de widget y el sustento se borra de caché al terminar o cancelar (RN-039)? |
| T-026 | Pantalla P-006 Mis justificaciones con estado y estado vacío | HU-011 | CMP-05 | Should | M | T-024 | ¿Una justificación rechazada muestra "Rechazada"? |
| T-027 | Descargar el PDF mensual (API-07) a un directorio temporal y abrir la hoja de compartir | HU-012 | CMP-06 | Should | M | T-017 | ¿El archivo se llama `asistencia-<nombre>-<aaaa-mm>.pdf` y se borra de caché al cerrar la hoja de compartir o si la descarga falla (RN-039)? |
| T-028 | Cambiar contraseña en P-007 (API-08) con las validaciones de RN-038 | HU-013 | CMP-02 | Should | M | T-012 | ¿Una contraseña nueva de 11 caracteres se rechaza sin llamar a la API? |
| T-029 | Pruebas de widget del chip de estado: 5 estados, texto al 200 % y contraste ≥ 4.5:1 | HU-005 | CMP-03 | Must | M | T-015 | ¿Los 5 chips muestran texto además de color (PRINCIPIO-007)? |
| T-030 | Prueba de integración: login → Inicio → Historial con API simulada | HU-001, HU-004, HU-006 | CMP-01 | Must | M | T-011, T-017 | ¿`integration_test` pasa en el pipeline? |
| T-031 | Prueba de integración: push simulado → refresco de tarjeta → bandeja | HU-007, HU-009 | CMP-04 | Must | M | T-020, T-023 | ¿El flujo pasa sin intervención manual? |
| T-032 | Prueba en Android 8 con 2 GB de RAM y medición del APK de release | Transversal | CMP-01 | Must | S | T-002, T-023 | ¿El APK pesa ≤ 30 MB y Inicio carga en ≤ 3 s con 4G? |
| T-033 | Dar de baja el token de push (API-10) al cerrar sesión; si no hay red, reintentar en el siguiente inicio de sesión | HU-003 | CMP-04 | Must | S | T-012, T-019 | ¿Tras cerrar sesión, una marca simulada no genera push en ese equipo? |
| T-035 | Estado de hoy sin fila de la API: "Sin marca aún" en día escolar (RN-033) y "Hoy no hay clases" con el último día escolar en fin de semana, pidiendo el mes anterior si hace falta (RN-034) | HU-005 | CMP-03 | Must | M | T-015 | ¿El lunes 07:35 sin fila de hoy se ve "Sin marca aún" y el domingo 01/11 se ve el viernes 30/10? |
| T-034 | Mostrar "En revisión" en P-003 para días con justificación pendiente (API-13) y ocultar "Justificar" | HU-010, HU-011 | CMP-03 | Should | S | T-017, T-024 | ¿El 10/11 con justificación pendiente dice "En revisión" y no tiene botón (RN-035)? |

## 2. Dependencias en el backend `qr-asist` (Módulo 2)

No son tareas de la app, pero la bloquean.

| ID | Tarea backend | Bloquea |
|---|---|---|
| B-01 | API-09 y API-10 (`push_device`) | T-019, T-033 |
| B-02 | API-11 y API-12 (tabla `notification`, purga de 30 días) | T-022 |
| B-03 | API-13 (justificaciones del propio apoderado) | T-024 |
| B-04 | `FcmNotificationSender`, estado y `lateMinutes` en el evento de entrada, marca `offline` (RN-014 a RN-019) | Pruebas reales de HU-007 y HU-008 |
| B-05 | Calendario de feriados para que el corte no marque "No registra entrada" en feriados (PA-09) | Lanzamiento a producción |

## 3. Sprints

| Sprint | Objetivo | Tareas |
|---|---|---|
| S1 · Base y acceso | Proyecto, pipeline, sesión | T-001 a T-013 |
| S2 · Asistencia | Inicio e Historial funcionando con la API existente | T-014 a T-017, T-029, T-030, T-035 |
| S3 · Avisos | Push y bandeja (requiere B-01, B-02, B-04) | T-018 a T-023, T-031, T-033 |
| S4 · Justificaciones, reportes y cuenta | Funcionalidades Should y prueba final | T-024 a T-028, T-032, T-034 |

## 4. Resumen

- **Total:** 35 tareas de la app + 5 dependencias de backend.
- **Por prioridad:** Must 29 · Should 6.
- **Por componente:** CMP-01: 9 · CMP-02: 6 · CMP-03: 8 · CMP-04: 8 · CMP-05: 3 · CMP-06: 1.
- **Por tamaño:** S 12 · M 18 · L 5 (no hay tareas de más de 2 horas).
- **Pruebas:** T-029, T-030, T-031 y T-032 son de prueba; además, cada tarea de pantalla incluye sus pruebas de widget.
- **Ruta crítica:** T-001 → T-003 → T-006 → T-014 → T-015 → T-020 → T-021 (con B-01 para T-019 en paralelo). El paso de mayor riesgo es T-010 (L), por la concurrencia en la renovación de tokens.
