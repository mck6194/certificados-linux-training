# Laboratorio 07 — Keystores y Truststores

En los laboratorios anteriores hemos generado certificados y trabajado con distintos formatos de almacenamiento.
En la práctica, los sistemas y aplicaciones necesitan **almacenes de certificados** que permitan gestionar claves y autoridades de confianza.

Estos almacenes se conocen normalmente como:

* **keystore**
* **truststore**

Comprender la diferencia entre ambos es fundamental para administrar certificados correctamente en sistemas Linux y aplicaciones empresariales.

---

## Qué aprenderás en este laboratorio

Durante este laboratorio aprenderás a:

* comprender la diferencia entre **keystore** y **truststore**
* gestionar autoridades certificadoras confiables en Linux
* trabajar con keystores utilizados por aplicaciones Java
* explorar bases de datos de certificados utilizadas por algunas aplicaciones.

Estos conceptos son esenciales para integrar certificados en sistemas reales.

---

## Conceptos clave

Los sistemas que utilizan certificados suelen separar dos tipos de almacenes.

**Keystore**

Contiene:

* claves privadas
* certificados asociados.

Se utiliza para identificar un servidor o una aplicación.

Ejemplo típico:

* certificado de un servidor HTTPS.

---

**Truststore**

Contiene:

* certificados de autoridades certificadoras confiables.

Se utiliza para verificar la identidad de servicios externos.

Ejemplo típico:

* las autoridades certificadoras confiables de un sistema operativo.

---

## Diferentes tipos de almacenes

Dependiendo del sistema o aplicación pueden utilizarse distintos formatos.

**Linux truststore**

* conjunto de certificados de autoridades confiables del sistema.

---

**Java keystore (JKS)**

* utilizado por aplicaciones Java
* gestionado mediante `keytool`.

---

**NSS database**

* utilizada por aplicaciones como navegadores o herramientas de seguridad.

Cada uno de estos sistemas gestiona los certificados de forma ligeramente distinta.

---

## Herramientas utilizadas

Durante este laboratorio utilizaremos herramientas como:

* **update-ca-certificates**
* **keytool**
* **certutil**

Estas herramientas permiten gestionar certificados en distintos tipos de almacenes.

---

## Laboratorios incluidos

Este laboratorio contiene los siguientes ejercicios:

```id="s4m7n2"
01-truststore-linux.md
02-keystore-java.md
03-nss-database.md
04-uso-certs-en-apps-java.md
```

Cada ejercicio explora un tipo diferente de almacén de certificados.

---

## Qué deberías comprender al terminar

Al finalizar este laboratorio deberías ser capaz de:

* comprender cómo los sistemas confían en autoridades certificadoras
* gestionar certificados en el truststore del sistema
* trabajar con keystores utilizados por aplicaciones Java
* explorar bases de datos de certificados utilizadas por aplicaciones.

Estos conocimientos serán necesarios para el siguiente laboratorio, donde utilizaremos certificados en **servicios reales como servidores web o sistemas de correo**.
