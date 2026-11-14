# Validation Log — AsistQR Familia

**Versión:** 1.0.1 · **Fecha:** 2026-11-14 · **Etapa BMAD:** Prototyping (Agente Prototype Reviewer + Agente Spec Finalizer + Consolidador)
**Prototipo evaluado:** `prototipos/asistqr-familia.html` (pantallas P-001 a P-007).

## Fuentes de validación

| Fuente | Quién | Estado |
|---|---|---|
| Agente Prototype Reviewer | LLM en chat nuevo, sin el historial de quien escribió el Spec Pack (prompt del Paso 5 de la guía) | ✅ Hecho |
| Agente Spec Finalizer | Mismo revisor independiente (prompt del Paso 7) | ✅ Hecho, 2 rondas |
| Validación con usuario real | Un apoderado o un compañero, con el protocolo de 5 minutos | ⏳ **Pendiente** (sección 4) |

## 1. Observaciones consolidadas

Fuente: **PR** = Agente Prototype Reviewer · **SF** = Agente Spec Finalizer.
Estado: ✅ APLICADA (cambiada en el prototipo o en el archivo) · 📌 DOCUMENTADA (va a preguntas abiertas o dependencias).

| # | Observación | Fuente | Tipo | Severidad | Acción tomada | HU / RN | Estado |
|---|---|---|---|---|---|---|---|
| OBS-01 | Antes de la primera marca o del corte, la API no devuelve la fila de hoy; la app no tenía regla para mostrar "Sin marca aún" sin violar PRINCIPIO-002. | SF | Gap | Crítica | Nuevas RN-033 y RN-034; excepción explícita en PRINCIPIO-002; nota en ADR-003; T-015 actualizada. | HU-005 / RN-033, RN-034 | ✅ |
| OBS-02 | T-007 y T-020 (Must) usan paquetes que la constitución no aprobaba. | SF | Gap | Crítica | Los 6 paquetes se aprueban en PRINCIPIO-001 (constitución 1.0.1); ADR-005 corregido. | — | ✅ |
| OBS-03 | Con "cuenta sin hijos", la barra inferior reaparecía y llevaba a datos de otro hijo sin salida. | PR | Bug | Alta | La barra se oculta siempre sin hijos; el estado vacío ofrece "Cerrar sesión". | HU-004 / RN-008 | ✅ |
| OBS-04 | El reloj del prototipo (07:58) contradecía el aviso de las 08:30 y nunca se veía "Sin marca aún". | PR | Gap | Alta | Reloj a las 08:35 y botón "antes del corte (07:58)" en el panel. | HU-005 / RN-009, RN-017 | ✅ |
| OBS-05 | PRINCIPIO-004 prohibía escribir en disco, pero el PDF y la foto del sustento pasan por archivos temporales. | SF | Gap | Media | Excepción acotada en PRINCIPIO-004 y nueva RN-039 (borrar al terminar). | HU-010, HU-012 / RN-039 | ✅ |
| OBS-06 | Cerrar sesión (Must) dependía de tareas Should y del backend. | SF | Gap | Media | T-012 se divide en T-012 (S1, solo local) y T-033 (baja del push); T-028 solo cambia la contraseña. | HU-003 / RN-006 | ✅ |
| OBS-07 | "Abrir el día desde un aviso" era Could en el alcance pero Must en el backlog. | SF | Gap | Media | F-13 se integra en F-04 (Must). | HU-009 | ✅ |
| OBS-08 | "En revisión" aparecía en la interfaz sin regla, sin término de glosario y sin tarea. | SF | Feature gap | Media | Nueva RN-035, término en el glosario, token `status.review` y T-034. | HU-010, HU-011 / RN-035 | ✅ |
| OBS-09 | El tipo de aviso `NOT_RECORDED` chocaba con el estado del mismo nombre. | SF | Gap | Media | Renombrado a `ABSENT` en `07-architecture.md`. | HU-009 | ✅ |
| OBS-10 | Los feriados generarían "No registra entrada" a todas las familias. | SF | Nuevo requisito | Media | PA-09 y dependencia B-05 (calendario de feriados en la API). | HU-008 / RN-017 | 📌 |
| OBS-11 | "Ordenar por grado" era ambiguo (primaria y secundaria). | SF | Gap | Media | Nueva RN-036 y PA-10. | HU-004 / RN-036 | ✅ |
| OBS-12 | No había criterio para cuando el apoderado niega el permiso de notificaciones. | SF | Feature gap | Media | Nueva RN-037, criterio en HU-007, franja en P-002 y en el prototipo. | HU-007 / RN-037 | ✅ |
| OBS-13 | Faltaba el mensaje "Tu sesión expiró". | PR | Feature gap | Media | Botón "Expirar la sesión" en el panel. | HU-002 / RN-005 | ✅ |
| OBS-14 | El botón "atrás" de P-005 y P-006 siempre volvía al Historial. | PR | Bug | Media | Vuelve a la pantalla de origen. | Flujos 4 y 7 | ✅ |
| OBS-15 | No se veía una justificación rechazada ni la opción de volver a justificar. | PR | Feature gap | Media | Caso "Rechazada" (Mateo, 04/11) con "Justificar de nuevo". | HU-011 | ✅ |
| OBS-16 | Los wireframes de P-003, P-004 y P-006 tenían datos inconsistentes entre sí (horas de "Ayer", día 10/11). | PR + SF | Bug visual | Media | Wireframes alineados con los datos del prototipo. | HU-006, HU-009 | ✅ |
| OBS-17 | El cambio de contraseña aceptaba cualquier contraseña actual y no tenía regla propia. | PR + SF | Gap | Baja | Nueva RN-038; el prototipo valida la actual, el rango de 12 a 128 y la coincidencia. | HU-013 / RN-038 | ✅ |
| OBS-18 | El historial no tenía límite hacia atrás. | SF | Gap | Baja | Nueva RN-040 (desde marzo 2026) y PA-11. | HU-006 / RN-040 | ✅ |
| OBS-19 | La UI spec dibujaba un calendario en P-005; el prototipo usa una lista de días elegibles. | PR | Preferencia | Baja | Se adopta la lista (evita elegir días no válidos). | HU-010 / RN-027 | ✅ |
| OBS-20 | Referencias cruzadas erróneas: brief → RN-010, PA-05 → HU-010, ejemplo de PRINCIPIO-005, rango de 30 días de RN-022, el corte "a las 08:30" (en realidad corre cada 5 min), el límite de 429 que incluye la renovación, y "Mateo" usado como hijo ajeno. | SF | Bug visual | Baja | Todas corregidas. | Varias | ✅ |
| OBS-21 | Faltan estados en el prototipo: cargando, deslizar para actualizar, paginación de la bandeja, tipo de archivo inválido, error al descargar el PDF y 403. | PR | Feature gap | Baja | Se mantienen en la UI spec y en los criterios de las HU; no se prototipan. | HU-005, HU-009, HU-010, HU-012 | 📌 |

