# Temario — Gestión de Certificados Digitales en Linux / Solaris

Este documento describe el contenido formativo del curso **Gestión de Certificados Digitales en Entornos Linux / Solaris**.

El curso combina fundamentos teóricos con laboratorios prácticos que permiten comprender cómo funcionan los certificados digitales y cómo administrarlos en sistemas reales.

---

# Objetivos del curso

Capacitar al alumnado para administrar certificados digitales **X.509**, comprender el funcionamiento de **TLS** y gestionar infraestructuras **PKI** en sistemas Linux y Solaris.

Al finalizar el curso los alumnos deberán ser capaces de:

* comprender el funcionamiento de la criptografía de clave pública
* generar y gestionar claves criptográficas
* emitir certificados mediante una infraestructura PKI
* configurar servicios para utilizar TLS
* validar certificados y gestionar revocaciones
* automatizar la gestión de certificados
* diagnosticar problemas TLS
* comprender los mecanismos equivalentes en sistemas Solaris.

---

# Bloques formativos

## 1 — Introducción a TLS y criptografía de clave pública

Conceptos fundamentales de criptografía aplicados a TLS.

Contenidos:

* criptografía simétrica vs asimétrica
* pares de claves públicas y privadas
* establecimiento de conexiones TLS
* handshake TLS
* papel de los certificados en TLS.

Laboratorios asociados:

* **Lab01 — Explorar TLS**

---

## 2 — Generación y gestión de claves criptográficas

Creación y análisis de claves utilizadas en certificados.

Contenidos:

* algoritmos criptográficos utilizados en TLS
* claves RSA
* claves ECDSA
* seguridad y protección de claves privadas
* análisis de claves criptográficas
* generación de claves y certificados con herramientas como `openssl` y `certtool`.

Laboratorios asociados:

* **Lab02 — Claves criptográficas**

---

## 3 — Certificados X.509

Estructura y funcionamiento de los certificados digitales.

Contenidos:

* formato X.509
* campos principales del certificado
* extensiones de certificados
* certificados autofirmados
* validación básica de certificados.

Laboratorios asociados:

* **Lab03 — Certificados X.509**

---

## 4 — Infraestructura de Clave Pública (PKI)

Construcción de una infraestructura de certificación.

Contenidos:

* concepto de PKI
* autoridades certificadoras
* jerarquía de certificación
* Root CA e Intermediate CA
* diferencias entre CA internas y externas
* seguridad de autoridades certificadoras.

Laboratorios asociados:

* **Lab04 — Crear CA**

---

## 5 — Emisión de certificados

Proceso de solicitud y firma de certificados.

Contenidos:

* generación de CSR
* firma de certificados
* gestión de certificados emitidos
* construcción de cadenas de certificados.

Laboratorios asociados:

* **Lab05 — Firmar certificados**

---

## 6 — Formatos de certificados

Conversión y gestión de certificados en distintos formatos.

Contenidos:

* formatos PEM y DER
* contenedores PKCS#12
* keystores Java (JKS)
* interoperabilidad entre formatos.

Laboratorios asociados:

* **Lab06 — Formatos de certificados**

---

## 7 — Keystores y Truststores

Gestión de certificados en sistemas y aplicaciones.

Contenidos:

* diferencia entre keystore y truststore
* truststore del sistema Linux
* keystores utilizados por aplicaciones Java
* bases de datos NSS.

Laboratorios asociados:

* **Lab07 — Keystores y truststores**

---

## 8 — Uso de TLS en servicios

Configuración de servicios para utilizar certificados.

Contenidos:

* configuración HTTPS en servidores web
* uso de certificados en servidores de correo
* uso de certificados en otros servicios como SSH u OpenVPN
* despliegue de servicios TLS
* pruebas y validación de conexiones TLS.

Laboratorios asociados:

* **Lab08 — TLS en servicios**

---

## 9 — Validación y revocación de certificados

Mecanismos para comprobar la validez de certificados.

Contenidos:

* listas de revocación de certificados (CRL)
* revocación de certificados
* protocolo OCSP
* verificación del estado de certificados.

Laboratorios asociados:

* **Lab09 — Validación de certificados**

---

## 10 — Automatización de certificados

Gestión automática de certificados en sistemas Linux.

Contenidos:

* automatización de renovación de certificados
* scripts de mantenimiento
* herramientas de gestión automática como `certmonger`
* integración con servicios de identidad mediante `sssd`
* automatización de despliegues mediante herramientas como `ansible`
* tareas programadas mediante cron.

Laboratorios asociados:

* **Lab10 — Automatización**

---

## 11 — Diagnóstico de problemas TLS

Identificación y resolución de problemas relacionados con certificados.

Contenidos:

* análisis de handshake TLS
* inspección de certificados presentados por servidores
* diagnóstico de errores TLS
* herramientas de análisis de conexiones seguras.

Laboratorios asociados:

* **Lab11 — Diagnóstico TLS**

---

## 12 — Gestión de certificados en Solaris

Introducción al modelo criptográfico de Solaris.

Contenidos:

* framework criptográfico de Solaris
* uso de pktool
* gestión de keystores PKCS#11
* relación entre herramientas Linux y Solaris
* integración con servicios gestionados mediante **SMF (Service Management Facility)**.

Laboratorios asociados:

* **Lab12 — Solaris**

---

## 13 — Buenas prácticas y automatización avanzada

Síntesis de buenas prácticas y patrones para operar certificados en producción.

Contenidos:

* buenas prácticas en el diseño de una PKI
* políticas de validez y rotación de certificados
* protección y almacenamiento seguro de claves privadas
* plantillas de scripting y automatización de rotación
* integración de automatización (cron, certmonger, ansible, PKCS#11).

Laboratorios asociados:

* **Lab13 — Buenas prácticas**

---

## 14 — Certificados en Kubernetes, OpenShift y cert-manager

Uso de certificados en entornos de orquestación de contenedores.

Contenidos:

* Secrets de Kubernetes para certificados y claves
* Ingress con TLS
* cert-manager: Certificate, Issuer y ClusterIssuer; renovación automática
* OpenShift: Rutas con TLS (edge, passthrough, reencryption)
* CA de confianza e inyección de certificados en el clúster
* service serving certificates en OpenShift (concepto).

Laboratorios asociados:

* **Lab14 — Kubernetes, OpenShift y cert-manager**

---

# Metodología

El curso sigue una metodología basada en **aprendizaje práctico**.

Cada laboratorio propone ejercicios donde el alumno:

* ejecuta comandos
* analiza resultados
* comprende el funcionamiento interno del sistema
* aplica el conocimiento en ejercicios posteriores.

Este enfoque permite adquirir experiencia práctica en la administración de certificados digitales.

---

# Duración estimada

Duración total del curso:

**25 horas**

Distribuidas entre:

* explicación conceptual
* ejecución de laboratorios
* análisis de resultados
* resolución de problemas.
