# Docker + Kubernetes 101

## Descripcion del curso

Curso introductorio para aprender a trabajar con contenedores y orquestacion de forma aplicada. El recorrido va desde los fundamentos de Docker hasta el despliegue y automatizacion de cargas en Kubernetes, terminando con un proyecto final integrador.

## Objetivos

Al finalizar el curso, el alumno sera capaz de:

- Comprender con claridad la diferencia entre imagen, contenedor y orquestacion.
- Construir imagenes Docker adaptadas a las necesidades de un proyecto.
- Ejecutar y operar contenedores en escenarios reales de desarrollo.
- Levantar stacks multi-servicio con Docker Compose.
- Desplegar aplicaciones en Kubernetes con manifiestos y Kustomize.
- Ejecutar procesos batch y programados con Job y CronJob.
- Integrar un flujo completo de build y despliegue en un proyecto final.

## Perfil del alumno

Este curso esta orientado a personas que:

- Trabajan o quieren trabajar con desarrollo, analitica o datos.
- Necesitan desplegar soluciones en contenedores sin partir de cero.
- Buscan un aprendizaje guiado, practico y aplicable al trabajo diario.

No se requiere experiencia previa avanzada en DevOps.

## Metodologia

La formacion sigue un formato progresivo y guiado:

- Cada modulo se divide en capitulos concretos.
- En cada capitulo se combinan explicacion conceptual y demostracion.
- Al cerrar cada modulo se realiza un laboratorio autonomo por pasos.
- El alumno avanza de forma secuencial y valida resultados en cada bloque.

## Temario (indice extendido)

0. Setup - Fundamentos y entorno (`labs/00-prework`)
   - Fundamentos base
     - Que es un contenedor y diferencias frente a una VM.
     - Relacion entre Docker, Kubernetes y entorno de trabajo.
   - Preparacion tecnica
     - Verificacion de Docker y Compose.
     - Instalacion y validacion de `kubectl`, `kind` y `helm`.

1. Modulo 1 - Docker basico (`labs/01-docker-basico`)
   - Conceptos y operativa esencial
     - Imagen, contenedor, ciclo de vida y comandos clave.
     - Interaccion con contenedores en modo no interactivo e interactivo.
   - Interaccion con el anfitrion
     - Variables de entorno, puertos y volumenes.
     - Persistencia y comunicacion entre contenedores.
   - Construccion y orquestacion inicial
     - Dockerfile y creacion de imagenes personalizadas.
     - Docker Compose para escenarios multi-servicio.

2. Modulo 2 - Docker para analitica (`labs/02-docker-analitica`)
   - Arquitectura del stack
     - Separacion en API, base de datos y ETL.
     - Configuracion por entorno y estructura de proyecto.
   - Flujo de ejecucion local
     - Build de servicios y arranque coordinado con Compose.
     - Validacion de endpoints y conectividad con base de datos.
   - Procesamiento de datos
     - Ejecucion de ETL bajo demanda.
     - Comprobacion de resultados y trazabilidad de ejecucion.

3. Modulo 3 - Kubernetes basico (`labs/03-k8s-basico`)
   - Primer despliegue en cluster
     - Namespace, Deployment, Service y Secret.
     - Verificacion de estado de pods y servicios.
   - Gestion de imagenes
     - Build y carga en `kind`.
     - Flujo alternativo con registry.
   - Organizacion de manifiestos
     - Estructura `base` y `overlays` con Kustomize.
     - Escalado y ajustes por entorno.

4. Modulo 4 - Kubernetes para analitica (`labs/04-k8s-analitica`)
   - Batch en Kubernetes
     - Jobs para ejecuciones puntuales.
     - CronJobs para ejecuciones programadas.
   - Configuracion externa
     - Uso de ConfigMap para parametros de proceso.
     - Uso de Secret para credenciales y datos sensibles.
   - Validacion operativa
     - Seguimiento de ejecuciones y revision de logs.
     - Comprobacion de estabilidad del pipeline batch.

5. Modulo 5 - Proyecto final guiado (`labs/05-proyecto-final`)
   - Definicion del proyecto
     - Alcance minimo funcional y estructura de entrega.
     - Integracion de API, ETL y despliegue.
   - Pipeline tecnico
     - Build de imagenes y despliegue en cluster.
     - Uso de compose y kustomize en flujo unificado.
   - Entrega y cierre
     - Evidencias tecnicas y criterios de validacion final.
     - Revision global del recorrido y buenas practicas.

## Roadmap del curso

1. Inicio y setup del entorno
   - Comprender el contexto de la contenerizacion
     - Alinear conceptos base antes de ejecutar practicas.
   - Dejar herramientas operativas
     - Validar entorno local para evitar bloqueos en modulos siguientes.

2. Dominio de Docker en contexto real
   - Operar contenedores con seguridad
     - Ejecutar, inspeccionar, reiniciar y eliminar contenedores.
   - Construir imagenes reproducibles
     - Definir Dockerfile y optimizar ciclo de build.

3. Construccion de un stack de analitica
   - Integrar servicios en compose
     - API, base de datos y ETL en un flujo coordinado.
   - Validar funcionamiento extremo a extremo
     - Comprobar salud de servicios y salida de procesos batch.

4. Paso a Kubernetes
   - Trasladar el stack a cluster
     - Aplicar manifiestos base y verificar despliegue.
   - Estandarizar configuracion
     - Organizar recursos con Kustomize para entornos distintos.

5. Automatizacion y operacion batch
   - Ejecutar cargas por demanda y por calendario
     - Implementar Jobs y CronJobs de forma controlada.
   - Externalizar configuracion y credenciales
     - Separar parametros funcionales de datos sensibles.

6. Cierre con proyecto final
   - Integrar todo el flujo en una entrega unica
     - Build, despliegue, validacion y documentacion.
   - Consolidar criterio tecnico
     - Demostrar autonomia operativa sobre Docker y Kubernetes.

## Acceso y arranque

Repositorio oficial:

- https://github.com/davidpestana/docker-k8s-101

Flujo recomendado:

1. Haz `Fork` del repositorio.
2. Abre tu fork en Codespaces.
3. Comienza en `labs/00-prework/README.md`.
