# GPS Guardian Escolar: arquitectura y seguimiento seguro de estudiantes

## Resumen

GPS Guardian Escolar es una solucion movil y web orientada al seguimiento de estudiantes durante trayectos escolares. El sistema combina una aplicacion React Native con Expo, una API REST en Spring Boot, PostgreSQL administrado mediante Liquibase y una infraestructura Docker Compose. Sus capacidades principales son la gestion de estudiantes, vinculacion por QR, visualizacion de ubicacion, rutas, zonas seguras y notificaciones.

**Palabras clave:** seguimiento GPS, estudiantes, zonas seguras, React Native, Spring Boot, PostgreSQL.

## 1. Introduccion

La propuesta aborda la necesidad de que los acudientes consulten el estado de un estudiante desde un dispositivo autorizado y reciban informacion comprensible sobre su trayecto. El SRS define los requisitos funcionales de autenticacion, gestion de estudiantes, rastreo, alertas, historial y configuracion de rutas y zonas.

## 2. Arquitectura

El backend es un monolito modular Spring Boot 3.5.16 sobre Java 17. Sus modulos incluyen autenticacion, seguridad, estudiantes, rutas, viajes, zonas, mapas, notificaciones, auditoria y administracion. La persistencia usa PostgreSQL y migraciones Liquibase. La comunicacion en tiempo real se apoya en WebSocket y la seguridad en JWT y roles.

El frontend usa Expo SDK 57, React Native 0.86.3 y Expo Router. Mantiene componentes compartidos y adaptadores de mapa para web y plataformas nativas. La Development Build es necesaria para capacidades nativas como camara, ubicacion en segundo plano, mapas nativos y notificaciones.

## 3. Funcionalidades y flujo de datos

El dispositivo del estudiante obtiene coordenadas y las envia al backend. El backend valida y persiste puntos, evalua distancia a rutas y zonas y publica eventos autorizados. El acudiente selecciona el estudiante y consulta la ultima ubicacion, el recorrido y las zonas. Las direcciones de rutas y zonas se geocodifican antes de persistir coordenadas; el radio se conserva en metros y se dibuja en el mapa mediante un circulo.

## 4. Estado de construccion

El codigo fuente y la configuracion se encuentran en las ramas `HU-22-dev`, `HU-15-develop`, `HU-09-dev` y `HU-02-dev`. La base de codigo contiene los contratos descritos, pero la operacion completa depende de servicios externos y de pruebas en dispositivos reales. En particular, QR, GPS en segundo plano, Google Maps, WebSocket, push y restricciones de bateria no se presentan como resultados medidos en este articulo.

## 5. Limitaciones y trabajo futuro

Se requiere ejecutar una matriz de pruebas con datos controlados, medir tiempos de respuesta y validar compatibilidad con fabricantes Android, incluido Honor. Tambien se debe revisar visualmente la version final de los manuales PDF y mantener sincronizados SRS, diagramas UML y codigo en cada entrega.

## Referencias internas

- SRS del proyecto, `Documentacion/SRS/srs/SRS.docx`.
- Backend, frontend, base de datos e infraestructura de Entregable 4.
- Documentos de arquitectura, pruebas y rectificacion incluidos en esta entrega.



## Actualización técnica — 16 de octubre de 2026

Este documento distingue entre funcionalidades presentes en el código, verificaciones observadas en Render y pruebas todavía pendientes. Las evidencias de funcionamiento se limitan a las informadas en el expediente; no se atribuyen resultados de campo inexistentes. La versión principal del proyecto usa Java 17, Spring Boot 3.5.16, PostgreSQL, Liquibase, Expo SDK 57, Expo Router y Docker Compose (GPS Guardian Escolar, 2026a, 2026b, 2026c).

El despliegue público documentado utiliza dos servicios web Docker en Render: https://gps-guardian-escolar-frontend.onrender.com (Expo Web exportado y servido por Nginx) y https://gps-guardian-escolar-backend.onrender.com (Spring Boot, puerto 8080), más PostgreSQL administrado en Render. Liquibase fue ejecutado desde infraestructura Docker contra la base de Render. El entorno local conserva Docker Compose y bases locales independientes (GPS Guardian Escolar, 2026a, 2026c).

Las pruebas automatizadas reportadas para la migración HU-29 fueron 23 satisfactorias y el Docker build de backend concluyó 16/16 etapas; estas evidencias no prueban el escaneo QR, el GPS Android en segundo plano, la entrega de correos de confirmación de dispositivos ni la operación prolongada del sistema. Las capturas posteriores documentan incidencias de interfaz y autenticación cuya solución desplegada no fue confirmada.


## Actualización técnica — 28 de octubre de 2026

La revisión estática de los repositorios actualizados identifica controles reforzados para autenticación por dispositivo, limitación de intentos, protección de tokens y códigos de vinculación, permisos de acceso compartido a estudiantes y transiciones de trayecto. La base incorpora bootstrap administrativo explícito, rol de aplicación y cambios en direcciones y auditoría. El frontend estabiliza el flujo QR, enlaza el proyecto EAS y ofrece descarga Android cuando se configura una URL de APK.

Los repositorios incluyen workflows de calidad; esta actualización no ejecutó esos pipelines, ni pruebas en producción o dispositivos físicos. Por ello, las afirmaciones de funcionamiento de QR, GPS en segundo plano, mensajes push/correo y descarga APK requieren evidencia adicional. Snapshots contrastados: backend `345a8b3`, frontend `d0648c1`, base `e6b84c5` e infraestructura `d06a61c`.
