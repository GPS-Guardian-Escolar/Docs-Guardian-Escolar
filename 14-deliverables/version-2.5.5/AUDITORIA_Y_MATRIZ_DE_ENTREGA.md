# Auditoría y matriz de entrega — 28 de octubre de 2026

## Alcance de la actualización

Se contrastaron los documentos entregados al 16 de octubre con el estado disponible en los repositorios de backend, frontend, base de datos e infraestructura. La revisión fue estática. No se ejecutaron pruebas, compilaciones, despliegues ni validaciones en dispositivos durante esta actualización.

## Cambios técnicos identificados

- Autenticación y sesiones: identificador de dispositivo, limitación de intentos, control y revocación de sesión, y protección de tokens mediante hash.
- Aplicación: contratos de API, vinculación QR, acceso compartido a estudiantes, estados de trayecto, notificaciones y configuración de publicación Android.
- Datos: migraciones de sesiones, códigos de enlace, direcciones y auditoría; separación del rol de aplicación e inicialización controlada del administrador.
- Operación: comprobaciones de salud y dependencias, configuración de secretos, flujos de calidad y procedimientos de reversión.

## Matriz de entregables

| # | Documento | Actualización |
|---|---|---|
| 1 | Especificación de requisitos | Autenticación, vinculación, sesiones y contratos; aceptación productiva pendiente. |
| 2 | Análisis de vistas dinámicas | Flujos QR, dispositivos y trayectos. |
| 3 | Propuesta técnica | Componentes, controles y distribución Android. |
| 4 | Diseño del software | Arquitectura, esquema, roles, sesiones y retención; diagramas históricos identificados. |
| 5 | Artefactos de código | Inventario técnico de repositorios y capacidades observadas. |
| 6 | Plan de pruebas | Flujos de calidad configurados; ejecución y evidencia pendientes. |
| 7 | Implementación | Compose, Liquibase, configuración y bootstrap controlado. |
| 8 | Acciones correctivas | Cambios observados; aceptación pendiente de evidencia. |
| 9 | Manual técnico | Configuración, controles y operación. |
| 10 | Manual de instalación | Compose, EAS y descarga APK condicionada por URL. |
| 11 | Manual de usuario | QR, acceso y distribución Android condicionada. |
| 12 | Artículo académico | Implementación observada y límites de evidencia. |

## Límites de verificación

La existencia de código no prueba operación correcta. Siguen pendientes las pruebas de QR/GPS en Android y segundo plano, compatibilidad por fabricante, batería, entrega de correo/push/WebSocket, disponibilidad del APK, arranque en frío y validación de políticas de retención y tratamiento de datos de menores. La disponibilidad y el resultado de CI deben comprobarse en la validación de cada versión.

## Fuentes técnicas

GPS Guardian Escolar. (2026a). *Backend-Guardian-Escolar* [Repositorio de código fuente].

GPS Guardian Escolar. (2026b). *Fronted-GuardianEscolar-* [Repositorio de código fuente].

GPS Guardian Escolar. (2026c). *DB-Structure-Guardian-Escolar* [Repositorio de código fuente].

GPS Guardian Escolar. (2026d). *Docker-Infra-Guardian-Escolar* [Repositorio de código fuente].
