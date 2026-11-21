# Diagrama C4 de contexto

Nivel 1 del modelo [C4](https://c4model.com/): ReMarket como un único sistema, las personas que lo usan y los sistemas externos con los que se integra. Lo que hay dentro de ReMarket (backend, frontend, servicios Python, PostgreSQL, Redis y MinIO) queda para el diagrama de contenedores.

```mermaid
flowchart LR
  particular["<b>Particular</b><br/>[Persona]<br/>Compra y vende artículos de segunda mano"]
  empresa["<b>Empresa</b><br/>[Persona]<br/>Tienda que oferta, vende y hace de punto seguro"]
  moderador["<b>Moderador</b><br/>[Persona]<br/>Atiende los tickets de soporte"]
  admin["<b>Admin</b><br/>[Persona]<br/>Gestiona la plataforma y consulta los paneles"]

  remarket["<b>ReMarket</b><br/>[Sistema]<br/>Marketplace de segunda mano con ofertas de empresas, punto seguro, precio sugerido y soporte con tickets"]

  stripe["<b>Stripe Connect</b><br/>[Sistema externo]<br/>Pagos en modo test"]
  nominatim["<b>Nominatim</b><br/>[Sistema externo]<br/>Geocodificación de OpenStreetMap"]
  ine["<b>INE</b><br/>[Sistema externo]<br/>Población por provincia y municipio"]
  discord["<b>Discord</b><br/>[Sistema externo]<br/>Bot de alertas"]
  telegram["<b>Telegram</b><br/>[Sistema externo]<br/>Bot de alertas"]
  entra["<b>Microsoft Entra ID</b><br/>[Sistema externo]<br/>Autentica el service principal"]
  powerbi["<b>Power BI</b><br/>[Sistema externo]<br/>Informes analíticos embebidos"]

  particular -- "Compra, vende y abre tickets" --> remarket
  empresa -- "Oferta, vende y verifica entregas" --> remarket
  moderador -- "Atiende tickets" --> remarket
  admin -- "Gestiona y consulta paneles" --> remarket
  admin -- "Ve los informes embebidos" --> powerbi

  remarket -- "Cobra, retiene, transfiere y reembolsa" --> stripe
  stripe -. "Webhooks firmados" .-> remarket
  remarket -- "Geocodifica direcciones" --> nominatim
  remarket -- "Carga la población" --> ine
  remarket -- "Envía alertas" --> discord
  remarket -- "Envía alertas" --> telegram
  remarket -- "Pide token de acceso" --> entra
  remarket -- "Genera el embed token" --> powerbi

  classDef person fill:#08427b,stroke:#052e56,color:#fff
  classDef system fill:#1168bd,stroke:#0b4884,color:#fff
  classDef external fill:#8c8c8c,stroke:#6b6b6b,color:#fff
  class particular,empresa,moderador,admin person
  class remarket system
  class stripe,nominatim,ine,discord,telegram,entra,powerbi external
```

**Leyenda:** azul oscuro, personas; azul, el sistema ReMarket; gris, sistemas externos. Cada flecha va de quien inicia la interacción a quien la recibe; la discontinua marca los webhooks, que Stripe envía a ReMarket de forma asíncrona.

> El diagrama usa un `flowchart` de Mermaid con la notación y los colores de C4 en lugar del tipo `C4Context`. Con `C4Context`, Mermaid coloca los elementos en filas fijas y las etiquetas de las relaciones se solapan entre sí y con las cajas; el `flowchart` reparte los elementos y las etiquetas automáticamente y se lee bien en GitHub.

## Elementos

### Personas

| Persona | Papel |
|---|---|
| Particular | Compra y vende. Decide si sus anuncios aceptan ofertas de empresas, acepta o rechaza las que recibe, abre tickets y recibe alertas en Discord o Telegram. |
| Empresa | Tienda de segunda mano validada mediante ticket. Publica su stock, oferta a mano por anuncios de particulares, hace de punto seguro y consulta su panel. |
| Moderador | Equipo de soporte interno. Atiende la bandeja de tickets, ejecuta las acciones de resolución y escala al admin lo que excede sus permisos. |
| Admin | Responsable de la plataforma. Gestiona usuarios, empresas y configuración, resuelve los tickets escalados y consulta el panel global y los informes de Power BI. |

### Sistemas externos

| Sistema | Para qué lo usa ReMarket |
|---|---|
| Stripe Connect | Solo en modo test. Cuentas Express para vendedores, cobro al comprador con retención y transferencia al vendedor al completar el pedido, descontando la comisión ("separate charges and transfers"). Reembolsos desde los tickets. Stripe avisa a ReMarket por webhooks firmados en `/api/webhooks/stripe`. |
| Nominatim | Geocodifica direcciones desde el backend, con `User-Agent` propio, como máximo una petición por segundo y caché en Redis. |
| INE | Su API JSON Tempus da la población por provincia y municipio. Un script Python la carga para normalizar métricas por habitante. |
| Discord | El usuario vincula su cuenta con un código del bot y recibe alertas de búsquedas guardadas, ofertas, pedidos y tickets. Los admins reciben el informe semanal en un canal. |
| Telegram | Las mismas alertas que Discord, a través de su propio bot. |
| Microsoft Entra ID | Autentica el service principal con el que el backend pide acceso a Power BI (patrón "app owns data"). |
| Power BI | El backend genera el embed token en `GET /api/admin/bi/embed-token` y el navegador del admin carga los informes embebidos. Desactivado por defecto con `app.bi.powerbi.enabled`. |
