# Informe de especificación de requisitos — GPS Guardian Escolar

**Versión:** 2026-10-28 (actualizada desde código)
**Estado:** Revisado y actualizado

## 1. Propósito

Definir los requisitos funcionales y no funcionales del sistema **GPS Guardian Escolar**, plataforma de seguimiento del trayecto escolar mediante ubicación GPS, vinculación de dispositivos, rutas, zonas seguras y notificaciones.

## 2. Alcance del producto

### Incluye

- Autenticación de usuarios (registro, login, verificación de correo, 2FA, recuperación de contraseña).
- Gestión de estudiantes, acudientes/guardianes y contactos de emergencia.
- Foto opcional del estudiante (`photo_data`).
- Vinculación de dispositivo del estudiante (QR o enlace).
- Definición de rutas escolares y paradas.
- Zonas seguras con radio en metros y alerta de inactividad.
- Trayectos activos con envío de coordenadas y difusión por WebSocket.
- Proxy autenticado a Google Maps (geocode, reverse, directions, autocomplete).
- Notificaciones in-app, preferencias por canal (push, email, SMS configurable).
- Panel administrativo (dashboard, alertas, auditoría, errores).
- Cliente web y móvil (Expo) y API REST + WebSocket.

### No incluye (no evidenciado en código como producto separado)

- Aplicación de escritorio nativa distinta de la web.
- Integración con sistemas escolares oficiales externos.
- Garantía de funcionamiento de GPS en segundo plano en todos los OEM sin prueba controlada.

## 3. Actores

| Actor                              | Descripción                                                                           |
| ---------------------------------- | -------------------------------------------------------------------------------------- |
| Acudiente / Guardian               | Usuario que registra estudiantes, define rutas y zonas, consulta seguimiento.          |
| Estudiante (dispositivo vinculado) | Dispositivo móvil vinculado por QR/enlace que reporta ubicación durante el trayecto. |
| Administrador                      | Usuario con acceso a `/api/admin` (dashboard, alertas, auditoría, errores).         |
| Sistema                            | Backend, base de datos, servicios de correo, push y SMS según flags de entorno.       |

## 4. Requisitos funcionales

### RF-01 Autenticación y cuenta

- RF-01.1 Registrar usuario con nombre, correo, teléfono opcional y contraseña.
- RF-01.2 Iniciar sesión y obtener JWT.
- RF-01.3 Verificar correo electrónico mediante token.
- RF-01.4 Recuperar y restablecer contraseña.
- RF-01.5 Solicitar, reenviar, verificar y deshabilitar 2FA.
- RF-01.6 Consultar perfil actual (`/api/auth/me`) y actualizar perfil, correo y contraseña.
- RF-01.7 Confirmar login en nuevo dispositivo cuando aplique.

### RF-02 Estudiantes y vínculos

- RF-02.1 Crear, listar, actualizar y eliminar (lógico) estudiantes.
- RF-02.2 Asociar foto opcional del estudiante.
- RF-02.3 Gestionar contactos de emergencia por estudiante.
- RF-02.4 Asignar guardianes adicionales (OWNER, GUARDIAN, VIEWER).
- RF-02.5 Vincular dispositivo mediante `POST /api/students/link-device`.
- RF-02.6 Desvincular dispositivo y consultar perfil vinculado.

### RF-03 Rutas

- RF-03.1 CRUD de rutas escolares.
- RF-03.2 Definir paradas ordenadas con coordenadas.
- RF-03.3 Asignar ruta a estudiante.

### RF-04 Zonas seguras

- RF-04.1 CRUD de zonas con latitud, longitud y `radius_meters` > 0.
- RF-04.2 Configurar `inactivity_alert_seconds` (por defecto 300).

### RF-05 Trayectos y tracking

- RF-05.1 Crear trayecto asociado a estudiante y ruta.
- RF-05.2 Cambiar estado del trayecto.
- RF-05.3 Registrar y consultar coordenadas (lat, lng, velocidad, tiempo detenido).
- RF-05.4 Difundir coordenadas por WebSocket a suscriptores autorizados.
- RF-05.5 Registrar desvío de ruta cuando se detecte (umbral configurable, valor por defecto 150 m).

### RF-06 Mapas

- RF-06.1 Geocodificación y geocodificación inversa vía backend autenticado.
- RF-06.2 Direcciones (directions) y autocompletado de lugares.
- RF-06.3 No exponer la API key de Google Maps en respuestas de estos endpoints.

### RF-07 Notificaciones

- RF-07.1 Listar notificaciones y marcar recibos como leídos.
- RF-07.2 Configurar preferencias (retraso, cambio de ruta, llegada, inactividad; canales push/email/sms).
- RF-07.3 Registrar dispositivos Expo Push.

### RF-08 Administración

- RF-08.1 Consultar dashboard, alertas, auditoría y errores.

## 5. Requisitos no funcionales

| ID     | Categoría       | Requisito                                                                                       |
| ------ | ---------------- | ----------------------------------------------------------------------------------------------- |
| RNF-01 | Seguridad        | Autenticación JWT; endpoints sensibles protegidos; WebSocket con Bearer en CONNECT.            |
| RNF-02 | Seguridad        | Secretos (JWT, Maps, SMS, correo) solo por variables de entorno.                                |
| RNF-03 | Disponibilidad   | Health en `/` y `/api/health`; Actuator (health, info, metrics, prometheus).                |
| RNF-04 | Portabilidad     | Cliente multiplataforma (iOS, Android, Web) con Expo 57.                                        |
| RNF-05 | Persistencia     | PostgreSQL 15; esquema gobernado por Liquibase; Hibernate en modo validate.                     |
| RNF-06 | Despliegue       | Docker Compose: database, liquibase, backend (8080), frontend Nginx (8081).                     |
| RNF-07 | Resiliencia Maps | Caché Caffeine, rate limit, reintentos y circuit breaker (Resilience4j).                       |
| RNF-08 | Privacidad       | Ubicación solo en contexto de trayecto; permisos de ubicación y cámara declarados en la app. |
| RNF-09 | Usabilidad       | Interfaz en español; flujos de auth, QR y mapas orientados a acudiente y estudiante.           |

## 6. Restricciones

- Java 17 y Spring Boot 3.5.x en backend.
- Expo SDK 57 en frontend.
- PostgreSQL como único motor de datos soportado en la configuración entregada.
- Correo, push y SMS deshabilitados por defecto (`APP_MAIL_ENABLED`, `EXPO_PUSH_ENABLED`, `SMS_ENABLED`).

## 7. Trazabilidad hacia implementación

| Requisito | Evidencia en código                                                                 |
| --------- | ------------------------------------------------------------------------------------ |
| RF-01     | `AuthController`, servicios de email y 2FA                                         |
| RF-02     | `StudentController`, `EmergencyContactController`, tabla `students.photo_data` |
| RF-03     | `RouteController`, tablas `routes`, `stops`, `student_routes`                |
| RF-04     | `SafeZoneController`, tabla `safe_zones.radius_meters`                           |
| RF-05     | `TripController`, `WebSocketConfig`, tablas `trips`, `coordinates`           |
| RF-06     | `MapsController`, módulo `maps`                                                 |
| RF-07     | `NotificationController`, `UserDeviceController`                                 |
| RF-08     | `AdminController`                                                                  |
