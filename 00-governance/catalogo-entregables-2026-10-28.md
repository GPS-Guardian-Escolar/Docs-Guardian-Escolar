# Índice y estado de entregables — Sección 2.5.5

**Proyecto:** GPS Guardian Escolar
**Fecha de revisión:** 28 de octubre de 2026
**Criterio:** los repositorios actualizados prevalecen sobre descripciones históricas; el código presente se distingue de la validación ejecutada.

## Estado de actualización

| #  | Producto / resultado                     | Estado al 28-10-2026                                                        | Acción documental                               |
| -- | ---------------------------------------- | --------------------------------------------------------------------------- | ------------------------------------------------ |
| 1  | Informe de especificación de requisitos | Revisado contra sesiones, autenticación, vinculación y contratos actuales | Actualizado; aceptación de entorno pendiente    |
| 2  | Análisis de vistas dinámicas           | Revisado contra flujos de QR, dispositivos y trayectos                      | Actualizado                                      |
| 3  | Propuesta técnica                       | Revisada contra componentes, seguridad y publicación Android               | Actualizada                                      |
| 4  | Diseño del software                     | Revisado contra esquema, roles, sesiones y retención                       | Actualizado; UML histórico identificado         |
| 5  | Artefactos de código                    | Inventario técnico de los repositorios y capacidades observadas            | Actualizado                                      |
| 6  | Plan de pruebas                          | Flujos de calidad configurados; ejecución no confirmada                    | Actualizado; evidencia pendiente                 |
| 7  | Implementación                          | Compose, Liquibase y bootstrap controlado                                   | Actualizada                                      |
| 8  | Acciones correctivas y mejora            | Cambios reflejados; aceptación no cerrada sin evidencia                    | Actualizado                                      |
| 9  | Manual técnico                          | Configuración y controles de operación                                    | Actualizado                                      |
| 10 | Manual de instalación                   | Compose, EAS y URL de APK                                                   | Actualizado; APK requiere validar                |
| 11 | Manual de usuario                        | QR, acceso y descarga Android condicionada                                  | Actualizado; pruebas físicas pendientes         |
| 12 | Artículo del proyecto                   | Implementación y límites de evidencia                                     | Actualizado sin resultados empíricos inventados |

## Cambios técnicos contrastados

- Backend: autenticación por dispositivo, limitación de intentos, sesiones y tokens, API, trayectos y notificaciones.
- Frontend: QR y vinculación, contratos, configuración EAS y botón APK condicionado por URL.
- Base de datos: códigos y sesiones, auditoría, rol de aplicación, direcciones y bootstrap controlado de administrador.
- Infraestructura: comprobaciones de salud, dependencias, variables de despliegue, flujo de calidad y reversión.

## Ubicación y límites

La entrega DOCX/PDF versionada está en [`14-deliverables/version-2.5.5`](../14-deliverables/version-2.5.5/). La versión 2.5.4 y sus referencias anteriores se conservan sin sobrescribir.

Los diagramas UML de versiones previas se conservan como históricos hasta que se cotejen y redibujen.
