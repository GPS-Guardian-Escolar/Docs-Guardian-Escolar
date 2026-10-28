# Procedimiento de implementación del software

**Proyecto:** GPS Guardian Escolar<br>
**Versión:** 2026-10-28<br>
**Estado:** Actualizado desde código y evidencia disponible al 28 de octubre de 2026


**Versión documental:** 2.5.5<br>
**Corte de código:** 2026-10-28<br>
**Snapshots:** Backend `345a8b3`; Frontend `d0648c1`; DB `e6b84c5`; Infra `d06a61c`.<br>
**Evidencia:** revisión estática; ejecución de CI y validación en dispositivos pendientes.
---

## 1. Objetivo

Describir un procedimiento reproducible para desplegar el sistema en entorno local o de demostración usando Docker Compose o componentes por separado.

## 2. Prerrequisitos

| Herramienta | Uso |
|---|---|
| Docker + Docker Compose | Stack completo |
| Java 17 + Maven Wrapper | Backend fuera de Docker |
| Node.js (compatible Expo 57) + npm | Frontend fuera de Docker |
| Cliente PostgreSQL (opcional) | Inspección de datos |
| Git | Obtención de repositorios |

Repositorios necesarios (rutas relativas típicas del monorepo de trabajo):

- `Backend-Guardian-Escolar`
- `Fronted-GuardianEscolar-` (nombre del repo con la grafía del código fuente)
- `DB-Structure-Guardian-Escolar`
- `Docker-Infra-Guardian-Escolar`

## 3. Variables de entorno mínimas

Crear `.env` (no versionar secretos reales):

```text
POSTGRES_DB=gps_guardian_escolar
POSTGRES_USER=postgres
POSTGRES_PASSWORD=<secreto>
POSTGRES_PORT=5432

BACKEND_PORT=8080
FRONTEND_PORT=8081

JWT_SECRET=<cadena-larga-aleatoria>
JWT_EXPIRATION_MINUTES=30
REQUIRE_HTTPS=false

CORS_ALLOWED_ORIGINS=http://localhost:8081,http://127.0.0.1:8081
FRONTEND_BASE_URL=http://localhost:8081
API_PUBLIC_BASE_URL=http://localhost:8080

EXPO_PUBLIC_API_URL=/api
EXPO_PUBLIC_WS_URL=/ws
EXPO_PUBLIC_STUDENT_LINK_BASE_URL=/
EXPO_PUBLIC_GOOGLE_MAPS_API_KEY=

APP_MAIL_ENABLED=false
EXPO_PUSH_ENABLED=false
SMS_ENABLED=false
GOOGLE_MAPS_API_KEY=
GPS_DESVIO_UMBRAL_METROS=150
```

Para desarrollo móvil en LAN, sustituir URLs públicas por `http://<IP-LAN>:8080/api` y `ws://<IP-LAN>:8080/ws`.

## 4. Procedimiento A — Stack completo con Docker

1. Colocar los cuatro repositorios como espera el `docker-compose.yaml` (contextos `../Backend-Guardian-Escolar`, `../DB-Structure-Guardian-Escolar`, `../Fronted-GuardianEscolar-`).
2. Copiar `.env.example` → `.env` en la carpeta de infra y completar secretos.
3. Desde la carpeta Docker-Infra:

```bash
docker compose --env-file .env up --build
```

4. Verificar:
   - http://localhost:8081 (frontend)
   - http://localhost:8080/api/health (backend)
   - PostgreSQL en 5432
5. El servicio `liquibase` debe completar `update` una vez y salir.
6. Detener conservando datos: `docker compose down`<br>
   Borrar volumen de datos: `docker compose down -v`

## 5. Procedimiento B — Componentes separados

### 5.1 Base de datos

1. Levantar PostgreSQL 15 con base `gps_guardian_escolar`.
2. Ejecutar Liquibase del repo DB-Structure contra ese motor (properties y changelog del repositorio).
3. Confirmar tablas de dominios users, routes, trips, safe_zones, notifications, etc.

### 5.2 Backend

1. Copiar `.env.example` del backend y apuntar `DB_URL` al PostgreSQL local.
2. Definir `JWT_SECRET` y, si aplica, `GOOGLE_MAPS_API_KEY`.
3. Ejecutar:

```bash
./mvnw spring-boot:run
```

4. Comprobar `/api/health`.
5. (Opcional) `./mvnw test`.

### 5.3 Frontend

1. En el repo frontend: `npm install`.
2. Configurar `.env` local con `EXPO_PUBLIC_*`.
3. Web: `npm run web:lan` (puerto 8081).
4. Móvil/dev: `npm run start:lan` (Metro 8082) o development build según EAS del proyecto.
5. Validar login contra el backend alcanzable.

## 6. Orden de arranque recomendado

1. Database healthy<br>
2. Liquibase update<br>
3. Backend<br>
4. Frontend<br>

## 7. Verificación post-implementación

| Chequeo | Criterio de éxito |
|---|---|
| Health backend | Respuesta OK en `/api/health` |
| Frontend carga | UI de welcome/login visible |
| Registro/login | Obtención de JWT |
| Migraciones | Tablas y seeds de catálogo presentes |
| Proxy Docker | `/api` y `/ws` desde el origen 8081 |

## 8. Rollback

- Aplicación: volver a imagen/commit anterior y reiniciar contenedores.
- Datos: restaurar volumen o backup de PostgreSQL; no usar `down -v` salvo entorno desechable.
- Esquema: utilizar cambiosets de rollback de Liquibase cuando existan para el changeset afectado.

## 9. Seguridad en la implementación

- No incluir secretos en imágenes ni en repositorios.
- Rotar `JWT_SECRET` y contraseñas al pasar de demo a entorno compartido.
- Restringir CORS a orígenes reales.
- Mantener `APP_MAIL_ENABLED`, push y SMS en false hasta configurar proveedores.

## 10. Implementación móvil (notas)

- Bundle ID / package: `com.guardianescolar.app`.
- Permisos de ubicación y cámara ya declarados en `app.json`.
- Para tracking en background se requiere development build o binario nativo; Expo Go puede limitar capacidades de segundo plano.

## 11. Registro

| Fecha | Evento |
|---|---|
| 2026-10-09 | Procedimiento creado como entregable faltante 2.5.4 |

## Actualización técnica — 28 de octubre de 2026

Compose espera salud de PostgreSQL antes de Liquibase, y migración completada antes de iniciar backend. El bootstrap inicial de administrador productivo requiere `LIQUIBASE_CONTEXTS=prod,bootstrap` y secretos inyectados; despliegues posteriores usan solo `prod`. Mantener `APP_DB_PASSWORD` separado de la cuenta administradora, respetar su mínimo documentado de 32 caracteres y no guardar secretos en Git. La URL `EXPO_PUBLIC_ANDROID_APK_URL` habilita el botón de descarga si existe artefacto vigente.
