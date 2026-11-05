# Producto 04 — Diseño del software: suplemento 2.5.6

**Base:** [Diseño de software 2.5.5](../../../version-2.5.5/Entregables/04_DISENO_SOFTWARE_ARQUITECTURA_INTERFACES_DATOS.docx).

- `student_routes.is_preferred` identifica la ruta preferida; migración 014 crea la columna y un índice parcial único para asignación activa.
- Si el request no entrega `routeId`, el servicio usa la asignación preferida activa válida.
- La autorización de gestión se conserva en backend aunque la interfaz oculte controles al dispositivo infantil.
- El borrado lógico preserva trazabilidad; trayecto activo termina como cancelado con timestamp de cierre.
- Disponibilidad GPS tiene umbral y frecuencia configurables; la respuesta paginada expone elementos y metadatos.

Los UML 2.5.5 permanecen históricos hasta cotejar y exportar diagramas actualizados; este README no los reemplaza.
