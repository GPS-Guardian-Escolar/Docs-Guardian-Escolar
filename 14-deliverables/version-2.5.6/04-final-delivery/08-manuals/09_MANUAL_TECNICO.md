---
title: "Manual técnico de GPS Guardián Escolar"
subtitle: "Proyecto formativo · GPS Guardián Escolar"
author: "Equipo del proyecto GPS Guardián Escolar"
date: "5 de noviembre de 2026"
lang: es-CO
---

**Versión documental:** 2.5.7 · **Estado:** versión editable preparada para subida y revisión de QA/arquitectura.

## Tabla de contenido

1. Resumen y público objetivo
2. 1. Inventario técnico de la solución
3. 2. Estructura de código y puntos de extensión
4. 3. Grupos de API y parámetros
5. 4. Mensajería STOMP y GPS
6. 5. Persistencia y cambios de esquema
7. 6. Variables de configuración y seguridad
8. 7. Calidad y mantenimiento
9. 8. Diagnóstico rápido
10. 9. Evidencias de aceptación técnica
11. Referencias y trazabilidad (APA 7.ª edición)


> **Corte y alcance de evidencia.** Revisión documental 2.5.7, 5 de noviembre de 2026. La descripción del **release de referencia** utiliza los commits de `main` observados en Backend (`1022058d`), Frontend (`3d9f59fa`), DB (`9278b1dc`) e Infra (`131e9895`). Se inspeccionaron además las ramas `dev`/`develop` más recientes y los suplementos documentales 2.5.6, pero **sus cambios no se consideran integrados a producción por esa sola razón**. La revisión es estática: no se ejecutaron en esta sesión pruebas, despliegues ni validaciones en teléfono. Los resultados operativos requieren evidencia real. 


## Resumen y público objetivo

Manual para desarrolladores, personal de mantenimiento, QA y administradores de infraestructura. Describe estructura, versiones, contratos, configuración, operación y diagnóstico del código examinado en los SHA de release del 5 de noviembre de 2026. No constituye certificación del ambiente productivo.

## 1. Inventario técnico de la solución

| Componente | Stack verificado | Función |
|---|---|---|
| Backend | Java 17, Spring Boot 3.5.16, Spring Security/JPA/Validation/Actuator | API y reglas de acceso |
| Autorización | JWT jjwt 0.12.6, roles y permisos | Sesiones y control de recursos |
| Tiempo real | Spring WebSocket STOMP/SockJS | Difusión de coordenadas |
| Mapas | Google Maps proxy, Caffeine, Resilience4j | Cálculos/cartografía con límites |
| Frontend | Expo 57, React Native 0.86.3, React 19.2.3, expo-router | Navegación web/móvil |
| Datos | PostgreSQL 15, Liquibase | Dominio y migraciones |
| Infra | Docker Compose, Nginx | Dependencias y proxy web |

**Organización:** `GPS-Guardian-Escolar`; repositorios `Backend-Guardian-Escolar`, `Fronted-GuardianEscolar-`, `DB-Structure-Guardian-Escolar`, `Docker-Infra-Guardian-Escolar` y `Docs-Guardian-Escolar`. Comprobar siempre el commit desplegado; una rama no equivale a release validado.

## 2. Estructura de código y puntos de extensión

**Backend:** `src/main/java/com/guardianescolar/api/modules/` incluye `auth`, `students`, `routes`, `trips`, `zones`, `maps`, `notifications`, `admin`, `audit` y `security`. En cada módulo, `controller` define rutas y permisos, `dto` define intercambio, `service` contiene reglas y `repository` abstrae acceso JPA. `shared/config`, `shared/security` y `shared/exception` concentran infraestructura transversal. Los tests viven en `src/test/java`.

**Frontend:** `app/(auth)` y `app/(tabs)` definen rutas Expo; `features/` agrupa experiencias (vínculo, niño, tracking); `components/` reutiliza elementos; `services/` integra API/Socket/Maps; `contexts/` mantiene sesión y estado; `translations/` centraliza textos; `config/` resuelve endpoints. Archivos a revisar ante cambios: `services/api.js`, `services/tracking.service.js`, `services/background/locationTracking.js`, `services/socket.js`, `app/scan-qr.js`.

**Datos:** `01_ddl`, `02_dml`, `03_dcl`, `04_tcl`, `05_rollbacks`; raíz de migraciones `changelog/changelog-master.yaml`. La migración 014 agrega ruta preferida; no reescribir changesets históricos ejecutados.

![Figura 1. Arquitectura de ejecución](../../assets/arquitectura.png)

