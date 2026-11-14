# UI Spec — AsistQR Familia

**Versión:** 1.0.1 · **Fecha:** 2026-11-14 · **Etapa BMAD:** Prototyping (Agente UI Designer)
Prototipo interactivo: `prototipos/asistqr-familia.html` (ábrelo en el navegador; simula un celular).

## 1. Inventario de pantallas

| ID | Pantalla | Actor | HUs | Reglas | Prioridad |
|---|---|---|---|---|---|
| P-001 | Login | Apoderado | HU-001, HU-002 | RN-001 a RN-005 | Must |
| P-002 | Inicio (mis hijos y estado de hoy) | Apoderado | HU-004, HU-005, HU-007, HU-008 | RN-007 a RN-013, RN-033, RN-034, RN-036, RN-037 | Must |
| P-003 | Historial del mes | Apoderado | HU-006, HU-011, HU-012 | RN-009, RN-013, RN-024, RN-025, RN-031, RN-032, RN-035, RN-040 | Must |
| P-004 | Avisos (bandeja) | Apoderado | HU-009 | RN-022, RN-023 | Must |
| P-005 | Justificar falta | Apoderado | HU-010 | RN-026 a RN-030, RN-035, RN-039 | Should |
| P-006 | Mis justificaciones | Apoderado | HU-011 | RN-031 | Should |
| P-007 | Cuenta | Apoderado | HU-003, HU-013 | RN-005, RN-006, RN-038 | Should (cerrar sesión: Must) |

Todas las HUs Must tienen al menos una pantalla. HU-007 y HU-008 (avisos push) se ven en la notificación del sistema y en P-002 y P-004.

## 2. User flows

**Flow 1 · Primer ingreso**
P-001 Login → escribir correo y contraseña → "Ingresar" → diálogo del sistema "Permitir notificaciones" → P-002 Inicio.

**Flow 2 · Aviso de entrada con la app cerrada**
Push "Valeria ingresó a las 07:41" → tocar → P-003 Historial de Valeria, mes actual, día de hoy resaltado → botón atrás → P-002 Inicio.

**Flow 3 · Revisar el día con la app abierta**
P-002 Inicio → llega push de tardanza → tarjeta de Valeria cambia a "Tardanza · 7 min" (≤ 2 s) → tocar tarjeta → P-003 Historial.

**Flow 4 · Justificar una falta**
P-002 Inicio → tarjeta "No registra entrada" → "Justificar" → P-005 (fecha ya elegida) → escribir motivo → "Adjuntar sustento" → "Enviar" → confirmación → P-003 con el día "En revisión".
Alternativo: P-003 → tocar un día "No registra entrada" → "Justificar" → P-005.

**Flow 5 · Bandeja de avisos**
Barra inferior "Avisos" (con contador) → P-004 → tocar aviso no leído → se marca leído → P-003 en el día del aviso.

**Flow 6 · Descargar el reporte**
P-003 → elegir mes con las flechas → "Descargar PDF" → hoja de compartir del sistema → volver a P-003.

**Flow 7 · Cerrar sesión**
Barra inferior "Cuenta" → P-007 → "Cerrar sesión" → confirmar → P-001.

**Validación de flujos:** no hay callejones sin salida (toda pantalla secundaria tiene "atrás" o vuelve a la barra inferior); todas las HUs Must están cubiertas por los flujos 1, 2, 3, 5 y 7.

## 3. Screen specs

### P-001 · Login
- **Datos:** ninguno.
- **Acciones:** escribir correo, escribir contraseña (con botón mostrar/ocultar), "Ingresar".
- **Estados:** cargando (botón con spinner), error 401, bloqueo 429 con cuenta regresiva, sesión expirada (mensaje arriba).
```
[Logo del colegio + "AsistQR Familia"]
[Texto] "Ingresa con el correo que registraste en secretaría"
[Campo] Correo electrónico
[Campo] Contraseña  [ojo]
[Mensaje de error en rojo, si aplica]
[Botón primario ancho completo] Ingresar
[Texto pequeño] "¿Olvidaste tu contraseña? Comunícate con secretaría."
```

