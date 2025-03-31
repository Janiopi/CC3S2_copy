### Actividad del Código a la producción: Infraestructura, contenedores, despliegue y observabilidad

> **Tarea teórica:**
>
> - Investigar una herramienta de IaC (p. ej. Terraform) y describir cómo organiza sus módulos.
> - Proponer la estructura de archivos y directorios para un proyecto hipotético que incluya tres módulos: `network`, `database` y `application`. Justificar la jerarquía elegida.

Puppet es una herramienta de Iac (Infraestructure as Code) que permite automatizar la configuración y gestión de infraestrucuras mediante código declarativo ( El estado deseado del sistema, no los pasos a sseguir).

¿Cómo se organizan los módulos en Puppet?
En Puppet, los módulos son unidades reutilizables de código que gestionan un conjunto específico de recursos. La estructura de los módulos sigue una convención estándar, lo cual facilita su uso, mantenimiento y distribución

```
module_name/
├── manifests/
│ └── init.pp
├── files/
├── templates/
├── lib/
├── facts.d/
├── examples/
└── README.md
```

### Descripción de carpetas:

- `manifests/`: contiene archivos `.pp` (Puppet DSL). `init.pp` es el archivo principal que define la clase principal del módulo.
- `files/`: archivos estáticos que se copian directamente a los nodos.
- `templates/`: archivos `.erb` (Embedded Ruby) usados para configuraciones dinámicas.
- `lib/`: contiene funciones o tipos personalizados en Ruby.
- `facts.d/`: para definir _custom facts_.
- `examples/`: ejemplos de uso del módulo.
- `README.md`: documentación del módulo.

## Propuesta de estructura de proyecto con tres módulos

Supongamos un proyecto que maneja tres componentes principales de infraestructura:

- `network`: configuración de red (firewalls, interfaces, rutas).
- `database`: instalación y configuración de bases de datos.
- `application`: despliegue de aplicaciones y sus dependencias.

### Estructura propuesta del proyecto:

```
project-iac/
├── environment.conf
├── site.pp
├── hiera.yaml
├── data/
│   └── common.yaml
└── modules/
    ├── network/
    │   ├── manifests/
    │   │   ├── init.pp
    │   │   └── firewall.pp
    │   ├── templates/
    │   └── README.md
    ├── database/
    │   ├── manifests/
    │   │   ├── init.pp
    │   │   └── users.pp
    │   ├── templates/
    │   └── README.md
    └── application/
        ├── manifests/
        │   ├── init.pp
        │   └── deploy.pp
        ├── files/
        ├── templates/
        └── README.md
```

---

## Justificación de la jerarquía propuesta

- **Modularidad**: facilita el mantenimiento y la reutilización.
- **Separación de responsabilidades**: cada módulo encapsula una parte lógica del sistema.
- **Organización estándar**: compatible con las buenas prácticas de Puppet y Puppet Forge.
- **Uso de Hiera**: facilita la externalización de configuraciones por entorno (`data/` y `hiera.yaml`).
- **Documentación por módulo**: `README.md` permite entender rápidamente el propósito y uso de cada módulo.

---

## Referencias

1. **Puppet Official Documentation – What Is Puppet**  
   [https://www.puppet.com/docs/puppet/8/what_is_puppet](https://www.puppet.com/docs/puppet/8/what_is_puppet)

2. **Puppet Official Documentation – Module fundamentals**  
   [https://puppet.com/docs/puppet/latest/modules_fundamentals.html](https://puppet.com/docs/puppet/latest/modules_fundamentals.html)

---

> **Tarea teórica:**
>
> - Describir un flujo simple de despliegue donde un desarrollador hace un cambio en el código, se construye una nueva imagen Docker y se actualiza un Deployment de Kubernetes.
> - Explicar las ventajas de usar Kubernetes para escalar una aplicación en un evento de alto tráfico.

1. **Desarrollador realiza un cambio en el código fuente.**  
   El cambio puede estar en el backend, frontend u otro componente de la aplicación.

2. **Se construye una nueva imagen Docker.**
   ```bash
   docker build -t mi-app:1.1 .
   ```
3. **Se inicia el nuevo contenedor.**
   ```bash
   docker run -d -p [IP_HOST]:[PUERTO_HOST]:[PUERTO_CONTENEDOR]
   ```

- -d: Corre el contenedor en segundo plano
- -p: Mapeo de puertos entre el contenedor y el host

  - IP_HOST: 127.0.0.1
    Solo se podrá acceder desde localhost. No se expone públicamente.

  - PUERTO_HOST: 3000
    El puerto que usas desde el navegador o una herramienta cliente (por ejemplo: http://localhost:3000).

  - PUERTO_CONTENEDOR: 3000
    El puerto dentro del contenedor en el que corre la aplicación.

4. **Verificar que los contenedores en ejecución**
   ```bash
   docker ps
   ```
5. **En caso que otro contenedor este ocupado el puerto**
   ```bash
   docker stop <the-container-id>
   ```
6. **Si ya no lo necesitamos, podemos removerlo**

```bash
   docker rm <the-container-id>
```
