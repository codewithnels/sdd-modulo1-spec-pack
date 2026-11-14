# Historias de usuario — AsistQR Familia

**Versión:** 1.0.1 · **Fecha:** 2026-11-14 · **Etapa BMAD:** Planning (Agente Product Owner + Agente QA Funcional)

Actor único: **apoderado**. Cada HU tiene criterios Dado/Cuando/Entonces: positivo (✅), negativo (⛔) y borde (◐).
Los criterios agregados por el Agente QA Funcional están marcados con [QA].

| Épica | HUs | Prioridad |
|---|---|---|
| E1 · Acceso | HU-001, HU-002, HU-003 | Must |
| E2 · Seguimiento de asistencia | HU-004, HU-005, HU-006 | Must |
| E3 · Avisos | HU-007, HU-008, HU-009 | Must |
| E4 · Justificaciones | HU-010, HU-011 | Should |
| E5 · Reportes y cuenta | HU-012, HU-013 | Should |

Las funcionalidades Could (F-11 silenciar avisos por tipo, F-12 tema oscuro) no tienen HU en v1.0; se redactan si el backlog Must y Should termina antes de lo previsto.

---

## E1 · Acceso

### HU-001: Iniciar sesión con correo y contraseña
**Prioridad:** Must · **Funcionalidad:** F-01 · **Reglas:** RN-001, RN-002, RN-003
Como apoderado, quiero ingresar con el correo y la contraseña que me dio secretaría, para ver la asistencia de mis hijos.

**Criterios de aceptación**
- ✅ DADO QUE tengo una cuenta activa `rosa.quispe@mail.com`, CUANDO escribo mi correo y contraseña correctos y toco "Ingresar", ENTONCES veo la pantalla Inicio con mis hijos en 3 segundos o menos con red 4G.
- ⛔ DADO QUE escribo una contraseña errada, CUANDO toco "Ingresar", ENTONCES veo "Correo o contraseña incorrectos" y el campo de contraseña se vacía.
- ◐ [QA] DADO QUE hice 20 intentos en el último minuto, CUANDO hago el intento 21, ENTONCES el botón "Ingresar" se deshabilita 60 segundos con la cuenta regresiva visible.
- ◐ [QA] DADO QUE el campo de correo dice `rosa.quispe@mail` (sin dominio completo), CUANDO toco "Ingresar", ENTONCES veo "Escribe un correo válido" y no se llama a la API.

### HU-002: Mantener la sesión abierta
**Prioridad:** Must · **Funcionalidad:** F-01 · **Reglas:** RN-004, RN-005
Como apoderado, quiero que la app recuerde mi sesión, para no escribir mi contraseña cada vez que la abro.

**Criterios de aceptación**
- ✅ DADO QUE ingresé hace 2 días, CUANDO abro la app, ENTONCES entro directo a Inicio sin pantalla de login.
- ✅ DADO QUE mi token de acceso venció hace 1 minuto, CUANDO la app consulta mis hijos, ENTONCES renueva el token sin mostrarme nada y la consulta responde.
- ⛔ DADO QUE no uso la app hace 8 días, CUANDO la abro, ENTONCES veo el login con "Tu sesión expiró. Ingresa de nuevo."
- ◐ [QA] DADO QUE dos consultas fallan al mismo tiempo por token vencido, CUANDO la app renueva, ENTONCES hace una sola renovación y reintenta ambas consultas.

### HU-003: Cerrar sesión
**Prioridad:** Must · **Funcionalidad:** F-01 · **Reglas:** RN-006
Como apoderado, quiero cerrar mi sesión, para que nadie más vea la información de mis hijos si presto o cambio de celular.

**Criterios de aceptación**
- ✅ DADO QUE tengo la sesión abierta, CUANDO toco "Cerrar sesión" y confirmo, ENTONCES vuelvo al login y el equipo ya no guarda ningún token.
- ✅ DADO QUE cerré sesión, CUANDO mi hijo marca su entrada, ENTONCES este celular no recibe el aviso.
- ⛔ [QA] DADO QUE no hay internet, CUANDO cierro sesión, ENTONCES los tokens se borran del equipo igual y la baja del token de push se reintenta en el siguiente inicio de sesión.
- ◐ [QA] DADO QUE toco "Cerrar sesión", CUANDO aparece la confirmación y toco "Cancelar", ENTONCES sigo en la app con la sesión abierta.

## E2 · Seguimiento de asistencia

