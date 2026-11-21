# ReMarket

Marketplace de segunda mano generalista (estilo Wallapop) donde conviven particulares y empresas (tiendas de segunda mano), con un equipo de moderadores que atiende el soporte mediante tickets. Es un proyecto personal de portfolio: prioriza funcionalidades completas de punta a punta, código limpio y un panel de estadísticas potente.

## Diferenciales del producto

1. **Ofertas de empresas.** El particular puede abrir su anuncio a ofertas de empresas. Las empresas ven esos anuncios en su bandeja de oportunidades y deciden a mano si ofertan y cuánto; nunca hay ofertas automáticas. El particular elige entre aceptar una oferta y vender ya, o esperar a otro particular por más dinero.
2. **Empresa como punto seguro.** Comprador y vendedor hacen la entrega en una empresa adherida, que revisa el artículo con un checklist y lo marca como "verificado por tienda". La empresa cobra una comisión por entrega.
3. **Precio inteligente.** Un servicio en Python sugiere precio al publicar y cada anuncio muestra si su precio es bueno, normal o alto respecto a similares.
4. **Soporte con tickets.** Incidencias, disputas, denuncias, revisiones de fraude y validaciones de empresa pasan por un único sistema de tickets que atienden los moderadores, con SLA, asignación, notas internas y escalado al admin.
5. **Alertas por Discord y Telegram.** Búsquedas guardadas, ofertas recibidas, cambios de estado del pedido y respuestas en tickets.
6. **Impacto.** CO2 evitado por comprar de segunda mano, por usuario y en total.

## Stack

| Capa | Tecnología |
|---|---|
| Frontend | Angular 22, standalone components, signals, control flow nativo (`@if`, `@for`), PrimeNG, ngx-echarts, Leaflet, powerbi-client-angular |
| Backend | Java 21, Spring Boot 4.1, Maven, Spring Web, Spring Security (JWT), Spring Data JPA + Hibernate, Hibernate Spatial, Flyway, WebSocket (STOMP), springdoc-openapi, stripe-java |
| Datos | PostgreSQL + PostGIS (imagen `postgis/postgis`), Redis (caché, rate limit, pub/sub), MinIO (fotos y adjuntos, compatible S3) |
| Python | Python 3.12, uv, FastAPI, scikit-learn, pandas, Faker, discord.py, python-telegram-bot |
| Infra | Docker Compose, GitHub Actions, Stripe CLI para webhooks en local |

## Estructura del repositorio

```
remarket/
├── CLAUDE.md
├── CHANGELOG.md
├── docker-compose.yml
├── .env.example
├── .github/
│   ├── workflows/           # CI
│   ├── ISSUE_TEMPLATE/      # story.md, bug.md, spike.md
│   ├── PULL_REQUEST_TEMPLATE.md
│   └── dependabot.yml
├── docs/
│   ├── vision.md            # qué resuelve, para quién y qué queda fuera
│   ├── PROGRESS.md          # checklist por fases, lo mantiene Claude
│   ├── github-project.md    # ids del tablero para mover tarjetas con gh
│   ├── adr/                 # Architecture Decision Records: 0001-titulo.md
│   ├── design/              # diagramas Mermaid: C4, entidad-relación, estados, secuencias
│   └── retros/              # retrospectiva de cada fase: fase-N.md
├── backend/                 # Spring Boot
├── frontend/                # Angular
├── python/
│   ├── ml-service/          # FastAPI: precio sugerido, fraude y clasificación de tickets
│   ├── bots/                # Discord y Telegram
│   └── scripts/             # seed sintético, carga INE, informe semanal
└── bi/                      # vistas analíticas documentadas + informe .pbix
```

## Tipos de cuenta

Hay dos tipos de cuenta para usuarios, que se eligen al registrarse y no cambian, y dos tipos de cuenta internas que solo crea un admin.

