# ADR-0003: Estrategia de seguridad (JWT/OAuth2, TLS 1.3, AES-256, WAF)

- **Estado:** Aceptado
- **Fecha:** 2026-10-13
- **Decisores:** Lorena Barragán, Diego Ñanguma, Jonathan Diaz (docente: Juan Camilo Rodríguez Villada)
- **Relacionado con:** [ADR-0001](0001-patron-arquitectonico.md), [ADR-0002](0002-sistema-de-persistencia.md), [ADR-0004](0004-estrategia-pagos-strategy-webhooks.md)

## Contexto y planteamiento del problema

CAMPORA maneja datos personales y de ubicación (Ley 1581 de 2012, RES-03) y transacciones financieras (R-05). El oficial de seguridad exige cifrado y autenticación robusta (ASR-03, ESC-03), y el sistema debe resistir inyecciones y manipulación de tokens.

## Factores de decisión

- Cumplimiento de Habeas Data.
- Protección de datos en tránsito y en reposo.
- Un único punto de entrada para aplicar controles.
- Bajo costo (RES-01): usar capacidades gestionadas del borde.

## Opciones consideradas

1. Seguridad implementada por cada microservicio por separado.
2. Seguridad centralizada en el API Gateway más WAF/CDN en el borde, con cifrado en tránsito y en reposo.
3. Delegar toda la identidad a un proveedor externo (IDaaS).

## Decisión

Se elige la **opción 2**:
- Autenticación con JWT/OAuth2 validada en el API Gateway.
- TLS 1.3 de extremo a extremo y WAF/CDN en el borde (Cloudflare).
- Cifrado en reposo AES-256 para datos sensibles.
- Rate limiting y bloqueo de IP por 15 minutos ante peticiones maliciosas, con registro de log.
- Minimización de datos personales y de ubicación según Habeas Data.

## Consecuencias

**Positivas**
- Un solo punto de control y auditoría; los microservicios internos quedan detrás del gateway.
- Menor superficie de ataque y costo bajo gracias al WAF gestionado.

**Negativas / costos**
- El gateway es un punto único de falla y debe escalar con el tráfico.
- Los tokens expiran: si el productor está offline, la sincronización devolverá 401 `AUTH_TOKEN_EXPIRED`. La PWA debe conservar la cola local intacta y pedir reautenticación sin perder datos.
- La verificación de identidad rural (KYC ligero, R-05) queda como riesgo residual fuera de esta decisión.

## Verificación

Análisis estático (SonarQube) y pruebas de penetración OWASP Top 10; prueba de que una petición con JWT manipulado es rechazada y registrada.

## Diagramas relacionados

- [Contenedores C4](../diagrams/c4-contenedores.puml)
- [Secuencia: sincronización offline (rama 401)](../diagrams/secuencia-proceso-clave.puml)
- [Despliegue](../diagrams/despliegue.puml)
