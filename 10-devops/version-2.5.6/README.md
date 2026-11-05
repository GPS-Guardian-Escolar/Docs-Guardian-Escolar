# GPS Guardian Escolar — Despliegue Render versión 2.5.6

**Tipo:** documentación técnica de operación y memoria histórica.
**Fecha de corte de evidencias:** 4 de noviembre de 2026.
**Fecha de sincronización documental:** 5 de noviembre de 2026.
**Estado:** actualización documental; pendiente certificación operativa independiente.

## Documentos

1. [Memoria cronológica de Render](./Informe_Cronologico_Render_GPS_Guardian_Escolar_Con_Mas_Evidencias.docx). Documenta el proceso histórico y sus evidencias visuales; no certifica la disponibilidad actual del servicio.
2. [Procedimiento de despliegue y verificación](./Procedimiento_Despliegue_GPS_Guardian_Escolar%20(3).docx). Runbook operativo para preparar, desplegar, verificar y revertir cambios.
3. [Informe de revisión documental](./INFORME_REVISION_DOCUMENTAL_2026-11-04.md). Registra las fuentes, los checksums, el alcance de contraste y sus límites.

## Repositorios de referencia

| Repositorio | Rama | SHA de referencia |
| --- | --- | --- |
| `Backend-Guardian-Escolar` | `dev` | `e53883c6b21f5072a0283b1fb9e7501c2a36b33c` |
| `Fronted-GuardianEscolar-` | `develop` | `aa508417d8329f1d86f4e584b14b6027f90a72af` |
| `DB-Structure-Guardian-Escolar` | `dev` | `513cbff76d6d9b07decdb96342aa85abebbedf29` |
| `Docker-Infra-Guardian-Escolar` | `dev` | `671021b93b0fb4e62302dc68c2ae67388b8c2908` |
| `Docs-Guardian-Escolar` | `main` | `35f65f40b0e9aaec836da39ab908d5c508f57d07` |

Los SHA corresponden a las puntas de las ramas remotas observadas el 5 de noviembre de 2026. La presencia de un commit en GitHub **no demuestra** su publicación en Render, ni la ejecución de pruebas o la integridad de la base externa. Esas verificaciones deben adjuntarse al acta de un despliegue concreto.

## Observaciones de publicación

El informe cronológico contiene capturas con datos identificables de un estudiante y un contacto de emergencia, un código QR y el nombre de host interno de PostgreSQL. Los documentos se conservaron sin modificar; revisar y anonimizar esas capturas antes de fusionar el PR o redistribuir el paquete.

La composición visual de los DOCX no se volvió a renderizar durante esta revisión. Confirmar paginación, legibilidad de capturas y metadatos en Word antes de aprobar el PR.

## Integración con la documentación actual

Este directorio es un suplemento especializado de Render; no sustituye el paquete de entregables `14-deliverables/version-2.5.5/`, el manual en `10-devops/version-2.5.4/` ni los diagramas de `08-uml/`. Esta actualización se entrega mediante PR para revisión independiente, de acuerdo con `00-governance/pull-request-governance.md`.
