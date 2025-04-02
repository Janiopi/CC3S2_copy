### Actividad del Código a la producción: Infraestructura, contenedores, despliegue y observabilidad

> **Tarea teórica:**
>
> - Investigar una herramienta de IaC (p. ej. Terraform) y describir cómo organiza sus módulos.
> - Proponer la estructura de archivos y directorios para un proyecto hipotético que incluya tres módulos: `network`, `database` y `application`. Justificar la jerarquía elegida.

## Puppet

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

## Flujo simple al hacer un cambio en el código y construir una nueva Docker image

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

## Actualizar deployment en Kubernetes

1. **Build y push de la nueva imagen**

2. **Actualizar el YAML**
   Cuando ya tengamos la nueva imagen tenemos que actualizar el YAML, el cual describe el estado del deployment, incluyendo la imagen a utilizar

```
  yaml
  spec:
   template:
    spec:
     containers:
      - name: my-app
       image: myregistry.com/my-app:latest
```

Este archivo YAML define una configuración típicamente usada para el despliegue de Kubernetes. A continuación se detalla su estructura:

a. **spec**: Este es el campo de nivel superior. Define las especificaciones para el estado deseado del objeto en la API de Kubernetes.
b. **template**: Este campo especifica la plantilla para el pod que será creado. Define la configuración del pod, incluyendo el o los contenedores que se ejecutarán.

c. **spec (dentro de template)**: Este campo, ubicado dentro de `template`, describe la configuración del pod. Especifica cómo se ejecutará el pod, incluyendo los contenedores dentro de él.

d. **containers**: Esta lista contiene las especificaciones de los contenedores que serán desplegados en el pod. En este caso:

- **name**: El nombre del contenedor, que en este caso es `my-app`.

- **image**: La imagen Docker que se usará para el contenedor. Aquí está configurada como `myregistry.com/my-app:latest`, lo que significa que la imagen está alojada en un registro llamado `myregistry.com` y está etiquetada con `latest`.

3.  **Aplicar los cambios**

```
kubectl apply -f deployment.yaml
```

4. **Monitorear el deployment**

```
kubectl rollout status deployment/my-app
```

Este comando brindará información acerca del estado actual del deployment , incluyendo el número de replicas disponibles y el estado de la actualización

5- **Rollback (en caso de errores)**

```
kubectl rollout undo deployment/my-app
```

Si la update introduce comportamientos inesperados o errores, Kubernetes permite "revertir" los cambios regresando a la versión anterior

## Referencias

