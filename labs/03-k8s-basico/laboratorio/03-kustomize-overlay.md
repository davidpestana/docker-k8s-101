# Step 03 - Overlay declarativo con Kustomize

## Objetivo del step

Aplicar una variacion del mismo despliegue sin duplicar YAML, usando `base` + `overlay`.

## Fundamento del step

Esto conecta con el tutorial oficial (misma app desplegada) y lo lleva a un escenario real:
mantener una unica base y modificar solo lo necesario por entorno.

## Ejecución guiada

### 1) Crear estructura Kustomize

```bash
mkdir -p labs/03-k8s-basico/trabajo/k8s/base
mkdir -p labs/03-k8s-basico/trabajo/k8s/overlays/local
```

### 2) Crear base con recursos del step anterior

Archivo: `labs/03-k8s-basico/trabajo/k8s/base/kustomization.yaml`

```yaml
resources:
  - ../manifests/00-namespace.yaml
  - ../manifests/01-deployment.yaml
  - ../manifests/02-service.yaml
```

### 3) Crear overlay local para ajustar replicas

Archivo: `labs/03-k8s-basico/trabajo/k8s/overlays/local/kustomization.yaml`

```yaml
resources:
  - ../../base

replicas:
  - name: kubernetes-bootcamp
    count: 3
```

### 4) Aplicar overlay y validar resultado

```bash
kubectl apply -k labs/03-k8s-basico/trabajo/k8s/overlays/local
kubectl -n k8s-basics get deployment kubernetes-bootcamp -o yaml | rg "replicas:"
```

## Qué validas y qué debes ver

- `kubectl apply -k` aplica namespace, deployment y service.
- El deployment queda con `replicas: 3` aunque en manifiesto base fuera distinto.

## Errores comunes

- Ruta incorrecta en `resources`.
- Nombre mal escrito en bloque `replicas.name`.

## Reto

Crea overlay `dev` con 1 replica y cambia la etiqueta comun `course` a `course: docker-k8s-101-dev`.

## Solución del reto

Crear `labs/03-k8s-basico/trabajo/k8s/overlays/dev/kustomization.yaml`:

```yaml
resources:
  - ../../base

replicas:
  - name: kubernetes-bootcamp
    count: 1

commonLabels:
  course: docker-k8s-101-dev
```

Aplicar:

```bash
kubectl apply -k labs/03-k8s-basico/trabajo/k8s/overlays/dev
```

## Navegacion del libro

- [Anterior](02-deploy-y-validar.md)
- [Siguiente](../../04-k8s-analitica/README.md)
