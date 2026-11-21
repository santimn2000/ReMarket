# ADR-0001: Registrar las decisiones de arquitectura con ADRs

- **Estado:** Aceptado
- **Fecha:** 2026-11-21

## Contexto

ReMarket es un proyecto de portfolio que se construye por fases durante meses, con varias partes (backend, frontend, servicios Python y BI) y decisiones que condicionan las fases siguientes: integraciones externas, modelo de datos, seguridad o patrones de pago. Si esas decisiones solo quedan en conversaciones o en PRs, se pierde por qué se tomaron y qué alternativas se descartaron, y es fácil volver a discutirlas o contradecirlas sin darse cuenta.

## Decisión

Cada decisión de arquitectura relevante se registra como un ADR en `docs/adr/`, numerado de forma correlativa (`NNNN-titulo.md`) y basado en la plantilla del flujo de trabajo. Un ADR aceptado no se edita: si la decisión cambia, se escribe uno nuevo y el anterior pasa a "Sustituido por ADR-NNNN".

## Alternativas descartadas

- Wiki de GitHub: vive fuera del repositorio, no se versiona junto al código y no se revisa en los PRs.
- Dejar las decisiones en la descripción de los PRs: quedan repartidas y son difíciles de encontrar después.

## Consecuencias

- Las decisiones quedan versionadas junto al código y se revisan en el mismo PR que las introduce.
- Cualquiera que lea el repositorio, incluido quien lo evalúe como portfolio, puede entender por qué el proyecto es como es.
- Hay que acordarse de escribir el ADR al tomar la decisión: las fases que exijan una decisión de arquitectura incluyen una issue `docs:` para ello.
