# Datos, privacidad y retencion

**Relacion:** H-44, H-51, H-58 y H-59

## Reglas tecnicas propuestas

- Usar `TIMESTAMPTZ` para auditoria, notificaciones y coordenadas.
- Indexar consultas por `notification_receipts(user_id)`, `notifications(safe_zone_id)` y `coordinates(trip_id, recorded_at)`.
- Consultar coordenadas por ventana de tiempo y paginacion; no devolver el historico completo por defecto.
- Separar fotografia del estudiante en una tabla o almacenamiento de objetos con URL controlada, en lugar de incluir base64 en cada respuesta de estudiante.
- Definir una retencion inicial de 90 dias para coordenadas, sujeta a aprobacion legal y operativa.

## Datos de menores

El sistema debe identificar al representante legal, registrar consentimiento verificable, finalidad, fecha de inicio, fecha de expiracion y mecanismo de consulta, rectificacion y supresion. La politica final debe ser aprobada por el responsable legal antes de habilitar un ambiente productivo.

## Seguridad de base de datos

Las politicas SQL deben coincidir con el usuario de aplicacion, los grants y las operaciones reales del backend. Una declaracion de roles sin una conexion con privilegio minimo probado no constituye evidencia de H-44 ni H-52.

