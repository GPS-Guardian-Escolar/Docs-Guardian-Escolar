# Auditoría y matriz de actualización documental — 28 de octubre de 2026 (versión 2.5.5; corte 2026-10-28)

## Alcance y evidencia

Se contrastaron los documentos entregados al 16 de octubre con los estados locales actualizados de cuatro repositorios. La revisión de código fue estática. Los commits de referencia son Backend `dev` **345a8b3**, Frontend `develop` **d0648c1**, DB-Structure `dev` **e6b84c5** y Docker-Infra `main` **d06a61c**. No se ejecutaron pruebas, builds, despliegues ni ensayos en dispositivos para esta actualización.

La documentación previa del 16 de octubre permanece sin modificar como referencia histórica. Este paquete contiene copias actualizadas de los 12 DOCX y 12 PDF, con portadas conservadas y su fecha/versión actualizadas, además de anexos técnicos fechados.

## Cambios contrastados

- Autenticación y sesiones: identificador de dispositivo, limitación de intentos, control/revocación de sesión y protección hash de tokens.
- Estudiantes y trayectos: protección de códigos de vinculación, acceso compartido, validación de estados y contratos de rutas/paradas.
- Base de datos: rol de aplicación, bootstrap explícito de administrador, auditoría y columnas de dirección; migraciones Liquibase forward-only.
- Frontend: ajustes del flujo QR, contratos y errores de API, enlace/configuración EAS y botón de descarga APK condicionado por `EXPO_PUBLIC_ANDROID_APK_URL`.
- DevOps: healthchecks y dependencias de Compose, configuración de secretos, workflows de calidad y guía de rollback.

## Matriz de entregables

| # | Entregable | Estado al 28-10-2026 |
|---|---|---|
| 1 | Especificación de requisitos | Actualizada con cambios de autenticación, sesiones, vinculación y BD; pruebas de aceptación pendientes. |
| 2 | Análisis de vistas dinámicas | Actualizado con flujos de dispositivo, QR, sesión y trayectos. |
| 3 | Propuesta técnica | Actualizada con controles y límites de evidencia. |
| 4 | Diseño de software | Actualizado con contratos, datos, roles y retención. |
| 5 | Artefactos de código | Inventario actualizado con ramas y commits. |
| 6 | Plan de pruebas | Actualizado; workflows presentes, ejecución no verificada. |
| 7 | Implementación | Actualizada con secuencia Compose/Liquibase y bootstrap de producción. |
| 8 | Acciones correctivas | Estados y criterios de cierre actualizados; aceptación pendiente. |
| 9 | Manual técnico | Actualizado con configuración y operación. |
| 10 | Manual de instalación | Actualizado con EAS, Compose y URL de APK. |
| 11 | Manual de usuario | Actualizado con QR, descarga y acceso. |
| 12 | Artículo académico | Actualizado sin atribuir resultados experimentales no medidos. |

## Limitaciones y riesgos abiertos

La existencia de código no prueba operación correcta. Siguen pendientes las pruebas físicas de QR/GPS Android y segundo plano, compatibilidad por fabricante, batería, entrega de correo/push/WebSocket, disponibilidad del artefacto APK, arranque/cold start y validación de políticas de retención y tratamiento de datos de menores. La disponibilidad y resultado de CI deben comprobarse en cada commit.

## Referencias de código

GPS Guardian Escolar. (2026a). *Backend-Guardian-Escolar*, rama `dev`, commit `345a8b3`.
GPS Guardian Escolar. (2026b). *Fronted-GuardianEscolar-*, rama `develop`, commit `d0648c1`.
GPS Guardian Escolar. (2026c). *DB-Structure-Guardian-Escolar*, rama `dev`, commit `e6b84c5`.
GPS Guardian Escolar. (2026d). *Docker-Infra-Guardian-Escolar*, rama `main`, commit `d06a61c`.
