# Documentación de acciones correctivas, preventivas y de mejoramiento del software

**Proyecto:** GPS Guardian Escolar<br>
**Versión:** 2026-10-28<br>
**Estado:** Actualizada desde código y evidencia disponible al 28 de octubre de 2026


**Revisión técnica:** análisis estático de los repositorios al 28 de octubre de 2026.<br>
**Validación:** no se ejecutaron flujos de CI ni pruebas en dispositivos durante esta actualización.
---

## 1. Objetivo

Registrar de forma consolidada las acciones derivadas del contraste entre código, configuración y documentación, así como las medidas preventivas y de mejora prioritarias.

## 2. Clasificación

| Tipo | Definición usada en este documento |
|---|---|
| Correctiva | Corrige un defecto o inconsistencia ya identificada |
| Preventiva | Reduce la probabilidad de recurrencia o de fallo en despliegue |
| Mejoramiento | Incrementa calidad, mantenibilidad o verificabilidad sin ser un bug crítico |

## 3. Acciones correctivas identificadas

| ID | Hallazgo | Acción correctiva propuesta | Prioridad | Estado |
|---|---|---|---|---|
| AC-01 | Posible desalineación `GPS_DESVIO_UMBRAL_METROS` (.env) vs `GPS_ROUTE_DEVIATION_THRESHOLD_METERS` (YAML) | Unificar nombre de variable en backend y plantillas `.env` | Alta | Pendiente |
| AC-02 | Regex de email en BD limita TLD | Evaluar ampliar CHECK o validar solo en aplicación según política real de usuarios | Media | Pendiente |
| AC-03 | Nombre del repositorio frontend “Fronted” | Homogeneizar nombre en docs, Docker contexts y README para evitar rutas rotas | Baja | Pendiente |
| AC-04 | Documentación histórica no reflejaba stacks actuales (Boot 3.5.16, Expo 57, photo_data, radius_meters) | Paquete 2.5.4 actualizado desde código (este entregable) | Alta | En curso / cerrado con este paquete |
| AC-05 | Afirmaciones de “probado en dispositivo” sin evidencia en material analizado | Separar requisitos de evidencia de prueba; no declarar verificado lo no ejecutado | Alta | Aplicado en docs de pruebas |

## 4. Acciones preventivas

| ID | Riesgo | Acción preventiva | Responsable sugerido |
|---|---|---|---|
| AP-01 | Secretos en Git | Mantener `.env` en `.gitignore`; solo `.env.example` sin valores reales | Equipo backend/infra |
| AP-02 | Desfase Liquibase ↔ JPA | CI que ejecute migrate + arranque backend con `ddl-auto: validate` | Equipo datos/backend |
| AP-03 | Rotura de contratos API | Suite de pruebas de contrato sobre endpoints críticos (auth, students, trips, zones) | Equipo QA/backend |
| AP-04 | Fallos de CORS en demos | Documentar orígenes permitidos por entorno (local, LAN, tunnel, Docker) | Equipo frontend |
| AP-05 | Pérdida de datos en demos | Prohibir `docker compose down -v` en entornos con datos de prueba valiosos; backups periódicos | Equipo infra |

## 5. Acciones de mejoramiento

| ID | Mejora | Beneficio | Prioridad |
|---|---|---|---|
| AM-01 | Ampliar pruebas automatizadas más allá de las 3 unitarias reportadas en README backend | Detección temprana de regresiones | Alta |
| AM-02 | Pipeline CI (build backend, lint frontend, liquibase validate) | Reproducibilidad | Alta |
| AM-03 | Adjuntar evidencias de prueba en dispositivo real (QR, background location, push) | Cierre objetivo de RNF de movilidad | Alta |
| AM-04 | Sincronizar diagramas UML/Figma con endpoints y tablas actuales | Trazabilidad diseño–código | Media |
| AM-05 | Exponer y versionar OpenAPI exportada | Contrato estable para integradores | Media |
| AM-06 | Métricas de Actuator/Prometheus en tablero simple | Observabilidad básica | Baja |
| AM-07 | Guía de operación de mail/push/SMS al habilitar flags | Evitar demos “silenciosas” por configuración | Media |

## 6. Registro de cambios de producto ya reflejados en código (historial de mejora)

Estos elementos constan en el código analizado y se consideran mejoras ya incorporadas respecto a un sistema mínimo de tracking:

- Verificación de correo y 2FA.
- Vinculación de dispositivo por QR/enlace y tabla `student_devices`.
- Foto opcional del estudiante (`photo_data`).
- Zonas seguras con radio en metros e inactividad configurable.
- Proxy Maps con caché, rate limit y circuit breaker.
- Panel admin, auditoría y error logs.
- Docker Compose con Liquibase one-shot y frontend Nginx con proxy.

## 7. Procedimiento de gestión de una nueva no conformidad

1. Registrar en plantilla de defectos (documento de pruebas).<br>
2. Clasificar severidad y tipo (correctiva/preventiva/mejora).<br>
3. Asignar responsable y fecha objetivo.<br>
4. Verificar corrección con caso de prueba reproducible.<br>
5. Actualizar este registro y, si aplica, el SRS o el diseño.

## 8. Indicadores sugeridos (sin valores inventados)

- Número de defectos abiertos por severidad.
- Porcentaje de casos TP-* ejecutados en el último ciclo.
- Tiempo desde detección hasta cierre de AC de prioridad alta.
- Fallos de pipeline CI (cuando exista).

Los valores numéricos deben medirse en el proceso real; no se fabrican en este documento.

## 9. Cierre

Este documento consolida hallazgos del análisis estático del 2026-10-09. Debe actualizarse en cada iteración de pruebas o release.

## Actualización técnica — 28 de octubre de 2026

Cambios implementados en código: límite de intentos, gestión de sesiones, hash de tokens/códigos, control de acceso compartido, cambios de roles de BD y bootstrap explícito. Su aceptación permanece abierta hasta adjuntar evidencia reproducible. Siguen pendientes QR/GPS en dispositivo, batería, notificaciones, cold start, APK y políticas de datos de menores.
