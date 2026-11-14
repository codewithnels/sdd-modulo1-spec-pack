# Preguntas abiertas — AsistQR Familia

**Versión:** 1.0.1 · **Fecha:** 2026-11-14

Lo que el equipo aún no sabe, a quién preguntarle y qué se asume mientras tanto. Ninguna bloquea el prototipo del Módulo 1; todas deben cerrarse antes del Módulo 2 (implementación), y PA-09 además bloquea el lanzamiento a producción.

| # | Pregunta | A quién | Supuesto mientras tanto | Bloquea |
|---|---|---|---|---|
| PA-01 | ¿Hay un plazo máximo para justificar una falta (por ejemplo, 7 días)? La API hoy no lo limita. | Dirección del colegio | Sin plazo, igual que la API. | RN-027 |
| PA-02 | ¿El colegio tendrá más de un turno (tarde) el próximo año? | Dirección | Un solo turno (mañana). | RN-017, textos de horario |
| PA-03 | ¿Se avisa también la salida cuando la recoge un familiar y no hay marca de salida? | Dirección y auxiliar de puerta | Solo se avisa si hay marca de salida. | RN-016 |
| PA-04 | ¿Cuánto tiempo se guardan los avisos en la bandeja: 30 días es suficiente? | Dirección y familias (encuesta) | 30 días. | RN-022 |
| PA-05 | ¿El tutor debe escribir un motivo cuando rechaza una justificación, para mostrarlo al apoderado? La API hoy solo guarda aprobado o rechazado. | Coordinación académica | No se muestra motivo de rechazo. | HU-011 |
| PA-06 | ¿Cómo recupera su contraseña un apoderado que la olvidó, mientras la app no tenga ese flujo? | Secretaría | La secretaría la restablece desde el panel web. | F-01 |
| PA-07 | ¿Quién crea y administra el proyecto de Firebase y las cuentas de tiendas (Google Play y Apple)? | Dirección y área de TI | El colegio, con apoyo del equipo de la tesis. | Publicación |
| PA-08 | ¿Los apoderados con Huawei sin Google Play Services son un grupo relevante? | Secretaría (encuesta de matrícula) | Menos del 5 %; usan la bandeja dentro de la app. | RN-018 |
| PA-09 | ¿Cómo se registran los feriados (ej.: jueves 08/10, Combate de Angamos)? Hoy la API solo excluye sábados y domingos, así que un feriado dispararía "No registra entrada" a todas las familias. | Dirección y equipo de backend | Se agrega un calendario de feriados en la API (dependencia B-05) antes del lanzamiento. | **Sí**: RN-017 en producción |
| PA-10 | ¿Se debe distinguir nivel (primaria y secundaria) al ordenar a los hijos? La API solo entrega el grado como texto. | Secretaría | Orden por grado y sección (RN-036); si la API agrega el nivel, primaria va primero. | RN-036 |
| PA-11 | ¿En qué mes empieza el año escolar 2026? | Dirección | Marzo 2026. | RN-040 |

## Resueltas por el equipo

| # | Pregunta | Decisión |
|---|---|---|
| PR-01 | ¿El aviso de falta debe decir "faltó"? | No. Dice "no registra entrada" porque el estudiante pudo entrar sin marcar (RN-017 y glosario). |
| PR-02 | ¿Se envían dos avisos cuando hay tardanza (entrada y tardanza)? | No. Uno solo con la tardanza (RN-015). |
