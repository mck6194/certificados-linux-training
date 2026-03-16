# Gestión de Certificados Digitales en Entornos Linux / Solaris

Este repositorio contiene una formación práctica centrada en la **administración de certificados digitales X.509 y PKI en sistemas Linux**, con una introducción a los mecanismos equivalentes utilizados en **Solaris**.

El curso está diseñado como una **secuencia progresiva de laboratorios**, donde cada ejercicio introduce nuevos conceptos mientras se construye una infraestructura de certificados funcional.

A lo largo del curso se trabajan tanto los **fundamentos teóricos** como las **tareas operativas reales** que realizan los administradores de sistemas.

---

# Objetivos del curso

El objetivo de esta formación es capacitar al alumnado para:

* comprender cómo funcionan los certificados digitales y TLS
* generar y gestionar claves criptográficas
* crear y operar una infraestructura PKI
* emitir y validar certificados
* integrar certificados en servicios reales
* automatizar la renovación de certificados
* diagnosticar problemas relacionados con TLS
* comprender el modelo criptográfico utilizado en Solaris.

Al finalizar el curso los alumnos deberían ser capaces de **administrar certificados digitales en entornos Linux empresariales** y comprender su funcionamiento en otras plataformas Unix.

---

# Enfoque de la formación

El curso se basa en un enfoque **práctico y progresivo**.

Cada laboratorio propone una serie de pasos donde el alumno:

* ejecuta comandos
* observa el comportamiento del sistema
* explora los resultados obtenidos
* comprende los conceptos involucrados
* aplica ese conocimiento en el siguiente ejercicio.

Este enfoque permite aprender **haciendo**, reproduciendo situaciones habituales en la administración de sistemas.

---

# Tecnologías y herramientas utilizadas

Durante los laboratorios se utilizarán herramientas ampliamente utilizadas en sistemas Linux.

Entre ellas:

* **OpenSSL**
* **curl**
* **keytool**
* **certutil / NSS**
* **pkcs11-tool**
* **SoftHSM**
* **certmonger**
* **cron**
* **Docker**
* **certtool (GnuTLS)**
* **sssd**
* **ansible**
* utilidades y servicios como **SSH** y **OpenVPN**

Estas herramientas permiten trabajar con certificados digitales, PKI y TLS en distintos contextos.

---

# Estructura del repositorio

El curso está organizado en una serie de laboratorios progresivos.

```id="q1f7c3"
labs/
 ├── lab00-entorno
 ├── lab01-explorar-tls
 ├── lab02-claves-criptograficas
 ├── lab03-certificados-x509
 ├── lab04-crear-ca
 ├── lab05-firmar-certificados
 ├── lab06-formatos-certificados
 ├── lab07-keystores-truststores
 ├── lab08-tls-en-servicios
 ├── lab09-validacion-certificados
 ├── lab10-automatizacion
 ├── lab11-diagnostico-tls
 ├── lab12-solaris
 ├── lab13-buenas-practicas
 └── lab14-kubernetes-openshift-certificados
```

Cada laboratorio contiene uno o varios **micro-laboratorios** que introducen nuevos conceptos y ejercicios prácticos.

---

# Progresión de los laboratorios

La secuencia de laboratorios sigue una progresión lógica:

1. Preparar el entorno de trabajo
2. Observar cómo funcionan las conexiones TLS
3. Generar claves criptográficas
4. Crear certificados X.509
5. Construir una PKI
6. Firmar certificados
7. Trabajar con formatos de certificados
8. Gestionar keystores y truststores
9. Utilizar certificados en servicios reales
10. Validar certificados y revocaciones
11. Automatizar la gestión de certificados
12. Diagnosticar problemas TLS
13. Comprender el modelo criptográfico en Solaris
14. Aplicar buenas prácticas y rotación automatizada de certificados
15. Gestionar certificados en Kubernetes, OpenShift y cert-manager.

Cada laboratorio se apoya en los anteriores, construyendo progresivamente una comprensión completa de la gestión de certificados.

---

# Requisitos

El curso está diseñado para ejecutarse en un entorno **Linux** con herramientas de desarrollo disponibles.

Se recomienda utilizar:

* un entorno **Codespaces**
* una máquina Linux local
* o una máquina virtual Linux.

También se utilizarán contenedores **Docker** para desplegar servicios de prueba.

---

# Perfil recomendado

Este curso está orientado a:

* administradores de sistemas Linux
* ingenieros DevOps
* responsables de infraestructura
* profesionales que gestionen servicios TLS o PKI.

Se recomienda tener conocimientos básicos de:

* línea de comandos Linux
* redes TCP/IP
* servicios web.

---

# Licencia

Este material está destinado a formación técnica y puede adaptarse o ampliarse según las necesidades de cada organización.
