
# ADR-006: Selección de arquitectura para CAMPORA

**Estado:** Propuesto  
**Fecha:** 21 de octubre de 2026

## 1. Contexto

CAMPORA es una plataforma de comercio bidireccional entre productores rurales, compradores urbanos y proveedores de insumos agropecuarios.

El sistema necesita gestionar ofertas, inventarios, pedidos, pagos externos y sincronización de información cuando se restablece la conectividad.

Se evaluaron cuatro alternativas:
- Arquitectura en capas.
- Modelo-Vista-Controlador (MVC).
- Arquitectura hexagonal.
- Clean Architecture.

## 2. Decisión propuesta

Seleccionar la arquitectura hexagonal para el núcleo de negocio de CAMPORA, implementada inicialmente como un monolito modular.

La decisión deberá ratificarse mediante la matriz comparativa ponderada.

## 3. Justificación

La arquitectura hexagonal permite:

- Separar las reglas comerciales de las tecnologías utilizadas.
- Integrar pasarelas de pago mediante adaptadores.
- Facilitar pruebas sin depender de servicios externos.
- Modificar mecanismos de persistencia sin afectar directamente el dominio.
- Incorporar la sincronización offline mediante interfaces específicas.

## 4. Consecuencias positivas

- Mayor mantenibilidad.
- Menor acoplamiento tecnológico.
- Mejor capacidad de pruebas.
- Flexibilidad para integrar servicios externos.

## 5. Consecuencias negativas

- Mayor complejidad inicial.
- Necesidad de definir puertos y adaptadores.
- Curva de aprendizaje para el equipo.

## 6. Estrategia de implementación

Comenzar con módulos de usuarios, ofertas, pedidos y pagos.

Mantener las reglas comerciales dentro del núcleo de negocio y conectar PostgreSQL, pasarelas de pago y otros servicios mediante adaptadores.

Conservar la PWA como interfaz de usuario con almacenamiento local y sincronización diferida.

## 7. Estado de la decisión

Esta propuesta se confirmará después de revisar los resultados de la matriz ponderada y contrastarla con las decisiones arquitectónicas anteriores del equipo.

