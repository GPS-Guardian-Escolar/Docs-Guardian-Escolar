# Manual de instalación — GPS Guardian Escolar

**Versión:** 2026-10-09  
**Estado:** Elaborado desde cero (entregable faltante en lista 2.5.4)  
**Audiencia:** quien despliega el entorno de desarrollo o demostración.

---

## 1. Opciones de instalación

| Opción | Cuándo usarla |
|---|---|
| A. Docker Compose | Demostración o entorno completo con un comando |
| B. Componentes separados | Desarrollo activo de un solo repositorio |

## 2. Requisitos previos

- Docker Engine y Docker Compose **o** Java 17 + Node.js (Expo 57) + PostgreSQL 15.
- Puertos libres: 5432, 8080, 8081 (y 8082 si usa Metro).
- Clave JWT propia; opcionalmente API key de Google Maps.

## 3. Instalación con Docker (recomendada)

### 3.1 Preparar archivos

1. Disponer los repos Backend, Frontend, DB-Structure y Docker-Infra en la disposición de rutas que usa el compose (contexts relativos `../...`).
2. En `Docker-Infra-Guardian-Escolar`:

```bash
cp .env.example .env
```

3. Editar `.env` y definir al menos:

```text
POSTGRES_PASSWORD=***
JWT_SECRET=***
```

### 3.2 Levantar

```bash
docker compose --env-file .env up --build
```

### 3.3 Comprobar

| URL | Esperado |
|---|---|
| http://localhost:8081 | Interfaz web |
| http://localhost:8080/api/health | Salud del API |
| localhost:5432 | PostgreSQL |

El contenedor Liquibase aplica migraciones y termina.

### 3.4 Detener

```bash
docker compose down          # conserva volumen de datos
docker compose down -v       # elimina datos (irreversible en ese volumen)
```

## 4. Instalación por componentes

### 4.1 PostgreSQL + Liquibase

1. Crear instancia PostgreSQL 15 y base `gps_guardian_escolar`.
2. Ejecutar Liquibase del repo DB-Structure (`liquibase update` con properties del repo).
3. Verificar existencia de tablas `users`, `students`, `trips`, etc.

### 4.2 Backend

```bash
cd Backend-Guardian-Escolar
cp .env.example .env
# completar DB_* y JWT_SECRET
./mvnw spring-boot:run
```

### 4.3 Frontend

```bash
cd Fronted-GuardianEscolar-
npm install
# crear .env con EXPO_PUBLIC_API_URL y EXPO_PUBLIC_WS_URL
npm run web:lan      # web :8081
# o
npm run start:lan    # Metro :8082
```

**Dispositivo físico:** use la IP LAN del host, no `localhost`.

## 5. Configuración móvil adicional

- Package/Bundle: `com.guardianescolar.app`
- Para funciones de ubicación en segundo plano, preferir development build / binario nativo.
- Variables EAS / `EXPO_PUBLIC_GOOGLE_MAPS_API_KEY` según necesidad del mapa nativo.

## 6. Problemas frecuentes

| Síntoma | Causa probable | Acción |
|---|---|---|
| Backend no arranca por esquema | Liquibase no aplicado o divergencia JPA | Reejecutar migrate; revisar entidades |
| CORS en navegador | Origen no listado | Ajustar CORS_ALLOWED_ORIGINS |
| App móvil no alcanza API | URL localhost en dispositivo | Usar IP LAN o tunnel |
| Maps 5xx | API key ausente o cuota | Configurar GOOGLE_MAPS_API_KEY |
| No llegan correos | APP_MAIL_ENABLED=false | Habilitar y configurar SMTP |

## 7. Desinstalación

- Docker: `docker compose down -v` y eliminar imágenes del proyecto si se desea.
- Local: detener procesos Java/Node y eliminar base de datos de desarrollo.

## 8. Seguridad post-instalación

- Cambiar contraseñas y JWT_SECRET por valores únicos.
- No publicar `.env` ni claves Maps en repositorios públicos.
- Limitar exposición de puertos en redes no confiables.
