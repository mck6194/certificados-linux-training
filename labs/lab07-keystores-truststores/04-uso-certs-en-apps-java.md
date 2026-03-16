# Laboratorio 07 — Keystores y almacenes de confianza

## Uso de certificados en aplicaciones Java y distintos JDK

En los ejercicios anteriores has trabajado con:

* el **truststore del sistema** (`/etc/ssl/certs` en Linux)
* keystores Java (JKS/PKCS12) con `keytool`
* bases de datos NSS.

En este ejercicio verás cómo se combinan estos elementos en aplicaciones Java reales y cómo influyen los distintos **JDK**.

---

### 1. Roles de certificados en aplicaciones

En una arquitectura típica aparecen varios tipos de certificados:

* **Certificado de servidor**
  * Lo presenta el servicio que termina TLS (servidor web, API, proxy, aplicación Java embebida).
  * Suele estar en un keystore (Java) o en archivos PEM (NGINX, Apache, etc.).

* **Certificado de cliente / aplicación**
  * Lo presenta la aplicación cuando se autentica frente a otro servicio (mTLS).
  * También se almacena en un keystore Java (entrada `PrivateKeyEntry`).

* **Certificado en proxy o balanceador**
  * El proxy termina TLS hacia los clientes externos.
  * Opcionalmente abre otra conexión TLS hacia el backend (reencryption).

En Java estos certificados se gestionan mediante **keystores** y **truststores**.

---

### 2. Keystore de la aplicación (identidad)

Una aplicación Java que actúa como servidor TLS o cliente mTLS necesita:

* un **keystore** con:
  * clave privada
  * certificado firmado por tu CA
  * (opcional) cadena completa de certificados.

Ejemplo de parámetros de arranque para usar un keystore específico:

```bash
java \
  -Djavax.net.ssl.keyStore=/ruta/app-keystore.p12 \
  -Djavax.net.ssl.keyStorePassword=changeit \
  -Djavax.net.ssl.keyStoreType=PKCS12 \
  -jar mi-aplicacion.jar
```

Esto indica a la JVM qué certificado debe presentar la aplicación cuando actúa como servidor TLS o como cliente autenticado.

---

### 3. Truststore de la aplicación (confianza)

Para validar certificados de otros servicios, Java utiliza un **truststore** con CAs de confianza.

* Por defecto, cada JDK trae un archivo `cacerts` con CAs públicas.
* Su ubicación típica depende de la versión:
  * Java 8 y anteriores: `${JAVA_HOME}/jre/lib/security/cacerts`
  * Java 9 y posteriores: `${JAVA_HOME}/lib/security/cacerts`
  * en muchas distribuciones Linux puede estar enlazado a `/etc/ssl/certs/java/cacerts`.

En lugar de modificar `cacerts` del JDK, suele ser mejor usar un truststore **específico de la aplicación**:

```bash
java \
  -Djavax.net.ssl.trustStore=/ruta/app-truststore.jks \
  -Djavax.net.ssl.trustStorePassword=changeit \
  -Djavax.net.ssl.trustStoreType=JKS \
  -jar mi-aplicacion.jar
```

En ese truststore puedes importar:

* la CA raíz de tu PKI
* las CAs internas que necesite la aplicación.

---

### 4. Diferentes JDK y su efecto

Cada JDK (Oracle, OpenJDK, Adoptium, etc.) puede traer:

* un conjunto de CAs preinstaladas distinto en `cacerts`
* rutas diferentes para los archivos de seguridad.

Consecuencias:

* dos aplicaciones que se ejecutan con JDK distintos pueden confiar en conjuntos de CAs diferentes.
* mover una aplicación de un JDK a otro puede cambiar qué certificados se aceptan por defecto.

Buena práctica:

* tratar `cacerts` del JDK como almacén base
* definir truststores específicos por aplicación cuando sea necesario añadir CAs internas o de laboratorio.

---

### 5. Integrar con el resto del curso

Relaciona este ejercicio con otros laboratorios:

* **Lab05 / Lab10** — el proceso de creación/renovación de certificados (CSR, firma, rotación) genera archivos `server.crt`, `fullchain.crt`, etc., que puedes empaquetar en un keystore Java (JKS/PKCS12).
* **Lab06** — utiliza `openssl` y `keytool` para convertir entre PEM, PKCS12 y JKS antes de cargarlos en aplicaciones Java.
* **Lab08** — cuando un servicio está detrás de un proxy o balanceador, decide dónde termina TLS:
  * si solo en el proxy, la app Java puede hablar HTTP interno
  * si también en la app, deberás configurar su keystore y truststore como se describe aquí.

Con estos conceptos podrás diseñar de forma consciente cómo las aplicaciones Java presentan su identidad TLS y en qué autoridades confían, independientemente del JDK utilizado.

