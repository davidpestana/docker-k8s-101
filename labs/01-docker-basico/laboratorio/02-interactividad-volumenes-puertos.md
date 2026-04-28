# Step 02 - Interactividad, volumenes y puertos

## Fundamento del step

Este step no es solo ejecución de comandos: su objetivo es construir criterio técnico. Cada acción busca evitar errores frecuentes de entorno, de configuración o de integración entre servicios. Antes de avanzar, asegúrate de entender qué problema resuelve cada bloque.

## Descripcion del laboratorio

En este paso vas a ver cómo interactuar con contenedores de forma directa (`-it`), cómo publicar puertos hacia el anfitrión y cómo persistir datos en directorios del host usando montajes.

## Qué haces

1. Ejecutas `busybox` en modo interactivo.
2. Levantas `nginx` en segundo plano mapeando puerto `9000:80`.
3. Levantas `mysql` con volumen persistente y variable de entorno.

```bash
docker run --rm -it busybox
docker run --name nginx --rm -d -p 9000:80 nginx
docker ps
docker run --name persisted-mysql -v ./data/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:5.7
```

## Qué validas y qué debes ver

- En `docker ps`, `nginx` aparece en estado `Up` y con mapeo `0.0.0.0:9000->80/tcp`.
- El contenedor `persisted-mysql` aparece levantado y con volumen asociado.
- En el host se crea el directorio `./data/mysql`.
- En modo interactivo con `busybox`, puedes escribir comandos y salir con `exit`.

## Limpieza

```bash
docker rm -f nginx persisted-mysql
```

## Reto

Arranca un contenedor `nginx` en `9010:80`, verifica que responde y luego deténlo sin borrarlo. Después vuelve a iniciarlo con `docker start`.

## Solucion del reto

```bash
docker run --name nginx-reto -d -p 9010:80 nginx
docker ps
curl -I http://localhost:9010
docker stop nginx-reto
docker start nginx-reto
docker rm -f nginx-reto
```

## Navegacion del libro

- [Anterior](01-ejecutar-y-listar-contenedores.md)
- [Siguiente](03-dockerfile-build-compose.md)