- **PERSONAL (particular):** compra y vende, decide si cada anuncio suyo acepta ofertas de empresas, acepta o rechaza las ofertas que recibe, abre tickets, guarda búsquedas y vincula Discord/Telegram.
- **BUSINESS (empresa):** se registra con CIF, razón social y dirección, y queda pendiente hasta que se valida su ticket de verificación. Una vez validada, publica su stock, hace ofertas a anuncios de particulares, puede actuar como punto seguro y tiene su propio panel de estadísticas. También compra y abre tickets como cualquier usuario.
- **MODERATOR (interna):** atiende tickets: los ve, se los asigna, responde, escribe notas internas, ejecuta las acciones de resolución y escala al admin lo que exceda sus permisos. Tiene su propio panel con su bandeja y sus métricas.
- **ADMIN (interna):** todo lo que hace un moderador, más gestión de usuarios, moderadores y empresas, configuración (categorías, comisiones, SLA, respuestas predefinidas), tickets escalados y panel global.

## Modelo de dominio (orientativo)

- `User` (`accountType`: `PERSONAL`, `BUSINESS`, `MODERATOR` o `ADMIN`)
- `BusinessProfile` (usuario, CIF, razón social, nombre comercial, dirección, ubicación como `Point`, horario, categorías de interés, radio de interés, `isSafePoint`, `verificationStatus`: `PENDING`, `VERIFIED` o `REJECTED`)
- `Category` (árbol, factor de CO2, checklist de verificación)
- `Listing` (vendedor, título, descripción, precio, estado del artículo, categoría, ubicación `Point`, `status`, `acceptsBusinessOffers` (solo en anuncios de particulares), precio sugerido, `priceRating`, `fraudScore`), `ListingPhoto`
- `Favorite`, `SavedSearch` (filtros en JSON, canal de alerta)
- `Conversation`, `Message`
- `Order` (comprador, vendedor, anuncio, importe, comisión, entrega `SHIPPING` o `SAFE_POINT`, `status`), `Payment` (ids de Stripe), `Shipment` (simulado)
- `BusinessOffer` (anuncio, empresa, importe, mensaje opcional, caducidad, `status`: `PENDING`, `ACCEPTED`, `REJECTED`, `EXPIRED` o `WITHDRAWN`)
- `SafePointHandover` (pedido, empresa, franja, checklist rellenado, fotos, resultado)
- `Ticket` (código legible tipo `TCK-000123`, `type`, `priority`, `status`, solicitante, moderador asignado, entidad relacionada (pedido, anuncio, usuario o empresa), vencimiento de primera respuesta y de resolución según SLA, código de resolución, valoración del solicitante 1-5, `@Version` para bloqueo optimista)
- `TicketMessage` (autor, texto, `internal` para notas que el usuario no ve), `TicketAttachment` (en MinIO), `TicketEvent` (historial: cambios de estado, asignación, prioridad, acciones ejecutadas)
- `CannedResponse` (respuestas predefinidas con variables como `{nombre}` o `{pedido}`), `SlaPolicy` (tiempos por prioridad)
- `Review`, `ProvinceStat` (datos INE), `AuditLog`, `ChannelLink` (Discord/Telegram)

Las disputas y denuncias no tienen entidad propia: son tickets de tipo `ORDER_ISSUE`, `LISTING_REPORT` o `USER_REPORT`.

Estados de `Order`:

```
PENDING_PAYMENT → PAID_HELD → SHIPPED | AWAITING_HANDOVER → DELIVERED | VERIFIED → COMPLETED
Desde PAID_HELD: → DISPUTED → REFUNDED | COMPLETED
Antes del pago: → CANCELLED
```

Estados de `Ticket`:

```
OPEN → IN_PROGRESS ⇄ WAITING_USER
IN_PROGRESS → ESCALATED → IN_PROGRESS (lo devuelve el admin) | RESOLVED
IN_PROGRESS → RESOLVED → CLOSED (automático a los 7 días)
RESOLVED → IN_PROGRESS (si el solicitante responde antes de cerrarse)
```

Las transiciones de estado de cada entidad viven en un único método de su servicio (un `switch`), no repartidas por el código.

## Flujos clave

### Compra normal
1. El comprador paga. Stripe cobra y el dinero queda retenido (`PAID_HELD`).
2. Envío simulado o entrega en punto seguro.
3. El comprador confirma la recepción (o la empresa del punto seguro verifica) y se transfiere al vendedor el importe menos la comisión.
4. Si hay un problema, el comprador abre un ticket `ORDER_ISSUE` desde el pedido y este pasa a `DISPUTED` hasta que se resuelve el ticket.

