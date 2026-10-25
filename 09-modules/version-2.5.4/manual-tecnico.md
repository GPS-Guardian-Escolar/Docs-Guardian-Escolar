# Manual técnico — GPS Guardian Escolar

**Versión:** 2026-10-09  
**Estado:** Elaborado desde cero (entregable faltante en lista 2.5.4)  
**Audiencia:** desarrolladores, integradores y personal de soporte técnico.

---

## 1. Descripción del sistema

GPS Guardian Escolar es una plataforma de seguimiento del trayecto escolar. El backend expone API REST y WebSocket; el cliente Expo cubre web y móvil; PostgreSQL almacena el dominio; Docker Compose orquesta el entorno de referencia.

## 2. Stack técnico vigente

| Capa | Tecnología |
|---|---|
| Backend | Spring Boot 3.5.16, Java 17, Spring Security, JPA, WebSocket STOMP, Mail, Actuator, SpringDoc 2.8.16 |
| Seguridad API | JWT (jjwt 0.12.6) |
| Resiliencia Maps | Resilience4j 2.3.0, Caffeine |
| Frontend | Expo 57, React 19.2.3, React Native 0.86.3, expo-router, axios, stompjs, react-native-maps |
| BD | PostgreSQL 15, Liquibase |
| Infra | Docker Compose, Nginx (frontend web) |

## 3. Estructura de repositorios

| Repositorio | Contenido |
|---|---|
| Backend-Guardian-Escolar | API, Dockerfile, docker-compose standalone, `.env.example` |
| Fronted-GuardianEscolar- | App Expo, Dockerfile, nginx.conf, services, app routes |
| DB-Structure-Guardian-Escolar | 01_ddl, 02_dml, 03_dcl, changelog Liquibase |
| Docker-Infra-Guardian-Escolar | compose multi-servicio |

## 4. Módulos backend

Paquete base: `com.guardianescolar.api`

- `modules.auth` — cuenta, JWT, email, 2FA  
- `modules.students` — estudiantes, guardianes, contactos, link-device  
- `modules.routes` — rutas y paradas  
- `modules.trips` — trayectos y coordenadas  
- `modules.zones` — zonas seguras  
- `modules.maps` — proxy Google Maps  
- `modules.notifications` — inbox, settings, dispositivos  
- `modules.admin` — dashboard, alertas, audit, errors  
- `shared.security` / `shared.config` — JWT, WebSocket, SecurityConfig  

## 5. Inventario de endpoints

```text
GET    /  ,  GET /api/health

POST   /api/auth/register | login
GET    /api/auth/me | verify-email | confirm-login
POST   /api/auth/password/forgot | password/reset
PUT    /api/auth/profile | email | password
POST   /api/auth/2fa/request | resend | verify | disable

GET|POST       /api/students
PUT|DELETE     /api/students/{id}
POST           /api/students/{id}/routes/{routeId}
DELETE         /api/students/{id}/device
GET|POST       /api/students/{id}/guardians
POST           /api/students/link-device
GET            /api/students/{id}/linked-profile
CRUD           /api/students/{id}/emergency-contacts[/{contactId}]

CRUD           /api/routes[/{id}]
POST           /api/routes/{id}/stops

GET|POST       /api/trips
PATCH          /api/trips/{id}/status
POST|GET       /api/trips/{id}/coordinates

CRUD           /api/safe-zones[/{id}]

POST           /api/maps/geocode | reverse-geocode | directions
GET            /api/maps/places/autocomplete

GET            /api/notifications
PATCH          /api/notifications/{receiptId}/read
GET|PUT        /api/notifications/settings

GET|POST       /api/devices
DELETE         /api/devices/{id}

GET            /api/admin/dashboard | alerts | audit | errors

WS             /ws  →  /topic/trips/{tripId}/coordinates
```

## 6. Modelo de datos (resumen)

Tablas clave: `users`, `students` (+ `photo_data`), `student_guardians`, `emergency_contacts`, `roles`, `permissions`, `user_sessions`, `routes`, `stops`, `student_routes`, `safe_zones` (`radius_meters`), `trips`, `coordinates`, `notifications`, `notification_receipts`, `notification_settings`, `user_devices`, `student_devices`, `audit_logs`, `error_logs`, `activity_logs`.

Esquema gobernado por Liquibase; Hibernate `ddl-auto: validate`.

## 7. Configuración crítica

| Variable | Uso |
|---|---|
| DB_URL / DB_USERNAME / DB_PASSWORD | Conexión JDBC |
| JWT_SECRET / JWT_EXPIRATION_MINUTES | Emisión de tokens |
| CORS_ALLOWED_ORIGINS | Orígenes permitidos |
| GOOGLE_MAPS_API_KEY | Solo servidor (proxy maps) |
| APP_MAIL_ENABLED / MAIL_* | Correo |
| EXPO_PUSH_ENABLED | Push |
| SMS_ENABLED / SMS_* | SMS |
| GPS_* umbral | Desvío de ruta (verificar nombre exacto en YAML) |

## 8. WebSocket

- Endpoint SockJS: `/ws`
- Broker: `/topic`
- Auth: Bearer en CONNECT (`WebSocketAuthChannelInterceptor`)
- Cliente: `@stomp/stompjs` + `sockjs-client`

## 9. Frontend — puntos de extensión

- `services/api.js` — axios, interceptores JWT y sesión
- `config/endpoints.js` — resolución de host LAN/tunnel/Docker
- `contexts/AuthContext.js` — estado de sesión
- Plugins Expo: location, camera, notifications, secure-store

## 10. Observabilidad

Actuator expone: health, info, metrics, prometheus (según `management.endpoints.web.exposure.include`).

## 11. Limitaciones técnicas conocidas

- GPS background sujeto a políticas del SO/OEM.
- Correo/push/SMS inactivos si flags en false.
- Regex de email en BD con TLD restringidos.
- Posible inconsistencia de nombre de variable de umbral de desvío.

## 12. Referencias internas del paquete

- Diseño: `04_Diseno/04_Diseno_Software.md`
- Implementación: `06_Implementacion/06_Procedimiento_Implementacion.md`
- Pruebas: `05_Pruebas/05_Documentacion_Pruebas.md`
