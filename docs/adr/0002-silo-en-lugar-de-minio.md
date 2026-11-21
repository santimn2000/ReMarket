# ADR-0002: Usar Silo como almacenamiento S3 en lugar de la imagen de MinIO

- **Estado:** Aceptado
- **Fecha:** 2026-11-21

## Contexto

El stack prevé MinIO para guardar las fotos de los anuncios y los adjuntos de los tickets, con una API compatible con S3. Al preparar el `docker-compose.yml` (#4), la imagen `minio/minio` ya no existe en Docker Hub: el repositorio se eliminó en septiembre de 2026, después de que MinIO archivara la edición comunitaria en GitHub. `quay.io/minio/minio` tampoco sirve la imagen sin autenticación. Hace falta una imagen mantenida, con versiones fijas y compatible con S3, que el backend pueda usar sin cambios desde la fase 1.

## Decisión

Usar Silo (`pgsty/silo`), el fork de MinIO que mantiene PGSTY, con una versión fija (`RELEASE.2026-11-12T00-00-00Z`). Es el mismo código que MinIO: mantiene las variables `MINIO_ROOT_USER` y `MINIO_ROOT_PASSWORD`, la API S3, la consola web y los endpoints de salud. En el proyecto se sigue hablando de "MinIO" para referirse a este almacenamiento.

## Alternativas descartadas

- RustFS (`rustfs/rustfs`): reescritura en Rust con licencia Apache-2.0 y mucha adopción, pero la 1.0 salió en octubre de 2026, su compatibilidad con S3 está menos probada y cambia variables y herramientas (`RUSTFS_*`).
- La última imagen de `minio/minio` en otro registro o espejo: sin parches de seguridad y sin una fuente oficial que la sirva.
- Compilar MinIO desde el código fuente: un `Dockerfile` propio que mantener y un arranque mucho más lento para un servicio que solo es infraestructura local.
- Garage o SeaweedFS: piden configuración adicional (topología de nodos, claves por CLI o varios procesos) que no aporta nada a un entorno de desarrollo.

## Consecuencias

- El backend usa un cliente S3 estándar contra un servidor que se comporta igual que MinIO, y la consola sigue en el puerto 9001.
- Para los tests de integración, el módulo MinIO de Testcontainers debería aceptar la imagen de Silo con `asCompatibleSubstituteFor("minio/minio")`. Hay que comprobarlo al añadir los tests de almacenamiento en la fase 1.
- Silo depende de un único mantenedor y tiene licencia AGPLv3, igual que MinIO. Si deja de publicarse, la alternativa es RustFS, que también es compatible con S3, y el cambio se queda en el `docker-compose.yml` y la configuración del cliente.