### HU-004: Ver a todos mis hijos
**Prioridad:** Must · **Funcionalidad:** F-02 · **Reglas:** RN-007, RN-008, RN-036
Como apoderado con varios hijos, quiero verlos a todos en una sola pantalla, para saber de un vistazo cómo le fue a cada uno hoy.

**Criterios de aceptación**
- ✅ DADO QUE tengo 3 hijos vinculados, CUANDO abro Inicio, ENTONCES veo 3 tarjetas ordenadas por grado y sección (RN-036), cada una con nombre, grado y sección, y el estado de hoy.
- ⛔ DADO QUE mi cuenta no tiene hijos vinculados, CUANDO abro Inicio, ENTONCES veo "Tu cuenta aún no tiene estudiantes vinculados. Comunícate con secretaría." y no veo las pestañas Historial ni Avisos.
- ◐ [QA] DADO QUE tengo 1 solo hijo, CUANDO abro Historial, ENTONCES no se muestra el selector de hijo.

### HU-005: Ver las marcas de hoy en tiempo real
**Prioridad:** Must · **Funcionalidad:** F-03 · **Reglas:** RN-009, RN-010, RN-011, RN-012, RN-013, RN-033, RN-034
Como apoderado, quiero ver si mi hijo ya entró al colegio y a qué hora, para estar tranquilo sin llamar a nadie.

**Criterios de aceptación**
- ✅ DADO QUE Valeria marcó entrada a las 07:41, CUANDO abro la app a las 07:50, ENTONCES su tarjeta dice "Puntual" con "Entrada 07:41".
- ✅ DADO QUE tengo la app abierta en Inicio, CUANDO llega el aviso de salida de las 14:05, ENTONCES la tarjeta muestra "Salida 14:05" en 2 segundos o menos sin recargar.
- ⛔ DADO QUE el celular no tiene internet, CUANDO abro Inicio, ENTONCES veo "Sin conexión. Revisa tu internet." con el botón "Reintentar" y ninguna marca vieja.
- ◐ [QA] DADO QUE son las 08:29, Valeria no tiene marca y la API aún no trae la fila de hoy, CUANDO veo su tarjeta, ENTONCES dice "Sin marca aún" (RN-033); y después del corte (a más tardar 08:35), tras actualizar, dice "No registra entrada".
- ◐ [QA] DADO QUE es sábado, CUANDO abro Inicio, ENTONCES cada tarjeta dice "Hoy no hay clases" y muestra el estado del último día escolar con su fecha (RN-034).

### HU-006: Ver el historial del mes
**Prioridad:** Must · **Funcionalidad:** F-05 · **Reglas:** RN-024, RN-025, RN-040, RN-009, RN-013
Como apoderado, quiero ver la asistencia de todo el mes de mi hijo, para detectar si está llegando tarde seguido.

**Criterios de aceptación**
- ✅ DADO QUE estoy en noviembre 2026 y hoy es viernes 13/11, CUANDO abro Historial de Valeria, ENTONCES veo los 10 días escolares del 02/11 al 13/11 con su estado y horas, y el resumen "8 puntual · 1 tardanza · 1 no registra entrada · 0 justificadas".
- ✅ DADO QUE estoy viendo noviembre, CUANDO toco "mes anterior", ENTONCES veo octubre 2026 completo.
- ⛔ DADO QUE estoy en el mes actual, CUANDO miro la flecha "mes siguiente", ENTONCES está deshabilitada.
- ◐ [QA] DADO QUE Valeria se matriculó el 10/11, CUANDO veo noviembre, ENTONCES los días antes del 10/11 no aparecen y el resumen solo cuenta desde esa fecha.

## E3 · Avisos

### HU-007: Recibir aviso de entrada o tardanza
**Prioridad:** Must · **Funcionalidad:** F-04 · **Reglas:** RN-014, RN-015, RN-018, RN-019, RN-021, RN-037
Como apoderado, quiero recibir un aviso cuando mi hijo entra al colegio, para saber que llegó bien y si llegó tarde.

