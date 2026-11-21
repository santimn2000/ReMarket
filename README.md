# ReMarket

Marketplace de segunda mano generalista donde conviven particulares y tiendas de segunda mano, con un equipo de moderadores que atiende el soporte mediante tickets.

> Proyecto personal de portfolio, en desarrollo. El avance por fases está en `docs/PROGRESS.md`.

## Qué lo diferencia

- **Ofertas de empresas:** el particular abre su anuncio a ofertas de tiendas y elige entre vender ya o esperar a otro particular.
- **Punto seguro:** la entrega se hace en una tienda adherida que revisa el artículo y lo marca como "verificado por tienda".
- **Precio inteligente:** sugerencia de precio al publicar y valoración del precio frente a anuncios similares.
- **Soporte con tickets:** incidencias, disputas, denuncias y revisiones de fraude con SLA, asignación, notas internas y escalado.
- **Alertas por Discord y Telegram** y **CO2 evitado** por comprar de segunda mano.

## Stack

| Capa | Tecnología |
|---|---|
| Frontend | Angular, PrimeNG, ngx-echarts, Leaflet |
| Backend | Java 21, Spring Boot, Spring Security (JWT), JPA + Hibernate Spatial, Flyway, WebSocket |
| Datos | PostgreSQL + PostGIS, Redis, MinIO |
| Python | FastAPI, scikit-learn, pandas, bots de Discord y Telegram |
| Infra | Docker Compose, GitHub Actions, Stripe (modo test) |

## Estructura

```
backend/     API Spring Boot
frontend/    aplicación Angular
python/      ml-service, bots y scripts
bi/          vistas analíticas e informe de Power BI
docs/        visión, ADRs, diagramas y retrospectivas
```

Las instrucciones para arrancarlo en local llegarán con la fase 0.
