# Producto 06 — Procedimiento de implementación: suplemento 2.5.6

**Base:** [Procedimiento 2.5.5](../../../version-2.5.5/Entregables/07_PROCEDIMIENTO_IMPLEMENTACION_SOFTWARE.docx).

1. Fijar cada repo en el commit de `final-delivery-2026`; no mezclar `dev`/`develop`.
2. Revisar variables y secretos; nunca guardar secretos en Git o evidencia pública.
3. Respaldar PostgreSQL y revisar estado Liquibase antes de migración 014.
4. Confirmar credenciales/proveedor de Maps, correo y push; revisar cuotas.
5. Validar en entorno de pruebas autenticación, vínculo, trayectos, historial, health checks y alertas antes de promover.
6. Registrar commits, artefactos, parámetros no secretos, resultado y responsable.

El esquema incluye rollback de la migración 014. Antes de usarlo, respaldar datos y revisar dependencias; regresar el servicio a artefacto/tag previo y verificar salud. No se afirma que despliegue o rollback se hayan ejecutado.
