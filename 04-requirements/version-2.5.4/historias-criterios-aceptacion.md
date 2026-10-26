# Historias de usuario y criterios de aceptacion

Este documento es el indice documental de las historias; la implementacion se verifica en los repositorios de codigo.

| HU | Historia resumida | Criterios de aceptacion |
|---|---|---|
| H-09 | El dispositivo hijo cierra un viaje | Solo usa estados del contrato; requiere credencial valida; persiste `trip_ended_at`; el error se muestra al usuario. |
| H-12 | Un acudiente autorizado comparte seguimiento | Solo `student_guardians` activos; ve viajes, coordenadas y alertas; un usuario ajeno recibe 403. |
| H-13 | El acudiente recibe coordenadas en tiempo real | STOMP/SockJS conecta; suscripcion autorizada recibe eventos; suscripcion ajena es rechazada. |
| H-15 | El acudiente vincula un dispositivo con codigo seguro | Codigo aleatorio; expiracion; un solo uso; limite de intentos; nunca se deriva del UUID. |
| H-20 | El equipo ejecuta pruebas frontend | Existe script reproducible; cubre servicios y utilidades; el gate se ejecuta en CI. |
| H-21 | El backend protege objetos y operaciones | Casos 401, 403 y objeto ajeno; las pruebas se ejecutan en cada PR. |
| H-62 | Cliente y servidor comparten contrato | OpenAPI versionado; tipos/cliente generados; comparacion automatica en CI. |
| H-63 | Los listados son paginables y eficientes | Page/size en API y cliente; consultas sin N+1; orden estable y limites maximos. |

Cada nueva HU debe agregar una fila con pantalla, endpoint, persistencia, riesgos y prueba asociada.

