# Step 02 - Despliegue final

## Objetivo del step

Ejecutar despliegue integrado local + cluster para comprobar que el proyecto final es operable.

## Fundamento del step

Este step no es solo ejecución de comandos: su objetivo es construir criterio técnico. Cada acción busca evitar errores frecuentes de entorno, de configuración o de integración entre servicios. Antes de avanzar, asegúrate de entender qué problema resuelve cada bloque.

## Ejecución guiada

### 1) Entrar en proyecto final

```bash
cd labs/05-proyecto-final/trabajo/proyecto-final
```

### 2) Levantar stack local

```bash
docker compose up --build
```

### 3) Aplicar overlay en Kubernetes

```bash
kubectl apply -k k8s/overlays/local
```

## Qué validas y qué debes ver

- Servicios locales levantan sin errores críticos.
- Recursos Kubernetes se crean correctamente.
- Al menos un pod de API en `Running`.

## Errores comunes

- Overlay apunta a rutas incorrectas de `base`.
- Imagen no disponible en cluster local.

## Reto

Versiona la imagen final con tag `0.1.1` y actualiza el overlay para desplegarla.

## Solución del reto

```bash
docker build -t proyecto-api:0.1.1 ./api
kind load docker-image proyecto-api:0.1.1 --name k8s-101
```

Actualiza el manifiesto/overlay con `proyecto-api:0.1.1` y reaplica.

## Navegacion del libro

- [Anterior](01-esqueleto-final.md)
- [Siguiente](03-validacion-y-entrega.md)
