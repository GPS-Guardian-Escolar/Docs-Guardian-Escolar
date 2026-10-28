# Documentación de diseño del software

**Proyecto:** GPS Guardian Escolar<br>
**Versión:** 2026-10-28 (actualizada desde código)<br>
**Estado:** Revisado y actualizado<br>
**Contenido:** interfaces gráficas, modelo arquitectónico, patrones de diseño y modelo de datos.


**Versión documental:** 2.5.5<br>
**Corte de código:** 2026-10-28<br>
**Snapshots:** Backend `345a8b3`; Frontend `d0648c1`; DB `e6b84c5`; Infra `d06a61c`.<br>
**Evidencia:** revisión estática; ejecución de CI y validación en dispositivos pendientes.
---

## 1. Modelo arquitectónico

### 1.1 Estilo

Arquitectura en capas cliente-servidor con API REST y mensajería STOMP:

1. **Presentación:** aplicación Expo (pantallas auth, tabs, QR, mapas).
2. **Aplicación / API:** controladores Spring REST + broker WebSocket.
3. **Dominio y servicios:** servicios por módulo (auth, students, trips, …).
4. **Persistencia:** repositorios JPA + PostgreSQL.
5. **Infraestructura:** Docker, Liquibase, Nginx (proxy `/api` y `/ws`).

### 1.2 Despliegue lógico

| Contenedor / proceso | Puerto | Responsabilidad |
|---|---|---|
| frontend (Nginx + static Expo web) | 8081 | UI web y reverse proxy |
| backend | 8080 | API y WebSocket |
| database | 5432 | Datos |
| liquibase | — (one-shot) | Migraciones |

### 1.3 Comunicación

- REST JSON bajo `/api/**`.
- WebSocket SockJS en `/ws`; destinos de aplicación `/app`; broker `/topic`.
- En Docker, el navegador puede usar rutas relativas `/api` y `/ws` (mismo origen 8081).

## 2. Patrones de diseño aplicados

| Patrón | Dónde se observa |
|---|---|
| Modularización por dominio | Paquetes `modules/auth`, `students`, `routes`, `trips`, `zones`, `maps`, `notifications`, `admin` |
| DTO | Clases de transferencia en controladores/servicios (p. ej. AdminDtos) |
| Filter / Interceptor | `JwtAuthenticationFilter`; `WebSocketAuthChannelInterceptor` |
| Proxy | `MapsController` / `GoogleMapsClient` frente a Google Maps Platform |
| Cache | Caffeine en módulo maps (TTL geocoding/directions) |
| Circuit breaker / Retry / Rate limit | Resilience4j y rate limiter en maps |
| Repository | Spring Data JPA |
| Dependency injection | Constructores / Lombok `@RequiredArgsConstructor` |
| Front controller (cliente) | expo-router (`app/(auth)`, `app/(tabs)`) |
| Context / Provider (cliente) | AuthContext, ThemeContext, LanguageContext, UserRoleContext |

## 3. Interfaces gráficas (cliente)

### 3.1 Estructura de navegación

```text
app/
  (auth)/
    welcome, login, register, verify-email, verify-code,
    forgot-password, reset-password, confirm-login,
    choose-recovery-action, verify-recovery-code
  (tabs)/
    index, student, tracking, zones, history,
    notifications, profile, admin
  scan-qr.js
  student-dashboard.js
```

### 3.2 Capacidades de UI por área

| Área | Elementos de interfaz presentes en código |
|---|---|
| Autenticación | Formularios de login/registro, verificación, recuperación, 2FA |
| Estudiantes | Listado/alta/edición, foto opcional, contactos, guardianes |
| Vinculación | Escáner QR (expo-camera) e ingreso manual |
| Mapas / tracking | react-native-maps; círculos de zonas con radio en metros; ruta y posición |
| Notificaciones | Lista y preferencias |
| Perfil | Datos de cuenta y seguridad |
| Admin | Vistas de panel (consumo de `/api/admin`) |

### 3.3 Componentes reutilizables

- `components/common`: CustomButton, CustomInput, CalendarDatePicker, JsonView
- `components/ui`: AppButton, AppCard
- `components/layout`: layouts de pantalla

