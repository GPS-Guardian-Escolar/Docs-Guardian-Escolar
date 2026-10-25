# Índice y estado de entregables — Sección 2.5.4

**Proyecto:** GPS Guardian Escolar  
**Fecha:** 9 de octubre de 2026  
**Criterio:** el código de Backend, Frontend, DB-Structure y Docker-Infra prevalece sobre documentación histórica.

## Matriz de estado (según anotaciones de la imagen 2.5.4)

| # | Producto / resultado | Estado en lista | Acción en este paquete |
|---|---|---|---|
| 1 | Informe de especificación de requisitos | Revisar o actualizar | Actualizado según código |
| 2 | Documentación de análisis del software (vistas dinámicas) | Revisar o actualizar | Actualizado según código |
| 3 | Documentación de propuesta técnica del software | Revisar o actualizar | Actualizado según código |
| 4 | Documentación de diseño del software (interfaces, arquitectura, patrones, modelo de datos) | Revisar o actualizar | Actualizado según código |
| 5 | Artefactos de código (aplicación web y móvil) | Listo | No regenerados; usados como referencia |
| 6 | Documentación de pruebas | Falta | Elaborada desde cero |
| 7 | Procedimiento de implementación del software | Falta | Elaborado desde cero |
| 8 | Documentación de acciones correctivas, preventivas y de mejoramiento | Falta | Elaborada desde cero |
| 9 | Manuales técnico, de instalación y de usuario final | Falta | Elaborados desde cero |
| 10 | Artículo del proyecto | Falta | Elaborado desde cero |

## Estructura del paquete

```text
00_INDICE_Y_ESTADO.md
01_Requisitos/01_Informe_Especificacion_Requisitos.md
02_Analisis/02_Analisis_Software_Vistas_Dinamicas.md
03_Propuesta_Tecnica/03_Propuesta_Tecnica_Software.md
04_Diseno/04_Diseno_Software.md
05_Pruebas/05_Documentacion_Pruebas.md
06_Implementacion/06_Procedimiento_Implementacion.md
07_Acciones_Correctivas/07_Acciones_Correctivas_Preventivas_Mejoramiento.md
08_Manuales/
  08a_Manual_Tecnico.md
  08b_Manual_Instalacion.md
  08c_Manual_Usuario_Final.md
09_Articulo/09_Articulo_Proyecto.md
```

## Fuentes de código

- Backend: Spring Boot 3.5.16, Java 17, módulos auth, students, routes, trips, zones, maps, notifications, admin.
- Frontend: Expo SDK 57, React Native 0.86, React 19, expo-router.
- Base de datos: PostgreSQL 15 + Liquibase (DDL/DML/DCL).
- Infraestructura: Docker Compose (database, liquibase, backend, frontend/Nginx).

## Cambios respecto a documentación previa

Los documentos de revisión (1–4) se reescriben alineados al estado del código. No se inventan funcionalidades no presentes en los repositorios. Los documentos 5–10 se crean porque no existían como entregables formalizados en el alcance de esta lista.
