# Step 02 - Interactividad, volúmenes y puertos

## Tareas

```bash
docker run --rm -it busybox
docker run --name nginx --rm -d -p 9000:80 nginx
docker ps
docker run --name persisted-mysql -v ./data/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:5.7
```

## Validación

- Identifica el mapeo de puertos de nginx.
- Verifica que existe `./data/mysql` en el host.

## Limpieza

```bash
docker rm -f nginx persisted-mysql
```

## Siguiente

- [step-03-dockerfile-build-compose.md](step-03-dockerfile-build-compose.md)
## Navegacion del libro

- [Anterior](step-01-ejecutar-y-listar-contenedores.md)
- [Siguiente](step-03-dockerfile-build-compose.md)
