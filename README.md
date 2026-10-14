# Arquitectura de CAMPORA: documentación viva

CAMPORA es una plataforma de conexión comercial bidireccional entre el campo y la ciudad (VOR Technology). Este directorio es la fuente de verdad de la arquitectura: se versiona en Git junto al código y se actualiza en el mismo Pull Request que cambia el sistema.

**Equipo:** Lorena Barragán, Diego Ñanguma, Jonathan Diaz · **Docente:** Juan Camilo Rodríguez Villada · UNIMINUTO, Arquitectura de Software.

## Estructura

```
docs/architecture/
├── README.md                      # Este documento
├── adr/
│   ├── 0001-patron-arquitectonico.md
│   ├── 0002-sistema-de-persistencia.md
│   ├── 0003-estrategia-seguridad.md
│   ├── 0004-estrategia-pagos-strategy-webhooks.md
│   └── 0005-validacion-ica-sincrona-vs-asincrona.md
└── diagrams/
    ├── c4-contexto.puml
    ├── c4-contenedores.puml
    ├── c4-componentes.puml
    ├── secuencia-proceso-clave.puml
    ├── secuencia-validacion-ica.puml
    └── despliegue.puml
```

## Decisiones (ADR)

| ADR | Decisión | Estado | Diagramas |
|---|---|---|---|
| [0001](adr/0001-patron-arquitectonico.md) | Microservicios modulares orientados a eventos | Aceptado | [contenedores](diagrams/c4-contenedores.puml), [componentes](diagrams/c4-componentes.puml) |
| [0002](adr/0002-sistema-de-persistencia.md) | PostgreSQL + Redis + PWA Offline-First (IndexedDB) | Aceptado | [contenedores](diagrams/c4-contenedores.puml), [secuencia offline](diagrams/secuencia-proceso-clave.puml), [despliegue](diagrams/despliegue.puml) |
| [0003](adr/0003-estrategia-seguridad.md) | JWT/OAuth2, TLS 1.3, AES-256, WAF | Aceptado | [contenedores](diagrams/c4-contenedores.puml), [secuencia offline](diagrams/secuencia-proceso-clave.puml), [despliegue](diagrams/despliegue.puml) |
| [0004](adr/0004-estrategia-pagos-strategy-webhooks.md) | Strategy + Webhooks para pagos | Aceptado | [contexto](diagrams/c4-contexto.puml), [componentes](diagrams/c4-componentes.puml) |
| [0005](adr/0005-validacion-ica-sincrona-vs-asincrona.md) | Validación ICA: síncrona vs. asíncrona | **Propuesto** | [secuencia ICA](diagrams/secuencia-validacion-ica.puml), [componentes](diagrams/c4-componentes.puml) |

**Cómo se relacionan:** 0001 define el estilo (microservicios + eventos); 0002 define dónde viven los datos y cómo opera el cliente offline; 0003 protege ese sistema y condiciona la sincronización (token expirado offline); 0004 usa el modelo de servicios aislados de 0001; 0005 depende del bus de eventos de 0001 y de la sincronización de 0002.

## Vistas (diagramas como código)

| Diagrama | Qué responde |
|---|---|
| [c4-contexto.puml](diagrams/c4-contexto.puml) | Quién usa CAMPORA y con qué sistemas externos se integra (C4 nivel 1) |
| [c4-contenedores.puml](diagrams/c4-contenedores.puml) | Qué piezas desplegables existen y cómo se comunican (C4 nivel 2) |
| [c4-componentes.puml](diagrams/c4-componentes.puml) | Cómo se organiza internamente el backend (C4 nivel 3) |
| [secuencia-proceso-clave.puml](diagrams/secuencia-proceso-clave.puml) | Sincronización diferida de ofertas offline |
| [secuencia-validacion-ica.puml](diagrams/secuencia-validacion-ica.puml) | Alternativas de validación fitosanitaria |
| [despliegue.puml](diagrams/despliegue.puml) | Dónde corre cada pieza (PaaS, WAF, datos) |

## Trazabilidad: necesidad → decisión → diseño

