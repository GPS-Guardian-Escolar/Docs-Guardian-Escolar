---
title: "Manual de usuario: acudiente, estudiante y administrador"
subtitle: "Proyecto formativo · GPS Guardián Escolar"
author: "Equipo del proyecto GPS Guardián Escolar"
date: "5 de noviembre de 2026"
lang: es-CO
---

**Versión documental:** 2.5.7 · **Estado:** versión editable preparada para subida y revisión de QA/arquitectura.

## Tabla de contenido

1. Bienvenida
2. 1. Requisitos para comenzar
3. 2. Crear cuenta, verificarla e iniciar sesión
4. 3. Crear y administrar un estudiante (acudiente)
5. 4. Crear rutas, asignarlas y elegir la preferida
6. 5. Vincular el dispositivo del estudiante
7. 6. Ubicación y seguimiento del viaje
8. 7. Configurar zonas seguras
9. 8. Bandeja de notificaciones y ajustes
10. 9. Perfil, sesiones y seguridad
11. 10. Funciones del administrador
12. 11. Solución de problemas frecuentes
13. 12. Uso responsable, soporte y reporte de incidencias
14. 13. Validación de pantallas pendiente
15. Referencias y trazabilidad (APA 7.ª edición)


> **Corte y alcance de evidencia.** Revisión documental 2.5.7, 5 de noviembre de 2026. La descripción del **release de referencia** utiliza los commits de `main` observados en Backend (`1022058d`), Frontend (`3d9f59fa`), DB (`9278b1dc`) e Infra (`131e9895`). Se inspeccionaron además las ramas `dev`/`develop` más recientes y los suplementos documentales 2.5.6, pero **sus cambios no se consideran integrados a producción por esa sola razón**. La revisión es estática: no se ejecutaron en esta sesión pruebas, despliegues ni validaciones en teléfono. Los resultados operativos requieren evidencia real. 


## Bienvenida

GPS Guardián Escolar permite consultar información de estudiantes, rutas escolares y ubicación de trayectos cuando hay dispositivos vinculados y conectividad. Este manual describe los flujos que el proyecto implementa a nivel de código en la revisión del 5 de noviembre de 2026. **La ubicación es orientativa y puede estar retrasada; no debe reemplazar la comunicación con el colegio o servicios de emergencia.** Los nombres y botones pueden cambiar según la versión instalada.

## 1. Requisitos para comenzar

Necesita una cuenta autorizada y conexión a Internet. Para registrar o recuperar cuenta, puede necesitar correo electrónico. Para vincular un teléfono se utiliza código QR o enlace/código manual; la cámara requiere permiso. Para compartir GPS en el dispositivo del estudiante se deben conceder los permisos de ubicación que solicite el sistema y mantener la configuración recomendada. Algunas funciones solo están disponibles cuando el administrador habilita Maps, correo o notificaciones.

## 2. Crear cuenta, verificarla e iniciar sesión

1. Abra la dirección web o la app proporcionada por la institución.
2. Seleccione **Crear cuenta**; complete los campos y revise términos y condiciones antes de confirmar. Si la casilla no cambia de estado, no asuma que se aceptó: informe al soporte y no reintente indefinidamente.
3. Consulte el correo registrado y use el enlace/código de verificación recibido, si se solicita.
4. Regrese a **Iniciar sesión** con sus datos. En caso de verificación de dos pasos o dispositivo nuevo, siga la confirmación que aparezca en pantalla.
5. Una vez autenticado, revise el panel principal. Si aparece un error de acceso, no use credenciales compartidas; solicite revisión del perfil.

**Recuperar contraseña:** elija «Olvidé mi contraseña» y siga el enlace/código enviado. No envíe contraseñas ni códigos a terceros y recuerde cerrar sesión en equipos compartidos.

## 3. Crear y administrar un estudiante (acudiente)

1. Abra la sección **Estudiantes**.
2. Seleccione agregar estudiante; complete los campos obligatorios y, si lo desea, adjunte una foto cuando esté disponible.
3. Compruebe que el registro aparece en la lista. Puede editarlo si la cuenta tiene permiso.
4. Agregue los contactos de emergencia pertinentes y revise que los acudientes autorizados correspondan a las personas designadas.
5. Para compartir acceso con otro acudiente, use el flujo de invitación/gestión disponible; al dejar de ser autorizado se debe revocar el vínculo.