**Criterios de aceptación**
- ✅ DADO QUE Valeria pasa su carnet a las 07:41 con la puerta en línea, CUANDO la API registra la entrada, ENTONCES recibo en 60 segundos o menos "Valeria ingresó a las 07:41".
- ✅ DADO QUE Valeria pasa su carnet a las 07:52, CUANDO la API la registra como tardanza de 7 min, ENTONCES recibo un solo aviso: "Valeria llegó tarde: ingresó a las 07:52 (7 min de tardanza)".
- ⛔ [QA] DADO QUE soy apoderado de Valeria pero no de Diego, su compañero de aula, CUANDO Diego marca entrada, ENTONCES no recibo ningún aviso.
- ◐ [QA] DADO QUE la tablet escaneó a las 07:40 sin internet y sincronizó a las 08:05, CUANDO recibo el aviso, ENTONCES dice "Valeria ingresó a las 07:40 (registrado sin conexión)".
- ◐ [QA] DADO QUE Valeria marca entrada a las 07:45 exactas, CUANDO la API la registra como puntual, ENTONCES el aviso es de entrada normal, no de tardanza.
- ⛔ [QA] DADO QUE negué el permiso de notificaciones, CUANDO abro Inicio, ENTONCES veo "Activa las notificaciones para saber cuándo tus hijos entran y salen" con el botón "Activar" (RN-037).

### HU-008: Recibir aviso de salida y de "no registra entrada"
**Prioridad:** Must · **Funcionalidad:** F-04 · **Reglas:** RN-016, RN-017, RN-020, RN-021
Como apoderado, quiero saber cuándo mi hijo sale del colegio y si no llegó, para actuar a tiempo.

**Criterios de aceptación**
- ✅ DADO QUE Valeria marca su salida a las 14:05, CUANDO la API la registra, ENTONCES recibo "Valeria salió a las 14:05".
- ✅ DADO QUE es lunes 16/11 y Valeria no marcó entrada, CUANDO corre el corte de las 08:30, ENTONCES recibo a más tardar a las 08:35 "Valeria no registra entrada hoy (16/11)".
- ⛔ DADO QUE mi vínculo con Valeria tiene los avisos desactivados, CUANDO ella marca entrada, ENTONCES no recibo push pero al abrir la app veo "Puntual" con "Entrada 07:41".
- ◐ [QA] DADO QUE Valeria llega a las 08:40, después del aviso de "no registra entrada", CUANDO marca su entrada, ENTONCES recibo "Valeria llegó tarde: ingresó a las 08:40 (55 min de tardanza)" y su tarjeta cambia a "Tardanza".
- ◐ [QA] DADO QUE es sábado, CUANDO son las 08:30, ENTONCES no recibo ningún aviso de "no registra entrada".

### HU-009: Revisar la bandeja de avisos
**Prioridad:** Must · **Funcionalidad:** F-06 · **Reglas:** RN-022, RN-023
Como apoderado, quiero volver a leer los avisos que recibí, para revisarlos aunque haya borrado la notificación del celular.

**Criterios de aceptación**
- ✅ DADO QUE recibí 25 avisos en los últimos 30 días, CUANDO abro Avisos, ENTONCES veo los 20 más recientes y al bajar al final se cargan los 5 restantes.
- ✅ DADO QUE tengo 3 avisos sin leer, CUANDO abro uno, ENTONCES se marca como leído y el contador de la campana pasa de 3 a 2.
- ⛔ DADO QUE no recibí avisos en 30 días, CUANDO abro Avisos, ENTONCES veo "Aún no tienes avisos".
- ◐ [QA] DADO QUE toco un aviso de tardanza de Valeria del 12/11, CUANDO se abre, ENTONCES la app me lleva al Historial de Valeria en noviembre con el 12/11 resaltado.

## E4 · Justificaciones

### HU-010: Justificar una falta
**Prioridad:** Should · **Funcionalidad:** F-07 · **Reglas:** RN-026, RN-027, RN-028, RN-029, RN-030, RN-035, RN-039
Como apoderado, quiero justificar desde el celular un día en que mi hijo no asistió, para no tener que ir a secretaría con un papel.

**Criterios de aceptación**
- ✅ DADO QUE el 10/11 Valeria "No registra entrada", CUANDO elijo ese día, escribo "Cita médica en EsSalud a las 08:00", adjunto una foto JPG de 2.1 MB y toco "Enviar", ENTONCES veo "Justificación enviada. El tutor la revisará." y el día muestra "En revisión" (RN-035).
- ⛔ DADO QUE el 12/11 Valeria tiene "Tardanza · 7 min", CUANDO abro ese día, ENTONCES no aparece el botón "Justificar".
- ⛔ [QA] DADO QUE ya envié una justificación para el 10/11 que sigue pendiente, CUANDO intento enviar otra, ENTONCES veo "Ya enviaste una justificación para este día y está en revisión."
- ◐ [QA] DADO QUE adjunto un PDF de 5.3 MB, CUANDO lo elijo, ENTONCES veo "El archivo pesa más de 5 MB" y no se sube.
- ◐ [QA] DADO QUE escribo un motivo de 9 caracteres, CUANDO toco "Enviar", ENTONCES veo "Escribe al menos 10 caracteres" y el botón no envía.

