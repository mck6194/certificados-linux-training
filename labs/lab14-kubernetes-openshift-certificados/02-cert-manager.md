# Laboratorio 14 — Certificados en Kubernetes y OpenShift

## Emitir y renovar certificados con cert-manager

**cert-manager** es un controlador que corre dentro del clúster y gestiona certificados mediante recursos de la API de Kubernetes: **Certificate**, **CertificateRequest**, **Issuer** y **ClusterIssuer**. Puede obtener certificados de una CA externa (por ejemplo Let's Encrypt) o de una CA interna; en este ejercicio usarás una CA interna alineada con la PKI del curso (por ejemplo una CA que emita contra un servicio HTTP o un ClusterIssuer que firme con una clave almacenada en un Secret).

En este ejercicio instalarás cert-manager, configurarás un **Issuer** o **ClusterIssuer** que use tu CA (o un emulador), y crearás un **Certificate** que cert-manager renovará automáticamente.

**Requisitos:** clúster Kubernetes con `kubectl` configurado. Opcional: PKI del curso con CA intermedia (intermediate.crt, intermediate.key) para usar como CA interna.

---

### Paso 1 — Instalar cert-manager

Instala cert-manager con el manifiesto oficial (comprueba la versión en [cert-manager.io](https://cert-manager.io/docs/installation/)):

```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.13.0/cert-manager.yaml
```

Espera a que los pods estén listos:

```bash
kubectl get pods -n cert-manager
```

Deberías ver pods como `cert-manager-*`, `cert-manager-webhook-*` y `cert-manager-cainjector-*` en estado `Running`.

---

### Paso 2 — Preparar la CA en el clúster (CA interna)

Para que cert-manager emita certificados con tu CA, la CA debe estar en el clúster. Crea un Secret con el certificado y la clave privada de la CA que firma (por ejemplo la intermedia del curso).

En un directorio donde tengas `intermediate.crt` e `intermediate.key` (o los archivos de tu CA):

```bash
kubectl create secret generic internal-ca \
  --from-file=ca.crt=intermediate.crt \
  --from-file=ca.key=intermediate.key \
  -n cert-manager
```

Ajusta nombres y namespace si usas otro. cert-manager usará este Secret en el Issuer/ClusterIssuer de tipo **CA**.

---

### Paso 3 — Crear un ClusterIssuer de tipo CA

Un **ClusterIssuer** permite emitir certificados en cualquier namespace. Crea uno que use el Secret de la CA (ajusta el nombre del Secret y el namespace):

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: internal-ca-issuer
spec:
  ca:
    secretName: internal-ca
```

El Secret `internal-ca` debe estar en el mismo namespace que cert-manager (por ejemplo `cert-manager`). Si lo creaste en otro namespace, usa un **Issuer** por namespace que apunte a ese Secret, o mueve el Secret al namespace de cert-manager.

Aplica el recurso:

```bash
kubectl apply -f - << 'EOF'
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: internal-ca-issuer
spec:
  ca:
    secretName: internal-ca
EOF
```

Comprueba el estado:

```bash
kubectl get clusterissuer internal-ca-issuer
```

El campo `Ready` debería ser `True` si el Secret existe y es válido.

---

### Paso 4 — Solicitar un Certificate

Un recurso **Certificate** indica a cert-manager que emita un certificado para un DNS concreto y que lo guarde en un Secret. Crea un Certificate en el namespace por defecto:

```yaml
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: web-app-tls
  namespace: default
spec:
  secretName: web-app-tls-secret
  issuerRef:
    name: internal-ca-issuer
    kind: ClusterIssuer
  dnsNames:
  - web.test.local
  - "*.test.local"
```

Aplica:

```bash
kubectl apply -f - << 'EOF'
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: web-app-tls
  namespace: default
spec:
  secretName: web-app-tls-secret
  issuerRef:
    name: internal-ca-issuer
    kind: ClusterIssuer
  dnsNames:
  - web.test.local
EOF
```

cert-manager creará un **CertificateRequest** y, con un Issuer de tipo CA, generará el certificado y lo almacenará en el Secret `web-app-tls-secret`.

Comprueba el estado del Certificate:

```bash
kubectl get certificate -n default
kubectl describe certificate web-app-tls -n default
```

Cuando esté listo, verás el Secret:

```bash
kubectl get secret web-app-tls-secret
```

Ese Secret tiene el mismo formato que los Secrets de tipo `kubernetes.io/tls` y puede usarse en un Ingress con `spec.tls[].secretName: web-app-tls-secret`.

---

### Paso 5 — Renovación automática

cert-manager revisa la validez de los certificados y los renueva antes de que expiren (por defecto con suficiente antelación). No necesitas hacer nada más; el Certificate que creaste se irá renovando.

Para inspeccionar eventos y renovaciones:

```bash
kubectl get certificaterequest -n default
kubectl describe certificate web-app-tls -n default
```

Opcional: ver el certificado almacenado en el Secret:

```bash
kubectl get secret web-app-tls-secret -o jsonpath='{.data.tls\.crt}' | base64 -d | openssl x509 -noout -dates -subject
```

---

### Nota sobre Issuer vs ClusterIssuer

* **Issuer** es por namespace: solo puede emitir certificados en su propio namespace. Útil cuando cada equipo tiene su namespace y su CA.
* **ClusterIssuer** es a nivel de clúster: cualquier Certificate en cualquier namespace puede referenciarlo con `issuerRef.kind: ClusterIssuer`.

Para Let's Encrypt se suele usar un ClusterIssuer con el challenge HTTP-01 o DNS-01; la configuración es distinta a la de una CA interna pero el flujo (Certificate → CertificateRequest → Secret) es el mismo.

---

En este ejercicio has instalado cert-manager, has configurado un ClusterIssuer con una CA interna y has creado un Certificate que cert-manager rellenó y mantendrá renovado. En el siguiente ejercicio aplicarás estos conceptos en **OpenShift** (Rutas y certificados).
