# ADR-0004: Patrón Strategy y Webhooks para pasarelas de pago

- **Estado:** Aceptado
- **Fecha:** 2026-10-13
- **Decisores:** Lorena Barragán, Diego Ñanguma, Jonathan Diaz (docente: Juan Camilo Rodríguez Villada)
- **Relacionado con:** [ADR-0001](0001-patron-arquitectonico.md), [ADR-0003](0003-estrategia-seguridad.md)

## Contexto y planteamiento del problema

Se deben integrar pasarelas colombianas (Wompi, Nequi, PSE; RES-05) y poder cambiar o añadir proveedores sin modificar la lógica del dominio (ASR-07, ESC-06). Las confirmaciones de pago llegan por Webhook y deben actualizar la orden en menos de 500 ms (ESC-05).

## Opciones consideradas

1. Acoplar directamente el SDK de cada pasarela en el servicio de pedidos.
2. Interfaz `PaymentProcessor` con patrón Strategy y adaptadores desacoplados que reciben confirmaciones vía Webhooks.

## Decisión

Se elige la **opción 2**. `PaymentAdapter` implementa `IPaymentService`; cada pasarela es un adaptador intercambiable. `OrderManager` solo conoce la interfaz.

## Consecuencias

**Positivas**
- Nuevos métodos de pago (por ejemplo Daviplata) en menos de 16 horas/hombre, sin impacto en otros módulos.
- Las dependencias externas quedan aisladas.

**Negativas / costos**
- Los Webhooks pueden repetirse: se requiere control de **idempotencia** para no duplicar pagos.
- Los endpoints de Webhook deben validar la firma del proveedor (ver [ADR-0003](0003-estrategia-seguridad.md)).

## Verificación

Pruebas de integración con servidor simulado (Mock Server) enviando respuestas exitosas (200 OK) y notificaciones repetidas para comprobar la idempotencia.

## Diagramas relacionados

- [Contexto C4](../diagrams/c4-contexto.puml)
- [Componentes C4](../diagrams/c4-componentes.puml)
