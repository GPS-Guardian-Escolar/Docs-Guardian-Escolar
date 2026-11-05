# Informe de revisión documental 2026-11-04

## Alcance y fechas

Este informe acompaña el suplemento operativo de Render guardado en `10-devops/version-2.5.6/`. El paquete conserva el corte de evidencias del 4 de noviembre de 2026; las referencias de GitHub se actualizaron el 5 de noviembre de 2026. El contenido describe una secuencia histórica de despliegue y un procedimiento de operación. No es un certificado del estado actual de producción.

## Archivos incluidos

| Archivo | Revisión | SHA-256 |
| --- | --- | --- |
| `Informe_Cronologico_Render_GPS_Guardian_Escolar_Con_Mas_Evidencias.docx` | 18 imágenes referenciadas; narrativa de despliegue, incidencias y evidencias históricas. | `6A475C2C2A057A86B38168D592A23C4C322C418EBDA89474774BAAC5334DE47B` |
| `Procedimiento_Despliegue_GPS_Guardian_Escolar (3).docx` | Runbook de despliegue, verificación, respaldo, rollback y cierre. | `7292271857CE4A7D139B8C1F7C80BD3AE522B8F0870DAD13F15A8309DAD61F19` |
| `README.md` | Índice del paquete, referencias de rama y advertencias de alcance. | Archivo actualizado en esta revisión. |

## Referencias de GitHub

Se contrastaron las puntas de las ramas remotas. Estos SHA identifican commits de repositorio; no prueban qué commit está ejecutándose en Render.

| Repositorio | Rama | SHA observado el 2026-11-05 |
| --- | --- | --- |
| `Backend-Guardian-Escolar` | `dev` | `e53883c6b21f5072a0283b1fb9e7501c2a36b33c` |
| `Fronted-GuardianEscolar-` | `develop` | `aa508417d8329f1d86f4e584b14b6027f90a72af` |
| `DB-Structure-Guardian-Escolar` | `dev` | `513cbff76d6d9b07decdb96342aa85abebbedf29` |
| `Docker-Infra-Guardian-Escolar` | `dev` | `671021b93b0fb4e62302dc68c2ae67388b8c2908` |
| `Docs-Guardian-Escolar` | `main` | `35f65f40b0e9aaec836da39ab908d5c508f57d07` |

## Hallazgos y límites

- El informe cronológico describe principalmente sucesos del 15 y 16 de octubre de 2026, con material reorganizado después. Las capturas son evidencia histórica, no una comprobación en vivo.
- El procedimiento presenta pasos que deben ejecutarse y registrarse durante una ventana de despliegue. Su presencia en el documento no significa que se hayan ejecutado.
- El informe cronológico incluye datos identificables de un estudiante y de un contacto de emergencia, un código QR y el nombre de host interno de PostgreSQL. Los DOCX se conservaron sin cambios. Anonimizar las capturas antes de fusionar el PR o redistribuir los archivos.
- No se comprobó el panel, los logs ni el estado en vivo de Render. Tampoco se ejecutaron pruebas de aplicación o migraciones.
- No se volvió a renderizar visualmente el DOCX en esta revisión. Antes de aprobar el PR, revisar paginación, legibilidad de capturas y metadatos en Word.

## Integración documental

El suplemento 2.5.6 amplía la documentación operativa de Render y conserva intacto el paquete de entregables `14-deliverables/version-2.5.5/`, el manual `10-devops/version-2.5.4/` y los diagramas de `08-uml/`. El README raíz enlaza este directorio. La integración queda sujeta a revisión independiente y a los checks requeridos por la gobernanza de PR.
