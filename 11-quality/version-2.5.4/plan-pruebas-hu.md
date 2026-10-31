# Plan de pruebas vinculado a HUs

| Capa | Comando o control | HUs |
|---|---|---|
| Backend | `mvn -B test` y MockMvc de 401/403/acceso ajeno | H-09, H-15, H-18, H-21, H-25 |
| Frontend | `npm test -- --runInBand` y lint | H-04, H-10, H-20, H-27, H-29 |
| Infra | `docker compose --env-file .env.example config --quiet` | H-23, H-24, H-35, H-55, H-67 |
| DB | Liquibase validate/update contra PostgreSQL de prueba | H-19, H-26, H-44, H-52, H-56, H-58, H-59 |
| Contrato | Comparacion de OpenAPI versionado | H-09, H-10, H-13, H-37, H-62, H-65 |
| Proceso | PR, CODEOWNERS, checks obligatorios e issues | H-06, H-08, H-22, H-33, H-34, H-66 |

Las ejecuciones deben conservar comando, commit, fecha, resultado y limitaciones en la carpeta externa `evidencias/`.

