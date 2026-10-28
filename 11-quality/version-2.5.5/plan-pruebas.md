# Documentación de pruebas

**Proyecto:** GPS Guardian Escolar

## 1. Objetivo

Definir el plan, los tipos de prueba, los casos reproducibles y los límites de evidencia a partir del código existente. No se reportan resultados de ejecución en dispositivos reales no verificados en este análisis.

## 2. Alcance

| Dentro de alcance                                      | Fuera de alcance (hasta prueba controlada)              |
| ------------------------------------------------------ | ------------------------------------------------------- |
| Pruebas unitarias backend existentes (`./mvnw test`) | Certificación de GPS en segundo plano en todos los OEM |
| Pruebas de contrato REST (casos diseñados)            | Pruebas de carga/producción                            |
| Checklist de UI/flujos frontend                        | Medición de consumo de batería en campo               |
| Verificación de migraciones Liquibase                 | Pruebas de penetración formales                        |
| Smoke de Docker Compose                                | Cumplimiento legal/privacidad externo                   |

## 3. Estrategia

1. **Unitarias (backend):** suite Maven actual; README del backend indica expectativa de BUILD SUCCESS con 3 tests en el estado documentado de esa rama.
2. **Integración ligera:** API + PostgreSQL levantados (local o Docker); casos de auth, CRUD estudiantes, trips, zonas.
3. **Cliente:** lint (`npm run lint`); recorridos manuales de auth, QR, mapas y tracking en web y, cuando sea posible, development build.
4. **Infraestructura:** `docker compose up --build` y health de servicios.
5. **Regresión:** re-ejecutar tras cambios en Liquibase o contratos `/api`.

## 4. Entorno de prueba recomendado

| Componente | Configuración                                                       |
| ---------- | -------------------------------------------------------------------- |
| SO host    | Linux/Windows/macOS con Docker                                       |
| Backend    | Java 17, puerto 8080                                                 |
| Frontend   | Node compatible con Expo 57, puertos 8081/8082                       |
| BD         | PostgreSQL 15, base `gps_guardian_escolar`                         |
| Variables  | `.env` con JWT_SECRET, POSTGRES_PASSWORD; Maps/Push/SMS opcionales |

## 5. Casos de prueba (diseño)

### 5.1 Autenticación

| ID         | Caso                     | Pasos                                                  | Resultado esperado                      |
| ---------- | ------------------------ | ------------------------------------------------------ | --------------------------------------- |
| TP-AUTH-01 | Registro válido         | POST /api/auth/register con datos válidos             | 2xx; usuario creado                     |
| TP-AUTH-02 | Login correcto           | POST /api/auth/login                                   | JWT en respuesta                        |
| TP-AUTH-03 | Login incorrecto         | Credenciales inválidas                                | 4xx; sin token usable                   |
| TP-AUTH-04 | Ruta protegida sin token | GET /api/auth/me sin Authorization                     | 401/403                                 |
| TP-AUTH-05 | Ruta protegida con token | GET /api/auth/me con Bearer                            | Datos del usuario                       |
| TP-AUTH-06 | Verify email             | GET /api/auth/verify-email con token válido/inválido | Éxito / error controlado               |
| TP-AUTH-07 | Forgot/reset password    | Flujo forgot → reset                                  | Actualización de hash si token válido |

### 5.2 Estudiantes y dispositivo

| ID        | Caso                        | Resultado esperado                                         |
| --------- | --------------------------- | ---------------------------------------------------------- |
| TP-STU-01 | CRUD estudiante autenticado | Creación/listado/actualización/borrado lógico           |
| TP-STU-02 | Foto opcional               | Persistencia de photo_data sin romper listados             |
| TP-STU-03 | Contactos de emergencia     | CRUD bajo /emergency-contacts                              |
| TP-STU-04 | link-device                 | Registro en student_devices; linked-profile consultable    |
| TP-STU-05 | Desvincular dispositivo     | DELETE device; dispositivo deja de reportar como vinculado |

### 5.3 Rutas, zonas y trips

