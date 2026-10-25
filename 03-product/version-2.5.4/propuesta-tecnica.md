# Documentación de propuesta técnica del software

**Proyecto:** GPS Guardian Escolar  
**Versión:** 2026-10-09 (actualizada desde código)  
**Estado:** Revisado y actualizado

---

## 1. Resumen de la propuesta

Implementar un sistema cliente-servidor con:

- **API REST + WebSocket** en Spring Boot 3.5.16 (Java 17).
- **Cliente unificado** Expo SDK 57 (React Native 0.86 / React 19) para Android, iOS y Web.
- **PostgreSQL 15** con migraciones Liquibase.
- **Orquestación Docker Compose** (database, liquibase, backend, frontend con Nginx).

Esta combinación permite un solo backend de negocio, un cliente multiplataforma y un esquema de datos versionado independiente del ORM (Hibernate en modo `validate`).

## 2. Arquitectura lógica propuesta (y adoptada)

```text
┌─────────────────────────────────────────────────────────┐
│  Clientes Expo (Web :8081 / Metro :8082 / Native)       │
│  - Auth, estudiantes, mapas, tracking, notificaciones   │
└───────────────────────┬─────────────────────────────────┘
                        │ REST /api  +  STOMP /ws
┌───────────────────────▼─────────────────────────────────┐
│  Backend Spring Boot                                    │
│  auth | students | routes | trips | zones | maps        │
│  notifications | admin | security | audit               │
└───────────────────────┬─────────────────────────────────┘
                        │ JDBC
┌───────────────────────▼─────────────────────────────────┐
│  PostgreSQL + Liquibase                                 │
└─────────────────────────────────────────────────────────┘
```

## 3. Decisiones tecnológicas

| Área | Decisión | Justificación en el proyecto |
|---|---|---|
| Backend | Spring Boot 3.5.16 + Java 17 | Ecosistema maduro REST/Security/JPA/WebSocket; Actuator y SpringDoc. |
| Seguridad | JWT (jjwt 0.12.6) + Spring Security | Stateless API; sesiones auditables en BD. |
| Tiempo real | STOMP sobre SockJS (`/ws`) | Suscripción por trayecto sin long-polling ad hoc. |
| Mapas | Proxy servidor a Google Maps | Evitar exponer API key de negocio; caché y rate limit. |
| Cliente | Expo 57 + expo-router | Un código para móvil y web; plugins de ubicación, cámara, notificaciones. |
| Datos | PostgreSQL + Liquibase | Control explícito de DDL/DML/DCL; rollbacks y patches. |
| Despliegue | Docker Compose | Reproducibilidad local y de demostración. |

## 4. Módulos de backend propuestos

Correspondencia 1:1 con paquetes `com.guardianescolar.api.modules.*`:

| Módulo | Capacidad |
|---|---|
| auth | Cuenta, JWT, email, 2FA, perfil |
| students | Estudiantes, guardianes, contactos, link-device |
| routes | Rutas y paradas |
| trips | Trayectos y coordenadas |
| zones | Zonas seguras |
| maps | Geocoding, directions, autocomplete |
| notifications | Inbox, settings, dispositivos push |
| admin | Dashboard y auditoría |
| audit / security | Logs y políticas |

## 5. Contratos de interfaz (resumen)

- Prefijo REST: `/api`
- Autenticación: `Authorization: Bearer <jwt>`
- Públicos: register, login, verify-email, confirm-login, password forgot/reset, 2fa resend/verify
- WebSocket: endpoint `/ws`, tópico `/topic/trips/{tripId}/coordinates`
- Health: `/` y `/api/health`

El inventario completo de endpoints se documenta en el diseño y en el manual técnico.

## 6. Datos y persistencia

- Claves primarias UUID.
- Soft-delete (`deleted_at`) en entidades de negocio relevantes.
- Enums de dominio (roles, estados de sesión/trayecto, plataformas).
- Columna `photo_data` TEXT en `students` para fotografía opcional.
- `radius_meters` obligatorio y positivo en zonas seguras.

## 7. Seguridad y configuración

- Secretos solo en entorno (`.env` / variables de contenedor).
- CORS configurable (`CORS_ALLOWED_ORIGINS`).
- HTTPS opcional vía `REQUIRE_HTTPS`.
- Rate limit y circuit breaker en el módulo Maps.
- Interceptor JWT en canal STOMP CONNECT.

## 8. Alternativas consideradas (y no adoptadas en el código entregado)

| Alternativa | Motivo de no adopción en el estado actual |
|---|---|
| Firebase como backend principal | El código centraliza lógica en Spring Boot y PostgreSQL. |
| GraphQL | API es REST + WebSocket. |
| ORM como dueño del esquema | Se eligió Liquibase + `ddl-auto: validate`. |
| App nativa pura (Swift/Kotlin sin Expo) | El cliente unificado es Expo. |

## 9. Riesgos técnicos reconocidos

| Riesgo | Mitigación observada / pendiente |
|---|---|
| Restricciones OEM de GPS en segundo plano | Permisos y foreground service declarados; falta prueba formal en dispositivos reales. |
| Coste y cuotas Google Maps | Caché, rate limit y proxy servidor. |
| Desfase esquema BD vs entidades | Liquibase como fuente de verdad; arranque falla si hay divergencia. |
| Nombre de variable de desvío GPS | Posible inconsistencia entre `.env` (`GPS_DESVIO_UMBRAL_METROS`) y YAML (`GPS_ROUTE_DEVIATION_THRESHOLD_METERS`). |

## 10. Registro de actualización

| Fecha | Cambio | Motivo |
|---|---|---|
| 2026-10-09 | Propuesta alineada a stacks y módulos reales del código | Lista 2.5.4 “Revisar o actualizar” |
