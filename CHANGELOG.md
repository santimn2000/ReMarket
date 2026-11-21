# Changelog

Todos los cambios relevantes del proyecto se documentan en este fichero.

El formato sigue [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/) y el proyecto usa [versionado semántico](https://semver.org/lang/es/): cada fase cerrada sube la versión menor y la `v1.0.0` llega al cerrar la fase 9.

## [Unreleased]

### Añadido

- README, `.gitignore` y `.gitattributes`.
- Plantillas de issue (historia de usuario, bug y spike) y de pull request.
- ADR-0001: registrar las decisiones de arquitectura con ADRs.
- Seguimiento por fases en `docs/PROGRESS.md` e ids del tablero en `docs/github-project.md`.
- Visión del producto en `docs/vision.md`: problema, tipos de cuenta, diferenciales y lo que queda fuera del alcance.
- Diagrama C4 de contexto en `docs/design/c4-contexto.md`.
- `docker-compose.yml` con PostgreSQL 18 + PostGIS 3.7, Redis 8.10 y Silo (fork de MinIO), con versiones fijas, healthchecks, volúmenes con nombre y puertos solo en `127.0.0.1`. Todas las variables están en `.env.example`.
- ADR-0002: usar Silo como almacenamiento S3 porque `minio/minio` ya no está en Docker Hub.

### Cambiado

- `docs/PROGRESS.md` enlaza cada línea de la fase 0 con sus issues, añade las que faltaban (ml-service, ADR de autenticación, alta de moderadores y Trivy) y explica en la cabecera cómo se mantiene.

[Unreleased]: https://github.com/santimn2000/ReMarket/commits/main
