---
title: "Acciones correctivas, preventivas y de mejora"
subtitle: "Proyecto formativo · GPS Guardián Escolar"
author: "Equipo del proyecto GPS Guardián Escolar"
date: "5 de noviembre de 2026"
lang: es-CO
---

**Versión documental:** 2.5.7 · **Estado:** versión editable preparada para subida y revisión de QA/arquitectura.

## Tabla de contenido

1. Resumen
2. 1. Método de seguimiento y prioridades
3. 2. Matriz de acciones — corte 2026-11-05
4. 3. Fichas de análisis de causa raíz
5. 4. Evidencias requeridas para cierre
6. 5. Flujo CAPA propuesto
7. 6. Indicadores de mejora (a medir)
8. 7. Estado documental
9. Referencias y trazabilidad (APA 7.ª edición)


> **Corte y alcance de evidencia.** Revisión documental 2.5.7, 5 de noviembre de 2026. La descripción del **release de referencia** utiliza los commits de `main` observados en Backend (`1022058d`), Frontend (`3d9f59fa`), DB (`9278b1dc`) e Infra (`131e9895`). Se inspeccionaron además las ramas `dev`/`develop` más recientes y los suplementos documentales 2.5.6, pero **sus cambios no se consideran integrados a producción por esa sola razón**. La revisión es estática: no se ejecutaron en esta sesión pruebas, despliegues ni validaciones en teléfono. Los resultados operativos requieren evidencia real. 


## Resumen

Registro de problemas, causas potenciales, acciones, responsables por rol, evidencias y criterios de cierre. Las acciones abiertas se consideran **propuestas para verificación**, no pruebas de defecto actualmente presente en producción. No se asignan nombres personales ni fechas prometidas sin acuerdo del equipo.

## 1. Método de seguimiento y prioridades

**Correctiva (C):** elimina causa de una no conformidad comprobada. **Preventiva (P):** reduce riesgo antes del incidente. **Mejora (M):** incrementa calidad/eficacia sin depender de fallo. Cada acción debe vincularse a issue/PR, evidencia técnica, control independiente de QA, responsable y fecha aprobada. Escala de prioridad: crítica cuando expone datos de menores o daña datos; alta si impide servicio o contrato; media si afecta experiencia y mantenibilidad.

## 2. Matriz de acciones — corte 2026-11-05

| ID | Tipo | Hallazgo/riesgo y causa por confirmar | Acción y entregable | Prioridad | Rol sugerido | Estado |
|---|---|---|---|---|---|---|
| AC-01 | P | `dev/develop`, `qa` y `main` divergen | Comparar PR y aprobar corte de release por SHA | Alta | Arquitectura + QA | Abierta |
| AC-02 | C | Manual anterior cita PATCH estado no expuesto | Corregir cliente/contrato o retirar flujo no implementado | Alta | Backend + Frontend | Abierta |
| AC-03 | P | Esquema puede diferir de ORM | Verificar Liquibase 014 y `ddl-auto: validate` en QA | Alta | BD + Backend | Abierta |
| AC-04 | P | Credenciales PostgreSQL privilegiadas | Probar rol `gps_guardian_app`, rotación y mínimo privilegio | Crítica | Infra + BD | Abierta |
| AC-05 | P | Manejo de menores y códigos QR | Ensayar expiración, desuso, otra cuenta y revocación | Crítica | Seguridad + QA | Abierta |
| AC-06 | M | Vistas UML históricas desfasadas | Regenerar secuencia/estados y ER con migración 014 | Media | Arquitectura | Abierta |
| AC-07 | P | GPS fondo dependiente de SO/OEM | Laboratorio Android con permiso, batería, cortes y logs | Alta | Frontend + QA | Abierta |
| AC-08 | P | Ubicación obsoleta sin prueba final | Ensayar 120 s/30 s, aviso y desduplicación | Alta | Backend + QA | Abierta |
| AC-09 | P | Push/correo/Maps sujetos a configuración | Prueba extremo a extremo y registro de proveedores/cuotas | Alta | Integraciones + QA | Abierta |
| AC-10 | M | Pruebas dispersas y sin acta por release | Unificar CI, resultados, reportes y matriz TP-xx | Alta | QA + DevOps | Abierta |
| AC-11 | P | Restauración no comprobada | Ensayar backup/restore y rollback seguro | Crítica | Infra + BD | Abierta |
| AC-12 | M | Publicación de artefactos sin trazabilidad | Adjuntar ZIP/APK aprobados cuando existan y SHA-256 | Media | Release | Abierta |
| AC-13 | M | Riesgo de visualización móvil incorrecta | Probar responsive del registro, términos y QR manual | Media | UX/Frontend | Abierta |
| AC-14 | P | Desfase documental 2.5.5/2.5.6 y ramas | PR único Docs con manifiesto, referencias y revisión | Alta | Documentación + QA | Abierta |

## 3. Fichas de análisis de causa raíz

**AC-01 Divergencia.** Hecho: GitHub reportó ramas divergentes en los cuatro repositorios de código. Hipótesis a confirmar: promociones por cherry-pick/merge selectivo, reversión o historiales no lineales. **Acción:** comparar commits y archivos, decidir origen de verdad y probar integración antes de release. **No usar:** `reset --hard`/force-push para “igualar” ambientes sin autorización.

