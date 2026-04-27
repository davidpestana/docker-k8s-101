# Entornos de desarrollo contenerizados

## Qué vas a aprender

- Ventajas y desventajas de contenerizar el desarrollo.
- Relación entre entorno dev y producción.
- Criterios para facilitar setup del equipo.

## Ventajas

- Reducción drástica del tiempo de setup.
- Entornos homogéneos entre desarrolladores.
- Menos conflictos de dependencias y versiones.
- Paridad alta entre desarrollo y producción.

## Desventajas

- Mayor complejidad al integrar herramientas locales del host.
- Curva inicial para depurar dentro de contenedores.
- Necesidad de diseño claro de volúmenes y puertos.

## Demo de cierre

```bash
docker ps -a
docker images | head
docker system df
```

Validación: el alumno explica qué recursos consume su entorno y cómo limpiarlo.

# LABORATORIO

- [step-01-ejecutar-y-listar-contenedores.md](laboratorio/step-01-ejecutar-y-listar-contenedores.md)
## Navegacion del libro

- [Anterior](orquestacion-con-docker-compose.md)
- [Siguiente](laboratorio/step-01-ejecutar-y-listar-contenedores.md)
