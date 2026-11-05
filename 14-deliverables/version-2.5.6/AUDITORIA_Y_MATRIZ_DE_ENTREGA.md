# Auditoría y matriz de entrega — 5 de noviembre de 2026

## Alcance

Se cotejó la documentación 2.5.5 con los árboles del tag `final-delivery-2026`. Revisión estática: no se ejecutaron pruebas, compilaciones ni despliegues. No se localizaron Releases con ZIP adjuntos para el tag en los cuatro repositorios.

La base reproducible es el tag, no ramas de desarrollo. La rama `main` local de DB diverge de `origin/main`; no se atribuyen a la entrega commits posteriores o locales que no estén en el tag.

## Commits revisados

| Repositorio | Tag / commit | Fuente |
|---|---|---|
| Backend-Guardian-Escolar | `final-delivery-2026` · `1022058d545a9e7936edbff75950034ec4f98e88` | [https://github.com/Nicolasruiz918/Backend-Guardian-Escolar](https://github.com/Nicolasruiz918/Backend-Guardian-Escolar/tree/final-delivery-2026) |
| Fronted-GuardianEscolar- | `final-delivery-2026` · `3d9f59fa3e91b5ecc5b8ae86591187546b966668` | [https://github.com/JhonCamiloSuaza/Fronted-GuardianEscolar-](https://github.com/JhonCamiloSuaza/Fronted-GuardianEscolar-/tree/final-delivery-2026) |
| DB-Structure-Guardian-Escolar | `final-delivery-2026` · `9278b1dcb224301536d6158357f74daee74b20ba` | [https://github.com/Nicolasruiz918/DB-Structure-Guardian-Escolar](https://github.com/Nicolasruiz918/DB-Structure-Guardian-Escolar/tree/final-delivery-2026) |
| Docker-Infra-Guardian-Escolar | `final-delivery-2026` · `131e98954430e95e9108174eded9896b3f0d5272` | [https://github.com/Nicolasruiz918/Docker-Infra-Guardian-Escolar](https://github.com/Nicolasruiz918/Docker-Infra-Guardian-Escolar/tree/final-delivery-2026) |
| Docs-Guardian-Escolar | Tag pendiente | Esta revisión está en rama; no etiquetar hasta integrar y revisar. |

## Matriz solicitada en la presentación

| N.º | Producto | Evidencia 2.5.6 | Estado |
|---|---|---|---|
| 1 | Requisitos | [Adenda SRS](./04-final-delivery/01-requirements-specification/SRS_ADENDA_2.5.6.docx) + SRS 2.5.5 | Actualización puntual; aceptación funcional pendiente. |
| 2 | Análisis | [Flujos](./04-final-delivery/02-software-analysis/README.md) | Alineado a lectura estática. |
| 3 | Propuesta | [Propuesta](./04-final-delivery/03-technical-proposal/README.md) | Dependencias externas identificadas; despliegue no certificado. |
| 4 | Diseño | [Diseño](./04-final-delivery/04-software-design/README.md) | Ruta preferida, baja lógica, permisos y paginación. |
| 5 | Código | [Tag y artefactos](./04-final-delivery/code-tag/README.md) | Cuatro tags; ZIP y checksums no encontrados. |
| 6 | Pruebas | [Matriz](./04-final-delivery/05-test-documentation/README.md) | Escenarios descritos; ejecución no confirmada. |
| 7 | Implementación | [Procedimiento](./04-final-delivery/06-implementation-procedure/README.md) | Requiere validación de operador/entorno. |
| 8 | Acciones de mejora | [Acciones](./04-final-delivery/07-improvement-actions/README.md) | Abiertas hasta aportar evidencia. |
| 9 | Manuales | [Suplemento](./04-final-delivery/08-manuals/README.md) | Referencia a los tres manuales base 2.5.5. |
| 10 | Artículo | [Artículo APA 7](./04-final-delivery/09-project-article/ARTICULO_GPS_GUARDIAN_ESCOLAR_2.5.6_APA7.docx) | Sin resultados empíricos inventados. |
| + | Equipo/registro | [Roster](./04-final-delivery/team-roster.md), [registro](./04-final-delivery/software-registration.md) | Contactos privados omitidos; declaración de autores pendiente. |
| + | Videos | [Estado](./04-final-delivery/10-videos/README.md) | Ambos pendientes, sin URL encontrada. |

## Cambios confirmados en el tag

- `DELETE /api/trips/{tripId}` se autoriza para `LOCATION_VIEW` o `ADMIN`, valida la gestión del estudiante y aplica baja lógica; un trayecto activo queda cancelado con hora de fin.
- La ruta preferida activa se usa al crear un trayecto sin ruta explícita. La migración 014 incorpora `is_preferred` y una restricción de unicidad parcial para asignaciones activas.
- La disponibilidad GPS usa 120 segundos como umbral predeterminado y revisa cada 30 segundos; el servicio contempla notificar pérdida de seguimiento. Esto describe configuración/código, no servicio observado.
- Listados de estudiantes, trayectos y coordenadas son paginados; los controladores usan página 0/tamaño 20 como valores predeterminados.
- El frontend revisa/solicita confirmación antes de vincular dispositivo y actualiza el historial periódicamente. El dispositivo infantil no recibe controles de finalización del trayecto.
- Push requiere credenciales y proveedor configurados; su entrega productiva no se verificó.

## Cierre pendiente

1. Publicar los dos ZIP requeridos por repositorio como assets de Releases y calcular SHA-256 sobre los archivos finales publicados.
2. Crear el pitch de 1–2 minutos y tutorial de 3–5 minutos, con URL verificable.
3. Ejecutar pruebas funcionales y en Android real (GPS en segundo plano, permisos, push, mapas y compatibilidad) y adjuntar evidencia.
4. Confirmar roles, declaración de originalidad/titularidad y licencias de terceros antes del registro formal.
5. Resolver divergencia local de `main` de DB antes de generar artefactos; mantener el corte de entrega fijado al tag.
6. Etiquetar Docs solo después de integrar y aprobar la revisión documental.

No se crean Releases, tags, ZIPs, hashes ni resultados de pruebas en esta tarea.