### Ofertas de empresas
1. El particular publica con `acceptsBusinessOffers = true`.
2. Las empresas validadas ven el anuncio en su bandeja "Oportunidades", filtrable por categoría, distancia, precio y estado del artículo. Pueden suscribirse a categorías para recibir avisos (WebSocket y bots), pero el sistema nunca crea ofertas por su cuenta.
3. La empresa decide a mano si oferta: importe, mensaje opcional y validez (entre 24 y 72 h). Como referencia ve el precio pedido y el precio sugerido por el ml-service.
4. Cada empresa tiene como máximo una oferta pendiente por anuncio. Puede retirarla mientras esté pendiente y hacer otra.
5. El particular ve todas las ofertas recibidas y acepta una o las rechaza. Al aceptar, se crea un `Order` con la empresa como compradora y entrega en punto seguro en esa empresa. El resto de ofertas pasan a `REJECTED` y el anuncio a `SOLD`.
6. Guarda siempre la mejor oferta recibida. Si el artículo acaba vendiéndose a un particular, la comparación entre esa oferta y el precio final alimenta la métrica "oferta de empresa frente a precio particular".

### Punto seguro
1. Comprador y vendedor eligen una empresa punto seguro y una franja horaria.
2. La empresa rellena el checklist de la categoría y sube fotos.
3. Resultado `VERIFIED`: se liberan los fondos y el anuncio luce el sello "verificado por tienda". Resultado fallido: se abre automáticamente un ticket `ORDER_ISSUE`.
4. La empresa cobra una comisión fija por entrega (configurable por el admin).

### Tickets
1. **Origen.** Los usuarios los abren desde el centro de ayuda, desde un pedido ("tengo un problema") o desde un anuncio o perfil ("denunciar"). El sistema los crea solo en tres casos: anuncio con `fraudScore` alto (`FRAUD_REVIEW`), registro de empresa (`BUSINESS_VERIFICATION`) y verificación fallida en punto seguro (`ORDER_ISSUE`).
2. **Tipos:** `ORDER_ISSUE`, `PAYMENT`, `LISTING_REPORT`, `USER_REPORT`, `FRAUD_REVIEW`, `BUSINESS_VERIFICATION`, `ACCOUNT` y `OTHER`.
3. **Prioridad** (`LOW`, `MEDIUM`, `HIGH`, `URGENT`): se asigna automáticamente según el tipo y el importe del pedido relacionado, y el moderador puede cambiarla. Al crear el ticket, el ml-service sugiere tipo y prioridad a partir del texto; es solo una sugerencia que el moderador confirma.
4. **SLA:** cada prioridad tiene un tiempo máximo de primera respuesta y de resolución (configurables). La bandeja se ordena por vencimiento y marca los tickets a punto de incumplir y los incumplidos.
5. **Asignación:** cola de tickets sin asignar donde el moderador pulsa "asignarme". El bloqueo optimista impide que dos moderadores cojan el mismo ticket. El admin puede reasignar.
6. **Conversación:** hilo con respuestas públicas, notas internas (solo moderadores y admin), adjuntos y respuestas predefinidas. Actualización en tiempo real por WebSocket. El usuario recibe aviso de cada respuesta por la web y por sus bots vinculados.
7. **Acciones de resolución**, ejecutadas desde el propio ticket según su tipo: liberar fondos, reembolso total o parcial, ocultar anuncio, suspender cuenta temporalmente y validar o rechazar empresa. Cada acción exige motivo, queda en `TicketEvent` y en el audit log, y se informa al usuario en el hilo.
8. **Escalado:** los reembolsos por encima de un umbral configurable (200 € por defecto) y las suspensiones permanentes solo los puede ejecutar el admin. El moderador escala el ticket con una nota y el admin lo resuelve o lo devuelve.
9. **Cierre:** al resolver se elige un código de resolución. El solicitante puede valorar la atención de 1 a 5. Si responde antes de 7 días el ticket se reabre; si no, se cierra solo.

## Integraciones