1. **Docker Official Documentation – Update the application**  
   [https://docs.docker.com/get-started/workshop/03_updating_app/](https://docs.docker.com/get-started/workshop/03_updating_app/)

2. **Ultimate Guide On Kubernetes Basics (With Step-by-Step Guide)**
   [https://zeet.co/blog/kubernetes-basics](https://zeet.co/blog/kubernetes-basics)

3. **Simple How-To: Kubernetes Update Deployment With New Image**  
   [https://zeet.co/blog/kubernetes-update-deployment-with-new-image](https://zeet.co/blog/kubernetes-update-deployment-with-new-image)

---

> **Tarea teórica:**
>
> - Investigar y describir cómo Prometheus y Grafana se integran con Kubernetes para monitorear los contenedores y el cluster.
> - Proponer un set de métricas y alertas mínimas para una aplicación web (por ejemplo, latencia de peticiones, uso de CPU/memoria, tasa de errores).

En el mundo de la contenerización y microservicios, el monitoreo es indispensable para mantener la salud, el rendimiento y la confiabilidad de su infraestructura.

## Prometheus y Graphana

Prometheus es un conjunto de herramientas de código abierto para la monitorización y alertas de sistemas, desarrollado originalmente en SoundCloud. Destaca por su monitorización de métricas y por ofrecer potentes funciones de consulta para datos de series temporales. Grafana complementa Prometheus ofreciendo funciones de visualización mediante paneles y gráficos personalizables.

## Integración con Kubernetes

- **Helm**: Helm es un gestor de paquetes para Kubernetes que facilita la instalación, configuración y gestión de aplicaciones dentro de clústeres de Kubernetes

## Paso 1: Añadir el repositorio de Helm

Primero, agrega el repositorio de Helm de la comunidad de **Prometheus** y actualízalo:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

## Paso 2: Instalar Prometheus y Grafana

Crea un archivo custom-values.yaml para personalizar la instalación del chart de Helm. Este archivo configurará Prometheus y Grafana para ser expuestos a través de NodePorts.

```
# custom-values.yaml
prometheus:
  service:
    type: NodePort
grafana:
  service:
    type: NodePort
```

Luego, instala (o actualiza) el kube-prometheus-stack utilizando este archivo de configuración personalizado:

```
helm upgrade --install kube-prometheus-stack prometheus-community/kube-prometheus-stack -f custom-values.yaml
```

## Paso 3: Verificar la instalación

Después de la instalación, puedes verificar que los servicios de Prometheus y Grafana se han creado y están expuestos a través de NodePort con el siguiente comando:

```
   kubectl get services
```

Deberías ver una salida similar a esta, mostrando los servicios con sus respectivos NodePorts:

```
   kube-prometheus-stack-grafana                    NodePort    10.245.30.17     <none>        80:31519/TCP
   kube-prometheus-stack-prometheus                 NodePort    10.245.151.164   <none>        9090:30090/TCP,8080:32295/TCP
```

## Paso 4: Acceder a Prometheus y Grafana

Para acceder a los tableros de Prometheus y Grafana fuera del clúster, necesitas la IP externa de cualquiera de los nodos del clúster y los NodePorts en los que se exponen los servicios.

Obtén las direcciones IP externas de tus nodos con el siguiente comando:

```bash
kubectl get nodes -o wide
```

Verás una salida similar a esta:

```bash
$ kubectl get nodes -o wide
NAME                   STATUS   ROLES    AGE   VERSION   INTERNAL-IP   EXTERNAL-IP      OS-IMAGE                         KERNEL-VERSION   CONTAINER-RUNTIME
pool-t5ss0fagn-jeb47   Ready    <none>   57d   v1.29.1   10.124.0.2    146.190.55.222   Debian GNU/Linux 12 (bookworm)   6.1.0-17-amd64   containerd://1.6.28

```

Usa la IP externa de cualquier nodo y los NodePorts para acceder a los tableros:

Prometheus: http://146.190.55.222:30090

Grafana: http://146.190.55.222:31519

- **NodePort**: Un NodePort es un tipo de servicio en Kubernetes que expone un puerto específico en cada nodo del clúster para acceder a un servicio. Este tipo de servicio permite que las aplicaciones dentro de un clúster de Kubernetes sean accesibles desde fuera del clúster, usando la IP de cualquiera de los nodos y un puerto específico.

  **Cómo funciona un NodePort**:
  Puertos Expuestos: Al crear un servicio de tipo NodePort, Kubernetes asigna un puerto dentro de un rango específico (generalmente entre 30000 y 32767) en cada nodo del clúster.

  **Acceso Externo**: Cualquier solicitud dirigida a la IP externa de cualquier nodo en ese puerto será redirigida al servicio que se está ejecutando dentro del clúster.

  **Redirección de Tráfico**: Kubernetes gestiona automáticamente la redirección del tráfico desde el puerto del nodo hacia el puerto del servicio, permitiendo que los clientes externos se conecten.

  **Ejemplo de uso:**
  Si tienes un servicio de tipo NodePort en Kubernetes expuesto en el puerto 30090, y el nodo tiene una IP externa de 146.190.55.222, entonces podrías acceder al servicio desde fuera del clúster usando:

  ```
     http://146.190.55.222:30090
  ```

## Referencias

1. **A Hands-On Guide to Kubernetes Monitoring Using Prometheus & Grafana**  
   [https://medium.com/@muppedaanvesh/a-hands-on-guide-to-kubernetes-monitoring-using-prometheus-grafana-%EF%B8%8F-b0e00b1ae039](https://medium.com/@muppedaanvesh/a-hands-on-guide-to-kubernetes-monitoring-using-prometheus-grafana-%EF%B8%8F-b0e00b1ae039)

2. **Helm Docs**
   [https://helm.sh/](https://helm.sh/)

3. **Prometheus Docs**  
   [https://prometheus.io/docs/introduction/overview/](https://prometheus.io/docs/introduction/overview/)

4. **Grafana Docs**
   [https://grafana.com/](https://grafana.com/)

---

> **Tarea teórica:**
>
> - Explicar la diferencia entre entrega continua (continuous delivery) y despliegue continuo (continuous deployment).
> - Describir la relevancia de implementar pruebas automáticas (unitarias, de integración, de seguridad) dentro del pipeline.

## CD/CI

CI/CD, siglas de integración continua y entrega/implementación continua, tiene como objetivo optimizar y acelerar el ciclo de vida del desarrollo de software.

La integración continua (CI) se refiere a la práctica de integrar de forma automática y frecuente los cambios de código en un repositorio de código fuente compartido. La entrega y/o implementación continua (CD) es un proceso de dos partes que se refiere a la integración, las pruebas y la entrega de los cambios de código. La entrega continua no llega a la implementación automática en producción, mientras que la implementación continua libera automáticamente las actualizaciones en el entorno de producción.

## Importancia de las pruebas automáticas

- **Pruebas unitarias**: Las pruebas unitarias se enfocan en probar las unidades más pequeñas de un sistema, como funciones o métodos individuales. Se diseñan para verificar que cada pieza del código funcione correctamente de manera aislada, sin interferencia de otros módulos o dependencias.

- **Pruebas de integración**: Las pruebas de integración se centran en verificar que diferentes módulos del sistema trabajen juntos de la manera esperada. A menudo se prueban las interacciones entre servicios, bases de datos, interfaces de usuario y otras dependencias externas.

-**Pruebas de seguridad**: Las pruebas de seguridad tienen como objetivo identificar vulnerabilidades dentro de un sistema, como brechas en la autenticación, manejo de datos sensibles o acceso no autorizado a servicios.

## Relevancia en el pipeline

- Detección temprana de errores: Las pruebas automáticas previenen que defectos se propaguen a fases posteriores del desarrollo.

- Mejor calidad del software: Con pruebas constantes y rápidas, se garantiza que el sistema siga siendo confiable y robusto, incluso con cambios frecuentes.

- Reducción del riesgo en producción: Al implementar pruebas automáticas, se reduce la probabilidad de que código defectuoso sea liberado en producción, lo que disminuye el riesgo de fallos críticos en el software.

## Referencias

1. **What is CI/CD?- RedHat**  
   [https://www.redhat.com/en/topics/devops/what-is-ci-cd](https://www.redhat.com/en/topics/devops/what-is-ci-cd)

# Actividad grupal

https://www.canva.com/design/DAGjPPPVCSM/i92gy5ULb9DZ1nKKhEkafg/edit
