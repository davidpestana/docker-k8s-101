# Dockerfile y personalizacion de imagenes

## Qué vas a aprender

- Escribir un Dockerfile.
- Construir imágenes con y sin nombre.
- Entender capas y orden de instrucciones.

## Teoría breve

Un `Dockerfile` define cómo se construye una imagen. Cada instrucción crea una capa cacheable.

Ejemplo base:

```dockerfile
FROM ubuntu:latest
RUN apt-get update
RUN apt-get -qqy install git
```

## Demo guiada

### Crear archivo

Crea `Dockerfile` con el contenido anterior en tu carpeta de trabajo.

### Build sin nombre útil

```bash
docker build -t temp-image .
```

### Build con nombre útil

```bash
docker build -t custom-ubuntu-git .
```

### Validación

```bash
docker images | head
```

Valida que existe `custom-ubuntu-git`.

## Continuar

- [orquestacion-con-docker-compose.md](orquestacion-con-docker-compose.md)
## Navegacion del libro

- [Anterior](interaccion-con-anfitrion-volumenes-puertos-env.md)
- [Siguiente](orquestacion-con-docker-compose.md)
