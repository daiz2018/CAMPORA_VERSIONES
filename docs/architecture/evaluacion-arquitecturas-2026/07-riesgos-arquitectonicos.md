
# CAMPORA - Riesgos y compromisos arquitectónicos

| Arquitectura | Riesgo | Impacto | Medida de mitigación |
|---|---|---|---|
| Capas | Los cambios en las reglas comerciales pueden afectar varias capas. | Alto | Definir interfaces y responsabilidades claras. |
| MVC | Los controladores pueden acumular demasiada lógica de negocio. | Alto | Separar la lógica en servicios especializados. |
| Hexagonal | Mayor complejidad inicial por puertos y adaptadores. | Medio | Implementar primero los módulos esenciales. |
| Clean Architecture | Mayor cantidad de interfaces, clases y estructura. | Alto | Aplicarla gradualmente, evitando abstracciones innecesarias. |
| Todas | Fallos de conectividad durante la sincronización de ofertas rurales. | Alto | Utilizar IndexedDB, cola local y sincronización con control de duplicados. |
| Todas | Errores o indisponibilidad de pasarelas de pago externas. | Alto | Implementar reintentos controlados, webhooks e idempotencia. |

## Análisis preliminar

Para CAMPORA, la arquitectura hexagonal presenta un equilibrio favorable entre mantenibilidad, facilidad de pruebas e integración con servicios externos.

Su principal desventaja es la complejidad inicial, especialmente para un equipo pequeño.

Esta conclusión es preliminar y deberá contrastarse con la matriz ponderada antes de tomar la decisión definitiva.