- **Stripe Connect (solo modo test, nunca claves live).** Cuentas Express para vendedores (particulares y empresas). Patrón "separate charges and transfers": se cobra al comprador, se retiene y se transfiere al vendedor al completar, descontando la comisión. Los reembolsos de tickets usan la API de refunds. Webhooks en `/api/webhooks/stripe` verificando la firma.
- **Nominatim.** Solo desde el backend, con `User-Agent` propio, máximo 1 petición por segundo (rate limiter) y caché en Redis. Las coordenadas exactas se guardan en BD. La API pública devuelve ubicación aproximada (redondeo de ~1 km) y nombre del barrio o municipio. Las empresas sí muestran su dirección exacta.
- **INE (API JSON Tempus).** Población por provincia y municipio, cargada por un script Python en `province_stats`. Sirve para normalizar métricas por habitante.
- **Envíos.** Simulados: un job avanza los estados con retardos aleatorios. Deja una interfaz preparada para integrar Packlink o Sendcloud más adelante.
- **Discord y Telegram.** El usuario vincula su cuenta con un código que le da el bot. El backend publica eventos en el canal Redis `notifications` y los bots los consumen y envían.
- **Mapas.** GeoJSON de provincias de España en `frontend/src/assets`.

## Servicios Python

- **ml-service (FastAPI)**
  - `POST /price/suggest` → `{min, max, suggested, confidence}` según categoría, estado y palabras clave del título.
  - `POST /fraud/score` → puntuación 0-1 y motivos (precio anómalo, cuenta nueva con muchos anuncios, enlaces externos en mensajes...). Reglas más IsolationForest.
  - `POST /tickets/classify` → tipo y prioridad sugeridos a partir del asunto y la descripción (TF-IDF más regresión logística).
  - Reentrenamiento nocturno con anuncios vendidos y tickets cerrados.
  - El backend lo llama con un timeout corto. Si falla, se sigue sin sugerencia: nunca bloquea.
- **scripts/seed.** Genera datos sintéticos realistas con Faker y semilla fija, de forma idempotente: ~5.000 particulares, ~80 empresas repartidas por provincias, ~8 moderadores, ~50.000 anuncios, ~15.000 pedidos y ~3.000 tickets en 18 meses. Debe incluir estacionalidad (fines de semana, Navidad, vuelta al cole), categorías que se ponen de moda, ofertas de empresas aceptadas, rechazadas, retiradas y caducadas, fraudes, y tickets de todos los tipos con conversaciones, SLA cumplidos e incumplidos, escalados, reaperturas, valoraciones y moderadores con rendimiento distinto. Sin esto los paneles no tienen sentido.
- **scripts/ine.** Descarga y carga los datos del INE.
- **scripts/weekly_report.** Calcula los KPIs de la semana (incluidos los de soporte) y el bot los publica en un canal de Discord para admins.

## Paneles de estadísticas

Requisitos comunes:
- Filtro global de fechas, con comparación frente al periodo anterior, que afecta a todo el panel.
- Exportación a CSV en cada tabla.
- Estados de carga y vacío, y diseño responsive.
- Las agregaciones se hacen en SQL (vistas o consultas nativas), nunca cargando filas en memoria en Java.

### Admin
- **Resumen:** volumen vendido (GMV), comisión ganada, pedidos, usuarios activos, anuncios nuevos y tickets abiertos, con % frente al periodo anterior. Actualización en vivo por WebSocket.
- **Ventas:** evolución temporal, particulares frente a empresas, por categoría, ticket medio y embudo visita → favorito o chat → compra.
- **Empresas:** ranking, tasa de aceptación de ofertas, % de anuncios abiertos a ofertas que reciben alguna, tiempo medio hasta la primera oferta, diferencia media entre la mejor oferta y el precio final de venta a particular, entregas en punto seguro y % de verificaciones fallidas.
- **Usuarios:** altas por tipo de cuenta, retención por cohortes (heatmap), mejores vendedores y mapa de España por provincia normalizado por habitantes.
- **Mercado:** tiempo medio hasta la venta por categoría, precio publicado frente a sugerido y categorías en tendencia.
- **Soporte:** backlog (abiertos frente a cerrados por día), tiempo de primera respuesta y de resolución (mediana y percentil 90), % de SLA cumplido por prioridad, tickets por tipo, % de reabiertos y de escalados, valoración media, heatmap de tickets por día y hora, y rendimiento por moderador (resueltos, tiempos, SLA y valoración).
- **Riesgo:** fraude detectado, pedidos en disputa e importe reembolsado.
- **Impacto:** CO2 evitado, total y por categoría.
- **Sistema:** estado y última ejecución de los servicios y scripts Python, errores recientes.
- **Gestión:** usuarios y moderadores, empresas, categorías y checklists, comisiones, SLA, respuestas predefinidas, tickets escalados y audit log.
- **Informes BI:** Power BI embebido (ver abajo).

