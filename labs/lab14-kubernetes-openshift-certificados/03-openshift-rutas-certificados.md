# Laboratorio 14 — Certificados en Kubernetes y OpenShift

## Certificados y TLS en OpenShift (Rutas y CA de confianza)

OpenShift extiende Kubernetes con el concepto de **Ruta (Route)**, que es el mecanismo principal para exponer servicios HTTP/HTTPS al exterior. Una Ruta puede terminar TLS en el edge (terminación TLS), hacer **passthrough** del TLS hasta el pod, o **reencryption** (terminar TLS en el router y abrir una nueva conexión TLS al backend).

En este ejercicio configurarás una **Ruta con TLS** usando un certificado propio (por ejemplo el de la PKI del curso), verás cómo inyectar una **CA de confianza** en el clúster para que los pods confíen en tu PKI, y conocerás brevemente los **service serving certificates** que OpenShift puede generar para comunicación interna.

**Requisitos:** clúster OpenShift (OpenShift Local/CRC, Developer Sandbox o instalación propia), `oc` configurado. Certificados en `~/pki-labs/web-server` y CA en `~/pki-ca`.

---

### Paso 1 — Crear un Secret TLS con tu certificado

OpenShift usa Secrets de tipo TLS igual que Kubernetes. Crea el Secret a partir de los archivos del servidor:

```bash
oc create secret tls my-route-tls \
  --cert=~/pki-labs/web-server/fullchain.crt \
  --key=~/pki-labs/web-server/server.key \
  -n default
```

Comprueba:

```bash
oc get secret my-route-tls
```

---

### Paso 2 — Desplegar un servicio y crear una Ruta con TLS

Despliega un backend y exponlo con un Service:

```bash
oc create deployment web-app --image=nginx:alpine --replicas=1
oc expose deployment web-app --port=80
```

Crea una **Ruta** que use TLS edge (terminación en el router) con tu certificado. Ajusta el host al que tengas acceso:

```bash
oc create route edge my-route \
  --service=web-app \
  --hostname=web.test.local \
  --key=~/pki-labs/web-server/server.key \
  --cert=~/pki-labs/web-server/fullchain.crt
```

Alternativamente, si ya tienes el Secret TLS:

```bash
oc create route edge my-route \
  --service=web-app \
  --hostname=web.test.local \
  --inbound-ca-cert=~/pki-ca/ca.crt
```

Para usar explícitamente el Secret existente, edita la ruta o créala con un YAML que referencie el Secret en `spec.tls.key` y `spec.tls.certificate` (según documentación de OpenShift). La forma más directa es la que usa `--key` y `--cert` como en el primer comando.

Comprueba la ruta:

```bash
oc get route my-route
oc describe route my-route
```

Obtén la URL (host/puerto) y prueba con curl usando la CA del curso:

```bash
curl -v --cacert ~/pki-ca/ca.crt https://web.test.local
```

---

### Paso 3 — Inyectar la CA de confianza en el clúster

Para que los pods del clúster confíen en certificados firmados por tu PKI (por ejemplo al llamar a APIs externas o a servicios que usen tu CA), puedes añadir tu CA al **trust bundle** que OpenShift inyecta en los pods.

En OpenShift 4.x suele usarse un **ConfigMap** o un **ConfigMap de CA** que la plataforma monta en los pods. Consulta la documentación de tu versión; un patrón común es:

1. Crear un ConfigMap con el certificado de la CA:

```bash
oc create configmap my-ca-bundle \
  --from-file=ca-bundle.crt=~/pki-ca/ca.crt \
  -n openshift-config
```

2. Referenciar ese ConfigMap en la configuración del clúster (por ejemplo en el recurso `proxy` o en la configuración de inyección de CA). Los detalles dependen de la versión de OpenShift.

Como alternativa práctica en un solo namespace, puedes montar el certificado de la CA en los pods que lo necesiten mediante un ConfigMap y la variable de entorno o ruta que use tu aplicación (por ejemplo `SSL_CERT_FILE` o `/etc/ssl/certs`).

Crea un ConfigMap en tu proyecto:

```bash
oc create configmap trusted-ca \
  --from-file=training-ca.crt=~/pki-ca/ca.crt \
  -n default
```

En el Deployment de tu aplicación, monta este ConfigMap en un volumen (por ejemplo en `/etc/pki/ca-trust/source/anchors/` o donde tu stack lea CAs) y ejecuta `update-ca-trust` si la imagen lo soporta, o configura la variable/archivo que tu cliente TLS use para la CA.

Así los pods de ese proyecto podrán validar certificados emitidos por tu PKI.

---

### Paso 4 — Service serving certificates (concepto)

OpenShift puede generar automáticamente certificados para que los servicios se comuniquen por TLS **dentro** del clúster. Esos certificados están firmados por la CA interna del clúster y los nombres siguen el patrón de los Services (por ejemplo `mi-servicio.mi-proyecto.svc`).

Para habilitar el **service serving certificate** en un Service, añade la anotación:

```bash
oc annotate service mi-servicio service.alpha.openshift.io/serving-cert-secret-name=mi-servicio-tls
```

OpenShift creará un Secret `mi-servicio-tls` con un certificado y clave válidos para el nombre del servicio. La aplicación debe montar ese Secret y usarlo para escuchar HTTPS. La CA que firma estos certificados está disponible en el clúster para que otros pods puedan verificar la identidad del servicio.

No es obligatorio hacer este paso en el laboratorio; basta con conocer que OpenShift ofrece esta opción para mTLS o HTTPS entre servicios dentro del clúster.

---

### Paso 5 — Resumen de tipos de Ruta TLS en OpenShift

* **Edge**: El router termina TLS con el cliente. Usas tu cert (o el de OpenShift por defecto). Tráfico interno al backend puede ser HTTP.
* **Passthrough**: El TLS llega hasta el pod; el router no descifra. El pod debe tener el certificado y la clave.
* **Reencryption**: El router termina TLS con el cliente y abre otra conexión TLS al backend. Necesitas configurar certificados en el router y, si el backend exige verificación, confianza en la CA del router en el backend.

Para la mayoría de los casos con certificados propios (como los de la PKI del curso), **edge** con `--cert` y `--key` (o Secret TLS) es lo más sencillo.

---

En este ejercicio has creado una Ruta OpenShift con TLS usando certificados propios, has visto cómo inyectar una CA de confianza para los pods y has conocido el concepto de service serving certificates. Con esto cierras el laboratorio 14: Kubernetes (Secrets, Ingress), cert-manager (emisión y renovación) y OpenShift (Rutas y CAs).
