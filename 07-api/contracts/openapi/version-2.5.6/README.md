# Contrato API — delta 2.5.6

**Base de código:** tag `final-delivery-2026` del Backend.
**Estado:** delta documental cotejado con controladores; la especificación OpenAPI versionada completa sigue pendiente.

| Método y ruta | Comportamiento observado | Autorización documentada en el controlador |
|---|---|---|
| `GET /api/students?page=0&size=20` | Lista estudiantes con respuesta paginada. | `STUDENT_READ` o `ADMIN`. |
| `PUT /api/students/{studentId}/preferred-route/{routeId}` | Marca una ruta del estudiante como preferida; respuesta 204. | `STUDENT_UPDATE` o `ADMIN`; servicio valida el manejo del estudiante. |
| `GET /api/trips?page=0&size=20` | Lista trayectos con respuesta paginada. | `LOCATION_VIEW` o `ADMIN`. |
| `POST /api/trips` | Crea un trayecto. Si se omite `routeId`, el servicio usa la ruta preferida activa cuando hay una válida. | `LOCATION_VIEW`, `CHILD_DEVICE` o `ADMIN`. |
| `DELETE /api/trips/{tripId}` | Baja lógica; si está activo se cancela y registra la hora de fin. Respuesta 204. | `LOCATION_VIEW` o `ADMIN`; servicio valida autorización sobre el estudiante. |
| `GET /api/trips/{tripId}/coordinates?page=0&size=20` | Lista coordenadas con respuesta paginada. | `LOCATION_VIEW` o `ADMIN`. |

La respuesta de página incluye `items`, `page`, `size`, `totalItems`, `totalPages`, `first` y `last`. El controlador limita/normaliza el tamaño mediante la utilidad de paginación.

## Faltante de contrato

Este resumen no es un `openapi.yaml` ni enumera todos los endpoints, esquemas, validaciones, códigos de error o esquemas de seguridad. Publicar la especificación SpringDoc generada para el mismo commit, revisar respuestas y enums contra los controladores y configurar comparación en CI, como exige el [contrato API v1](../version-1/README.md). No generar payloads de ejemplo sin cotejarlos con DTOs.

**Fuente de verdad:** [TripController](https://github.com/Nicolasruiz918/Backend-Guardian-Escolar/blob/final-delivery-2026/src/main/java/com/guardianescolar/api/modules/trips/controller/TripController.java), [StudentController](https://github.com/Nicolasruiz918/Backend-Guardian-Escolar/blob/final-delivery-2026/src/main/java/com/guardianescolar/api/modules/students/controller/StudentController.java), [TripService](https://github.com/Nicolasruiz918/Backend-Guardian-Escolar/blob/final-delivery-2026/src/main/java/com/guardianescolar/api/modules/trips/service/TripService.java).
