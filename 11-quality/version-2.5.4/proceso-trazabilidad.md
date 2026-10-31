# Proceso y trazabilidad

**Relacion:** H-06, H-08, H-22, H-33, H-34 y H-66

## Flujo de entrega

`dev -> qa -> main`, con un PR por cambio, revision obligatoria, checks verdes y referencia a una HU o issue. No se deben recomprometer los mismos cambios por cada ambiente.

## Convencion de commit

Usar Conventional Commits: `feat`, `fix`, `docs`, `test`, `refactor`, `ci`, `chore`, seguido de alcance opcional y descripcion imperativa. Ejemplo: `fix(auth): reject expired device confirmation`.

## Evidencia minima por PR

- HU e issue relacionados.
- Descripcion del problema y criterio de aceptacion.
- Pruebas ejecutadas y resultado.
- Riesgo de migracion o despliegue.
- Aprobacion de otro integrante.

La proteccion de ramas, CODEOWNERS, issues y checks obligatorios deben configurarse en GitHub; este documento no afirma que ya esten activos.

