# Interaccion con anfitrion: volumenes, puertos y variables de entorno

## Qué vas a aprender

- Montar directorios y persistir datos.
- Parametrizar contenedores con variables de entorno.
- Publicar puertos hacia el host.

## Referencia oficial

![Docker Moby logo](https://www.docker.com/app/uploads/2022/03/Moby-logo.png)

Fuente: [Docker Docs - Volumes](https://docs.docker.com/engine/storage/volumes/)

## Casos prácticos

### Variables de entorno

```bash
docker run --name custom-mysql -v ./custom:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:5.7
docker run --name persisted-mysql -v ./data/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:5.7
```

Validación: identifica qué contenedor usa volumen de configuración y cuál de datos persistentes.

### Publicar puertos

```bash
docker run --name nginx --rm -d -p 9000:80 nginx
docker ps
```

Validación: comprueba `0.0.0.0:9000->80/tcp` y abre el servicio en el puerto 9000 del Codespace.

### Limpieza

```bash
docker rm -f custom-mysql persisted-mysql nginx
```

## Continuar

- [04-dockerfile-y-personalizacion-de-imagenes.md](04-dockerfile-y-personalizacion-de-imagenes.md)
## Navegacion del libro

- [Anterior](02-operativa-basica-de-contenedores.md)
- [Siguiente](04-dockerfile-y-personalizacion-de-imagenes.md)
