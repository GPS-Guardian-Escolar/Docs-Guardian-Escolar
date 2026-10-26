# Trazabilidad documental de historias de usuario

**Proyecto:** GPS Guardian Escolar  
**Version de trabajo:** 2.5.4  
**Fuente de verdad funcional:** repositorios Backend, Frontend, DB e Infra.  
**Regla:** este repositorio documenta decisiones, requisitos y procedimientos; no reemplaza las pruebas ni los cambios de codigo.

## HUs con entregable documental

| HU | Documento en Docs | Estado documental |
|---|---|---|
| H-05 | [Vision, alcance e indicadores](../../03-product/version-2.5.4/vision-alcance-indicadores.md) | En revision del equipo |
| H-13 | [ADR de tiempo real](../../05-architecture/decisions/records/ADR-001-tiempo-real.md) | Decision documentada; falta validar E2E |
| H-23, H-24, H-35, H-55, H-56, H-60, H-67 | [Despliegue y configuracion](../../10-devops/version-2.5.4/despliegue-configuracion-rollback.md) | Documentado; faltan verificaciones externas donde se indican |
| H-30, H-31, H-32, H-41, H-42, H-43 | Este repositorio y sus indices | Centralizado |
| H-33, H-34, H-66 | [Proceso y trazabilidad](../../11-quality/version-2.5.4/proceso-trazabilidad.md) | Requiere configuracion de GitHub |
| H-44, H-51, H-58, H-59 | [Datos, privacidad y retencion](../../06-data/version-2.5.4/datos-privacidad-retencion.md) | Politica propuesta; requiere aprobacion legal y tecnica |
| H-62, H-65 | [Contrato API](../../07-api/contracts/openapi/version-1/README.md) | Especificacion documental; falta publicar el contrato generado |
| H-20, H-21, H-22 | [Plan de pruebas y gates](../../11-quality/version-2.5.4/plan-pruebas-hu.md) | Pruebas locales disponibles; CI externo pendiente |

## HUs que no son solo documentales

H-01, H-03, H-09, H-12, H-15, H-17, H-18, H-19, H-26, H-27, H-29, H-38, H-45, H-49, H-52, H-53, H-61 y H-63 requieren cambios de backend, frontend, base de datos o infraestructura. Se documentan aqui cuando hace falta, pero su cierre depende de codigo probado.