**AC-02 Contrato de estado.** Hecho: el `TripController.java` del SHA `1022058d` expone GET/POST/DELETE y coordenadas, pero no PATCH de estado. Posible causa: documentación heredada o cliente desfasado. **Acción:** inspeccionar UI/servicio y decidir implementar endpoint con pruebas y autorización, o adaptar la UI/manual a la baja lógica existente. Cierre exige trazas API y prueba E2E, no solo corrección textual.

**AC-07/AC-08 Continuidad GPS.** Hecho: hay código de ubicación y detector de obsolescencia; no se aportó resultado en Android físico. Causas posibles: permiso, ahorro energético, conectividad o programación de tareas. **Acción:** prueba controlada de red/permisos con última posición y timestamps. Evitar representar coordenada antigua como ubicación actual.

**AC-11 Recuperación.** Riesgo: actualizaciones de esquema o imágenes sin respaldo restaurable. **Acción:** simular restauración a QA y documentar RPO/RTO medidos; no usar comandos que eliminen volúmenes para resolver errores del stack.

## 4. Evidencias requeridas para cierre

| Grupo | Criterios medibles de cierre |
|---|---|
| Código/seguridad | PR aprobado, tests con SHA, rechazo de rol indebido, logs sin secreto |
| Base de datos | Migración aplicada en BD de ensayo, índice y roles confirmados, rollback revisado |
| Cliente Android | Captura real, modelo/SO, build, permisos, red y recepción de eventos |
| Operación | Salida de `docker compose ps`, health, backup restaurado, reporte del despliegue |
| Documentación | Archivos DOCX/PDF/MD revisados, diagramas fieles y enlaces a SHA |

## 5. Flujo CAPA propuesto

1. Registrar hallazgo con fecha, ambiente, pasos y evidencia.
2. Clasificar severidad, urgencia y alcance; proteger datos.
3. Analizar causa mediante logs, cambios, configuración y comparación de ambientes.
4. Asignar dueño y fecha límite acordados, desarrollar corrección en rama `fix/*`.
5. Ejecutar pruebas previas y nuevas de regresión; adjuntar reporte del mismo commit.
6. Revisión de arquitectura y QA, PR a rama de trabajo, promoción controlada.
7. Verificar en ambiente destino y cerrar solo con resultado reproducible.
8. Actualizar documentación, indicadores y aprendizaje para evitar recurrencia.

## 6. Indicadores de mejora (a medir)

Tasa de defectos reabiertos; porcentaje de TP ejecutados/aprobados; tiempo de cierre por prioridad; restauraciones ensayadas; desfase medio entre corrección y documentación; cantidad de incidentes por dependencia externa; cobertura del pipeline por release. **Sin resultados numéricos inventados:** completar únicamente desde tablero/CI con periodo y denominador explícito.

## 7. Estado documental

Esta edición incorpora hallazgos de revisión estática y alimenta un backlog de seguimiento. Las acciones permanecen abiertas hasta que el equipo adjunte pruebas de ejecución, decisiones firmadas y referencias de PR. El pitch localizado en el ZIP puede anexarse como material de divulgación, no como prueba de software operacional.

## Referencias y trazabilidad (APA 7.ª edición)

- GPS Guardian Escolar. (2026a). *Backend-Guardian-Escolar* [Código fuente, rama main, commit `1022058d`]. GitHub. https://github.com/GPS-Guardian-Escolar/Backend-Guardian-Escolar/tree/1022058d545a9e7936edbff75950034ec4f98e88
- GPS Guardian Escolar. (2026b). *Fronted-GuardianEscolar-* [Código fuente, rama main, commit `3d9f59fa`]. GitHub. https://github.com/GPS-Guardian-Escolar/Fronted-GuardianEscolar-/tree/3d9f59fa3e91b5ecc5b8ae86591187546b966668
- GPS Guardian Escolar. (2026c). *DB-Structure-Guardian-Escolar* [Migraciones SQL, rama main, commit `9278b1dc`]. GitHub. https://github.com/GPS-Guardian-Escolar/DB-Structure-Guardian-Escolar/tree/9278b1dcb224301536d6158357f74daee74b20ba
- GPS Guardian Escolar. (2026d). *Docker-Infra-Guardian-Escolar* [Configuración de despliegue, rama main, commit `131e9895`]. GitHub. https://github.com/GPS-Guardian-Escolar/Docker-Infra-Guardian-Escolar/tree/131e98954430e95e9108174eded9896b3f0d5272
- GPS Guardian Escolar. (2026e). *Docs-Guardian-Escolar: entregables 2.5.5 y suplementos 2.5.6* [Documentación del proyecto]. GitHub. https://github.com/GPS-Guardian-Escolar/Docs-Guardian-Escolar

**Fuentes primarias relevantes:** `TripController.java`, `TripService.java`, `StudentController.java`, `LocationAvailabilityService.java`, `WebSocketConfig.java`, `application.yml`, `package.json`, `docker-compose.yaml` y `01_ddl/04_alter/014_preferred_student_route.sql`, según el documento. Las referencias a rutas son comprobables en los repositorios indicados.
