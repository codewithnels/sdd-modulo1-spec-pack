# Constitución del proyecto — AsistQR Familia

**Versión:** 1.0.1 · **Fecha:** 2026-11-14
**Ámbito:** Obligatoria para toda persona o agente de IA que especifique, diseñe, programe o pruebe la app móvil AsistQR Familia.
**Producto:** App móvil Flutter para que los apoderados de un colegio privado del Perú vean en tiempo real las marcas de asistencia de sus hijos (registradas con carnet QR en la puerta) y reciban notificaciones push.
**Sistema existente:** API `qr-asist` (Java 21, Spring Boot, PostgreSQL 16). La app es un cliente de esa API; no tiene base de datos propia en servidor.

---

### PRINCIPIO-001: Stack móvil fijo
La app se construye con Flutter 3 (Dart 3) y estos paquetes: `flutter_riverpod` (estado), `go_router` (navegación), `dio` (HTTP), `firebase_messaging` (push), `flutter_secure_storage` (tokens), `intl` con locale `es_PE`. Paquetes de apoyo aprobados en la versión 1.0.1 (ADR-005): `flutter_local_notifications`, `connectivity_plus`, `image_picker`, `file_picker`, `share_plus` y `path_provider`. Agregar, quitar o reemplazar un paquete de esta lista requiere aprobación escrita del equipo humano.
- **Cumplimiento:** El estado de la lista de hijos se maneja con un `Provider` de Riverpod.
- **Violación:** Un agente agrega `provider`, `bloc` o `http` porque "es más simple".

### PRINCIPIO-002: La API qr-asist es la única fuente de verdad
La app solo lee y escribe datos a través de la API `qr-asist` (`/api/v1/...`). No calcula estados de asistencia (puntual, tardanza, falta): muestra los que entrega la API. Única excepción aprobada: en un día escolar en que la API todavía no trae la fila de hoy, la app muestra "Sin marca aún" (RN-033), y en sábado o domingo muestra "Hoy no hay clases" (RN-034).
- **Cumplimiento:** La pantalla muestra "Tardanza · 7 min" porque la API respondió `status: LATE, lateMinutes: 7`.
- **Violación:** La app compara la hora de entrada con 07:45 y decide por su cuenta que fue tardanza.

### PRINCIPIO-003: Un apoderado solo ve a sus propios hijos
La app nunca muestra datos de un estudiante que no esté vinculado al apoderado autenticado. La lista de hijos sale únicamente de `GET /api/v1/guardians/me/students`. Un `403` de la API se muestra como "No tienes acceso a este estudiante" y no se reintenta.
- **Cumplimiento:** Un enlace profundo a un `personId` ajeno muestra el mensaje de acceso denegado.
- **Violación:** La app guarda en caché un `personId` de otra cuenta y lo consulta.

### PRINCIPIO-004: Protección de datos personales (Ley N.° 29733 y DS 016-2024-JUS)
En el equipo solo se guardan el token de acceso, el token de renovación y el token de push, cifrados con `flutter_secure_storage` (Keystore en Android, Keychain en iOS). Nombres, marcas, fotos y DNI no se escriben en disco, en `SharedPreferences` ni en logs. Toda comunicación usa HTTPS con TLS 1.2 o superior. Excepción acotada: el PDF del reporte y el archivo de sustento pueden existir en el directorio de caché de la app solo mientras se comparten o se suben, y se borran al terminar (RN-039).
- **Cumplimiento:** Al cerrar la app y abrirla sin internet, la pantalla dice "Sin conexión" y no muestra marcas viejas.
- **Violación:** Se guarda la lista de hijos en `SharedPreferences` para "abrir más rápido".

### PRINCIPIO-005: El contenido de una notificación push no expone datos sensibles
El texto visible de un push lleva solo el nombre de pila del estudiante, el tipo de evento y la hora (ej.: "Valeria ingresó a las 07:52"). Nunca lleva DNI, apellidos completos, foto, sección ni motivo de una justificación.
- **Cumplimiento:** "Valeria llegó tarde: ingresó a las 07:52 (7 min de tardanza)".
- **Violación:** "Valeria Quispe Huamán, DNI 7xxxxxxx, 2.° B, ingresó tarde".

### PRINCIPIO-006: Idioma
Textos de la interfaz, mensajes de error y documentación en español del Perú (fechas `dd/MM/yyyy`, hora de 24 h, zona `America/Lima`, moneda no aplica). Identificadores de código en inglés, igual que en la API `qr-asist`.
- **Cumplimiento:** Botón "Justificar falta"; clase `JustificationRepository`.
- **Violación:** Botón "Submit"; fecha mostrada como `11/14/2026`.

### PRINCIPIO-007: Accesibilidad mínima
Contraste de texto ≥ 4.5:1 (WCAG 2.1 AA), áreas táctiles ≥ 48 × 48 dp, la interfaz funciona sin cortes con tamaño de texto del sistema hasta 200 %, y el estado de asistencia nunca se comunica solo con color (siempre va con texto o ícono).
- **Cumplimiento:** El chip de tardanza es ámbar y además dice "Tardanza".
- **Violación:** Un punto rojo sin texto para indicar falta.

### PRINCIPIO-008: Equipos objetivo
La app funciona en Android 8.0 (API 26) o superior e iOS 15 o superior, en equipos de 2 GB de RAM y con red 3G. El APK de release pesa como máximo 30 MB.
- **Cumplimiento:** Se prueba en un Android de gama baja con 2 GB de RAM antes de cada release.
- **Violación:** Se agrega una librería de animaciones de 12 MB para la pantalla de inicio.

### PRINCIPIO-009: Lo que un agente de IA NO puede hacer
Un agente de IA no puede: (a) cambiar endpoints, contratos o reglas de la API `qr-asist` desde el trabajo de la app; (b) escribir credenciales, claves de Firebase o tokens reales en el código o en el repositorio; (c) inventar reglas de negocio que no estén en `03-reglas-de-negocio.md`; (d) desactivar pruebas para que el pipeline pase. Si falta una regla, la anota en `05-preguntas-abiertas.md` y se detiene.
- **Cumplimiento:** El agente detecta que no existe endpoint para la bandeja de avisos y lo registra como dependencia del backend.
- **Violación:** El agente "simula" la bandeja guardando los push en una base local.

### PRINCIPIO-010: Calidad verificable
Cada historia de usuario Must tiene al menos una prueba automatizada (`flutter test` para lógica y widgets, `integration_test` para flujos). Cobertura de líneas ≥ 70 % en `lib/`. `flutter analyze` sin advertencias. Nada se integra a `main` sin pasar el pipeline de GitHub Actions.
- **Cumplimiento:** La HU-005 (ver marcas del día) tiene una prueba de widget con datos de ejemplo.
- **Violación:** Se hace merge con `// ignore:` en 12 advertencias del analizador.