**Privacidad:** registre solamente datos necesarios, mantenga actualizada la lista de autorizados y evite descargar/capturar fotos y ubicaciones para grupos públicos.

## 4. Crear rutas, asignarlas y elegir la preferida

Las rutas se administran desde la sección correspondiente cuando el rol tiene permisos. Introduzca origen/destino y paradas, guarde la ruta y asígnela al estudiante. Si tiene varias rutas activas, seleccione la que se utilizará como **preferida** para futuros viajes. El sistema intenta usar la preferida al crear un viaje sin ruta elegida; si no puede decidir porque existen varias opciones, le solicitará seleccionar una.

**Recomendación:** antes de iniciar un trayecto confirme que la ruta activa corresponde al recorrido del día; una ruta en el sistema no confirma que el vehículo haya iniciado efectivamente el servicio.

## 5. Vincular el dispositivo del estudiante

1. Desde la ficha del estudiante, localice la opción para generar código/enlace de vinculación.
2. En el teléfono que llevará el estudiante, instale y abra una versión aprobada de la aplicación.
3. Seleccione **Escanear QR** o el ingreso manual cuando exista; conceda permiso de cámara únicamente si lo necesita.
4. Revise que el estudiante y la operación mostrados sean los esperados y confirme la vinculación.
5. Compruebe en la ficha del acudiente que el teléfono aparece vinculado. Si hay error de código vencido o usado, genere uno nuevo en lugar de publicar el anterior.
6. Si cambia de teléfono, pierde el equipo o termina la autorización, utilice **Desvincular dispositivo** desde la ficha del estudiante.

La vinculación es una operación sensible. No compartas fotografías de QR ni códigos por redes sociales o grupos abiertos.

## 6. Ubicación y seguimiento del viaje

1. Entre a **Seguimiento** y seleccione el estudiante y el trayecto activo disponible.
2. En el teléfono infantil active ubicación y conectividad; acepte los permisos necesarios para el seguimiento previsto.
3. Compruebe en la pantalla de acudiente la posición y, especialmente, **la hora de la última actualización**. El mapa puede reflejar el último dato recibido, no la posición exacta del momento.
4. Si la posición deja de llegar, la aplicación puede generar aviso de ubicación no reciente; el valor predeterminado del backend es 120 segundos y el monitor revisa periódicamente. Una notificación persistida no siempre garantiza recepción push.
5. Si desea retirar/cancelar un viaje activo, utilice únicamente la acción de gestión disponible y autorizada. En el corte revisado, la API realiza una **baja lógica/cancelación mediante DELETE**; no hay evidencia de un endpoint PATCH de finalización en el controlador inspeccionado.

**Limitación importante:** no informe que un trayecto se «completó» solo porque desapareció de la lista. Si el dispositivo pierde batería/señal, la ubicación puede quedar obsoleta; contacte directamente con la institución ante dudas de seguridad.

## 7. Configurar zonas seguras

Desde **Zonas seguras**, cuando esté habilitado, seleccione estudiante, nombre, ubicación en mapa y radio en metros. Confirme los datos antes de guardar. Las alertas por entrada/salida u otras condiciones dependen del procesamiento de coordenadas y configuración, por lo que conviene ensayar con datos de prueba si la institución necesita verificar que se emiten correctamente.

## 8. Bandeja de notificaciones y ajustes

Abra **Notificaciones** para revisar eventos disponibles y marcar los recibidos como leídos. En **Ajustes**, revise los canales permitidos por su cuenta y por el administrador. Correo, push y SMS pueden no estar activos en todos los ambientes. Si esperaba una alerta y no llegó, compruebe permisos de notificaciones del teléfono, conexión, configuración de la cuenta y registro en la bandeja antes de reportar.

## 9. Perfil, sesiones y seguridad

En **Perfil** puede consultar y, según permisos, modificar datos, correo y contraseña. Si se detecta un inicio desde otro dispositivo, siga el flujo de confirmación habilitado; un correo de confirmación jamás debe pedirle compartir su clave por mensaje. Si sospecha acceso no autorizado, cierre sesión, cambie su contraseña y contacte al administrador para revisar sesiones vinculadas.

