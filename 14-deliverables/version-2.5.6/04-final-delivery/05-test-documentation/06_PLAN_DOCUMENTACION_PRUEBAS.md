---
title: "Plan y documentación de pruebas de software"
subtitle: "Proyecto formativo · GPS Guardián Escolar"
author: "Equipo del proyecto GPS Guardián Escolar"
date: "5 de noviembre de 2026"
lang: es-CO
---

**Versión documental:** 2.5.7 · **Estado:** versión editable preparada para subida y revisión de QA/arquitectura.

## Tabla de contenido

1. Resumen y declaración de resultados
2. 1. Alcance y niveles
3. 2. Criterios de entrada y salida
4. 3. Matriz de casos de prueba
5. 4. Procedimientos automatizados
6. 5. Pasos manuales esenciales
7. 6. Plantilla de acta de ejecución por caso
8. 7. Prioridades y gestión de defectos
9. 8. Evidencia histórica y limitaciones
10. Referencias y trazabilidad (APA 7.ª edición)


> **Corte y alcance de evidencia.** Revisión documental 2.5.7, 5 de noviembre de 2026. La descripción del **release de referencia** utiliza los commits de `main` observados en Backend (`1022058d`), Frontend (`3d9f59fa`), DB (`9278b1dc`) e Infra (`131e9895`). Se inspeccionaron además las ramas `dev`/`develop` más recientes y los suplementos documentales 2.5.6, pero **sus cambios no se consideran integrados a producción por esa sola razón**. La revisión es estática: no se ejecutaron en esta sesión pruebas, despliegues ni validaciones en teléfono. Los resultados operativos requieren evidencia real. 


## Resumen y declaración de resultados

Este documento contiene la estrategia, matriz de escenarios, pasos de reproducción, criterios de aceptación y plantilla de evidencias. **Estado de ejecución al 5 de noviembre de 2026: NO EJECUTADO en esta revisión.** La existencia de clases `*Test.java` y scripts `npm test` se considera evidencia de infraestructura de pruebas, no un resultado satisfactorio. Ninguna casilla debe cambiar a «Aprobado» sin logs originales, SHA y ambiente.

## 1. Alcance y niveles

La estrategia abarca: pruebas unitarias de validaciones de negocio; integración API↔BD y permisos; contratos REST/STOMP; funcionales de interfaz; escenarios Android real (cámara, GPS, ahorro de batería); seguridad de accesos; pruebas de humo de despliegue; respaldo/recuperación; accesibilidad y rendimiento bajo carga definida. Datos de ensayo sintéticos y usuarios con roles separados para evitar exposición de menores.

**Entorno mínimo de prueba reproducible:** commits fijados de Backend, Frontend, DB e Infra; `PostgreSQL 15`; Java 17; Node compatible con Expo 57; Docker Compose; credenciales de prueba; configuración de servicios externos registrada sin secretos. Cada ejecución debe documentar quién, cuándo, qué versión, dónde y con qué resultado.

## 2. Criterios de entrada y salida

| Entrada | Salida |
|---|---|
| Fuentes y dependencias fijadas en SHA, variables configuradas, BD migrada y datos de prueba | Compilaciones y pruebas automatizadas exitosas, defectos críticos cerrados y evidencias conservadas |
| Casos trazados a requisitos, rutas API y responsables | Sin defectos bloqueantes abiertos o con excepción aprobada por QA |
| Dispositivo Android identificado para pruebas nativas | QR, background GPS y push con resultados reproducibles y permisos registrados |
| Copia de respaldo previa a migraciones | Restauración/rollback ensayados o riesgo explícitamente aceptado |

## 3. Matriz de casos de prueba

