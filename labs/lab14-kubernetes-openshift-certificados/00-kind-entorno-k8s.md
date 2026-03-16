# Laboratorio 14 — Certificados en Kubernetes y OpenShift

## Preparar un clúster Kubernetes local con kind

Para poder realizar los ejercicios de este laboratorio necesitas un clúster Kubernetes accesible con `kubectl`.
Si no dispones de uno, puedes crear un clúster local usando **kind** (Kubernetes in Docker).

kind crea un clúster Kubernetes dentro de contenedores Docker, lo que permite hacer prácticas sin infraestructura adicional.

---

### Paso 1 — Verificar requisitos

Comprueba que tienes **Docker** instalado y en ejecución en tu máquina (o en el entorno donde se ejecuta este curso).

```bash
docker version
```

Después, verifica si `kind` está disponible:

```bash
kind --version
```

Si el comando no existe, instala kind siguiendo la documentación oficial (`https://kind.sigs.k8s.io/`), por ejemplo descargando el binario y colocándolo en un directorio incluido en tu `PATH`.

---

### Paso 2 — Crear un clúster básico con kind

Para los ejercicios de este laboratorio basta con un clúster de un solo nodo de control.

Crea el clúster:

```bash
kind create cluster --name tls-lab
```

Comprueba que `kubectl` puede hablar con el clúster:

```bash
kubectl cluster-info
kubectl get nodes
```

Deberías ver un nodo `control-plane` en estado `Ready`.

---

### Paso 3 — (Opcional) Configuración para pruebas HTTPS desde el host

Si quieres exponer servicios HTTPS del clúster directamente por puertos del host (por ejemplo 8443), puedes usar un archivo de configuración de kind con `extraPortMappings`.

Crea un archivo `kind-config.yaml` con un control-plane que exponga los puertos 80/443 del nodo como 8080/8443 en el host:

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 80
    hostPort: 8080
    protocol: TCP
  - containerPort: 443
    hostPort: 8443
    protocol: TCP
```

Luego crea el clúster usando esa configuración:

```bash
kind create cluster --name tls-lab --config kind-config.yaml
```

Más adelante, cuando configures un Ingress con TLS en el puerto 443 dentro del clúster, podrás probarlo desde el host usando `https://localhost:8443` (ajustando los ejemplos del laboratorio según tus necesidades).

---

### Paso 4 — Usar el clúster en los ejercicios del lab14

Una vez que el clúster kind está en marcha y `kubectl` apunta a él, puedes:

* seguir el ejercicio **01-kubernetes-secrets-tls.md** para crear Secrets TLS e Ingress,
* instalar **cert-manager** en el clúster kind en **02-cert-manager.md**,
* reutilizar conceptos de Kubernetes cuando leas el ejercicio de **OpenShift** (aunque este último está orientado a un entorno OpenShift real).

Si más adelante no necesitas el clúster, elimínalo para liberar recursos:

```bash
kind delete cluster --name tls-lab
```

Con esto dispones de un entorno Kubernetes local coherente con el resto del curso para practicar certificados y TLS en contenedores.

