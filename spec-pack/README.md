# Spec Pack v1.0 — AsistQR Familia

App móvil Flutter para que los apoderados de un colegio privado del Perú vean en tiempo real las marcas de asistencia QR de sus hijos y reciban notificaciones push. Es el cliente para familias de la API `qr-asist` (Java 21, Spring Boot, PostgreSQL 16).

Trabajo final del Módulo 1 (Ingeniería de Requerimientos & SDD) del Diploma AI-First Software Architecture, DMC Institute.

## Archivos

| Fase BMAD | Archivo | Contenido |
|---|---|---|
| Discovery | `00-constitucion.md` | 10 principios no negociables |
| Discovery | `01-project-brief.md` | Problema, usuarios, valor, modelo, KPIs, riesgos, restricciones |
| Discovery | `02-alcance.md` | MoSCoW (12 funcionalidades), 9 fuera de alcance, MVP, dependencias |
| Discovery | `03-reglas-de-negocio.md` | 40 reglas EARS con ejemplo y contraejemplo |
| Discovery | `04-glosario.md` | Lenguaje ubicuo con equivalencias en la API |
| Discovery | `05-preguntas-abiertas.md` | Preguntas abiertas con dueño y supuesto |
| Planning | `06-user-stories.md` | 13 HUs en 5 épicas, criterios Dado/Cuando/Entonces |
| Planning | `07-architecture.md` | Componentes, modelo de datos, 13 contratos de API, 5 ADRs |
| Planning | `08-backlog.md` | 35 tareas atómicas, 5 dependencias de backend, sprints, ruta crítica |
| Prototyping | `09-ui-spec.md` | 7 pantallas, flujos, wireframes, design tokens, mapa de navegación |
| Prototyping | `10-validation-log.md` | 21 observaciones de los agentes revisores, decisiones y la sección pendiente de validación con un usuario real |
| Prototyping | `prototipos/asistqr-familia.html` | Prototipo interactivo (abrir en el navegador) |

## Trazabilidad

`RN-XXX` (regla) → `HU-XXX` (historia) → `CMP-XX` (componente) → `T-XXX` (tarea) → `P-XXX` (pantalla) → `OBS-XX` (validación).

## Cómo ver el prototipo

Abre `prototipos/asistqr-familia.html` en cualquier navegador. El panel de la derecha simula avisos push, la falta de conexión y una cuenta sin hijos. La fecha simulada es el viernes 13/11/2026.
