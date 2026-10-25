# Artículo del proyecto — GPS Guardian Escolar

**Versión:** 2026-10-09  
**Estado:** Elaborado desde cero (entregable faltante en lista 2.5.4)  
**Nota:** Este artículo describe el sistema a partir del código y la documentación técnica del paquete. No presenta resultados empíricos de campo no medidos.

---

## Resumen

**GPS Guardian Escolar** es un sistema de software orientado al acompañamiento del trayecto escolar mediante geolocalización. Integra una API REST y un canal WebSocket implementados con Spring Boot 3.5.16 (Java 17), un cliente multiplataforma basado en Expo SDK 57 (web, Android e iOS) y una base de datos PostgreSQL 15 versionada con Liquibase. El sistema permite registrar acudientes y estudiantes, vincular un dispositivo móvil al estudiante, definir rutas y zonas seguras con radio en metros, transmitir coordenadas durante el trayecto y gestionar notificaciones y un panel administrativo. El despliegue de referencia se realiza con Docker Compose.

## 1. Introducción

El desplazamiento diario entre el hogar y la institución educativa es un proceso en el que las familias requieren información oportuna sobre la ubicación del estudiante. GPS Guardian Escolar aborda ese problema con una solución cliente-servidor que separa claramente la lógica de negocio (backend), la experiencia de usuario (aplicación Expo) y la persistencia (PostgreSQL).

El presente artículo sintetiza la arquitectura, los módulos funcionales y las decisiones técnicas observables en el código fuente de los repositorios de backend, frontend, estructura de base de datos e infraestructura Docker.

## 2. Objetivos del sistema

1. Autenticar usuarios con mecanismos de verificación de correo y segundo factor.
2. Administrar estudiantes, contactos de emergencia y guardianes adicionales.
3. Vincular un dispositivo al estudiante (código QR o enlace).
4. Modelar rutas escolares, paradas y zonas seguras.
5. Registrar trayectos y coordenadas, con difusión casi en tiempo real.
6. Ofrecer notificaciones y preferencias por canal.
7. Proveer un panel de administración y registros de auditoría.

## 3. Arquitectura

La arquitectura es en capas:

- **Clientes:** aplicación Expo con enrutamiento por carpetas (`(auth)`, `(tabs)`), servicios HTTP (axios) y cliente STOMP/SockJS.
- **API:** módulos Spring por dominio (auth, students, routes, trips, zones, maps, notifications, admin).
- **Datos:** PostgreSQL; el esquema no lo crea Hibernate (`ddl-auto: validate`), sino Liquibase.
- **Integraciones:** Google Maps a través de un proxy autenticado en el servidor; correo, Expo Push y SMS activables por configuración.

En el entorno Docker, Nginx sirve el frontend web en el puerto 8081 y reenvía `/api` y `/ws` al backend (8080), lo que simplifica demos con un único origen público.

## 4. Funcionalidades principales

**Identidad y seguridad.** Registro, login con JWT, verificación de correo, recuperación de contraseña, 2FA y gestión de sesiones de dispositivo.

**Estudiantes.** Alta y mantenimiento de estudiantes, fotografía opcional, contactos de emergencia, asignación de rutas y vinculación/desvinculación de dispositivos.

**Georreferenciación.** Rutas con paradas; zonas seguras definidas por centro y radio en metros; geocodificación y direcciones mediante el módulo maps del backend.

**Trayectos.** Creación de viajes, envío de coordenadas (latitud, longitud, velocidad, tiempo detenido), posible marcación de desvío y publicación en tópicos WebSocket por identificador de trayecto.

**Notificaciones y administración.** Bandeja y preferencias; dispositivos push; consultas de dashboard, alertas, auditoría y errores.

## 5. Modelo de datos (síntesis)

El dominio se organiza en usuarios, estudiantes y guardianes; roles y permisos; rutas y paradas; zonas seguras; trayectos y coordenadas; notificaciones y recibos; dispositivos de usuario y de estudiante; y tablas de auditoría. Las claves son UUID. Varias entidades incorporan borrado lógico (`deleted_at`). La columna `photo_data` almacena la foto opcional del estudiante; `radius_meters` condiciona la geometría de las zonas seguras.

## 6. Implementación y operación

El procedimiento de referencia consiste en definir variables de entorno (base de datos, JWT, orígenes CORS, claves opcionales de Maps) y ejecutar Docker Compose, o bien levantar PostgreSQL/Liquibase, el backend con Maven y el frontend con npm de forma independiente. Los puertos documentados son 5432 (base de datos), 8080 (API) y 8081 (web).

## 7. Limitaciones y trabajo futuro

El análisis estático del código no sustituye pruebas en dispositivos reales: el comportamiento del GPS en segundo plano depende del sistema operativo y del fabricante. Correo, push y SMS permanecen desactivados hasta configurar proveedores. Se identificaron mejoras prioritarias de unificación de variables de configuración, ampliación de pruebas automatizadas y sincronización de diagramas de diseño con el estado del código.

## 8. Conclusiones

GPS Guardian Escolar materializa un diseño coherente cliente-servidor para el seguimiento escolar, con separación clara entre API, cliente multiplataforma y base de datos versionada. La documentación del paquete 2.5.4 alinea requisitos, análisis, propuesta, diseño, pruebas, implementación, acciones de mejora, manuales y este artículo con la implementación real, de modo que el equipo y los evaluadores dispongan de una base verificable y actualizable.

## Referencias técnicas internas

- Informe de especificación de requisitos (paquete 01).
- Análisis de vistas dinámicas (paquete 02).
- Propuesta técnica y diseño de software (paquetes 03 y 04).
- Documentación de pruebas e implementación (paquetes 05 y 06).
- Código fuente de Backend, Frontend, DB-Structure y Docker-Infra analizado en octubre de 2026.
