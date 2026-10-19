# Sazón Urbano: diseño arquitectónico del sistema

Propuesta de arquitectura de software para **Sazón Urbano**, un restaurante con servicio en mesa, para llevar y a domicilio. Es la continuación del análisis de negocio de la Unidad 2 (BRD, historias de usuario, glosario y BPMN) y define **dónde vive cada regla de negocio y cómo colaboran las partes del sistema antes de escribir código**.

> **Actividad ACT-F03. Diseño arquitectural del sistema**
> Curso: Componentes de Lógica de Negocios · Profesor: Miguel Ángel Gómez Marroquín
> Facultad de Ingeniería Mecánica y Eléctrica (FIME), UANL · Equipo 3 · Octubre de 2026

## Equipo

- Landon Alexander Bautista Hernandez
- Karla Judith Baez Villanueva
- Alexa Daiana Amaya Torres

## Resumen de la propuesta

| Elemento | Decisión |
|---|---|
| Proceso principal | Gestión del pedido, desde que el cliente ordena por cualquier canal hasta que se entrega o se cancela |
| Patrón de lógica de negocio | **Domain Model** para los procesos centrales (pedido, inventario, pago, promoción, lealtad); **Table Module** para reportes y consultas agregadas |
| Aggregates | Pedido, Promoción, Inventario, Entrega, Repartidor, Pago y Cliente |
| Domain services | AplicadorDePromocion, AsignadorDeEntregas y ConciliadorDeCaja |
| Service Layer | Tres Application Services (Pedido, Pago y Entrega), un método por caso de uso |
| Persistencia | Siete repositorios con interfaz en el dominio y una Unit of Work por solicitud |
| CQRS básico | Nueve commands y cuatro queries con DTO, sobre una sola base de datos |
| Capas | Dominio, aplicación e infraestructura, con dependencias hacia interfaces |
| Decisiones | Tres ADR (patrón, Repository y Unit of Work, CQRS) |

## Casos de uso analizados

1. **CU-01** Registrar pedido
2. **CU-02** Consultar inventario
3. **CU-03** Cancelar pedido
4. **CU-04** Registrar pago
5. **CU-05** Registrar merma

Cada caso incluye actor, objetivo, información de entrada, reglas relacionadas, resultado esperado y camino de rechazo.

## Estructura del repositorio

```
.
├── Diagramas/
│   ├── Clases.png / Clases.puml                          Diagrama de clases por capas
│   ├── Dominio.png                                       Mapa de dominio (aggregates)
│   ├── registrar un pago en efectivo.png / .puml         Secuencia: flujo principal
│   └── secuencia-cancelar-pedido.png / .puml             Secuencia: regla con excepción y alternativas
├── Documentos/
│   ├── Tablas comparativas.pdf                           Selección del patrón de lógica de negocio
│   ├── Mapa de dominio.pdf                               Aggregates, value objects, servicios y eventos
│   ├── Service layer.pdf                                 Application Services
│   ├── Repository y Unit of Work.pdf                     Repositorios y transacciones
│   ├── Commands y Queries (CQRS básico).pdf              Commands, queries y DTO
│   ├── Revisión con SOLID.pdf                            Hallazgos y correcciones
│   ├── ADR.pdf                                           Architecture Decision Records
│   ├── Validación de propuesta.pdf                       Recorrido de casos de uso y validaciones
│   └── Bitácora de IA.pdf                                Uso de IA, errores detectados y decisiones
└── README.md
```

## Cómo revisar el repositorio

Las etapas siguen el orden de la actividad. Una lectura de principio a fin:

| # | Etapa | Dónde verla |
|---|---|---|
| 1 | Preparación del caso (casos de uso y hallazgos) | Reporte completo |
| 2 | Selección del patrón de lógica de negocio | `Documentos/Tablas comparativas.pdf` |
| 3 | Mapa de dominio | `Documentos/Mapa de dominio.pdf` · `Diagramas/Dominio.png` |
| 4 | Service Layer | `Documentos/Service layer.pdf` |
| 5 | Repository y Unit of Work | `Documentos/Repository y Unit of Work.pdf` |
| 6 | Commands y Queries (CQRS básico) | `Documentos/Commands y Queries (CQRS básico).pdf` |
| 7 | Revisión con SOLID | `Documentos/Revisión con SOLID.pdf` |
| 8 | Diagramas UML y ADR | `Diagramas/` · `Documentos/ADR.pdf` |
| 9 | Validación de la propuesta | `Documentos/Validación de propuesta.pdf` |
| — | Uso de IA | `Documentos/Bitácora de IA.pdf` |

## Diagramas

Los diagramas están hechos con **PlantUML** (clases y secuencias) y **draw.io** (mapa de dominio). Cada diagrama tiene su código fuente `.puml` junto a la imagen exportada en PNG.

- **Diagrama de clases:** separa las tres capas por color (dominio en verde, aplicación en azul, infraestructura en naranja). Las flechas apuntan a interfaces, no a implementaciones.
- **Secuencia de pago en efectivo:** flujo principal con permiso, Unit of Work, validación en el dominio y publicación del evento después de confirmar.
- **Secuencia de cancelación de pedido:** tres caminos según el estado de la preparación y la autorización del Encargado.

Para regenerar un diagrama, pega el contenido del archivo `.puml` en [editor.plantuml.com](https://editor.plantuml.com) o ábrelo con la extensión de PlantUML de tu editor.

## Principios de diseño

- **Una regla, un lugar:** las reglas de negocio viven en el dominio (Pedido, Promoción, Inventario, Pago, Entrega). Los permisos por rol viven en la capa de aplicación.
- **Aggregates pequeños:** se conocen solo por identificador y se comunican con eventos de dominio.
- **Servicios delgados:** el Service Layer orquesta y enruta, y cada paso vive en su handler.
- **Dominio sin infraestructura:** repositorios, Unit of Work, publicador, notificador y control de acceso son interfaces; las implementaciones viven en infraestructura.
- **Lectura separada de escritura:** las queries devuelven DTO y nunca exponen el aggregate.

## Supuestos y límites

- Es una propuesta de diseño: no incluye una aplicación en producción ni base de datos real. Las implementaciones de persistencia son en memoria.
- No se implementó control de concurrencia, para mantener el diseño simple; el riesgo está documentado en el ADR de Repository y Unit of Work.
- Las reglas de negocio (RN) están numeradas por el equipo porque el BRD no las numera.
- Los supuestos pendientes de validar están al final de `Validación de propuesta.pdf`.

## Uso de inteligencia artificial

Se usó Claude (Anthropic) como apoyo para proponer, comparar y revisar. Cada propuesta se contrastó con el BRD y con la guía de la actividad antes de aceptarla. Los errores detectados, los supuestos y las decisiones del equipo están en `Documentos/Bitácora de IA.pdf`.

## Fuentes

- `03_Sazón_Urbano.docx`: material de la Unidad 2 (BRD, historias de usuario, glosario y BPMN), proporcionado en la plataforma Teams.
- Guía de la actividad ACT-F03, proporcionada en la plataforma Teams.
