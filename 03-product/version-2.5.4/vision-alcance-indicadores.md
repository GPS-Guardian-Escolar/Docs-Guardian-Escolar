# Vision, alcance e indicadores

## Problema

Los acudientes necesitan saber si el estudiante inicio, continua y termino el trayecto escolar, recibir alertas relevantes y compartir el seguimiento con acudientes autorizados. La solucion debe priorizar privacidad, disponibilidad y coherencia entre aplicacion, API y base de datos.

## Usuarios

- **Acudiente:** registra estudiantes, consulta rutas, coordenadas, alertas y notificaciones autorizadas.
- **Estudiante/dispositivo hijo:** envia coordenadas y puede iniciar o cerrar su trayecto dentro de los permisos definidos.
- **Administrador:** gestiona configuracion y soporte con permisos elevados.

## Alcance MVP

Incluye registro y autenticacion, estudiantes, contactos de emergencia, vinculacion de dispositivo, rutas y paradas, zonas seguras, seguimiento, coordenadas, alertas y notificaciones. No incluye facturacion, analitica avanzada ni integraciones de terceros no descritas en el contrato.

## Indicadores

| Indicador | Meta inicial | Fuente |
|---|---:|---|
| Porcentaje de cierres de viaje validos | >= 99% | Respuestas 2xx de `POST/PATCH /trips` y estado final persistido |
| Tiempo entre coordenada recibida y alerta emitida | <= 10 s | `coordinates.recorded_at` y `notification.created_at` |
| Solicitudes no autorizadas rechazadas | 100% en pruebas negativas | Suite MockMvc y pruebas de suscripcion WebSocket |

## Criterios de aceptacion comunes

1. Toda historia debe enlazar pantalla, endpoint, persistencia y prueba.
2. Un error de API no puede convertirse silenciosamente en una lista vacia.
3. El acceso a estudiantes, viajes, coordenadas y alertas debe validarse por propietario, acudiente activo o administrador.
4. Los cambios que afecten contrato deben actualizar OpenAPI versionado y su prueba de compatibilidad.