| ID | Nivel | Escenario / entrada | Criterio esperado | Estado |
|---|---|---|---|---|
| TP-01 | API | Registro con datos válidos | Usuario creado sin exponer token | Pendiente |
| TP-02 | API | Login válido / inválido | Token solo para válido; error controlado | Pendiente |
| TP-03 | Seguridad | Intentos repetidos / dispositivo desconocido | Límite, confirmación o bloqueo configurado | Pendiente |
| TP-04 | Integración | Confirmar email y 2FA | Estado y sesión coherentes | Pendiente |
| TP-05 | API | Estudiante: crear/listar 0, 1, última página | Datos aislados y paginación correcta | Pendiente |
| TP-06 | Seguridad | Usuario ajeno consulta estudiante | Denegación, sin datos privados | Pendiente |
| TP-07 | API/DB | Asignar y cambiar ruta preferida | Solo una preferida activa por estudiante | Pendiente |
| TP-08 | Integración | Crear viaje sin routeId con preferida activa | Usa preferida; responde 201 | Pendiente |
| TP-09 | Integración | Varias rutas sin preferida | Rechaza y solicita selección | Pendiente |
| TP-10 | Integración | Viaje activo duplicado | No genera otro `IN_PROGRESS` | Pendiente |
| TP-11 | API | Coordenada de viaje activo | Persiste, responde y emite STOMP | Pendiente |
| TP-12 | Seguridad | Coordenada para estudiante ajeno | Rechazo por vínculo/permisos | Pendiente |
| TP-13 | Negativo | Salto GPS imposible | No guarda coordenada falsa | Pendiente |
| TP-14 | API | `DELETE /api/trips/{tripId}` activo | CANCELED, `trip_ended_at`, `deleted_at` | Pendiente |
| TP-15 | Seguridad | `DELETE` sin rol/propiedad | 403; viaje íntegro | Pendiente |
| TP-16 | Tiempo | GPS 119/120/121 segundos sin novedad | Notificación según umbral; sin duplicados | Pendiente |
| TP-17 | Cliente | QR válido/expirado/reutilizado | Vínculo solo válido; error legible | Pendiente |
| TP-18 | Android | Ubicación fondo/foreground/batería | Observación registrada en modelo/build | Pendiente |
| TP-19 | Integración | Expo Push/mail habilitados | Recibo de proveedor y dispositivo | Pendiente |
| TP-20 | API | Listado de coordenadas con `page/size` | Metadatos y límites consistentes | Pendiente |
| TP-21 | Infra | Arranque PostgreSQL→Liquibase→API→UI | Contenedores y healthchecks sanos | Pendiente |
| TP-22 | DevOps | Backup/restore y rollback | Restauración verificable sin pérdida no aceptada | Pendiente |
| TP-23 | UX | Registro en pantalla angosta | Campos/términos navegables y accesibles | Pendiente |
| TP-24 | Contrato | Cliente llama solo endpoints existentes | Ausencia de llamadas a PATCH `/api/trips/.../status` no implementado | Pendiente |

## 4. Procedimientos automatizados

**Backend (Windows PowerShell):**

```powershell
cd Backend-Guardian-Escolar
.\mvnw.cmd --batch-mode clean verify
Get-ChildItem target\surefire-reports\*.txt | Get-Content | Select-String 'Tests run'
```

**Frontend:**

```powershell
cd Fronted-GuardianEscolar-
npm ci
npm test
npm run lint
npm run security:release
```

**Base / infraestructura:** revisar `docker compose config`, aplicar migraciones en base descartable, inspeccionar `DATABASECHANGELOG`, verificar constraints/roles y correr los workflows del repo. No asumir que un workflow verde de un commit anterior corresponde al SHA evaluado.

## 5. Pasos manuales esenciales

**TP-08:** crear estudiante ficticio, asociar dos rutas y designar una preferida; registrar ID y `student_routes`; enviar `POST /api/trips` sin `routeId`; comparar `routeId` devuelto. Repetir sin ruta preferida con dos rutas y confirmar rechazo. **TP-14:** crear viaje activo, inspeccionar `trips`, ejecutar DELETE con gestor autorizado y volver a consultar desde rol técnico; confirmar `CANCELED`, fin y baja lógica. Repetir desde cuenta ajena; no debe mutar.