## 3. Grupos de API y parámetros

| Grupo | Métodos/rutas significativas | Observaciones |
|---|---|---|
| Auth | POST `/api/auth/register`, `/login`, `/logout`; GET `/me`, `/verify-email` | Confirmación de equipo/2FA disponibles según política |
| Estudiantes | GET/POST `/api/students`; PUT/DELETE `/{studentId}` | `STUDENT_*` y `ADMIN` |
| Acudientes | GET/POST `/api/students/{id}/guardians`; DELETE `/{id}/guardians/{userId}` | Acceso compartido revocable |
| Vínculo | POST `/api/students/{id}/link-code`, `/api/students/link-device`; GET `/api/students/child-profile` | Código controlado y perfil infantil |
| Preferencia | PUT `/api/students/{id}/preferred-route/{routeId}` | Solo ruta activa asignada |
| Rutas | GET/POST `/api/routes`; PUT/DELETE `/{routeId}`; POST `/{routeId}/stops` | `ROUTE_*` / ADMIN |
| Viajes | GET/POST `/api/trips`; GET `/active?studentId=`; DELETE `/{tripId}` | 0-based `page`/`size` para listados |
| Coordenadas | GET/POST `/api/trips/{id}/coordinates` | Solo persistencia en trayecto activo |
| Notificaciones | GET `/api/notifications`; PATCH `/{receiptId}/read`; GET/PUT `/settings` | Inbox frente a entrega externa |
| Zonas | `/api/safe-zones` | Requiere permisos y datos geográficos válidos |
| Mapas | `/api/maps/geocode`, `/reverse-geocode`, `/directions` | Proveedor Google según configuración |
| Salud | GET `/api/health` | Respuesta salud de aplicación |

No introducir `PATCH /api/trips/{id}/status` en SDK ni documentación como funcional sin implementarlo: **no está en el controlador revisado**. El `DELETE` exige `LOCATION_VIEW` o ADMIN, además de validación de gestión en el servicio; si el viaje está `IN_PROGRESS`, termina `CANCELED` y se marca `deleted_at`. Para contrato completo, exportar `/v3/api-docs` en un ambiente controlado y compararlo con los clientes.

## 4. Mensajería STOMP y GPS

`WebSocketConfig` declara broker `/topic`, prefijo de aplicación `/app` y endpoint `/ws`; la conexión tiene interceptor de autenticación. Al registrar una coordenada aceptada, `TripService` publica `/topic/trips/{tripId}/coordinates`. Usar token autorizado, revocar al cerrar sesión, controlar reconexión y evitar acceso cruzado a estudiantes. No usar ubicación de teléfono como «actual» si la fecha `recordedAt` supera el umbral obsoleto.

`LocationAvailabilityService` vigila trayectos `IN_PROGRESS` con umbral de 120 segundos y revisión programada cada 30000 ms por defecto. Genera evento `LOCATION_UNAVAILABLE` para propietarios/guardianes activos mediante servicio de notificaciones, con prevención de duplicados. El operador debe distinguir **persistencia del evento** de **entrega efectiva** por el proveedor.

## 5. Persistencia y cambios de esquema

Tablas principales: `users`, `students`, `student_guardians`, `student_devices`, `routes`, `stops`, `student_routes`, `trips`, `coordinates`, `safe_zones`, `notifications`, `notification_receipts`, `notification_settings`, `user_devices`, `roles`, `permissions`, `user_sessions`, `audit_logs` y `error_logs`. Índices, tipos, constraints y grants son responsabilidad del repositorio DB; `spring.jpa.hibernate.ddl-auto=validate` protege de algunas diferencias, pero no sustituye tests de migración ni controles de datos.

Regla de cambios: nuevo changeset → validación en BD descartable → respaldo de destino → ejecución en ventana aprobada → control de `DATABASECHANGELOG` → smoke API. Rollback SQL requiere revisión de integridad; la reversión de imagen no revierte automáticamente un esquema.

## 6. Variables de configuración y seguridad

**Servidor:** `SERVER_PORT`, `SPRING_PROFILES_ACTIVE`, `DB_URL`, `DB_USERNAME`, `DB_PASSWORD`, `JWT_SECRET`, `JWT_EXPIRATION_MINUTES`, `CORS_ALLOWED_ORIGINS`, `REQUIRE_HTTPS`, `APP_MAIL_ENABLED`, `MAIL_PROVIDER`, `MAIL_*`, `BREVO_API_KEY`, `GOOGLE_MAPS_API_KEY`, `EXPO_PUSH_ENABLED`, `GPS_ROUTE_DEVIATION_THRESHOLD_METERS`. **Infra:** `POSTGRES_PASSWORD`, `APP_DB_USERNAME`, `APP_DB_PASSWORD`, `LIQUIBASE_CONTEXTS` y referencias de imágenes. **Cliente:** `EXPO_PUBLIC_API_URL`, `EXPO_PUBLIC_WS_URL`, `EXPO_PUBLIC_WEB_PROXY`, `EXPO_PUBLIC_ANDROID_APK_URL`.

