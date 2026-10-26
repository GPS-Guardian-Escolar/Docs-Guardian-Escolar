# Contrato API version 1

**Relacion:** H-09, H-10, H-13, H-37, H-62 y H-65

El contrato versionado debe publicarse como `openapi.yaml` o `openapi.json`, incluir todos los controladores, seguridad, parametros, respuestas y enums, y ser la entrada para generar tipos o cliente del frontend.

## Reglas de compatibilidad

1. Cada cambio de endpoint, enum, estado de viaje o respuesta requiere actualizar el contrato.
2. CI debe comparar el contrato generado con la version comprometida.
3. Los cambios incompatibles requieren una nueva version mayor o una migracion documentada.
4. Las pruebas de contrato deben cubrir autenticacion, autorizacion, cierre de viaje, notificaciones y vinculacion de dispositivo.

## Estado actual

SpringDoc genera una especificacion en tiempo de ejecucion, pero la existencia de Swagger UI no prueba que el contrato este versionado ni completo. H-62 queda pendiente hasta publicar el artefacto y ejecutar la comparacion en CI.