### P-002 · Inicio
- **Datos:** hijos (API-03); estado de hoy por hijo (API-04); contador de no leídos (API-11).
- **Acciones:** tocar tarjeta → P-003; "Justificar" en tarjeta con "No registra entrada" → P-005; deslizar hacia abajo para actualizar.
- **Estados:** cargando (tarjetas esqueleto), sin hijos (RN-008, con botón "Cerrar sesión" y sin barra inferior), sin conexión (RN-012), sin fila de hoy ("Sin marca aún", RN-033), fin de semana ("Hoy no hay clases", RN-034), permiso de notificaciones negado (franja con "Activar", RN-037).
```
[Barra superior] "Hola, Rosa" ............ [campana con contador 3]
[Texto] "Hoy, viernes 13/11 · 08:35"
(tarjetas ordenadas por grado, HU-004)
[Tarjeta hijo]
   Lucía Quispe · 1.° A
   [Chip rojo ! No registra entrada]
   [Botón secundario] Justificar
[Tarjeta hijo]
   Valeria Quispe · 2.° B
   [Chip verde ✓ Puntual]
   Entrada 07:41   ·   Salida —
[Tarjeta hijo]
   Mateo Quispe · 5.° A
   [Chip ámbar ⏱ Tardanza · 7 min]
   Entrada 07:52   ·   Salida —
[Barra inferior] Inicio | Historial | Avisos (3) | Cuenta
```

### P-003 · Historial del mes
- **Datos:** días del mes (API-04); justificaciones del hijo (API-13) para mostrar "En revisión".
- **Acciones:** cambiar hijo (selector, oculto si hay 1); mes anterior/siguiente (siguiente deshabilitado en el mes actual); tocar día "No registra entrada" → P-005; "Descargar PDF"; "Mis justificaciones" → P-006.
- **Estados:** cargando, mes sin días escolares registrados ("Sin registros este mes"), sin conexión.
```
[Barra superior] ← Historial ............ [ícono PDF]
[Selector de hijo] Lucía | Valeria | Mateo
[‹  Noviembre 2026  ›(deshabilitado)]
[Resumen] 8 Puntual · 1 Tardanza · 1 No registra · 0 Justificada
[Lista]
  Vie 13/11  [✓ Puntual]          07:41 – —
  Jue 12/11  [⏱ Tardanza · 7 min] 07:52 – 14:02   (resaltado si viene de un aviso)
  Mié 11/11  [✓ Puntual]          07:38 – 14:04
  Mar 10/11  [✎ En revisión]        (justificación pendiente, RN-035)
  Lun 09/11  [✓ Puntual]          07:36 – 14:03
  ...
[Enlace] Mis justificaciones
```

### P-004 · Avisos
- **Datos:** avisos (API-11), páginas de 20.
- **Acciones:** tocar aviso → marcar leído (API-12) → P-003 en el día; bajar para cargar más.
- **Estados:** cargando, vacío ("Aún no tienes avisos"), sin conexión.
```
[Barra superior] Avisos
[Grupo] Hoy
  ● [!] Lucía no registra entrada hoy (13/11)                         08:30
  ● [⏱] Mateo llegó tarde: ingresó a las 07:52 (7 min de tardanza)   07:52
  ● [✓] Valeria ingresó a las 07:41                                   07:41
[Grupo] Ayer
    [→] Valeria salió a las 14:02                                    14:02
    [⏱] Valeria llegó tarde: ingresó a las 07:52 (7 min de tardanza)  07:52
[Grupo] Mié 11/11
    [✓] Valeria ingresó a las 07:38 (registrado sin conexión)        07:38
(● = no leído, fondo resaltado y texto en negrita)
```

### P-005 · Justificar falta
- **Datos:** hijo y fecha recibidos por la ruta; días elegibles = días con `ABSENT` o `NOT_RECORDED` hasta hoy.
- **Acciones:** elegir hijo y fecha (solo elegibles), escribir motivo (contador 0/500), "Adjuntar sustento" (cámara, galería o PDF), quitar adjunto, "Enviar".
- **Estados:** enviando (botón con spinner y campos bloqueados), error de archivo (> 5 MB o tipo no permitido), 409 (ya hay una pendiente), éxito.
```
[Barra superior] ← Justificar falta
[Campo] Estudiante: Valeria Quispe
[Lista] Fecha: mar 10/11/2026  (solo días sin entrada, no futuros y sin justificación pendiente)
[Área de texto] Motivo (mínimo 10 caracteres)           34/500
[Botón secundario] 📎 Adjuntar sustento (foto o PDF, máx. 5 MB)
[Miniatura] certificado.jpg · 2.1 MB   [x]
[Texto] "El tutor revisará tu justificación."
[Botón primario] Enviar
```

