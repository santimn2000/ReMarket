# Visión del producto

## Qué es ReMarket

ReMarket es un marketplace generalista de segunda mano para España en el que conviven particulares y empresas (tiendas de segunda mano). Además de comprar y vender entre particulares, las empresas pueden ofertar por los artículos de los particulares y actuar como punto seguro de entrega. Un equipo de moderadores atiende todo el soporte mediante tickets.

Es un proyecto personal de portfolio: prioriza funcionalidades completas de punta a punta, código limpio y un panel de estadísticas potente.

## Qué problema resuelve

Vender y comprar de segunda mano entre particulares tiene cuatro problemas habituales:

- **Vender lleva tiempo.** El particular que quiere deshacerse de algo rápido solo puede esperar a que aparezca un comprador. En ReMarket puede abrir su anuncio a ofertas de empresas y elegir entre vender ya a una tienda o esperar a otro particular por más dinero.
- **La entrega da desconfianza.** Ni el comprador sabe si el artículo es lo que dice el anuncio ni el vendedor si va a cobrar. En ReMarket el pago queda retenido hasta la entrega, y la entrega puede hacerse en una empresa adherida que revisa el artículo antes de liberar el dinero.
- **No se sabe qué precio poner.** Sin referencia, los anuncios salen caros y no se venden, o baratos y se pierde dinero. ReMarket sugiere un precio al publicar y muestra en cada anuncio si su precio es bueno, normal o alto respecto a artículos similares.
- **Los problemas no tienen a quién acudir.** Disputas, denuncias y fraudes se pierden en chats o correos. En ReMarket todo pasa por un sistema de tickets con prioridad, plazos de respuesta, historial y acciones de resolución trazables.

## Para quién

Hay cuatro tipos de cuenta. Las dos de usuario se eligen al registrarse y no cambian; las dos internas solo las crea un admin.

| Cuenta | Quién es | Qué hace en ReMarket |
|---|---|---|
| **Particular** (`PERSONAL`) | Persona que compra y vende sus cosas | Compra y vende, decide si cada anuncio suyo acepta ofertas de empresas y acepta o rechaza las que recibe, abre tickets, guarda búsquedas y vincula Discord o Telegram para recibir alertas. |
| **Empresa** (`BUSINESS`) | Tienda de segunda mano con CIF | Se registra con sus datos fiscales y queda pendiente hasta que un moderador la valida. Validada, publica su stock, oferta por anuncios de particulares, puede actuar como punto seguro y tiene su propio panel de estadísticas. También compra y abre tickets. |
| **Moderador** (`MODERATOR`, interna) | Persona del equipo de soporte | Atiende tickets: se los asigna, responde, escribe notas internas, ejecuta las acciones de resolución y escala al admin lo que excede sus permisos. Tiene su bandeja y sus métricas. |
| **Admin** (`ADMIN`, interna) | Responsable de la plataforma | Todo lo del moderador, más la gestión de usuarios, moderadores y empresas, la configuración (categorías, comisiones, SLA y respuestas predefinidas), los tickets escalados y el panel global con los informes de Power BI. |

## Diferenciales

1. **Ofertas de empresas.** El particular puede abrir su anuncio a ofertas de empresas. Las empresas validadas lo ven en su bandeja de oportunidades y deciden a mano si ofertan y cuánto, con el precio pedido y el sugerido como referencia. Nunca hay ofertas automáticas. El particular elige entre aceptar una oferta y vender ya, o esperar a otro particular.
2. **Empresa como punto seguro.** Comprador y vendedor pueden hacer la entrega en una empresa adherida, que revisa el artículo con el checklist de su categoría y lo marca como "verificado por tienda". Si la verificación falla se abre un ticket automáticamente. La empresa cobra una comisión fija por entrega.
3. **Precio inteligente.** Un servicio en Python sugiere un rango de precio al publicar a partir de la categoría, el estado y el título, y cada anuncio muestra si su precio es bueno, normal o alto respecto a similares. Si el servicio no responde, la publicación sigue sin sugerencia.
4. **Soporte con tickets.** Incidencias, disputas, denuncias, revisiones de fraude y validaciones de empresa pasan por un único sistema de tickets, con SLA por prioridad, asignación con bloqueo optimista, notas internas, respuestas predefinidas, acciones de resolución y escalado al admin.
5. **Alertas por Discord y Telegram.** El usuario vincula su cuenta con un bot y recibe avisos de búsquedas guardadas, ofertas recibidas, cambios de estado de sus pedidos y respuestas en sus tickets.
6. **Impacto.** ReMarket calcula el CO2 evitado por comprar de segunda mano, por usuario, por categoría y en total, y lo muestra en los paneles.

## Fuera del alcance

Lo siguiente queda fuera de forma explícita. Si alguna de estas decisiones cambia, se registra en un ADR.

- **Envíos reales.** Los envíos son simulados: un job avanza sus estados con retardos aleatorios. Queda preparada una interfaz para integrar Packlink o Sendcloud más adelante.
- **Pagos reales.** Stripe Connect funciona solo en modo test. No se usan claves live ni se mueve dinero real.
- **Ofertas automáticas.** El sistema avisa a las empresas de anuncios que les interesan, pero nunca crea ofertas por su cuenta.
- **Apps móviles nativas.** ReMarket es una aplicación web con diseño responsive.
- **Otros países.** El producto está pensado para España: datos del INE, provincias españolas en los mapas y CIF en el registro de empresas.
- **Ubicación exacta de los particulares.** Se guarda en la base de datos, pero la API pública solo devuelve una ubicación aproximada (~1 km) y el barrio o municipio. Solo las empresas muestran su dirección exacta.
- **Facturación y fiscalidad.** No se emiten facturas ni se calculan impuestos sobre ventas o comisiones.
- **Avisos por email.** Los canales de aviso son la propia web (en tiempo real), Discord y Telegram.

## Más información

- [Diagrama C4 de contexto](design/c4-contexto.md): ReMarket, sus usuarios y los sistemas externos con los que se integra.
- [Decisiones de arquitectura](adr/): ADRs del proyecto.
- [Progreso por fases](PROGRESS.md).
