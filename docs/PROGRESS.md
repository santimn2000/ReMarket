# Progreso

Checklist por fases, sacada de `## Fases` del CLAUDE.md. Cada fase es un milestone en GitHub y se cierra con su release.

Cada línea enlaza la issue o las issues que la cubren. En qué consiste cada una (criterios de aceptación y notas técnicas) está en la propia issue. Las issues de una fase se crean al empezarla, y en ese momento se enlazan aquí.

- Una línea se marca `[x]` en el PR que cierra su issue. Si tiene varias, en el PR que cierra la última.
- Lo que está en curso o en revisión se ve en el [tablero](https://github.com/users/santimn2000/projects/2).
- Los enlaces se escriben `[#n]`, con la URL al final de cada fase, porque GitHub no convierte `#n` en enlace dentro de los ficheros del repo.
- La preparación inicial (etiquetas, milestones y tablero) se hizo antes de que hubiera issues y no enlaza ninguna.

**Fase actual:** 0 · Base

## Fase 0 · Base · `v0.0.0`

[Milestone](https://github.com/santimn2000/ReMarket/milestone/1)

- [x] Etiquetas, milestones y tablero
- [x] Plantillas y documentación del flujo de trabajo ([#1])
- [x] ADR-0001: registrar decisiones con ADRs ([#1])
- [x] Índice de issues en `docs/PROGRESS.md` ([#22])
- [ ] Dependabot ([#12])
- [ ] SonarQube Cloud ([#11])
- [x] `docs/vision.md` y diagrama C4 de contexto ([#3])
- [x] Monorepo y docker-compose (PostgreSQL + PostGIS, Redis y MinIO) ([#4])
- [ ] Spring Boot arrancando ([#5], [#6])
- [ ] Angular arrancando con layout de PrimeNG ([#7], [#8])
- [ ] Esqueleto del ml-service con FastAPI y uv ([#9])
- [ ] ADR de autenticación con JWT y diagrama de secuencia ([#13])
- [ ] Registro y login JWT con los cuatro tipos de cuenta ([#14], [#15], [#16], [#17], [#18])
- [ ] Alta de moderadores desde el panel de admin ([#19])
- [ ] CI completa (backend, frontend y Python) ([#10])
- [ ] Escaneo de seguridad con Trivy ([#20])

[#1]: https://github.com/santimn2000/ReMarket/issues/1
[#3]: https://github.com/santimn2000/ReMarket/issues/3
[#4]: https://github.com/santimn2000/ReMarket/issues/4
[#5]: https://github.com/santimn2000/ReMarket/issues/5
[#6]: https://github.com/santimn2000/ReMarket/issues/6
[#7]: https://github.com/santimn2000/ReMarket/issues/7
[#8]: https://github.com/santimn2000/ReMarket/issues/8
[#9]: https://github.com/santimn2000/ReMarket/issues/9
[#10]: https://github.com/santimn2000/ReMarket/issues/10
[#11]: https://github.com/santimn2000/ReMarket/issues/11
[#12]: https://github.com/santimn2000/ReMarket/issues/12
[#13]: https://github.com/santimn2000/ReMarket/issues/13
[#14]: https://github.com/santimn2000/ReMarket/issues/14
[#15]: https://github.com/santimn2000/ReMarket/issues/15
[#16]: https://github.com/santimn2000/ReMarket/issues/16
[#17]: https://github.com/santimn2000/ReMarket/issues/17
[#18]: https://github.com/santimn2000/ReMarket/issues/18
[#19]: https://github.com/santimn2000/ReMarket/issues/19
[#20]: https://github.com/santimn2000/ReMarket/issues/20
[#22]: https://github.com/santimn2000/ReMarket/issues/22

## Fase 1 · Marketplace · `v0.1.0`

[Milestone](https://github.com/santimn2000/ReMarket/milestone/2)

- [ ] Categorías
- [ ] Anuncios con fotos en MinIO
- [ ] Búsqueda con filtros y distancia (PostGIS)
- [ ] Geocodificación con Nominatim
- [ ] Favoritos
- [ ] Chat en tiempo real

## Fase 2 · Pedidos y pagos · `v0.2.0`

[Milestone](https://github.com/santimn2000/ReMarket/milestone/3)

- [ ] Stripe Connect en modo test
- [ ] Retención y liberación de fondos
- [ ] Envío simulado
- [ ] Valoraciones

## Fase 3 · Soporte · `v0.3.0`

[Milestone](https://github.com/santimn2000/ReMarket/milestone/4)

- [ ] Tickets con tipos, prioridad y SLA
- [ ] Asignación con bloqueo optimista
- [ ] Notas internas, adjuntos y respuestas predefinidas
- [ ] Acciones de resolución
- [ ] Escalado al admin
- [ ] Reapertura, cierre automático y valoración
- [ ] Disputas de pedidos y denuncias integradas

## Fase 4 · Empresas · `v0.4.0`

[Milestone](https://github.com/santimn2000/ReMarket/milestone/5)

- [ ] Validación de empresas mediante ticket
- [ ] Escaparate de empresa
- [ ] Bandeja de oportunidades con ofertas manuales
- [ ] Punto seguro con verificación

## Fase 5 · Python · `v0.5.0`

[Milestone](https://github.com/santimn2000/ReMarket/milestone/6)

- [ ] Seed sintético
- [ ] Carga de datos del INE
- [ ] ml-service: precio sugerido
- [ ] ml-service: puntuación de fraude
- [ ] ml-service: clasificación de tickets

## Fase 6 · Paneles · `v0.6.0`

[Milestone](https://github.com/santimn2000/ReMarket/milestone/7)

- [ ] Panel de admin
- [ ] Panel de moderador
- [ ] Panel de empresa
- [ ] Panel de particular

## Fase 7 · Alertas · `v0.7.0`

[Milestone](https://github.com/santimn2000/ReMarket/milestone/8)

- [ ] Búsquedas guardadas
- [ ] Bot de Discord
- [ ] Bot de Telegram
- [ ] Informe semanal

## Fase 8 · Power BI · `v0.8.0`

[Milestone](https://github.com/santimn2000/ReMarket/milestone/9)

- [ ] Esquema `analytics` con vistas en estrella
- [ ] Power BI embebido en el panel de admin

## Fase 9 · Pulido · `v1.0.0`

[Milestone](https://github.com/santimn2000/ReMarket/milestone/10)

- [ ] Tests e2e con Playwright
- [ ] Revisión de seguridad
- [ ] README con capturas
