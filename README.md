# info de la materia: S2566-0673 Topicos especiales en telematica

## Estudiante(s): Juan David Zapata Moncada (jdzapatam@eafit.edu.co) y Juan Miguel Muñoz Garcia (jmmunozg1@eafit.edu.co)

## Profesor: Alvaro Enrique Ospina Sanjuan

# Proyecto 2

### 1. breve descripción de la actividad

El presente proyecto corresponde al Proyecto 2 del curso ST0263 – Tópicos Especiales en Telemática de la Universidad EAFIT, cuyo objetivo principal es diseñar e implementar la evolución de una aplicación monolítica hacia una arquitectura escalable en la nube, utilizando servicios de Amazon Web Services (AWS).

Para ello, se parte de la aplicación BookStore, un sistema monolítico desarrollado en Python (Flask) y MySQL, que simula un portal de venta y compra de libros de segunda mano. La aplicación permite registrar usuarios, visualizar catálogos, gestionar compras y simular procesos de pago y envío.

A lo largo del proyecto, se busca aplicar diferentes patrones y niveles de escalabilidad en la nube, cumpliendo con los siguientes objetivos secuenciales:

Objetivo 1: Desplegar la aplicación monolítica en dos máquinas virtuales (una para la base de datos y otra para la aplicación con NGINX y certificado SSL).

Objetivo 2: Escalar la aplicación monolítica en AWS utilizando Auto Scaling Groups (ASG), Load Balancer (ELB), RDS para la base de datos y EFS para almacenamiento compartido.

Objetivo 3: Desplegar la misma aplicación monolítica dentro de un clúster Kubernetes (EKS), integrando servicios como Amazon EFS y Amazon RDS para garantizar alta disponibilidad y tolerancia a fallos.

Objetivo 4: Evolucionar el sistema a una arquitectura basada en microservicios, o desplegar una base de datos en clúster dentro de Kubernetes.


## 1.1. Que aspectos cumplió o desarrolló de la actividad propuesta por el profesor (requerimientos funcionales y no funcionales)


### Objetivo 1: Despliegue de la aplicación monolítica en 2 máquinas virtuales (20%)

#### Requerimientos funcionales cumplidos:

 - Se desplegó la aplicación BookStore monolítica en dos instancias EC2 en AWS:

- Una VM para la base de datos MySQL.

- Otra VM para la aplicación Flask + NGINX.

- Se configuró un proxy inverso con NGINX, que direcciona el tráfico HTTP hacia el contenedor Flask.

- Se generó y aplicó un certificado SSL mediante Certbot para asegurar la comunicación HTTPS.

#### Requerimientos no funcionales cumplidos:

- Comunicación segura (HTTPS).

- Separación de responsabilidades entre la capa de aplicación y la base de datos.

- Configuración de un firewall (Security Groups) restringiendo puertos innecesarios.

- Documentación detallada del proceso de despliegue manual.
  


### Objetivo 2: Escalamiento de la aplicación monolítica en AWS (30%)

#### Requerimientos funcionales cumplidos:

- Implementación de un Auto Scaling Group (ASG) para instancias EC2, lo que permite escalar horizontalmente la capa de aplicación.

- Integración de un Load Balancer (ELB) para distribuir el tráfico entre las instancias.

- Migración de la base de datos a Amazon RDS (MySQL), garantizando disponibilidad y respaldo automático.

- Configuración de Amazon EFS como sistema de archivos compartido entre las instancias de aplicación.

#### Requerimientos no funcionales cumplidos:

- Alta disponibilidad mediante múltiples instancias balanceadas.

- Escalabilidad automática basada en carga.

- Persistencia y consistencia de datos garantizada mediante RDS y EFS.

- Reducción del tiempo de inactividad mediante balanceo y monitoreo.

### Objetivo 3: Despliegue en clúster Kubernetes (EKS) (20%)

#### Requerimientos funcionales cumplidos:

- Creación de un clúster EKS en AWS con eksctl.

- Despliegue de la aplicación monolítica dentro del clúster mediante archivos YAML (Deployment, Service, Namespace, Secret, PersistentVolume).

- Integración con Amazon EFS como volumen compartido.

- Configuración de un LoadBalancer Service que expone la aplicación al público.

#### Requerimientos no funcionales cumplidos:

- Despliegue automatizado y reproducible mediante kubectl y kustomize.

- Escalabilidad horizontal configurada a través de réplicas en el Deployment.

-  Resiliencia ante fallos mediante pods distribuidos y balanceados.

- Persistencia del almacenamiento externo (EFS).

- Integración con RDS para la base de datos.

### Objetivo 4: Evolución hacia microservicios o alta disponibilidad de base de datos (30%)

#### Requerimientos funcionales cumplidos:

- Se exploró la reingeniería de la aplicación BookStore para dividirla en tres posibles microservicios:

- Autenticación

- Catálogo

- Compras y envíos

- Alternativamente, se diseñó un entorno con Percona XtraDB Cluster (PXC) como base de datos en clúster con alta disponibilidad.

#### Requerimientos no funcionales cumplidos:

- Diseño modular orientado a separación de responsabilidades.

- Alta disponibilidad y replicación de datos (en caso de PXC).

- Capacidad de despliegue independiente por servicio (en caso de evolución a microservicios).

- Mantenimiento simplificado mediante separación lógica de componentes.

