# Operativa basica de contenedores

## Qué vas a aprender

- Ejecutar, listar, detener, iniciar, reiniciar y eliminar contenedores.
- Entender `--rm`, nombres, IDs y estados.
- Ejecutar comandos en contenedores nuevos y en ejecución.

## Teoría + demo

### Ejecutar contenedor

```bash
docker run hello-world
docker run --name hello-world hello-world
```

Validación: identifica nombre, ID y estado final con `docker ps -a`.

### Listado básico y avanzado

```bash
docker ps
docker ps -a
docker ps --no-trunc --format '{{.Names}}\t{{.Command}}'
docker ps -a -f "name=busybox" --format 'table {{.ID}}\t{{.Status}}\t{{.Names}}'
docker ps -aq
```

Validación: explica cuándo usar `--format`, `-f` y `-q`.

### Auto-remover

```bash
docker run --rm hello-world
docker ps -a
```

Validación: el contenedor no permanece después de terminar.

### Sobrescribir comando por defecto

```bash
docker run hello-world ls -lah
docker run busybox ls -lah
```

Validación: explica por qué `busybox` sí funciona para `ls -lah`.

### Interactividad

```bash
docker run --rm -it busybox
docker run --rm -it busybox vi
```

Validación: confirma que `-it` conecta stdin/tty del anfitrión.

### Ciclo de vida

```bash
docker run --name nginx --rm -d -p 9000:80 nginx
docker stop nginx
docker start nginx
docker restart nginx
docker rm -f nginx
```

Validación: diferencia entre ejecutar (`run`) y arrancar/reiniciar (`start`/`restart`).

### Ejecutar en contenedor ya arrancado

```bash
docker run --name nginx --rm -d -p 9000:80 nginx
docker exec nginx cat /etc/nginx/nginx.conf | grep worker_processes
docker exec -it nginx bash
```

Validación: explica la diferencia entre `run` y `exec`.

## Continuar

- [interaccion-con-anfitrion-volumenes-puertos-env.md](interaccion-con-anfitrion-volumenes-puertos-env.md)
## Navegacion del libro

- [Anterior](conceptos-imagen-contenedor-orquestacion.md)
- [Siguiente](interaccion-con-anfitrion-volumenes-puertos-env.md)