| ID       | Caso                            | Resultado esperado                             |
| -------- | ------------------------------- | ---------------------------------------------- |
| TP-RT-01 | CRUD ruta + stops               | Paradas ordenadas persistidas                  |
| TP-ZN-01 | Zona con radius_meters válido  | 2xx                                            |
| TP-ZN-02 | Zona con radius_meters ≤ 0     | 4xx validación                                |
| TP-TR-01 | Crear trip y enviar coordenadas | Filas en trips y coordinates                   |
| TP-TR-02 | Cambiar status                  | Estado actualizado                             |
| TP-TR-03 | Suscripción WebSocket          | Mensajes en tópico del trip (con JWT válido) |

### 5.4 Maps y notificaciones

| ID       | Caso                                  | Resultado esperado       |
| -------- | ------------------------------------- | ------------------------ |
| TP-MP-01 | Geocode con JWT y API key configurada | Respuesta de geocoding   |
| TP-MP-02 | Geocode sin JWT                       | 401/403                  |
| TP-NT-01 | Listar notificaciones y marcar leído | was_read actualizado     |
| TP-NT-02 | Actualizar settings                   | Preferencias persistidas |

### 5.5 Admin e infraestructura

| ID       | Caso                                  | Resultado esperado                             |
| -------- | ------------------------------------- | ---------------------------------------------- |
| TP-AD-01 | /api/admin/dashboard con rol adecuado | 2xx con datos agregados                        |
| TP-IN-01 | GET /api/health                       | UP                                             |
| TP-IN-02 | docker compose up                     | Servicios healthy; frontend 8081, backend 8080 |
| TP-IN-03 | liquibase update                      | Changelog aplicado sin error                   |

### 5.6 Frontend (checklist manual)

| ID       | Flujo                       | Verificar                                                   |
| -------- | --------------------------- | ----------------------------------------------------------- |
| TP-FE-01 | Login/registro en web       | Navegación post-auth a tabs                                |
| TP-FE-02 | Scanner QR / ingreso manual | Llamada a link-device                                       |
| TP-FE-03 | Mapa de zona                | Círculo con radio en metros                                |
| TP-FE-04 | Tracking                    | Actualización de posición (si hay trip activo y permisos) |
| TP-FE-05 | Sesión expirada            | Limpieza de storage ante 401/403                            |

## 6. Pruebas automatizadas existentes

- Backend: `./mvnw test`
- Frontend: `npm run lint`
- No se documenta aquí un porcentaje de cobertura medido; debe obtenerse en CI o local con los plugins del equipo.

## 7. Criterios de aceptación de release de documentación

- Casos TP-AUTH, TP-STU, TP-RT, TP-ZN, TP-TR y TP-IN ejecutados al menos una vez en entorno controlado.
- Fallos registrados en el documento de acciones correctivas.
- No afirmar “probado en dispositivo X” sin evidencia adjunta (capturas, logs, fecha).

## 8. Registro de defectos (plantilla)

| ID   | Fecha | Severidad       | Módulo | Descripción | Estado          |
| ---- | ----- | --------------- | ------- | ------------ | --------------- |
| DEF- |       | Alta/Media/Baja |         |              | Abierto/Cerrado |

## 9. Límites de evidencia (explícitos)

La existencia de endpoints y pantallas **no** demuestra por sí sola:

- Funcionamiento estable de GPS en background en Honor u otros OEM.
- Entrega real de push/SMS/correo si los flags están en false.
- Exactitud cartográfica de Google Maps en todas las regiones.

Esas verificaciones deben ejecutarse y adjuntarse como anexos de prueba.

## Actualización técnica — 28 de octubre de 2026

Los repositorios contienen workflows de calidad para backend, frontend y base de datos/infraestructura; no se declara un resultado de ejecución en esta revisión. Añadir evidencia para bloqueo/429, revocación de sesión, rechazo de QR reutilizado, acceso compartido, transiciones de viaje, bootstrap de administrador, retención, APK y GPS Android real.
