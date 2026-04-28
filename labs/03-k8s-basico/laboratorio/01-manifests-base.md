# Step 01 - Crear manifiestos base

## Objetivo del step

Definir recursos Kubernetes mínimos para API y base de datos en un namespace dedicado.

## Qué construyes

- Namespace
- Secret para PostgreSQL
- Deployment + Service de PostgreSQL
- Secret de aplicación
- Deployment + Service de API

## Fundamento del step

Este step no es solo ejecución de comandos: su objetivo es construir criterio técnico. Cada acción busca evitar errores frecuentes de entorno, de configuración o de integración entre servicios. Antes de avanzar, asegúrate de entender qué problema resuelve cada bloque.

## Ejecución guiada

### 1) Crear estructura de manifiestos

```bash
mkdir -p labs/03-k8s-basico/trabajo/k8s/manifests
```

### 2) Crear archivos YAML

Crea los archivos en `manifests/` según los capítulos del módulo.

### 3) Comprobar sintaxis básica

```bash
ls -1 labs/03-k8s-basico/trabajo/k8s/manifests
```

## Qué validas y qué debes ver

- Están los archivos `00` a `04` definidos.
- Todos tienen `apiVersion`, `kind` y `metadata`.

## Errores comunes

- Namespace omitido en recursos namespaced.
- Secret con claves mal escritas (`POSTGRES_USER`, etc.).

## Reto

Añade una etiqueta común `course: docker-k8s-101` en todos los recursos.

## Solución del reto

En cada YAML, dentro de `metadata`:

```yaml
labels:
  course: docker-k8s-101
```

## Navegacion del libro

- [Anterior](../03-kustomize-base-overlays.md)
- [Siguiente](02-deploy-y-validar.md)