### Moderador
- **Bandeja:** mis tickets, sin asignar y a punto de incumplir SLA, con filtros por tipo, prioridad y estado.
- **Mis métricas:** tickets resueltos, tiempos de respuesta y resolución, % de SLA cumplido y valoración media, comparados con la media del equipo.

### Empresa
Ofertas enviadas, aceptadas, rechazadas y caducadas, tasa de éxito, importe medio ofertado frente al precio pedido, margen entre compra y reventa, rotación de stock, entregas en punto seguro, ingresos por comisiones y valoraciones.

### Particular
Mis compras y ventas, ofertas recibidas de empresas, mis tickets, ingresos y CO2 evitado.

## Power BI

- Esquema `analytics` en PostgreSQL con vistas en estrella: `fact_orders`, `fact_listings`, `fact_offers`, `fact_tickets`, `dim_date`, `dim_category`, `dim_province`, `dim_business`, `dim_moderator`. Documentadas en `bi/README.md`. Power BI Desktop se conecta a estas vistas.
- Sección "Informes BI" del panel de admin con `powerbi-client-angular`. El backend genera el embed token en `GET /api/admin/bi/embed-token` con un service principal de Entra ID (patrón "app owns data").
- Todo va detrás de `app.bi.powerbi.enabled` (`false` por defecto). Sin credenciales, la sección muestra un aviso y la app funciona igual. Credenciales solo por variables de entorno.

## Convenciones

### General
- Código, identificadores y endpoints en inglés. Textos de la interfaz en español.
- Lógica plana e inline. No crees helpers, interfaces ni capas de abstracción para un único uso; extrae solo cuando algo se repite de verdad.
- Ningún secreto en el repo: todo por `.env`, y mantén `.env.example` al día.
- No añadas dependencias sin una necesidad clara. Si añades una, dilo.
- Commits pequeños con Conventional Commits.

### Backend
- Paquetes por funcionalidad: `com.remarket.{auth,user,business,listing,order,payment,offer,safepoint,chat,ticket,notification,admin,analytics}`, cada uno con su controller, service, repository y dto.
- DTOs como `record`. Nunca expongas entidades en la API.
- Esquema solo con Flyway (`spring.jpa.hibernate.ddl-auto=validate`).
- Errores con `@RestControllerAdvice` y `ProblemDetail`.
- Seguridad: access token JWT de 15 minutos más refresh token. Autorización por tipo de cuenta con `@PreAuthorize`. Las acciones de empresa exigen además `verificationStatus = VERIFIED`. Un usuario solo ve sus propios tickets y nunca las notas internas.
- API REST bajo `/api`, documentada con springdoc (Swagger UI).
- Tests: JUnit con Testcontainers (PostgreSQL + PostGIS) para integración y MockMvc para controladores. Cada flujo clave (compra, oferta de empresa, punto seguro, ciclo de vida de un ticket con escalado y reembolso) tiene su test de integración.

### Frontend
- Standalone components, signals para el estado, control flow nativo e `inject()`.
- Rutas lazy por área (`/`, `/listing/:id`, `/me`, `/support`, `/business`, `/moderation`, `/admin`) con guards por tipo de cuenta.
- Registro con selector de tipo de cuenta; el de empresa pide los datos fiscales y muestra el estado "pendiente de validación".
- Interceptor para el JWT y el refresh. Un servicio por funcionalidad para hablar con la API.
- PrimeNG para la interfaz (Table con filtros y exportación, DatePicker, Dialog, Toast, Timeline para el historial del ticket). ngx-echarts para gráficas. Leaflet para mapas.
- TypeScript en modo strict.

### Python
- uv para dependencias, ruff para lint y pytest para tests.
- Cada servicio o script con un README corto: qué hace y cómo se ejecuta.

## Comandos