### 3.4 Permisos de plataforma (diseño de interacción)

- Cámara: escaneo QR de vinculación.
- Ubicación en uso y en segundo plano: trayecto escolar.
- Foreground service (Android): continuidad del tracking.
- Notificaciones: alertas al acudiente.

## 4. Diseño de la API (contratos)

### 4.1 Convenciones

- Base: `/api`
- Identificadores: UUID en path
- Errores: manejados por `ApiExceptionHandler`
- Documentación interactiva: SpringDoc OpenAPI

### 4.2 Mapa de recursos

Ver inventario en sección 10 del documento técnico de código o el manual técnico de este paquete. Resumen de grupos:

- `/api/auth/**`
- `/api/students/**` (+ emergency-contacts, link-device)
- `/api/routes/**`
- `/api/trips/**`
- `/api/safe-zones/**`
- `/api/maps/**`
- `/api/notifications/**`
- `/api/devices/**`
- `/api/admin/**`
- `/api/health`

### 4.3 WebSocket

- Endpoint: `/ws` (SockJS)
- Auth: cabecera Authorization Bearer en CONNECT
- Suscripción típica: `/topic/trips/{tripId}/coordinates`

## 5. Modelo de datos

### 5.1 Dominios

1. **Usuarios y estudiantes:** users, students, student_guardians, emergency_contacts<br>
2. **Seguridad:** roles, permissions, user_roles, role_permissions, user_sessions, password_policies, security_settings<br>
3. **Rutas:** routes, stops, student_routes<br>
4. **Zonas:** safe_zones<br>
5. **Trayectos:** trips, coordinates<br>
6. **Notificaciones:** notification_settings, notifications, notification_receipts<br>
7. **Dispositivos:** user_devices, student_devices<br>
8. **Auditoría:** activity_logs, audit_logs, error_logs<br>

### 5.2 Relaciones principales

```text
users 1──* students
users *──* students          (via student_guardians)
students 1──* emergency_contacts
students *──* routes         (via student_routes)
routes 1──* stops
students 1──* safe_zones
students 1──* trips
routes 1──* trips
trips 1──* coordinates
users 1──1 notification_settings
notifications 1──* notification_receipts
users 1──* user_devices
students 1──* student_devices
```

### 5.3 Atributos críticos

| Tabla | Atributo | Nota de diseño |
|---|---|---|
| students | photo_data (TEXT) | Foto opcional |
| safe_zones | radius_meters | CHECK > 0 |
| safe_zones | inactivity_alert_seconds | Default 300 |
| trips | had_route_deviation | Flag de desvío |
| coordinates | speed_kmh, stopped_seconds | Telemetría |
| users | email | CHECK regex TLD restringido |
| student_devices | device_identifier | UNIQUE |

### 5.4 Gobernanza del esquema

- Scripts en `01_ddl`, `02_dml`, `03_dcl`.
- Patches DML para endurecer reglas de registro y URLs de confirmación de dispositivo.
- Índices y constraints de normalización en `10_indexes`.
- Triggers de `updated_at`.

## 6. Diseño de seguridad

- Hash de contraseñas gestionado por el stack Spring Security (configuración en `SecurityConfig`).
- JWT con secreto y expiración configurables.
- Separación de endpoints públicos y protegidos.
- Soft-delete y auditoría de acciones/errores.
- No persistir API keys de Maps en código fuente.

## 7. Diseño de resiliencia (Maps)

- Timeouts de conexión/lectura configurables.
- Caché con TTL diferenciado (geocoding vs directions).
- Reintentos, circuit breaker y límite de peticiones por minuto.

## 8. Registro de actualización

| Fecha | Cambio | Motivo |
|---|---|---|
| 2026-10-09 | Arquitectura, patrones, UI, API y modelo de datos alineados al código | Lista 2.5.4 “Revisar o actualizar” |

## Actualización técnica — 28 de octubre de 2026

La actualización documenta tokens de sesión y códigos de enlace protegidos, identificador de dispositivo, límite de intentos, acceso de acudientes y validación de trayectos. La base separa rol de aplicación y administrador, incluye cambios de auditoría y direcciones, y expone retención manual restringida de coordenadas. Los diagramas UML conservados siguen siendo referencias históricas.
