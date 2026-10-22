
# CAMPORA - Reflexión arquitectónica

## 1. ¿Qué arquitectura facilita más el mantenimiento?

La arquitectura hexagonal, porque separa las reglas de negocio de las tecnologías externas. Esto permite modificar las pasarelas de pago o la base de datos sin alterar directamente la lógica comercial de CAMPORA.

## 2. ¿Cuál resulta más sencilla de implementar?

La arquitectura en capas, porque organiza el sistema en niveles con responsabilidades definidas. Requiere menos estructura inicial que las arquitecturas hexagonal y Clean.

## 3. ¿Cuál permite realizar pruebas con mayor facilidad?

Las arquitecturas hexagonal y Clean, porque permiten probar las reglas comerciales de forma independiente de PostgreSQL, Redis y otros servicios externos.

## 4. ¿Qué arquitectura responde mejor a las necesidades de CAMPORA?

La arquitectura hexagonal, debido a las integraciones externas, la sincronización de ofertas rurales y la necesidad de mantener independientes las reglas comerciales.

## 5. ¿Qué compromiso implica seleccionar esa arquitectura?

Aceptar una mayor complejidad inicial a cambio de facilitar el mantenimiento, las pruebas y la sustitución de servicios externos. Para controlar este riesgo, CAMPORA puede implementarse inicialmente como un monolito modular.
