# Despliegue, configuracion y rollback

**Relacion:** H-23, H-24, H-35, H-55, H-56, H-60 y H-67

## Orden de arranque

1. PostgreSQL saludable.
2. Liquibase aplica cambiosets con el contexto correspondiente.
3. Backend arranca con usuario de aplicacion, JWT y variables obligatorias.
4. Frontend/Nginx publica la aplicacion y enruta `/api` y `/ws`.

## Variables canonicas

- `JWT_SECRET`
- `GPS_ROUTE_DEVIATION_THRESHOLD_METERS`
- `DB_USERNAME` y `DB_PASSWORD`
- `EXPO_PUBLIC_API_URL` y `EXPO_PUBLIC_WS_URL`

No se deben agregar respaldos publicos para secretos ni aliases que no consuma el backend.

## Release y rollback

Las imagenes deben etiquetarse con commit o version, nunca depender solo de `latest`. Antes de promover se ejecutan pruebas, `docker compose config`, migracion validada y healthchecks. El rollback debe seleccionar la imagen anterior y el changeset reversible correspondiente; no se debe ejecutar rollback destructivo sin respaldo y aprobacion.

## Limitaciones verificables

La configuracion local valida estructura y nombres, pero no demuestra disponibilidad de Render, TLS real, secretos de QA/produccion ni proteccion de ramas. Esas evidencias deben agregarse desde los sistemas externos.