```bash
docker compose up -d                                   # postgres+postgis, redis, minio
cd backend && ./mvnw spring-boot:run                   # API en :8080
cd backend && ./mvnw verify                            # tests backend
cd frontend && npm start                               # app en :4200
cd frontend && npm test                                # tests frontend
cd python/ml-service && uv run uvicorn app.main:app --reload --port 8000
cd python/scripts && uv run seed.py                    # datos sintéticos
stripe listen --forward-to localhost:8080/api/webhooks/stripe
```

Actualiza esta sección si cambian los comandos.

## Calidad y CI

- GitHub Actions en cada PR y en cada push a `main`, con un job por parte:
  - Backend: `./mvnw verify` (tests, cobertura con JaCoCo y formato con Spotless).
  - Frontend: ESLint, Prettier, tests y build de producción.
  - Python: ruff y pytest en cada servicio.
- SonarQube Cloud analiza cada PR. Objetivo: quality gate en verde y ~80 % de cobertura en la lógica de negocio (servicios y flujos), sin perseguir cobertura en DTOs ni configuración.
- Dependabot para Maven, npm, pip y GitHub Actions. Trivy escanea las imágenes Docker.
- Antes de cada release, repaso del OWASP Top 10 contra los cambios de la fase.

## Flujo de trabajo

Este proyecto sigue la skill personal `flujo-github`: cárgala antes de preparar el repositorio, empezar o cerrar una fase, o trabajar una issue. Si no está disponible, para y avísame.

- **Tablero:** `ReMarket`, con las columnas en inglés: `Backlog`, `Ready` (Listo), `In Progress` (En curso), `In Review` (Revisión) y `Done` (Hecho). Los ids están en `docs/github-project.md`.
- **Áreas:** `backend`, `frontend`, `python`, `infra`
- **Tipos de usuario para las demos:** particular, empresa (pendiente y validada), moderador y admin.
- **v1.0.0:** al cerrar la fase 9.
- **Definición de hecho, además de la de la skill:** quality gate de SonarQube Cloud en verde, y Swagger y migraciones de Flyway al día.

### Reglas que no se saltan

- Nunca hagas push directo a `main`, merge de PRs, `git push --force` ni `git reset --hard` sobre ramas ya subidas. Única excepción: el commit inicial de un repositorio vacío, con mi confirmación.
- No cierres issues a mano: se cierran con el merge del PR.
- Pide confirmación antes de crear etiquetas o releases y antes de cambiar la configuración del repositorio.
- Una rama y un PR por issue.

## Fases

0. **Base:**
   - Preparar el proyecto con la skill `flujo-github` (labels, milestones, plantillas y comprobación del tablero), más Dependabot y SonarQube Cloud. La protección de `main` y el tablero los creo yo.
   - Documentación inicial: `docs/vision.md`, diagrama C4 de contexto y ADR-0001 (registrar decisiones con ADRs).
   - Monorepo, docker-compose, Spring Boot y Angular arrancando, y layout con PrimeNG.
   - Registro y login JWT con los cuatro tipos de cuenta (particular, empresa pendiente de validar, moderador y admin).
   - CI completa según "Calidad y CI".
1. **Marketplace:** categorías, anuncios con fotos (MinIO), búsqueda con filtros y distancia (PostGIS), Nominatim, favoritos y chat en tiempo real.
2. **Pedidos y pagos:** Stripe Connect en test, retención y liberación de fondos, envío simulado y valoraciones.
3. **Soporte:** tickets completos (tipos, prioridad, SLA, asignación, notas internas, adjuntos, respuestas predefinidas, acciones de resolución, escalado, reapertura y valoración), con disputas de pedidos y denuncias integradas.
4. **Empresas:** validación mediante ticket, escaparate, bandeja de oportunidades con ofertas manuales y punto seguro con verificación.
5. **Python:** seed sintético, carga del INE y ml-service (precio sugerido, fraude y clasificación de tickets).
6. **Paneles:** admin, moderador, empresa y particular con todas las métricas.
7. **Alertas:** búsquedas guardadas, bots de Discord y Telegram, informe semanal.
8. **Power BI:** esquema `analytics` y embebido en el admin.
9. **Pulido:** tests e2e con Playwright, revisión de seguridad y README con capturas.