**Reglas:** contraseñas y JWT fuera de Git; `EXPO_PUBLIC_*` nunca debe contener credenciales privadas; rol DB de la app distinto del superusuario; contexto de administrador bootstrap solo por una ejecución aprobada. Evitar poner correo real, tokens, ubicación o claves en evidencia compartida.

## 7. Calidad y mantenimiento

```powershell
# Backend
.\mvnw.cmd --batch-mode clean verify
# Frontend
npm ci
npm test
npm run lint
npm run security:release
# Infra (desde Docker-Infra con env preparado)
docker compose --env-file .env config --quiet
docker compose --env-file .env ps
docker compose --env-file .env logs --tail=100 backend
```

Comprobar los resultados reales asociados al SHA; no fijar un número histórico de tests. Registrar dependencias, SBOM/licencias cuando se prepare un release y revisar alertas de seguridad de librerías.

## 8. Diagnóstico rápido

| Síntoma | Verificar primero | Medida segura |
|---|---|---|
| Backend no inicia | Migración, variables JDBC, DB role, logs | Probar conexión y `DATABASECHANGELOG` |
| 401/403 | JWT, permisos y pertenencia del estudiante | Reproducir con usuario autorizado/no autorizado |
| 429/Maps 5xx | Rate-limit, cuota, API key y logs | Revisar proveedor, evitar exponer clave |
| QR fallido | Validez código, permisos, red, versión de app | Regenerar código y reintentar seguro |
| GPS congelado | `recordedAt`, permisos, tarea, red | Mostrar marca de tiempo; no ocultar degradación |
| Sin correos/push | Flags, credenciales, proveedor y token device | Probar canal y conservar recibo sin datos personales |
| Diferencia QA/main | SHA y PR integrados | No mezclar ramas automáticamente |

## 9. Evidencias de aceptación técnica

Requiere logs CI, reporte de pruebas de permisos, verificación de migración 014, ensayo GPS/QR nativo, recepción de alertas, estado real de Render y restauración desde respaldo. La presente edición registra lo observado en el código, no certifica esos resultados.

## Referencias y trazabilidad (APA 7.ª edición)

- GPS Guardian Escolar. (2026a). *Backend-Guardian-Escolar* [Código fuente, rama main, commit `1022058d`]. GitHub. https://github.com/GPS-Guardian-Escolar/Backend-Guardian-Escolar/tree/1022058d545a9e7936edbff75950034ec4f98e88
- GPS Guardian Escolar. (2026b). *Fronted-GuardianEscolar-* [Código fuente, rama main, commit `3d9f59fa`]. GitHub. https://github.com/GPS-Guardian-Escolar/Fronted-GuardianEscolar-/tree/3d9f59fa3e91b5ecc5b8ae86591187546b966668
- GPS Guardian Escolar. (2026c). *DB-Structure-Guardian-Escolar* [Migraciones SQL, rama main, commit `9278b1dc`]. GitHub. https://github.com/GPS-Guardian-Escolar/DB-Structure-Guardian-Escolar/tree/9278b1dcb224301536d6158357f74daee74b20ba
- GPS Guardian Escolar. (2026d). *Docker-Infra-Guardian-Escolar* [Configuración de despliegue, rama main, commit `131e9895`]. GitHub. https://github.com/GPS-Guardian-Escolar/Docker-Infra-Guardian-Escolar/tree/131e98954430e95e9108174eded9896b3f0d5272
- GPS Guardian Escolar. (2026e). *Docs-Guardian-Escolar: entregables 2.5.5 y suplementos 2.5.6* [Documentación del proyecto]. GitHub. https://github.com/GPS-Guardian-Escolar/Docs-Guardian-Escolar

**Fuentes primarias relevantes:** `TripController.java`, `TripService.java`, `StudentController.java`, `LocationAvailabilityService.java`, `WebSocketConfig.java`, `application.yml`, `package.json`, `docker-compose.yaml` y `01_ddl/04_alter/014_preferred_student_route.sql`, según el documento. Las referencias a rutas son comprobables en los repositorios indicados.