## 10. Funciones del administrador

Las cuentas con permisos administrativos pueden consultar panel general, alertas, auditoría y errores. Deben usar los datos solo para fines autorizados por la institución. El administrador gestiona configuración y soporte, pero no puede interpretar una alerta de GPS como certeza física de la ubicación. Los permisos de modificación/consulta del backend también rigen para administradores.

## 11. Solución de problemas frecuentes

| Situación | Qué hacer |
|---|---|
| No recibo correo | Verifique spam y dirección. Contacte soporte; el proveedor puede estar deshabilitado. |
| No puedo iniciar | Revise datos, 2FA y dispositivo nuevo; evite reintentos masivos. |
| No se marca aceptación de términos | No fuerce el registro; tome captura sin datos privados y reporte. |
| QR no funciona | Permita cámara o use ingreso manual, confirme vigencia del código. |
| Teléfono no aparece vinculado | Revise conectividad y perfil; desvincule el anterior si corresponde. |
| El mapa no carga | Compruebe Internet y solicite revisión de Google Maps del servidor. |
| La ubicación es antigua | Observe fecha/hora, batería, datos y permisos en el teléfono. |
| No llegan avisos | Revise bandeja, permisos de push y configuración del proveedor. |
| Recibo 403 | Es posible que la cuenta no esté autorizada; consulte soporte. |

## 12. Uso responsable, soporte y reporte de incidencias

No distribuya enlaces o capturas con ubicación del estudiante. Si el teléfono se pierde, solicite desvinculación inmediata. Al reportar una falla incluya fecha, pantalla, versión de app, modelo, sistema operativo y mensaje de error, **sin contraseñas, tokens, códigos de vinculación ni ubicaciones identificables de menores**. El contacto de soporte debe ser proporcionado por la institución; no se inventa un correo oficial en este manual.

## 13. Validación de pantallas pendiente

Este manual fue alineado con rutas y capacidades presentes en el código. La edición no incorpora capturas de una build certificada porque no se ejecutó ni validó una en esta revisión; antes de uso institucional conviene anexar capturas reales y comprobar que las etiquetas de la interfaz corresponden a los pasos aquí descritos.

## Referencias y trazabilidad (APA 7.ª edición)

- GPS Guardian Escolar. (2026a). *Backend-Guardian-Escolar* [Código fuente, rama main, commit `1022058d`]. GitHub. https://github.com/GPS-Guardian-Escolar/Backend-Guardian-Escolar/tree/1022058d545a9e7936edbff75950034ec4f98e88
- GPS Guardian Escolar. (2026b). *Fronted-GuardianEscolar-* [Código fuente, rama main, commit `3d9f59fa`]. GitHub. https://github.com/GPS-Guardian-Escolar/Fronted-GuardianEscolar-/tree/3d9f59fa3e91b5ecc5b8ae86591187546b966668
- GPS Guardian Escolar. (2026c). *DB-Structure-Guardian-Escolar* [Migraciones SQL, rama main, commit `9278b1dc`]. GitHub. https://github.com/GPS-Guardian-Escolar/DB-Structure-Guardian-Escolar/tree/9278b1dcb224301536d6158357f74daee74b20ba
- GPS Guardian Escolar. (2026d). *Docker-Infra-Guardian-Escolar* [Configuración de despliegue, rama main, commit `131e9895`]. GitHub. https://github.com/GPS-Guardian-Escolar/Docker-Infra-Guardian-Escolar/tree/131e98954430e95e9108174eded9896b3f0d5272
- GPS Guardian Escolar. (2026e). *Docs-Guardian-Escolar: entregables 2.5.5 y suplementos 2.5.6* [Documentación del proyecto]. GitHub. https://github.com/GPS-Guardian-Escolar/Docs-Guardian-Escolar

**Fuentes primarias relevantes:** `TripController.java`, `TripService.java`, `StudentController.java`, `LocationAvailabilityService.java`, `WebSocketConfig.java`, `application.yml`, `package.json`, `docker-compose.yaml` y `01_ddl/04_alter/014_preferred_student_route.sql`, según el documento. Las referencias a rutas son comprobables en los repositorios indicados.
