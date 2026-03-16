# Laboratorio 14 — Certificados en Kubernetes, OpenShift y cert-manager

En laboratorios anteriores configuraste TLS en servicios tradicionales (NGINX, Apache, Postfix).
En entornos de **orquestación de contenedores** los certificados se gestionan de forma distinta: Secrets, Ingress, operadores y controladores como **cert-manager** automatizan la emisión y renovación.

Este laboratorio introduce el uso de certificados en **Kubernetes**, **OpenShift** y **cert-manager**, aplicando los conceptos de PKI y TLS ya vistos en el curso.

---

## Qué aprenderás en este laboratorio

Durante este laboratorio aprenderás a:

* usar **Secrets** de Kubernetes para almacenar certificados y claves privadas
* exponer servicios HTTPS mediante **Ingress** con TLS
* instalar y usar **cert-manager** para emitir y renovar certificados en el clúster
* configurar certificados en **OpenShift** (Rutas, CA de confianza, service serving certificates).

Conectarás la PKI del curso con plataformas cloud nativas.

---

## Conceptos clave

**Kubernetes**

* Los certificados y claves se guardan en **Secrets** (tipo `tls` o `Opaque`).
* El **Ingress** puede terminar TLS usando un Secret que referencia cert + key.
* Cada namespace puede tener sus propios Secrets; el control de acceso se gestiona con RBAC.

**cert-manager**

* Controlador que gestiona certificados dentro del clúster mediante recursos **Certificate**, **Issuer** y **ClusterIssuer**.
* Puede obtener certificados de Let's Encrypt o de una CA interna (por ejemplo la PKI del curso).
* Renueva automáticamente los certificados antes de su expiración.

**OpenShift**

* **Rutas** (Route) son el equivalente a Ingress; admiten TLS edge, passthrough o reencryption.
* OpenShift puede generar **service serving certificates** para comunicación interna entre servicios.
* La CA de confianza del clúster se puede ampliar inyectando certificados de tu PKI.

---

## Requisitos previos

* Haber realizado al menos los laboratorios **Lab04** (CA), **Lab05** (firmar certificados) y **Lab08** (TLS en servicios).
* Tener un clúster de Kubernetes u OpenShift disponible (minikube, kind, OpenShift Local/CRC, o clúster de prueba).
* `kubectl` (y `oc` si usas OpenShift) configurado y con acceso al clúster.

---

## Laboratorios incluidos

Este laboratorio contiene los siguientes ejercicios:

```id="lab14-exercises"
01-kubernetes-secrets-tls.md
02-cert-manager.md
03-openshift-rutas-certificados.md
```

Cada ejercicio cubre un aspecto distinto de los certificados en entornos de orquestación.

---

## Herramientas utilizadas

* **kubectl** — Gestión de recursos en Kubernetes.
* **oc** — Cliente de OpenShift (compatible con recursos estándar de Kubernetes).
* **cert-manager** — Operador/controlador para certificados en el clúster.
* **openssl** — Verificación de certificados y creación de Secrets desde archivos PEM.

---

## Qué deberías comprender al terminar

Al finalizar este laboratorio deberías ser capaz de:

* crear y usar Secrets de tipo TLS en Kubernetes
* configurar un Ingress con TLS usando certificados de tu PKI
* desplegar cert-manager y emitir certificados mediante Issuer/ClusterIssuer
* configurar Rutas TLS en OpenShift e inyectar CAs de confianza cuando sea necesario.

Este conocimiento te permitirá operar certificados en entornos Kubernetes y OpenShift de forma coherente con el resto del curso.
