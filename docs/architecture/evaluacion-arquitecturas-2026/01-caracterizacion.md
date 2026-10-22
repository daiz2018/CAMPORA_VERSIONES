
# Fase 1. Caracterización arquitectónica de CAMPORA

## 1. Nombre y problema
CAMPORA es una plataforma de conexión comercial entre el campo y la ciudad. Busca reducir las dificultades de comercialización de productos agrícolas y facilitar el acceso de productores rurales a insumos agropecuarios.

## 2. Objetivo principal
Facilitar la comercialización de productos agrícolas e insumos mediante una plataforma digital que permita gestionar ofertas, pedidos, pagos y operaciones relacionadas.

## 3. Actores
- Productor rural.
- Comprador urbano.
- Proveedor de insumos.
- Administrador o auditor.

## 4. Requerimientos funcionales prioritarios
- RF01. Registrar y autenticar usuarios.
- RF02. Publicar y administrar ofertas agrícolas.
- RF03. Buscar productos y consultar disponibilidad.
- RF04. Crear y gestionar pedidos.
- RF05. Gestionar pagos mediante pasarelas externas.
- RF06. Registrar ofertas sin conexión y sincronizarlas posteriormente.

## 5. Requerimientos no funcionales
- RNF01. Disponibilidad: permitir el registro local de ofertas sin internet.
- RNF02. Seguridad: proteger los datos personales y las transacciones.
- RNF03. Integridad: evitar duplicaciones e inconsistencias en pedidos y pagos.
- RNF04. Mantenibilidad: facilitar cambios en los módulos y las integraciones.
- RNF05. Rendimiento: permitir búsquedas eficientes de productos.

## 6. Restricciones y decisiones documentadas
- Equipo de desarrollo de tres integrantes.
- Conectividad rural limitada o intermitente.
- PWA con almacenamiento local mediante IndexedDB.
- PostgreSQL y Redis como tecnologías seleccionadas previamente.
- Integración prevista con pasarelas de pago.
- Validación fitosanitaria pendiente de confirmar.

Nota: Las metas de presupuesto, tiempo y rendimiento de los documentos anteriores deben verificarse antes de tratarlas como restricciones definitivas.

## 7. Vista lógica existente
El diseño anterior contempla los siguientes componentes:

- AuthComponent: usuarios y autenticación.
- OfferComponent: ofertas y sincronización.
- MatchingEngine: búsquedas y emparejamiento.
- OrderManager: gestión de pedidos.
- ICAValidator: validación de insumos.
- PaymentAdapter: integración con pagos.

Diagrama de referencia: ../docs/architecture/diagrams/c4-componentes.puml

Esta arquitectura es el punto de partida para comparar las cuatro alternativas solicitadas.
