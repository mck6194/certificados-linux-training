# Laboratorio 14 — Certificados en Kubernetes y OpenShift

## Certificados y TLS en Kubernetes (Secrets e Ingress)

En Kubernetes los certificados y las claves privadas no se guardan en archivos dentro del nodo, sino en **Secrets**. Un Secret de tipo `kubernetes.io/tls` almacena un certificado y su clave privada en formato base64; los pods pueden montarlos como volúmenes o usarlos a través de variables de entorno.

En este ejercicio crearás un Secret TLS a partir de los certificados de la PKI del curso y configurarás un **Ingress** para servir HTTPS.

**Requisitos:** clúster Kubernetes (minikube, kind, k3s, etc.), `kubectl` configurado, certificados en `~/pki-labs/web-server` (server.crt, server.key o fullchain.crt) y CA raíz en `~/pki-ca/ca.crt`.

---

### Paso 1 — Crear un Secret TLS desde archivos PEM

Desde el directorio donde tienes el certificado y la clave del servidor (por ejemplo `~/pki-labs/web-server`), crea un Secret que Kubernetes use para TLS.

Si tienes certificado y clave por separado:

```bash
kubectl create secret tls web-server-tls \
  --cert=server.crt \
  --key=server.key \
  -n default
```

Si quieres incluir la cadena completa (servidor + intermedios), concatena el fullchain y úsalo como `--cert`:

```bash
kubectl create secret tls web-server-tls \
  --cert=fullchain.crt \
  --key=server.key \
  -n default
```

Comprueba que el Secret se ha creado:

```bash
kubectl get secret web-server-tls
kubectl describe secret web-server-tls
```

El Secret contendrá los campos `tls.crt` y `tls.key` que el Ingress (o un servidor dentro del pod) utilizará para TLS.

---

### Paso 2 — Desplegar un servicio backend simple

Para que el Ingress tenga algo que servir, despliega un Deployment y un Service. Por ejemplo, un NGINX que escuche en el puerto 80:

```bash
kubectl create deployment web-backend --image=nginx:alpine --replicas=1
kubectl expose deployment web-backend --port=80
```

Comprueba que el pod está listo:

```bash
kubectl get pods -l app=web-backend
kubectl get svc web-backend
```

---

### Paso 3 — Crear un Ingress con TLS

Crea un recurso Ingress que termine TLS usando el Secret creado en el paso 1. Ajusta el host (`host`) al que vayas a acceder (por ejemplo un nombre que resuelvas en `/etc/hosts` o el que proporcione tu entorno).

Crea un archivo `ingress-tls.yaml`:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-ingress-tls
spec:
  ingressClassName: nginx   # o "nginx", "traefik", etc., según tu clúster
  tls:
  - hosts:
    - web.test.local
    secretName: web-server-tls
  rules:
  - host: web.test.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-backend
            port:
              number: 80
```

Aplica el Ingress:

```bash
kubectl apply -f ingress-tls.yaml
```

Si tu clúster no tiene controlador Ingress, instala uno (por ejemplo Ingress NGINX o el que recomiende tu distribución). Comprueba el estado:

```bash
kubectl get ingress
```

---

### Paso 4 — Probar la conexión HTTPS

Obtén la IP o hostname del Ingress (o del LoadBalancer/NodePort según tu entorno):

```bash
kubectl get ingress web-ingress-tls
```

Desde una máquina que resuelva el host (o añadiendo una entrada en `/etc/hosts`), prueba con curl usando el certificado de la CA raíz del curso:

```bash
curl -v --cacert ~/pki-ca/ca.crt https://web.test.local
```

Deberías ver el handshake TLS correcto y la respuesta del backend. Si el certificado del servidor no incluye el nombre que usas en la URL, verás un error de nombre; en ese caso ajusta el CN/SAN del certificado en la PKI o el host en el Ingress.

---

### Paso 5 — Montar el Secret como archivos en un Pod (opcional)

Para ver cómo un pod consume el certificado como archivos, crea un Deployment que monte el Secret en un directorio:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-with-certs
spec:
  replicas: 1
  selector:
    matchLabels:
      app: app-with-certs
  template:
    metadata:
      labels:
        app: app-with-certs
    spec:
      containers:
      - name: app
        image: alpine
        command: ["sleep", "3600"]
        volumeMounts:
        - name: tls
          mountPath: /etc/tls
          readOnly: true
      volumes:
      - name: tls
        secret:
          secretName: web-server-tls
```

Aplica y entra en el pod:

```bash
kubectl apply -f - << 'EOF'
# pega el YAML anterior
EOF
kubectl exec -it deployment/app-with-certs -- sh
# Dentro del pod:
ls -la /etc/tls
cat /etc/tls/tls.crt | head -5
exit
```

Verás `tls.crt` y `tls.key` en `/etc/tls`; así es como muchas aplicaciones leen certificados en Kubernetes.

---

En este ejercicio has creado un Secret TLS a partir de certificados PEM, has configurado un Ingress con TLS y has comprobado que los pods pueden usar el Secret como archivos. En el siguiente ejercicio usarás **cert-manager** para que el clúster emita y renueve certificados automáticamente.
