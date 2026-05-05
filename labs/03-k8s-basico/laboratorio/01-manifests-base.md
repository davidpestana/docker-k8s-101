# Step 01 - Crear manifiestos base (Deployment + Service)

## Objetivo del step

Construir el despliegue basico del tutorial oficial de Kubernetes con enfoque declarativo:

- `Namespace`
- `Deployment`
- `Service` tipo `NodePort`

## Fundamento del step

Este step traduce el "deploy app" del tutorial oficial a trabajo profesional con YAML versionado.
No usamos comandos imperativos (`kubectl run`, `kubectl expose`) porque en equipos reales interesa tener manifiestos auditables y repetibles.

## Ejecución guiada

### 1) Crear estructura de trabajo

```bash
mkdir -p labs/03-k8s-basico/trabajo/k8s/manifests
```

### 2) Crear `00-namespace.yaml`

Archivo: `labs/03-k8s-basico/trabajo/k8s/manifests/00-namespace.yaml`

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: k8s-basics
  labels:
    course: docker-k8s-101
```

### 3) Crear `01-deployment.yaml`

Archivo: `labs/03-k8s-basico/trabajo/k8s/manifests/01-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kubernetes-bootcamp
  namespace: k8s-basics
  labels:
    app: kubernetes-bootcamp
    course: docker-k8s-101
spec:
  replicas: 2
  selector:
    matchLabels:
      app: kubernetes-bootcamp
  template:
    metadata:
      labels:
        app: kubernetes-bootcamp
        course: docker-k8s-101
    spec:
      containers:
        - name: kubernetes-bootcamp
          image: gcr.io/google-samples/kubernetes-bootcamp:v1
          ports:
            - containerPort: 8080
          readinessProbe:
            httpGet:
              path: /
              port: 8080
            initialDelaySeconds: 5
            periodSeconds: 5
```

### 4) Crear `02-service.yaml`

Archivo: `labs/03-k8s-basico/trabajo/k8s/manifests/02-service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: kubernetes-bootcamp
  namespace: k8s-basics
  labels:
    app: kubernetes-bootcamp
    course: docker-k8s-101
spec:
  type: NodePort
  selector:
    app: kubernetes-bootcamp
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30080
```

### 5) Verificar estructura y nombres

```bash
ls -1 labs/03-k8s-basico/trabajo/k8s/manifests
```

## Qué validas y qué debes ver

- Existen los tres YAML con prefijos `00`, `01`, `02`.
- `Service.spec.selector.app` coincide con `Pod template labels.app`.
- Todos los recursos tienen `namespace: k8s-basics` excepto el propio Namespace.

## Errores comunes

- Selector del `Service` no coincide con labels del `Deployment`.
- Namespace distinto entre recursos.
- Poner `containerPort` y `targetPort` con números diferentes sin motivo.

## Reto

Añade variable de entorno `APP_ENV=training` al contenedor del Deployment.

## Solución del reto

Dentro de `containers[0]` en `01-deployment.yaml`:

```yaml
env:
  - name: APP_ENV
    value: training
```

## Navegacion del libro

- [Anterior](../03-kustomize-base-overlays.md)
- [Siguiente](02-deploy-y-validar.md)
