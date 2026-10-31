# Gobernanza de Pull Requests

**Relacion:** H-77  
**Alcance:** Backend, Frontend, DB, Infra y Docs

## Objetivo

Mantener un historial auditable: una HU por cambio, un PR revisable y una rama de entrega
identificable. El merge es la evidencia de que el cambio fue revisado y validado; un commit
aislado o una rama abierta no se considera una entrega terminada.

## Reglas obligatorias

1. Cada PR debe enlazar una única HU o issue principal. Si una modificación afecta otra HU,
   se documenta como dependencia, no se mezclan historias sin relación.
2. El PR debe describir problema, alcance, archivos afectados, pruebas ejecutadas, riesgos y
   plan de reversión cuando aplique.
3. El autor no puede ser la única persona aprobadora. `main`, `qa` y `dev` deben exigir al
   menos una aprobación de otro integrante con permisos de escritura.
4. Los checks obligatorios deben estar verdes antes del merge. Si fallan, el PR permanece
   abierto hasta corregirlos o documentar una excepción aprobada.
5. Un PR debe mantenerse enfocado. Como guía, más de 40 archivos modificados o más de 800
   líneas netas requiere dividir el trabajo o justificar explícitamente la excepción.
6. No se deben recrear HUs ya existentes. Antes de abrir una HU nueva se busca por título,
   problema, repositorio y criterios de aceptación; las duplicadas se cierran enlazando la
   HU canónica.
7. Después del merge se actualiza la issue y el tablero a `Done`, se conserva el enlace al PR
   y se elimina la rama de trabajo cuando ya no sea necesaria.

## Plantilla mínima de PR

```markdown
## HU / issue
- Closes #<issue>

## Problem and scope
Describe the problem and the files or services affected.

## Validation
- Command: `<command>`
- Result: PASS/FAIL

## Risks and rollback
Describe compatibility, migration, deployment or rollback risks.

## Review checklist
- [ ] One HU and one focused scope
- [ ] Tests and checks are green
- [ ] Another contributor reviewed the change
- [ ] Issue and project board will be updated after merge
```

## Criterio de cierre de H-77

- La protección de ramas exige revisión independiente y checks obligatorios.
- Los PR nuevos enlazan una HU y usan la plantilla de alcance y validación.
- Las HUs duplicadas quedan consolidadas con enlaces a la HU canónica.
- No quedan PR de la HU abiertos sin estado documentado.

