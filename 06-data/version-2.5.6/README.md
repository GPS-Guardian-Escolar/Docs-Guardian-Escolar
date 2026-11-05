# Base de datos — delta 2.5.6

**Base de código:** tag `final-delivery-2026` del repositorio DB.
**Estado:** inspección estática de migración; no aplicada en esta revisión.

La migración `01_ddl/04_alter/014_preferred_student_route.sql` agrega `is_preferred` a la relación de rutas de estudiantes, promueve una ruta activa existente cuando corresponde y crea el índice parcial único `uq_student_routes_preferred_active` para evitar múltiples rutas activas preferidas por estudiante. Su rollback está en `05_rollbacks/01_ddl/04_alter/014_preferred_student_route.rollback.sql`.

Antes de aplicar en un entorno, respaldar la base, comprobar estado de Liquibase, confirmar asignaciones resultantes y desplegar primero en validación. Antes del rollback, identificar consumidores de la columna y preservar los datos necesarios. Este documento no afirma que la migración se haya aplicado ni probado en una base desplegada.

La descripción de privacidad y retención permanece en [2.5.4](../version-2.5.4/datos-privacidad-retencion.md); debe revisarse y aprobarse con el responsable correspondiente antes de tratar datos reales de menores.