**Conteo:** 21 observaciones · 2 críticas, 2 altas, 12 medias, 5 bajas · 19 aplicadas, 2 documentadas.

## 2. Decisiones tomadas

1. La app puede mostrar "Sin marca aún" y "Hoy no hay clases" sin un estado de la API; es la única excepción a PRINCIPIO-002.
2. Se aprueban 6 paquetes de apoyo de Flutter (constitución 1.0.1).
3. Los archivos temporales del PDF y del sustento se permiten solo mientras se usan.
4. Abrir el día desde un aviso pasa a ser parte del Must F-04.
5. Los feriados son responsabilidad del backend (B-05) y bloquean el lanzamiento a producción, no el prototipo.
6. La fecha de una justificación se elige de una lista de días elegibles, no de un calendario libre.

## 3. Archivos actualizados (versión 1.0.1)

| Archivo | Cambio |
|---|---|
| `00-constitucion.md` | PRINCIPIO-001 (paquetes), 002 (excepción), 004 (temporales), 005 (ejemplo) |
| `01-project-brief.md` | Referencia a RN-019 |
| `02-alcance.md` | F-13 integrada en F-04 |
| `03-reglas-de-negocio.md` | RN-003, 010, 017, 020 y 022 corregidas; nuevas RN-033 a RN-040 |
| `04-glosario.md` | Día escolar (feriados), tipo de aviso, panel web, ProblemDetail, Módulo 2, En revisión, Hoy no hay clases, hoja de compartir, lista de días elegibles |
| `05-preguntas-abiertas.md` | PA-05 corregida; nuevas PA-09, PA-10 y PA-11 |
| `06-user-stories.md` | Nuevos criterios y reglas en HU-004, 005, 006, 007, 008, 010, 012 y 013; tabla de cobertura con 40 reglas |
| `07-architecture.md` | Tipo `ABSENT`, ADR-003, ADR-004, ADR-005, CMP-06, restricción B-05 |
| `08-backlog.md` | T-012 dividida, nuevas T-033 y T-034, T-015/T-018/T-028/T-032 ajustadas, B-05, sprints y resumen |
| `09-ui-spec.md` | Wireframes P-002 a P-005, estados de P-002, token `status.review`, reglas en el inventario |
| `prototipos/asistqr-familia.html` | OBS-03, 04, 12, 13, 14, 15, 17 |

