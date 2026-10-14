# ADR-0005: Validación fitosanitaria ICA, síncrona vs. asíncrona

- **Estado:** Propuesto
- **Fecha:** 2026-10-13
- **Decisores:** Lorena Barragán, Diego Ñanguma, Jonathan Diaz (pendiente de validación con el docente)
- **Relacionado con:** [ADR-0001](0001-patron-arquitectonico.md), [ADR-0002](0002-sistema-de-persistencia.md)

## Contexto y planteamiento del problema

El catálogo de insumos debe impedir la publicación de fitosanitarios no homologados y registrar cada intento en un log inmutable (ASR-05, ESC-08, RES-06). Nuestros propios documentos son inconsistentes:

- El **dossier** (sección 6.2) plantea validación **asíncrona**: el producto queda "En revisión normativa" mientras un worker valida en menos de 5 s.
- El **C4, Contrato 2** define una validación **síncrona**: `POST /api/v1/inputs/validate-ica` responde 422 de inmediato.

Además, el diseño asume una "REST API" del ICA/INVIMA, pero **aún no se ha confirmado** que exista una API pública utilizable ni su disponibilidad y latencia.

## Factores de decisión

- Cumplimiento legal: nunca publicar un producto prohibido (categorías IA/IB).
- Experiencia del proveedor: respuesta inmediata cuando sea posible.
- Dependencia de un servicio externo cuya disponibilidad no controlamos.
- Trazabilidad: todo intento debe quedar en un log inmutable.

## Opciones consideradas

- **A. Síncrona:** el gateway espera la respuesta del ICA y responde 200 o 422.
- **B. Asíncrona:** se publica en estado `EN_REVISION_NORMATIVA` y un worker valida vía eventos.
- **C. Híbrida (propuesta):** validación local inmediata de formato y categorías prohibidas IA/IB con rechazo 422; la verificación del registro contra el ICA se hace de forma asíncrona.

## Decisión propuesta

Se propone la **opción C**, sujeta a confirmar los puntos pendientes. Ningún producto pasa a visible en el catálogo público mientras no esté aprobado.

## Consecuencias esperadas

**Positivas**
- Rechazo inmediato de lo evidentemente prohibido.
- El catálogo no depende de la disponibilidad del ICA para responder.
- Reutiliza el Event Bus de [ADR-0001](0001-patron-arquitectonico.md).

**Negativas / costos**
- Dos rutas de validación que mantener y probar.
- Estado intermedio visible para el proveedor y notificación posterior.
- Si el proveedor registra insumos sin conexión, la validación ocurre tras la sincronización de [ADR-0002](0002-sistema-de-persistencia.md).

## Preguntas abiertas (para pasar a Aceptado)

1. ¿Existe una API o fuente de datos oficial del ICA/INVIMA consultable? Si no, ¿se usará un listado cargado y actualizado periódicamente?
2. ¿Cuál es la latencia real de la consulta? Esto decide si el objetivo de 5 s es realista.
3. ¿Qué ocurre si el servicio externo no responde (reintentos, tiempo máximo, estado resultante)?
4. Unificar el dossier y el Contrato 2 con la opción elegida.

## Verificación prevista

Pruebas unitarias de dominio insertando productos sin código ICA activo y de categoría IA/IB; comprobar que el 100 % se bloquea y que cada intento queda en el log.

## Diagramas relacionados

- [Secuencia: alternativas de validación ICA](../diagrams/secuencia-validacion-ica.puml)
- [Componentes C4](../diagrams/c4-componentes.puml)
