# Step 01 - Esqueleto final

## Objetivo del step

Preparar la estructura final del proyecto para integrar API, ETL, Compose y Kubernetes en una entrega única.

## Fundamento del step

Este step no es solo ejecución de comandos: su objetivo es construir criterio técnico. Cada acción busca evitar errores frecuentes de entorno, de configuración o de integración entre servicios. Antes de avanzar, asegúrate de entender qué problema resuelve cada bloque.

## Ejecución guiada

### 1) Crear estructura principal

```bash
mkdir -p labs/05-proyecto-final/trabajo/proyecto-final/{api,etl,k8s/base,k8s/overlays/local}
```

### 2) Crear archivos mínimos de entrada

```bash
touch labs/05-proyecto-final/trabajo/proyecto-final/docker-compose.yml
touch labs/05-proyecto-final/trabajo/proyecto-final/README.md
```

### 3) Verificar árbol de trabajo

```bash
ls -R labs/05-proyecto-final/trabajo/proyecto-final
```

## Qué validas y qué debes ver

- Carpetas `api`, `etl`, `k8s/base`, `k8s/overlays/local` creadas.
- Archivos base presentes para comenzar integración.

## Errores comunes

- Crear rutas fuera de `trabajo/proyecto-final`.
- Olvidar carpeta `overlays/local`.

## Reto

Añade carpeta `k8s/overlays/batch` para separar configuración de procesos batch.

## Solución del reto

```bash
mkdir -p labs/05-proyecto-final/trabajo/proyecto-final/k8s/overlays/batch
```

## Navegacion del libro

- [Anterior](../03-criterios-de-entrega.md)
- [Siguiente](02-despliegue-final.md)
