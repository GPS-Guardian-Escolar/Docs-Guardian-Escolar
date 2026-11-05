# Producto 05 — Documentación de pruebas: suplemento 2.5.6

**Base:** [Plan de pruebas 2.5.5](../../../version-2.5.5/Entregables/06_PLAN_DOCUMENTACION_PRUEBAS.docx).

| Escenario | Resultado esperado | Evidencia para cierre |
|---|---|---|
| Usuario sin permiso elimina viaje | Rechazo y registro preservado | Request/response y log. |
| Acudiente autorizado elimina viaje activo | Baja lógica, estado cancelado y hora de fin | Consulta antes/después y auditoría. |
| Crear viaje sin ruta indicada | Se toma ruta preferida activa | Asignación y respuesta del viaje. |
| GPS reciente vs. obsoleto | Alerta acorde a umbral configurado | Tiempos controlados y notificación. |
| Listados paginados | Contenido/metadatos coherentes en páginas inicial, final y vacía | Respuestas API guardadas. |
| Vinculación y confirmación | No vincular sin revisión requerida | Captura de build y evento API. |
| GPS segundo plano / push | Resultado observado en Android configurado | Modelo, Android, build, permisos y recepción. |

Hay pruebas automatizadas presentes en el repositorio, pero esta revisión no las ejecutó ni adjunta reporte CI, capturas o certificación de dispositivos. Estado de ejecución: pendiente.