**TP-16:** con un viaje activo, inyectar/ajustar datos de prueba o usar reloj controlado; observar 119, 120 y 121 s; ejecutar ciclo programado y revisar notificaciones creadas por usuario; volver a ejecutar y verificar desduplicación. **TP-18:** registrar Android, versión del SO, build EAS/APK, permisos concedidos, políticas de batería y red; capturar logs y pantalla de última posición al minimizar app.

## 6. Plantilla de acta de ejecución por caso

| Campo | Completar con evidencia real |
|---|---|
| ID y requisito | TP-___ / RF-___ |
| Fecha / hora / ejecutor | [a registrar] |
| Ambiente, URL y dispositivo | [a registrar] |
| Commit Backend/Frontend/DB/Infra | [SHA completos] |
| Datos precondición | [IDs ficticios y parámetros] |
| Request / pasos | [pasos precisos] |
| Resultado esperado / obtenido | [comparación objetiva] |
| Evidencia | [enlace CI, logs, video, captura, SQL] |
| Estado y defecto asociado | Pendiente / Aprobado / Fallido / Bloqueado; issue # |

## 7. Prioridades y gestión de defectos

**Bloqueante:** acceso a datos de menores ajenos, corrupción de migración, sesiones sin control, GPS engañoso sin aviso. **Alta:** ruta equivocada, falta de notificación de pérdida de señal, ruptura de vínculo. **Media:** inconsistencias de UI, traducciones, paginación o carga lenta. Cada defecto requiere reproducción mínima, compromiso de corrección y reejecución documentada. No resolver un caso solo cerrando issue o fusionando PR.

## 8. Evidencia histórica y limitaciones

Los repos contienen tests Spring/Jest y workflows de calidad. Esta edición no ejecutó esos comandos ni inspeccionó artefactos CI de este corte. El ZIP adjunto aporta diagramas y un pitch, pero no reemplaza trazas de prueba. No atribuir a estos documentos una tasa de aprobación, rendimiento, capturas actuales o funcionamiento real del hosting sin registros adjuntos.

## Referencias y trazabilidad (APA 7.ª edición)

- GPS Guardian Escolar. (2026a). *Backend-Guardian-Escolar* [Código fuente, rama main, commit `1022058d`]. GitHub. https://github.com/GPS-Guardian-Escolar/Backend-Guardian-Escolar/tree/1022058d545a9e7936edbff75950034ec4f98e88
- GPS Guardian Escolar. (2026b). *Fronted-GuardianEscolar-* [Código fuente, rama main, commit `3d9f59fa`]. GitHub. https://github.com/GPS-Guardian-Escolar/Fronted-GuardianEscolar-/tree/3d9f59fa3e91b5ecc5b8ae86591187546b966668
- GPS Guardian Escolar. (2026c). *DB-Structure-Guardian-Escolar* [Migraciones SQL, rama main, commit `9278b1dc`]. GitHub. https://github.com/GPS-Guardian-Escolar/DB-Structure-Guardian-Escolar/tree/9278b1dcb224301536d6158357f74daee74b20ba
- GPS Guardian Escolar. (2026d). *Docker-Infra-Guardian-Escolar* [Configuración de despliegue, rama main, commit `131e9895`]. GitHub. https://github.com/GPS-Guardian-Escolar/Docker-Infra-Guardian-Escolar/tree/131e98954430e95e9108174eded9896b3f0d5272
- GPS Guardian Escolar. (2026e). *Docs-Guardian-Escolar: entregables 2.5.5 y suplementos 2.5.6* [Documentación del proyecto]. GitHub. https://github.com/GPS-Guardian-Escolar/Docs-Guardian-Escolar

**Fuentes primarias relevantes:** `TripController.java`, `TripService.java`, `StudentController.java`, `LocationAvailabilityService.java`, `WebSocketConfig.java`, `application.yml`, `package.json`, `docker-compose.yaml` y `01_ddl/04_alter/014_preferred_student_route.sql`, según el documento. Las referencias a rutas son comprobables en los repositorios indicados.