### HU-011: Ver el estado de mis justificaciones
**Prioridad:** Should · **Funcionalidad:** F-08 · **Reglas:** RN-031, RN-035
Como apoderado, quiero saber si el tutor aprobó mi justificación, para estar seguro de que la falta quedó justificada.

**Criterios de aceptación**
- ✅ DADO QUE envié 2 justificaciones, CUANDO abro "Mis justificaciones", ENTONCES veo cada una con fecha del día, hijo y estado ("En revisión", "Aprobada" o "Rechazada").
- ✅ DADO QUE el tutor aprobó la del 10/11, CUANDO abro el Historial de noviembre, ENTONCES el 10/11 dice "Falta justificada".
- ⛔ DADO QUE nunca envié una justificación, CUANDO abro "Mis justificaciones", ENTONCES veo "No has enviado justificaciones".
- ◐ [QA] DADO QUE el tutor rechazó la del 10/11, CUANDO la veo, ENTONCES dice "Rechazada" y el día sigue como "No registra entrada" con el botón "Justificar" disponible otra vez.

## E5 · Reportes y cuenta

### HU-012: Descargar el reporte mensual en PDF
**Prioridad:** Should · **Funcionalidad:** F-09 · **Reglas:** RN-032, RN-039
Como apoderado, quiero descargar el reporte de asistencia del mes, para guardarlo o enviarlo a quien lo necesite.

**Criterios de aceptación**
- ✅ DADO QUE estoy en el Historial de octubre 2026 de Valeria, CUANDO toco "Descargar PDF", ENTONCES en 5 segundos o menos se abre la hoja de compartir con `asistencia-valeria-2026-10.pdf`.
- ⛔ DADO QUE se corta el internet durante la descarga, CUANDO falla, ENTONCES veo "No se pudo descargar el reporte. Intenta de nuevo." y no se crea un archivo incompleto.
- ◐ [QA] DADO QUE estoy en el mes actual con 10 días escolares, CUANDO descargo el PDF, ENTONCES el reporte incluye solo los días hasta hoy.

### HU-013: Cambiar mi contraseña
**Prioridad:** Should · **Funcionalidad:** F-10 · **Reglas:** RN-038
Como apoderado, quiero cambiar la contraseña que me dio secretaría, para que solo yo la conozca.

**Criterios de aceptación**
- ✅ DADO QUE escribo mi contraseña actual correcta y una nueva de 12 a 128 caracteres (mínimo de la API) repetida igual, CUANDO toco "Guardar", ENTONCES veo "Contraseña actualizada" y sigo con la sesión abierta.
- ⛔ DADO QUE mi contraseña actual es incorrecta, CUANDO toco "Guardar", ENTONCES veo "La contraseña actual no es correcta".
- ◐ [QA] DADO QUE la nueva contraseña y su repetición no coinciden, CUANDO toco "Guardar", ENTONCES veo "Las contraseñas no coinciden" y no se llama a la API.

---

## Cobertura de reglas

| Regla | HU |
|---|---|
| RN-001, RN-002, RN-003 | HU-001 |
| RN-004 | HU-002 |
| RN-005 | HU-002 |
| RN-006 | HU-003 |
| RN-007, RN-008 | HU-004 |
| RN-010, RN-011, RN-012 | HU-005 |
| RN-009, RN-013 | HU-005, HU-006 |
| RN-024, RN-025 | HU-006 |
| RN-014, RN-015, RN-018, RN-019 | HU-007 |
| RN-016, RN-017, RN-020 | HU-008 |
| RN-021 | HU-007, HU-008 |
| RN-022, RN-023 | HU-009 |
| RN-026 a RN-030 | HU-010 |
| RN-031 | HU-011 |
| RN-032 | HU-012 |
| RN-033, RN-034 | HU-005 |
| RN-035 | HU-010, HU-011 |
| RN-036 | HU-004 |
| RN-037 | HU-007 |
| RN-038 | HU-013 |
| RN-039 | HU-010, HU-012 |
| RN-040 | HU-006 |

Las 40 reglas tienen al menos una HU.
