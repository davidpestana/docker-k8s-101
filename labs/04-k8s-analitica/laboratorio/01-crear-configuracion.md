# Step 01 - Crear configuración externa

## Objetivo del step

Definir configuración no sensible en ConfigMap y credenciales en Secret para separar responsabilidades operativas.

## Fundamento del step

Este step no es solo ejecución de comandos: su objetivo es construir criterio técnico. Cada acción busca evitar errores frecuentes de entorno, de configuración o de integración entre servicios. Antes de avanzar, asegúrate de entender qué problema resuelve cada bloque.

## Ejecución guiada

### 1) Aplicar ConfigMap

```bash
kubectl apply -f labs/04-k8s-analitica/trabajo/k8s-batch/00-configmap-etl.yaml
```

### 2) Aplicar Secret

```bash
kubectl apply -f labs/04-k8s-analitica/trabajo/k8s-batch/01-secret-etl-db.yaml
```

### 3) Verificar recursos

```bash
kubectl -n analytics-lab get configmap,secret
```

## Qué validas y qué debes ver

- `etl-config` presente en ConfigMap.
- `etl-db` presente en Secret.
- Namespace correcto (`analytics-lab`).

## Errores comunes

- Crear recursos en namespace por defecto.
- Clave `DATABASE_URL` mal escrita.

## Reto

Añade en el ConfigMap la clave `BATCH_OWNER=equipo-datos` y reaplica.

## Solución del reto

Edita `00-configmap-etl.yaml`:

```yaml
data:
  BATCH_OWNER: "equipo-datos"
```

Luego:

```bash
kubectl apply -f labs/04-k8s-analitica/trabajo/k8s-batch/00-configmap-etl.yaml
```

## Navegacion del libro

- [Anterior](../03-configmap-y-secret.md)
- [Siguiente](02-ejecutar-job.md)
