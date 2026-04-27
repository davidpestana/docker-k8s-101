# Step 03 - Dockerfile, build y compose

## Tareas

1. Crear un `Dockerfile` mínimo y construir imagen.
2. Crear `docker-compose.yml` con Postgres + cliente Python.
3. Ejecutar compose y validar conexión SQL.

```bash
docker build -t custom-ubuntu-git .
cd labs/01-docker-basico/trabajo/pg-demo
docker compose up --abort-on-container-exit
```

## Validación final

- Imagen listada con `docker images`.
- Salida `(1,)` en compose.

## Continuar

- [Ir al Modulo 2](../../02-docker-analitica/README.md)
## Navegacion del libro

- [Anterior](step-02-interactividad-volumenes-puertos.md)
- [Siguiente](../../02-docker-analitica/README.md)