## 4. Validación con usuario real — ⏳ PENDIENTE

> Esta sección la completa el estudiante. El agente no puede reemplazar a un stakeholder humano (antipatrón 3 de la clase: "validar con uno mismo").

**Con quién:** un padre o una madre de familia (ideal) o un compañero del diploma haciendo de apoderado.
**Cómo:** abrir `prototipos/asistqr-familia.html` y aplicar el protocolo de 5 minutos **sin explicar nada antes**:

1. Silencio (30 s): mostrar Inicio.
2. "¿Qué crees que hace esta app?" (1 min).
3. "¿Cómo justificarías la falta de Lucía de hoy?" (1 min). Observar dónde toca.
4. "¿Qué información esperabas ver y no está?" (1 min).
5. "¿Qué te confunde?" (1 min).

| # | Observación del usuario | Tipo | Severidad | Acción | HU / RN | Estado |
|---|---|---|---|---|---|---|
| OBS-22 | _(completar)_ | | | | | |
| OBS-23 | _(completar)_ | | | | | |
| OBS-24 | _(completar)_ | | | | | |

Validado por: _(nombre o rol)_ · Fecha: _(dd/mm/aaaa)_

## 5. Resultado del Agente Spec Finalizer

| Ronda | Críticos | Operatividad | Utilidad | Orden |
|---|---|---|---|---|
| 1 (antes de las correcciones) | 2 | 3/5 | 4/5 | 4/5 |
| 2 (después de las correcciones) | 0 | 4/5 | 5/5 | 4/5 |

**Hallazgos de la ronda 2 (todos aplicados en la versión 1.0.1):**

| # | Hallazgo | Severidad | Acción |
|---|---|---|---|
| R2-01 | RN-040 (límite hacia atrás del historial) no tenía tarea y el prototipo la contradecía. | Mayor | T-017 cita RN-040; el panel del prototipo aclara que solo trae octubre y noviembre. |
| R2-02 | RN-034 en el primer fin de semana del mes: el último día escolar cae en el mes anterior. | Mayor | ADR-003 pide también el mes anterior; nueva T-035 (separa RN-033 y RN-034 de T-015). |
| R2-03 | Ninguna tarea verificaba el borrado de temporales (RN-039). | Mayor (parcial de la ronda 1) | Criterios de T-025 y T-027 actualizados. |
| R2-04 | Referencias cruzadas menores: HU-011 sin RN-035, P-007 sin RN-038, `07` §7 sin CMP-03 en HU-010, P-006 sin ejemplo "Rechazada", mapa sin P-006 → P-005, introducción de `05` frente a PA-09, ADR-005 decía "Proponer", T-018 sin verificar RN-037. | Sugerencia | Todas corregidas. |
| R2-05 | Prototipo: "atrás" de P-005 desde P-006, agrupación "Hoy" en modo sábado, "sábado" y "antes del corte" activos a la vez, validación duplicada en el cambio de contraseña. | Sugerencia | Corregidos y probados en el navegador. |

No se pidió una tercera ronda; con 0 críticos el Spec Pack cumple el checklist de entrega ("0 hallazgos críticos", "Operatividad ≥ 3/5"). La tarea final pide Operatividad ≥ 4/5, que se alcanzó en la ronda 2.
