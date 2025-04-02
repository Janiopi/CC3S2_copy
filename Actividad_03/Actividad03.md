# Cuestionario

## Motivaciones para la nube

- (a) ¿Qué problemas o limitaciones existían antes del surgimiento de la computación en la nube y cómo los solucionó la centralización de servidores en data centers?

  **Problemas previos:**

  - Alto costo de adquisición y mantenimiento de infraestructura propia.

  - Dificultad para escalar rápidamente ante incrementos de demanda.

  - Desperdicio de recursos .

  - Administración compleja y dispersa de servidores.

  **Soluciones:**

  - Facilitó economías de escala al compartir recursos.

  - Permitió elasticidad y escalabilidad rápida según la demanda.

  - Redujo costos operativos pagando solo por el consumo real.

  - Simplificó la administración desde un punto centralizado.

- (b) ¿Por qué se habla de “The Power Wall” y cómo influyó la aparición de procesadores multi-core en la evolución hacia la nube?

  - "The Power Wall" hace referencia al límite físico de calor generado y energía consumida, que impide aumentar más la frecuencia de los procesadores.

  - Esto llevó al surgimiento de procesadores multi-core, que permiten ejecutar múltiples tareas en paralelo en vez de aumentar frecuencias.

## Clusters y load balancing

- (a) Explica cómo la necesidad de atender grandes volúmenes de tráfico en sitios web condujo a la adopción de clústeres y balanceadores de carga.
  - Los sitios web con alto tráfico saturaban un único servidor, causando caídas o lentitud. Los clústeres permiten repartir la carga entre varios servidores mediante los balanceadores de carga
- (b) Describe un ejemplo práctico de cómo un desarrollador de software puede beneficiarse del uso de load balancers para una aplicación web.

  - Un desarrollador tiene una aplicación web que recibe tráfico elevado durante eventos especiales (Black Friday, promociones).

  - Al utilizar load balancing, múltiples instancias de la aplicación gestionan simultáneamente las solicitudes.

  - Esto evita caídas del servicio, mejora la experiencia del usuario y simplifica la gestión técnica.

## Elastic Computing

- (a) Define con tus propias palabras el concepto de Elastic Computing.
  - Elastic Computing es la capacidad de aumentar o disminuir rápidamente recursos computacionales (CPU, memoria, almacenamiento, red) según la demanda, pagando únicamente por lo que se usa.
- (b) ¿Por qué la virtualización es una pieza clave para la elasticidad en la nube?

  - La virtualización abstrae recursos físicos y facilita ajustar rápidamente la cantidad de recursos asignados. Sin virtualización sería complejo y lento adaptar la infraestructura física real, impidiendo elasticidad.

- (c) Menciona un escenario donde, desde la perspectiva de desarrollo, sería muy difícil escalar la infraestructura sin un entorno elástico.

  - Aplicaciones que experimentan grandes fluctuaciones en el tráfico, como tiendas online durante eventos de descuentos (por ejemplo, Black Friday).

  - Sin elasticidad, habría que sobredimensionar infraestructura para los picos más altos (muy costoso), o arriesgarse a caídas del servicio.

## Modelos de servicios (Iaas,Paas,SaaS,DaaS)

- (a) Diferencia cada uno de estos modelos. ¿En qué casos un desarrollador optaría por PaaS en lugar de IaaS?

| Modelo   | Descripción                                                                                                         | Qué gestiona el desarrollador          |
| -------- | ------------------------------------------------------------------------------------------------------------------- | -------------------------------------- |
| **IaaS** | Infraestructura virtualizada como servidores, almacenamiento y redes.                                               | Desde sistema operativo hacia arriba.  |
| **PaaS** | Plataforma que incluye sistema operativo, entorno de ejecución y herramientas listas para desarrollar aplicaciones. | Solo aplicaciones y datos.             |
| **SaaS** | Software listo para usar entregado a través de Internet.                                                            | Solo utilización directa del software. |
| **DaaS** | Acceso a datos gestionados como servicio bajo demanda.                                                              | Consumo de datos únicamente.           |

**Un desarrollador optaría por PaaS en lugar de IaaS** cuando desea centrarse exclusivamente en el desarrollo de aplicaciones sin preocuparse por configurar y administrar sistemas operativos, redes o hardware.

- (b) Enumera tres ejemplos concretos de proveedores o herramientas que correspondan a cada tipo de servicio.

| Tipo     | Ejemplos                                                  |
| -------- | --------------------------------------------------------- |
| **IaaS** | Amazon EC2, Azure Virtual Machines, Google Compute Engine |
| **PaaS** | Heroku, Google App Engine, Azure App Service              |
| **SaaS** | Gmail, Salesforce, Microsoft Office 365                   |
| **DaaS** | Snowflake, Azure Data Share, AWS Data Exchange            |

## Tipos de nubes (Pública,Privada,Híbrida,Multi-Cloud)

- (a) ¿Cuáles son las ventajas de implementar una nube privada para una organización grande?

  - Mayor seguridad y privacidad en datos críticos.
  - Control absoluto sobre infraestructura, políticas y configuraciones.
  - Facilidad para cumplir requisitos regulatorios específicos.
  - Rendimiento dedicado y predecible al no compartir recursos con terceros.

- (b) ¿Por qué una empresa podría verse afectada por el “provider lock-in”?

  - Ocurre cuando una empresa depende demasiado de servicios específicos o propietarios ofrecidos por un único proveedor de nube.
  - Esto limita la posibilidad de migrar fácilmente hacia otros proveedores, incrementa costos operativos y reduce la flexibilidad ante cambios o mejores oportunidades tecnológicas en el mercado.

- (c) ¿Qué rol juegan los “hyperscalers” en el ecosistema de la nube?

  - Los “hyperscalers” (AWS, Google Cloud, Azure, etc.) son proveedores globales de nube a gran escala, con enormes infraestructuras distribuidas.
  - Permiten a las empresas escalar sus aplicaciones de manera inmediata y globalmente, garantizando disponibilidad y rendimiento.
  - Impulsan la innovación tecnológica, estableciendo estándares y desarrollando servicios avanzados en la nube que otras compañías adoptan y aprovechan.