### P-006 · Mis justificaciones
- **Datos:** justificaciones propias (API-13).
- **Acciones:** tocar una → P-003 en ese día.
- **Estados:** vacío ("No has enviado justificaciones"), cargando, sin conexión.
```
[Barra superior] ← Mis justificaciones
[Item] Valeria · mar 10/11 · "Cita médica en EsSalud…" [En revisión]
[Item] Mateo · mié 04/11 · "Viaje familiar a Huancayo" [Rechazada]  [Justificar de nuevo]
[Item] Valeria · vie 23/10 · "Fiebre, descanso médico…" [Aprobada]
```

### P-007 · Cuenta
- **Datos:** correo de la sesión.
- **Acciones:** "Cambiar contraseña" (formulario en la misma pantalla: actual, nueva, repetir); "Mis justificaciones" → P-006; "Cerrar sesión" con confirmación.
```
[Barra superior] Cuenta
[Texto] rosa.quispe@mail.com
[Sección] Cambiar contraseña
   [Campo] Contraseña actual
   [Campo] Nueva contraseña (12 a 128 caracteres)
   [Campo] Repite la nueva contraseña
   [Botón] Guardar
[Item] Mis justificaciones  ›
[Botón texto rojo] Cerrar sesión
[Texto pequeño] Versión 1.0.0
```

## 4. Design tokens

| Token | Valor | Uso |
|---|---|---|
| `color.primary` | `#1E5AA8` | Botones primarios, barra inferior activa |
| `color.onPrimary` | `#FFFFFF` | Texto sobre primario (contraste 6.8:1) |
| `color.background` | `#F6F7F9` | Fondo de pantallas |
| `color.surface` | `#FFFFFF` | Tarjetas |
| `color.text` | `#1C1F24` | Texto principal |
| `color.textMuted` | `#5B6270` | Texto secundario (contraste 6.1:1 sobre blanco) |
| `status.onTime` | texto `#14612E` sobre `#E3F4E8` · ícono ✓ | Puntual |
| `status.late` | texto `#7A4300` sobre `#FDF0DC` · ícono ⏱ | Tardanza |
| `status.absent` | texto `#9B1C1C` sobre `#FCE6E6` · ícono ! | No registra entrada |
| `status.justified` | texto `#4B3696` sobre `#ECE8F8` · ícono ✎ | Falta justificada |
| `status.review` | texto `#1E4F8F` sobre `#E4EEFA` · ícono ✎ | En revisión (contraste 6.97:1) |
| `status.pending` | texto `#3D4450` sobre `#ECEEF1` · ícono ○ | Sin marca aún |
| `font.family` | Roboto (Android) / SF Pro (iOS) — fuente del sistema | Todo |
| `font.title` | 22 sp, peso 600 | Títulos de pantalla |
| `font.body` | 16 sp, peso 400 | Texto general |
| `font.label` | 14 sp, peso 500 | Chips, etiquetas (mínimo permitido) |
| `space.unit` | 8 dp (márgenes 16 dp) | Espaciado |
| `radius.card` | 12 dp | Tarjetas y chips |
| `size.touch` | 48 × 48 dp mínimo | Botones, ítems de lista |

Todos los pares texto/fondo de estado tienen contraste entre 6.6:1 y 8.4:1, por encima del 4.5:1 exigido (PRINCIPIO-007; calculado con la fórmula WCAG 2.1) y cada estado lleva ícono y texto, no solo color.

## 5. Navigation map

```
P-001 Login
  └── (sesión válida) ──▶ Barra inferior
                            ├── P-002 Inicio
                            │     ├── tarjeta ──▶ P-003 Historial
                            │     └── "Justificar" ──▶ P-005 Justificar
                            ├── P-003 Historial
                            │     ├── día "No registra entrada" ──▶ P-005
                            │     ├── "Descargar PDF" ──▶ hoja de compartir (sistema)
                            │     └── "Mis justificaciones" ──▶ P-006
                            ├── P-004 Avisos ──▶ aviso ──▶ P-003 (día resaltado)
                            └── P-007 Cuenta
                                  ├── "Mis justificaciones" ──▶ P-006 ──▶ "Justificar de nuevo" ──▶ P-005
                                  └── "Cerrar sesión" ──▶ P-001
Push del sistema ──▶ P-003 (día del aviso)
```
