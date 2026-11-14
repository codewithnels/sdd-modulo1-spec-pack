# Project Brief — AsistQR Familia

**Versión:** 1.0.1 · **Fecha:** 2026-11-14 · **Etapa BMAD:** Discovery (Agente Analyst)

## 1. Problema y oportunidad

Un colegio privado de Lima con unos **800 estudiantes** controla la asistencia con carnets QR firmados que se escanean en la puerta (sistema `qr-asist`, unas 1 700 marcas al día entre estudiantes y personal). El sistema ya sabe a qué hora entró y salió cada estudiante, pero **esa información no llega a las familias**:

- El apoderado no sabe si su hijo llegó al colegio hasta que el hijo vuelve a casa o el tutor llama.
- Las faltas y tardanzas se enteran por agenda escrita o en la reunión de padres, a veces semanas después.
- Para justificar una falta, el apoderado va a secretaría o envía un papel con el hijo, y el papel se pierde.
- El tutor dedica tiempo a llamar a familias para preguntar por faltas que la familia ya conocía.

**Oportunidad:** la API ya emite los eventos de entrada, salida y falta, y ya tiene un rol de apoderado. Falta el canal hacia la familia: una app móvil con notificaciones push.

## 2. Usuarios y segmentos objetivo

| Persona | Segmento | Necesidad principal |
|---|---|---|
| **Rosa, 38 años** — madre trabajadora, sale de casa antes que su hija | Apoderado con 1 hijo | Saber en el momento que su hija llegó al colegio sin tener que llamar a nadie. |
| **Carlos, 45 años** — padre de 3 hijos en primaria y secundaria | Apoderado con varios hijos | Ver a todos sus hijos en una sola cuenta y saber cuál faltó o llegó tarde. |
| **Abuela Elena, 66 años** — recoge a su nieto, usa un Android de gama baja | Apoderado con poca experiencia digital | Letras grandes, pocos pasos, mensajes claros sin términos técnicos. |
| **Tutor de aula** (usuario indirecto) | Personal del colegio, usa el panel web | Recibir las justificaciones por escrito y con sustento, sin papeles. |

Actor principal de la app: **Apoderado** (rol `GUARDIAN` en la API). El tutor no usa la app; aprueba justificaciones en el panel web.

## 3. Propuesta de valor

- **Tranquilidad inmediata:** aviso en el celular en menos de 60 segundos después de que el hijo pasa su carnet por la puerta.
- **Todo en un lugar:** marcas del día, historial del mes y reporte PDF de todos los hijos con una sola cuenta.
- **Justificar sin papeles:** la falta se justifica desde el celular con foto del sustento y se ve si fue aprobada.
- Frente a lo que existe hoy (agenda en papel, llamadas, WhatsApp del aula): es automático, privado y queda registrado.

## 4. Modelo de negocio preliminar

- La app es **gratuita para las familias** y parte del servicio educativo del colegio.
- El costo lo asume el colegio: cuentas de desarrollador (Google Play US$ 25 pago único, Apple US$ 99 al año). El push con Firebase Cloud Messaging (FCM) no tiene costo. El servidor ya existe (Oracle Cloud Always Free).
- Valor para el colegio: menos llamadas del tutor, menos papeles en secretaría y un diferencial frente a otros colegios al momento de la matrícula.

## 5. Métricas de éxito (KPIs)

| KPI | Meta | Plazo |
|---|---|---|
| Apoderados con la app instalada y sesión iniciada | ≥ 60 % de las familias | 2 meses después del lanzamiento |
| Tiempo entre la marca en la puerta (en línea) y el push recibido | ≤ 60 s en el 95 % de los casos | Desde el lanzamiento |
| Justificaciones enviadas por la app frente a las de papel | ≥ 70 % | 3 meses después del lanzamiento |
| Fallos (crash) de la app | < 1 % de sesiones | Mensual |
| Calificación en tiendas | ≥ 4.2 / 5 | 3 meses después del lanzamiento |

## 6. Riesgos identificados y supuestos

| Tipo | Riesgo | Mitigación |
|---|---|---|
| Negocio | Las familias no instalan la app. | Activación guiada en la reunión de padres; correo de bienvenida con enlace. |
| Negocio | Avisos de "falta" falsos (el hijo entró sin pasar el carnet) generan quejas. | El aviso dice "No registra entrada" y no "Faltó"; el tutor puede corregir. |
| Técnico | La puerta marca sin internet y sincroniza después; el aviso llega tarde. | El push muestra la hora real del escaneo e indica cuando fue sincronizado tarde (RN-019). |
| Técnico | iOS y algunos Android (Xiaomi, Huawei sin Google) limitan los push. | La bandeja de avisos dentro de la app es la fuente de verdad; el push es solo un aviso. |
| Técnico | El backend aún no envía push ni guarda la bandeja. | Dependencia explícita para el Módulo 2 (ver `07-architecture.md`, sección 6). |
| Legal | Tratamiento de datos de menores. | Constitución PRINCIPIO-004 y 005; mínimo dato en el push. |

**Supuestos:** cada apoderado tiene correo electrónico; la secretaría crea la cuenta y vincula a los hijos; el colegio tiene un solo turno (mañana) en esta versión.

## 7. Restricciones conocidas

- Debe consumir la API `qr-asist` existente; la app no tiene servidor propio.
- Flutter 3 para Android e iOS con un solo código.
- La sesión de la API dura 15 minutos y se renueva con un token válido por 7 días.
- Archivos de sustento: JPG, PNG, WEBP o PDF, máximo 5 MB.
- Presupuesto de operación cercano a US$ 0 al mes (solo cuentas de tiendas).
- En este módulo del diploma se construye el front; el backend faltante se completa en el Módulo 2.
