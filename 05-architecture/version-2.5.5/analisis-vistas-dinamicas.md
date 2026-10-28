# Documentación de análisis del software — Vistas dinámicas

**Proyecto:** GPS Guardian Escolar
**Versión:** 2026-10-28
**Estado:** Revisado y actualizado

## 1. Objetivo

Describir el comportamiento dinámico del sistema a partir de los módulos, endpoints y servicios implementados: flujos de autenticación, vinculación de dispositivo, trayecto con GPS, zonas seguras y notificaciones.

## 2. Vista de contexto

```text
[Acudiente / Admin / Dispositivo estudiante]
        |  HTTPS REST (/api/**) + WSS/WS STOMP (/ws)
        v
[Backend Spring Boot :8080]
        |  JDBC
        v
[PostgreSQL :5432]
        ^
        |  Liquibase (migración)
[Google Maps API] <--- proxy autenticado (/api/maps)
[Expo Push / SMTP / SMS] <--- según flags de entorno
```

## 3. Flujos dinámicos principales

### 3.1 Registro y verificación de correo

1. Cliente envía `POST /api/auth/register`.
2. Backend valida datos, persiste `users` (email_verified=false) y genera token de verificación.
3. Si `APP_MAIL_ENABLED=true`, se envía correo; en caso contrario el flujo depende de configuración de desarrollo.
4. Usuario abre enlace → `GET /api/auth/verify-email?token=...`.
5. Backend marca correo verificado y responde éxito/error.

### 3.2 Login, sesión y 2FA

1. `POST /api/auth/login` con credenciales.
2. Si 2FA está activo, el flujo continúa con request/resend/verify de código.
3. Se emite JWT y se registra sesión en `user_sessions` (dispositivo, plataforma, IP).
4. Cliente almacena token (SecureStore / storage web) y lo adjunta en cabecera Authorization.
5. `GET /api/auth/me` confirma identidad y roles.

### 3.3 Alta de estudiante y vinculación de dispositivo

1. Acudiente autenticado: `POST /api/students` (nombre, grado, fecha nacimiento, foto opcional).
2. Opcional: contactos de emergencia, guardianes, asignación de ruta.
3. Generación/uso de enlace o QR de vinculación (frontend: `scan-qr`, servicios de student).
4. Dispositivo estudiante: `POST /api/students/link-device` con identificador de dispositivo.
5. Persistencia en `student_devices`; el dispositivo puede operar como perfil vinculado (`linked-profile`).

### 3.4 Definición de zona segura

1. `POST /api/safe-zones` con studentId, nombre, lat, lng, radiusMeters, inactivityAlertSeconds.
2. Backend valida radius_meters > 0 y persiste.
3. Frontend renderiza círculo en mapa nativo con radio en metros.

### 3.5 Trayecto activo y coordenadas

1. Inicio: `POST /api/trips` (studentId, routeId).
2. App del estudiante solicita permisos de ubicación (foreground/background según plataforma).
3. Tarea en segundo plano / servicio de tracking envía periódicamente `POST /api/trips/{tripId}/coordinates`.
4. Backend persiste en `coordinates`; puede evaluar desvío respecto a la ruta (umbral configurable).
5. Publicación STOMP a `/topic/trips/{tripId}/coordinates`.
6. Acudiente suscrito vía `@stomp/stompjs` + SockJS actualiza el mapa en tiempo casi real.
7. Cierre: `PATCH /api/trips/{tripId}/status` (p. ej. finalizado).

### 3.6 Notificaciones

1. Eventos de negocio (desvío, inactividad, llegada, etc.) generan filas en `notifications` y `notification_receipts`.
2. Cliente consulta `GET /api/notifications` y marca leídos.
3. Preferencias en `notification_settings`.
4. Si Expo Push está habilitado y existe token en `user_devices`, se intenta envío al endpoint de Expo.

### 3.7 Uso de mapas

1. Cliente autenticado llama `POST /api/maps/geocode` (u otros).
2. Backend aplica rate limit, caché Caffeine y, si es necesario, llama a Google Maps con la key de servidor.
3. Respuesta normalizada al cliente sin revelar la API key.

## 4. Diagrama de secuencia resumido — Tracking

```text
EstudianteApp          Backend              PostgreSQL           AcudienteApp
     |                    |                     |                      |
     | POST /trips        |                     |                      |
     |------------------->| INSERT trips        |                      |
     |                    |-------------------->|                      |
     | tripId             |                     |                      |
     |<-------------------|                     |                      |
     | POST /coordinates  |                     |                      |
     |------------------->| INSERT coordinates  |                      |
     |                    |-------------------->|                      |
     |                    | STOMP /topic/...    |                      |
     |                    |------------------------------------------->|
     |                    |                     |           actualiza mapa
```

## 5. Estados relevantes

| Entidad              | Estados / flags en código o BD                                    |
| -------------------- | ------------------------------------------------------------------ |
| Usuario              | is_active, email_verified, two_factor_enabled, deleted_at          |
| Sesión              | session_status (enum), email_confirmed                             |
| Guardian             | PENDING, ACTIVE, REVOKED                                           |
| Trayecto             | trip_status_enum (p. ej. PENDING y transiciones vía PATCH status) |
| Zona                 | is_active, deleted_at                                              |
| Notificación recibo | was_read                                                           |

## 6. Dependencias externas dinámicas

| Dependencia | Activación         | Efecto si no está disponible                                       |
| ----------- | ------------------- | ------------------------------------------------------------------- |
| Google Maps | GOOGLE_MAPS_API_KEY | Fallo controlado en /api/maps (circuit breaker / error de servicio) |
| SMTP        | APP_MAIL_ENABLED    | Sin envío real de correo                                           |
| Expo Push   | EXPO_PUSH_ENABLED   | Notificaciones solo persistidas / in-app                            |
| SMS         | SMS_ENABLED         | Canal SMS inactivo                                                  |

Los flujos incorporan autenticación por dispositivo, respuesta limitada ante intentos repetidos, protección de sesión y vinculación QR con código de uso controlado. Los estados de trayecto y el acceso compartido se validan en backend; notificaciones por correo, push y WebSocket dependen de la configuración de cada proveedor. Validar en web y Android con cuentas de prueba.
