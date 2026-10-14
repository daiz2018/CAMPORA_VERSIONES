# ADR-0002: Persistencia híbrida (PostgreSQL + Redis) y cliente PWA Offline-First con IndexedDB

- **Estado:** Aceptado
- **Fecha:** 2026-10-13
- **Decisores:** Lorena Barragán, Diego Ñanguma, Jonathan Diaz (docente: Juan Camilo Rodríguez Villada)
- **Relacionado con:** [ADR-0001](0001-patron-arquitectonico.md), [ADR-0003](0003-estrategia-seguridad.md), [ADR-0005](0005-validacion-ica-sincrona-vs-asincrona.md)

## Contexto y planteamiento del problema

Los productores operan en zonas rurales del Tolima con redes 2G/3G o sin datos (RES-02, R-01). Deben poder registrar ofertas sin perder información y sincronizarlas al recuperar señal (ASR-01, ESC-01). Del lado del servidor se necesitan transacciones consistentes, un log de auditoría inmutable (ASR-05) y búsquedas rápidas bajo carga (ASR-02, ESC-02).

## Factores de decisión

- Operar sin conexión, con escritura local menor a 200 ms.
- Consistencia transaccional para pedidos y pagos.
- Búsqueda menor a 800 ms (p95) con 5000 usuarios activos.
- Bajo costo y bajo consumo de datos en el cliente.

## Opciones consideradas

1. App nativa solo online + base de datos única.
2. PWA con IndexedDB + PostgreSQL únicamente.
3. PWA con IndexedDB + PostgreSQL (transaccional y auditoría) + Redis (caché de búsqueda y cola de eventos).
4. Base NoSQL documental como almacén principal.

## Decisión

Se elige la **opción 3**:
- **Cliente:** PWA con IndexedDB y Service Worker que sincroniza en segundo plano al detectar red.
- **Servidor:** PostgreSQL para usuarios, órdenes y logs de auditoría (tabla de solo inserción para el log inmutable); Redis como caché de lectura y cola de eventos.
- **Conflictos de sincronización:** First-Write-Wins con verificación de marca de tiempo. Las ofertas creadas offline entran como `PENDIENTE_SINCRONIZACION` y no se publican hasta que el backend confirme la reserva de inventario.

## Consecuencias

**Positivas**
- Operación continua en el campo con un único código para Android/iOS.
- Consistencia fuerte donde importa (órdenes, pagos, auditoría).
- Búsquedas rápidas gracias a la caché.

**Negativas / costos**
- Complejidad en la resolución de conflictos (riesgo residual documentado en el dossier, sección 10.2).
- Dos motores de datos en el servidor que operar y respaldar.
- Redis como cola de eventos ofrece menos garantías de durabilidad que un broker dedicado; debe configurarse con persistencia y reintentos, y revisarse si crece el volumen.
- Si el JWT expira estando offline, la sincronización falla con 401; la cola local no debe perderse (ver [ADR-0003](0003-estrategia-seguridad.md)).

## Pros y contras de las opciones

| Opción | A favor | En contra |
|---|---|---|
| App nativa online | Simple | Inviable sin conexión; incumple ASR-01 |
| PWA + solo PostgreSQL | Menos piezas | Búsqueda lenta en picos; sin cola de eventos |
| PWA + PostgreSQL + Redis | Cumple offline, consistencia y rendimiento | Mayor operación y complejidad de sync |
| NoSQL principal | Flexibilidad de esquema | Transacciones y auditoría más débiles |

## Verificación

Simular desconexión en Chrome DevTools (Network: Offline), registrar datos y validar que la sincronización termine en menos de 5 s al reconectar.

## Diagramas relacionados

- [Contenedores C4](../diagrams/c4-contenedores.puml)
- [Secuencia: sincronización offline](../diagrams/secuencia-proceso-clave.puml)
- [Despliegue](../diagrams/despliegue.puml)
