# Conceptos: imagen, contenedor y orquestacion

## Qué vas a aprender

- Qué es una imagen y qué es un contenedor.
- Diferencia entre contenedores stateless y stateful.
- Qué significa orquestar contenedores.

## Referencia visual oficial

![Docker architecture](https://docs.docker.com/get-started/images/docker-architecture.webp)

Fuente: [Docker Docs - What is a container?](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/)

## Teoría clave

### Qué es una imagen

Una imagen es una plantilla inmutable con sistema base, librerías, runtime y configuración. No ejecuta nada por sí sola.

### Qué es un contenedor

Un contenedor es una instancia en ejecución de una imagen. Tiene ciclo de vida, estado en memoria y un proceso principal.

### Stateless vs stateful

- Stateless: no depende del estado local del contenedor para seguir funcionando.
- Stateful: necesita persistencia de datos, por ejemplo bases de datos.

### Qué es orquestación

Orquestar significa gestionar de forma coordinada múltiples contenedores: arranque, dependencia, red, escalado y recuperación.

## Demo guiada

```bash
docker run --rm hello-world
docker run --name hello-world hello-world
docker ps -a
```

## Validación

- Debes ver al menos un contenedor `hello-world` terminado.
- Debes poder explicar diferencia entre imagen descargada y contenedor ejecutado.

## Continuar

- [operativa-basica-de-contenedores.md](operativa-basica-de-contenedores.md)
## Navegacion del libro

- [Anterior](README.md)
- [Siguiente](operativa-basica-de-contenedores.md)
