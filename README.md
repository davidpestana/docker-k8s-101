# Docker + Kubernetes 101

Curso de entrada, orientado a analistas y perfiles de datos, con enfoque de uso practico:

- 70% laboratorio guiado.
- 30% teoria minima y contextual.
- Sin necesidad de experiencia previa en DevOps.

## Objetivo del repositorio

Este repositorio esta preparado para que cada alumno:

1. Haga un fork.
2. Lance un Codespace.
3. Complete los labs paso a paso con instrucciones claras.

## Estructura

- `docs/modulos/`: guias por modulo (teoria breve + practica).
- `docs/guias/`: hojas de ruta para alumnos.
- `docs/instructor/`: notas para dinamica de sesiones.
- `labs/`: libro de practicas (cada `README.md` es un capitulo con pasos, comandos, YAML y **creacion explicita de ficheros** bajo `labs/*/trabajo/`).
- `plantillas/`: base reusable para Dockerfile, Compose y Kubernetes.
- `soluciones/`: referencia para instructor (o publicacion diferida).
- `assets/datasets/`: datasets de ejemplo.

## Ruta de aprendizaje

1. `labs/00-prework`: validacion de entorno.
2. `labs/01-docker-basico`: contenedores, imagenes, volumenes.
3. `labs/02-docker-analitica`: Compose con API FastAPI + PostgreSQL + ETL batch.
4. `labs/03-k8s-basico`: Pods, Deployments, Services, imagenes, GHCR o kind load, Kustomize base/overlays.
5. `labs/04-k8s-analitica`: Jobs, CronJobs, ConfigMaps, Secrets.
6. `labs/05-proyecto-final`: flujo end-to-end guiado.

## Modo de trabajo recomendado para alumnos

- Leer objetivo del lab.
- Ejecutar pasos guiados.
- Validar con checklist.
- Entregar evidencia minima (captura/log/comando).

## GitHub y Codespaces

Repositorio oficial del curso:

- https://github.com/davidpestana/docker-k8s-101

Flujo recomendado para alumnos:

1. Entrar al repo y pulsar `Fork`.
2. Abrir el fork y pulsar `Code` -> `Codespaces` -> `Create codespace on main`.
3. Ejecutar `labs/00-prework` para completar el setup del entorno.

Nota: `kubectl`, `kind` y `helm` se instalan durante el setup para reforzar aprendizaje practico.

## Empezar ahora (flujo guiado)

Pulsa aqui para comenzar directamente:

- [Abrir libro de practicas](labs/README.md)
- [Siguiente: Capitulo 1 - Setup](labs/00-prework/README.md)
