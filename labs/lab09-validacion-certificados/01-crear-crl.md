# Laboratorio 09 — Validación de certificados

## Crear y publicar una lista de revocación (CRL)

En una infraestructura PKI no basta con emitir certificados.
También es necesario poder **revocar certificados antes de su fecha de expiración**.

Esto puede ocurrir por varios motivos:

* compromiso de una clave privada
* cambio de identidad de un servicio
* errores en la emisión de certificados.

Una forma de comunicar estas revocaciones es mediante una **CRL (Certificate Revocation List)**.

En este ejercicio generaremos una CRL utilizando la autoridad certificadora creada en los laboratorios anteriores.

---

### Paso 1 — Ir al directorio de la autoridad certificadora

Sitúate en el directorio donde creamos la PKI.

```bash id="u7b4uv"
cd ~/pki-ca
```

Comprueba que los archivos principales de la CA están disponibles.

```bash id="y0m0dn"
ls
```

Deberías ver elementos como:

```id="bmb2ow"
ca.crt
private
certs
crl
index.txt
serial
```

Estos archivos permiten a la CA gestionar certificados y revocaciones.

---

### Paso 2 — Generar una CRL inicial

Crea una lista de revocación vacía.

> Si al ejecutar el siguiente comando obtienes el error
> `cannot lookup how long until the next CRL is due`,
> significa que falta la directiva `default_crl_days` en tu `openssl-ca.cnf`.
> Abre el archivo y añade esta línea dentro de la sección `[ CA_default ]`:
>
> ```text
> default_crl_days  = 30
> ```

```bash id="kqu5hw"
openssl ca -config openssl-ca.cnf -gencrl -out crl/ca.crl
```

Comprueba que el archivo se ha generado.

```bash id="ovm08n"
ls crl
```

Deberías ver:

```id="5rc8ds"
ca.crl
```

Esta CRL aún no contiene certificados revocados.

---

### Paso 3 — Generar la CRL de la CA intermedia

En nuestra PKI los certificados de servidor fueron firmados por la **CA intermedia**.
Para poder verificar revocaciones con `-crl_check`, OpenSSL necesita una CRL
de cada CA en la cadena, así que también necesitamos la CRL de la intermedia.

```bash id="int-crl"
openssl ca \
  -config intermediate/openssl-intermediate.cnf \
  -gencrl \
  -out intermediate/crl/intermediate.crl
```

> Si obtienes `cannot lookup how long until the next CRL is due`,
> añade `default_crl_days = 30` en la sección `[ CA_default ]` de tu
> `openssl-intermediate.cnf`, igual que hicimos con la CA raíz.

Comprueba que se ha generado:

```bash id="ls-int-crl"
ls intermediate/crl
```

Deberías ver:

```id="int-crl-out"
intermediate.crl
```

---

### Paso 4 — Examinar las CRL generadas

Visualiza el contenido de la CRL de la CA raíz:

```bash id="6p4knk"
openssl crl -in crl/ca.crl -text -noout
```

Y la de la CA intermedia:

```bash id="6p4knk-int"
openssl crl -in intermediate/crl/intermediate.crl -text -noout
```

Busca en ambas una sección similar a:

```id="n2pn68"
No Revoked Certificates
```

Esto indica que actualmente ningún certificado ha sido revocado en ninguna de las dos CAs.

---

### Paso 5 — Verificar la firma de las CRL

Comprueba que cada CRL ha sido firmada por su autoridad certificadora correspondiente.

```bash id="scopst"
openssl crl -in crl/ca.crl -noout -issuer
openssl crl -in intermediate/crl/intermediate.crl -noout -issuer
```

La salida debería mostrar algo similar a:

```id="2d4kxb"
issuer=CN = Training Root CA
issuer=CN = Training Intermediate CA
```

Esto confirma que cada CRL pertenece a la CA que la emitió.

---

### Paso 6 — Comprobar la validez temporal de las CRL

Muestra las fechas de validez de ambas listas de revocación.

```bash id="j3m6u3"
openssl crl -in crl/ca.crl -noout -nextupdate -lastupdate
openssl crl -in intermediate/crl/intermediate.crl -noout -nextupdate -lastupdate
```

Estas fechas indican:

* cuándo se publicó cada CRL
* cuándo deberá generarse una nueva versión.

Las CRL deben actualizarse periódicamente para que los clientes puedan comprobar revocaciones recientes.

---

En este ejercicio hemos generado listas de revocación tanto de la CA raíz como de la CA intermedia, y hemos examinado su contenido, firma y vigencia.
