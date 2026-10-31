# ADR-001: mecanismo de seguimiento en tiempo real

**Estado:** Propuesto para validacion E2E  
**Fecha:** 2026-10-26  
**Relacion:** H-13 y H-18

## Contexto

El backend expone STOMP sobre SockJS y publica coordenadas en destinos de viaje. El cliente no debe abrir un WebSocket nativo incompatible ni mantener una ruta muerta sin suscripcion.

## Decision

Usar SockJS + STOMP en el frontend, con `useTrackingRealtime`, reconexion limitada y suscripcion al destino del viaje. El backend autoriza la suscripcion solo para propietario, administrador o acudiente activo del estudiante.

## Verificacion requerida

- Conectar cliente autenticado y comprobar recepcion de una coordenada.
- Intentar suscribirse con un usuario sin acceso y comprobar rechazo.
- Comprobar reconexion y liberacion de la suscripcion al desmontar la pantalla.

## Alternativas descartadas

El WebSocket nativo se descarta porque no negocia el protocolo STOMP del servidor. El sondeo queda como alternativa operativa si el broker no puede mantenerse disponible, pero requeriria una ADR nueva y retirar STOMP de ambos lados.

