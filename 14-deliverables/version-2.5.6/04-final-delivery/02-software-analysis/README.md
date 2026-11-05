# Producto 02 — Análisis de software: suplemento 2.5.6

**Base:** [Análisis de vistas dinámicas 2.5.5](../../../version-2.5.5/Entregables/02_ANALISIS_SOFTWARE_VISTAS_DINAMICAS.docx).

## Flujos actualizados por el tag

1. Al crear trayecto sin ruta explícita, backend busca la ruta preferida activa del estudiante.
2. El frontend solicita revisión/confirmación antes de vincular dispositivo.
3. Durante trayecto activo se evalúa ubicación obsoleta: valor predeterminado 120 s, revisión programada cada 30 s, con aviso previsto al acudiente.
4. El acudiente autorizado/administrador gestiona el estado del viaje; el dispositivo registra ubicación y no finaliza el trayecto desde la interfaz.
5. El borrado autorizado es lógico; si está activo, se marca cancelado y se registra la hora de fin.
6. Estudiantes, trayectos y coordenadas disponen de listados paginados.

Esta descripción proviene de lectura estática. Secuencias de éxito, error y notificación requieren pruebas con evidencia.
