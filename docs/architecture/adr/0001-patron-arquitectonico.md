# ADR-0001: Microservicios modulares orientados a eventos como patrón arquitectónico

- **Estado:** Aceptado
- **Fecha:** 2026-10-13
- **Decisores:** Lorena Barragán, Diego Ñanguma, Jonathan Diaz (docente: Juan Camilo Rodríguez Villada)
- **Relacionado con:** [ADR-0002](0002-sistema-de-persistencia.md), [ADR-0004](0004-estrategia-pagos-strategy-webhooks.md), [ADR-0005](0005-validacion-ica-sincrona-vs-asincrona.md)

## Contexto y planteamiento del problema

CAMPORA maneja dominios heterogéneos (Ofertas/Marketplace, Insumos regulados, Órdenes/Matching, Pagos) con necesidades distintas. En época de cosecha el tráfico puede subir hasta un 300 % (ASR-04), y el dominio de insumos exige aislamiento por cumplimiento normativo (ASR-05, RES-06). La restricción de presupuesto (RES-01) y el MVP de cuatro meses (RES-04) limitan la complejidad que podemos asumir.

## Factores de decisión

- Aislamiento de fallos: una caída en notificaciones no debe frenar las ventas.
- Escalado selectivo en cosecha sin sobredimensionar todo el sistema (ASR-04).
- Reemplazar pasarelas o servicios externos sin tocar el dominio (ASR-07).
- Costo de infraestructura bajo 150 USD/mes (RES-01).
- Equipo de tres personas y un MVP de cuatro meses.

## Opciones consideradas

1. Monolito tradicional.
2. Monolito modular.
3. Microservicios modulares con Event Bus y arquitectura hexagonal.

## Decisión

Se elige la **opción 3**: backend en microservicios con arquitectura limpia/hexagonal, usando REST para lecturas síncronas y un Event Bus asíncrono para eventos de negocio. El código vive en un monorepositorio (`frontend-pwa`, `api-gateway`, `core-services`) para reducir la carga operativa del equipo.

## Consecuencias

**Positivas**
- Escalado independiente de búsqueda y pedidos en cosecha.
- Aislamiento de fallos entre dominios.
- La lógica de negocio queda separada de los detalles técnicos (capas dominio, aplicación e infraestructura).

**Negativas / costos**
- Mayor complejidad de despliegue y observabilidad.
- Consistencia eventual entre servicios; hay que diseñar eventos idempotentes.
- Riesgo de sobreingeniería para un equipo de tres personas y un MVP.

**Condición de revisión:** si durante el MVP el costo operativo o la complejidad superan lo manejable, se reevalúa la opción 2 (monolito modular) manteniendo los mismos límites de dominio, lo que permitiría volver a separar servicios después.

## Pros y contras de las opciones

| Opción | A favor | En contra |
|---|---|---|
| Monolito | Simple y rápido de desplegar | Alto acoplamiento; no escala por dominio |
| Monolito modular | Menor costo operativo; fronteras claras | Escalado todo-o-nada; el aislamiento de fallos es débil |
| Microservicios + EDA | Escalado y aislamiento selectivos | Complejidad operativa y de datos distribuidos |

## Verificación

Desconectar en caliente el servicio de notificaciones y comprobar que la creación de pedidos sigue funcionando.

## Diagramas relacionados

- [Contexto C4](../diagrams/c4-contexto.puml)
- [Contenedores C4](../diagrams/c4-contenedores.puml)
- [Componentes C4](../diagrams/c4-componentes.puml)
