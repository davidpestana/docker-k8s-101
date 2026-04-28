# Step 01 - Ejecutar y listar contenedores

## Descripcion del laboratorio

En este paso vas a practicar el ciclo de vida más básico de un contenedor: crear, ejecutar, listar y entender cuándo un contenedor queda detenido o se elimina automáticamente.

## Qué haces

1. Ejecutas un contenedor de prueba (`hello-world`).
2. Lo ejecutas de nuevo con nombre explícito.
3. Comparas `docker ps` frente a `docker ps -a`.
4. Ejecutas con `--rm` para ver auto-eliminación.

```bash
docker run hello-world
docker run --name hello-world hello-world
docker ps
docker ps -a
docker run --rm hello-world
docker ps -a
```

## Qué validas y qué debes ver

- `docker ps` muestra solo contenedores en ejecución.
- `docker ps -a` muestra también detenidos/finalizados.
- Tras `docker run --rm hello-world`, ese contenedor no aparece en el listado final.
- Si repites `--name hello-world` sin borrar el anterior, verás conflicto de nombre.

## Reto

Lanza un contenedor `busybox` con nombre `mi-busybox`, ejecuta `ls -lah` dentro del arranque y verifica en `docker ps -a` que terminó.

## Solucion del reto

```bash
docker run --name mi-busybox busybox ls -lah
docker ps -a -f "name=mi-busybox" --format 'table {{.Names}}\t{{.Status}}\t{{.Command}}'
```

## Navegacion del libro

- [Anterior](../06-entornos-de-desarrollo-contenorizados.md)
- [Siguiente](02-interactividad-volumenes-puertos.md)