## 1.2. Que aspectos NO cumplió o desarrolló de la actividad propuesta por el profesor (requerimientos funcionales y no funcionales)


# 2. información general de diseño de alto nivel, arquitectura, patrones, mejores prácticas utilizadas.

El proyecto BookStore evolucionó desde una aplicación monolítica local hacia una arquitectura distribuida y escalable en la nube AWS, pasando por distintas etapas que incorporan principios de separación de responsabilidades, alta disponibilidad, automatización e infraestructura como código.

El diseño general se compone de los siguientes niveles:

## Capa de aplicación (Flask):
La aplicación web desarrollada en Python (Flask) se ejecuta inicialmente en contenedores Docker, y posteriormente se despliega en EKS (Elastic Kubernetes Service).
En cada fase se utilizan prácticas de empaquetamiento reproducible (Dockerfile) y despliegue automatizado mediante YAML Manifests (Deployment, Service, Namespace, Secrets).

## Capa de base de datos:
En las primeras fases, la base de datos se implementa como un contenedor MySQL dentro de una máquina virtual.
En el escalamiento se migra hacia un servicio administrado de base de datos (Amazon RDS), y en el objetivo final se propone una base de datos en clúster (Percona XtraDB) dentro de Kubernetes, garantizando redundancia y tolerancia a fallos.

## Capa de almacenamiento compartido (EFS):
Se incorpora Amazon EFS (Elastic File System) para compartir archivos entre réplicas del contenedor Flask y entre pods dentro de EKS.
Esto asegura persistencia y consistencia en un entorno escalable.

## Capa de red y balanceo:
En el despliegue en la nube, se utilizan Elastic Load Balancer (ELB) y Service Type=LoadBalancer para distribuir tráfico entre instancias o pods.
Esto permite garantizar disponibilidad y recuperación ante fallos.

## Capa de seguridad:
Se aplican medidas de seguridad en cada nivel:

- Certificados SSL y Let's Encrypt.

- Security Groups con control estricto de puertos.

- Gestión de secretos mediante objetos Kubernetes Secrets.

- Accesos IAM limitados a roles específicos (LabEksClusterRole, LabEksNodeRole).
  
## Arquitectura implementada
<img width="668" height="699" alt="image" src="https://github.com/user-attachments/assets/3173a7ea-f421-4b75-8af1-fe6c5b6dd706" />

## Patrones de diseño y arquitectura aplicados

| Categoría               | Patrón aplicado                       | Descripción                                                                                       |
| ----------------------- | ------------------------------------- | ------------------------------------------------------------------------------------------------- |
| **Arquitectura**        | **Separación de capas (3-tier)**      | Separa la lógica de aplicación, la base de datos y la capa de presentación/red.                   |
| **Escalabilidad**       | **Horizontal Scaling**                | Se agregan instancias (réplicas o pods) según la carga del sistema.                               |
| **Infraestructura**     | **Infraestructura como Código (IaC)** | Todo el entorno se define en archivos YAML (`cluster-eks.yaml`, `bookstore.yaml`, `efs-pv.yaml`). |
| **Despliegue**          | **Containerization Pattern**          | Uso de Docker para empaquetar la aplicación y garantizar portabilidad.                            |
| **Disponibilidad**      | **Load Balancing Pattern**            | Distribuye tráfico mediante ELB y Kubernetes Services.                                            |
| **Persistencia**        | **Shared Storage Pattern**            | Uso de EFS como volumen persistente accesible desde todos los pods.                               |
| **Alta disponibilidad** | **Replication Pattern**               | Réplicas de pods en diferentes nodos del clúster.                                                 |
| **Seguridad**           | **Secret Management Pattern**         | Variables sensibles gestionadas con `Secrets` en Kubernetes.                                      |



# 3. Descripción del ambiente de desarrollo y técnico: lenguaje de programación, librerias, paquetes, etc, con sus numeros de versiones.

## como se compila y ejecuta.
## detalles del desarrollo.
## detalles técnicos
## descripción y como se configura los parámetros del proyecto (ej: ip, puertos, conexión a bases de datos, variables de ambiente, parámetros, etc)
## opcional - detalles de la organización del código por carpetas o descripción de algún archivo. (ESTRUCTURA DE DIRECTORIOS Y ARCHIVOS IMPORTANTE DEL PROYECTO, comando 'tree' de linux)
## 
## opcionalmente - si quiere mostrar resultados o pantallazos 

# 4. Descripción del ambiente de EJECUCIÓN (en producción) lenguaje de programación, librerias, paquetes, etc, con sus numeros de versiones.

# IP o nombres de dominio en nube o en la máquina servidor.

## descripción y como se configura los parámetros del proyecto (ej: ip, puertos, conexión a bases de datos, variables de ambiente, parámetros, etc)

## como se lanza el servidor.

## una mini guia de como un usuario utilizaría el software o la aplicación

## opcionalmente - si quiere mostrar resultados o pantallazos 

# 5. otra información que considere relevante para esta actividad.

# referencias:
<debemos siempre reconocer los créditos de partes del código que reutilizaremos, así como referencias a youtube, o referencias bibliográficas utilizadas para desarrollar el proyecto o la actividad>
## sitio1-url 
## sitio2-url
## url de donde tomo info para desarrollar este proyecto﻿