| Necesidad / riesgo | Requisito | ADR | Diagrama |
|---|---|---|---|
| Productor sin señal (R-01) | ASR-01, ESC-01, RES-02 | [0002](adr/0002-sistema-de-persistencia.md) | [secuencia offline](diagrams/secuencia-proceso-clave.puml) |
| Picos de cosecha y bajo costo (R-04) | ASR-04, RES-01 | [0001](adr/0001-patron-arquitectonico.md) | [contenedores](diagrams/c4-contenedores.puml), [despliegue](diagrams/despliegue.puml) |
| Seguridad y Habeas Data (R-05) | ASR-03, ESC-03, RES-03 | [0003](adr/0003-estrategia-seguridad.md) | [despliegue](diagrams/despliegue.puml) |
| Cambiar pasarela sin tocar el dominio | ASR-07, ESC-06, RES-05 | [0004](adr/0004-estrategia-pagos-strategy-webhooks.md) | [componentes](diagrams/c4-componentes.puml) |
| Cumplimiento ICA (R-02) | ASR-05, ESC-08, RES-06 | [0005](adr/0005-validacion-ica-sincrona-vs-asincrona.md) | [secuencia ICA](diagrams/secuencia-validacion-ica.puml) |

## Sustentación técnica

### Cómo la arquitectura mitiga la deuda técnica

- **Decisiones registradas, no tácitas.** Cada decisión crítica tiene contexto, alternativas descartadas y consecuencias negativas explícitas. La deuda aceptada conscientemente (por ejemplo, Redis como cola con menor durabilidad en [ADR-0002](adr/0002-sistema-de-persistencia.md)) queda documentada con su condición de revisión, en vez de descubrirse más tarde.
- **Bajo acoplamiento.** La arquitectura hexagonal y los límites por dominio ([ADR-0001](adr/0001-patron-arquitectonico.md)) aíslan la lógica de negocio. El patrón Strategy ([ADR-0004](adr/0004-estrategia-pagos-strategy-webhooks.md)) permite cambiar de pasarela sin reescribir el núcleo.
- **Estado "Propuesto" como control.** Lo que aún no está decidido ([ADR-0005](adr/0005-validacion-ica-sincrona-vs-asincrona.md)) se declara como tal, con preguntas abiertas, para no construir sobre supuestos sin validar.
- **Documentación sincronizada con el código.** Los diagramas son texto (PlantUML) en Git: se revisan en Pull Requests, tienen historial y no quedan obsoletos en un archivo binario aparte.
- **Inconsistencias visibles.** Contradicciones entre documentos (dossier vs. contrato C4 sobre la validación ICA) se elevan a un ADR en lugar de quedar escondidas.

### Cómo facilita el onboarding

**Desarrolladores nuevos**, ruta de lectura de unos 30 minutos:
1. [Contexto](diagrams/c4-contexto.puml) → [contenedores](diagrams/c4-contenedores.puml) → [componentes](diagrams/c4-componentes.puml): de lo general a lo específico.
2. ADR 0001 a 0004 para entender el porqué de la estructura.
3. ADR 0005 para saber qué decisiones siguen abiertas y dónde se puede aportar.
4. La estructura del monorepositorio (`frontend-pwa`, `api-gateway`, `core-services`) refleja los diagramas, así que el mapa mental coincide con las carpetas.

**Asistentes de IA:** los ADR y los `.puml` son texto plano, estructurado y versionado, por lo que pueden incluirse directamente como contexto. Un asistente puede leer el estado de cada decisión, respetar las alternativas ya descartadas y distinguir lo aceptado de lo propuesto antes de generar código. Las referencias cruzadas relativas le permiten navegar de la necesidad a la decisión y al diseño.

## Convenciones

- **Nuevo ADR:** copiar el formato MADR de los existentes, numerar de forma consecutiva y enlazarlo aquí en el mismo PR.
- **Ciclo de estados:** Propuesto → Aceptado → (Obsoleto | Reemplazado por ADR-XXXX). Un ADR aceptado no se edita: se reemplaza con uno nuevo.
- **Renderizar diagramas:**
  - VS Code: extensión *PlantUML* (Alt+D para previsualizar).
  - Línea de comandos: `plantuml -tpng docs/architecture/diagrams/*.puml`.
  - Los diagramas C4 usan la librería estándar (`!include <C4/...>`) incluida en versiones recientes de PlantUML.
