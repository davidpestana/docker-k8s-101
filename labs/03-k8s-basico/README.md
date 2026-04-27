# Capitulo 4/6 - Kubernetes basico (Modulo 4 del temario)

**Tiempo orientativo**: ~5h. Llevas el stack de analitica del **Capitulo 3** al cluster **kind**: primero con manifiestos YAML “planos”, luego refactor a **Kustomize** (`base/` + `overlays/`) para ver por que escala el mantenimiento. Incluye **build de imagen**, **carga a kind** y camino alternativo **registry (GHCR)**.

## Objetivo

- Traducir “contenedor en mi maquina” a **Pod/Deployment/Service**.
- Versionar imagenes (`:0.1.0`, no solo `latest`).
- Entender **por que** Kustomize: mismos manifiestos, distintos entornos sin copiar/pegar gigante.

## Navegacion rapida

- [Anterior: Capitulo 3 - Docker para analitica](../02-docker-analitica/README.md)
- [Siguiente: Capitulo 5 - Kubernetes para analitica](../04-k8s-analitica/README.md)

## Prerrequisitos

- Capitulo 1: cluster `kind` llamado `k8s-101` y `kubectl` funcionando.
- Capitulo 3: carpeta `labs/02-docker-analitica/trabajo/stack-analitica/` con API construible.

Comprueba:

```bash
kind get clusters
kubectl config current-context
kubectl get nodes
```

---

## Mapa de carpetas del alumno

Vas a crear dos fases en el mismo arbol:

```
labs/03-k8s-basico/trabajo/k8s/
  manifests/           # Fase A: YAML sueltos (aprender objetos K8s)
    00-namespace.yaml
    01-postgres-secret.yaml
    02-postgres.yaml
    03-api-config.yaml
    04-api.yaml
  base/                # Fase B: Kustomize “verdad unica”
    kustomization.yaml
    namespace.yaml
    postgres-secret.yaml
    postgres.yaml
    api-config.yaml
    api.yaml
  overlays/
    local/
      kustomization.yaml
```

Por que este orden pedagogico:

1. **Fase A** fuerza a entender cada recurso suelto (debug mas facil al principio).
2. **Fase B** introduce **DRY**: un `base` estable y overlays que solo cambian lo necesario (replicas, imagen, labels).

---

## Fase A.0 - Construir y nombrar la imagen de la API

Desde el stack del capitulo 3:

```bash
cd labs/02-docker-analitica/trabajo/stack-analitica
docker build -t analytics-api:0.1.0 ./api
```

Por que etiqueta `0.1.0`: entrenar **versionado semantico** minimo; `latest` en equipo suele ser impredecible.

### Opcion 1 (recomendada en clase) - Cargar imagen en kind sin registry

```bash
kind load docker-image analytics-api:0.1.0 --name k8s-101
```

Por que funciona: kind ejecuta contenedores en el mismo Docker host; `load` copia la imagen al cluster.

### Opcion 2 - Publicar en GHCR (flujo “real”)

Sustituye `TU_USUARIO` y el nombre de imagen si tu paquete GHCR difiere:

```bash
echo "$GITHUB_TOKEN" | docker login ghcr.io -u TU_USUARIO --password-stdin
docker tag analytics-api:0.1.0 ghcr.io/TU_USUARIO/docker-k8s-101/analytics-api:0.1.0
docker push ghcr.io/TU_USUARIO/docker-k8s-101/analytics-api:0.1.0
```

Por que: en cloud (EKS/GKE/AKS) **no** tienes `kind load`; el cluster tira de un registry.

**Nota**: si el paquete GHCR es privado, necesitas `imagePullSecrets`; en curso inicial suele bastar **paquete publico** o quedarse en **Opcion 1**.

---

## Fase A.1 - Crear `00-namespace.yaml`

**Crea** `labs/03-k8s-basico/trabajo/k8s/manifests/00-namespace.yaml`:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: analytics-lab
```

Por que Namespace: aislar recursos del laboratorio; borrar el namespace limpia todo de un golpe.

---

## Fase A.2 - Secret de Postgres

**Crea** `labs/03-k8s-basico/trabajo/k8s/manifests/01-postgres-secret.yaml`:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: postgres-secret
  namespace: analytics-lab
type: Opaque
stringData:
  POSTGRES_USER: analytics
  POSTGRES_PASSWORD: analytics
  POSTGRES_DB: analytics
```

Por que Secret y no ConfigMap para password: aunque en clase el password es debil, entrenas **separacion** credencial vs configuracion.

---

## Fase A.3 - Postgres Deployment + Service

**Crea** `labs/03-k8s-basico/trabajo/k8s/manifests/02-postgres.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
  namespace: analytics-lab
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
        - name: postgres
          image: postgres:16
          ports:
            - containerPort: 5432
          envFrom:
            - secretRef:
                name: postgres-secret
          readinessProbe:
            exec:
              command: ["sh", "-c", "pg_isready -U $POSTGRES_USER -d $POSTGRES_DB"]
            initialDelaySeconds: 5
            periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: postgres
  namespace: analytics-lab
spec:
  selector:
    app: postgres
  ports:
    - port: 5432
      targetPort: 5432
```

Por que `readinessProbe`: la API no deberia conectarse antes de que Postgres acepte conexiones (paralelo al healthcheck de Compose).

---

## Fase A.4 - ConfigMap con URL de base de datos (no secreto en URL aqui)

Para no empujar password en ConfigMap, la API puede leer usuario/password/db por env; para coincidir con el codigo del Capitulo 3 que espera `DATABASE_URL`, usamos **Secret** para la URL completa.

**Crea** `labs/03-k8s-basico/trabajo/k8s/manifests/03-api-config.yaml`:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: analytics-app-secret
  namespace: analytics-lab
type: Opaque
stringData:
  DATABASE_URL: postgresql+psycopg://analytics:analytics@postgres:5432/analytics
```

Por que host `postgres`: es el nombre DNS del **Service** dentro del mismo Namespace.

---

## Fase A.5 - API Deployment + Service

**Crea** `labs/03-k8s-basico/trabajo/k8s/manifests/04-api.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: analytics-api
  namespace: analytics-lab
spec:
  replicas: 1
  selector:
    matchLabels:
      app: analytics-api
  template:
    metadata:
      labels:
        app: analytics-api
    spec:
      containers:
        - name: api
          image: analytics-api:0.1.0
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 8000
          envFrom:
            - secretRef:
                name: analytics-app-secret
          readinessProbe:
            httpGet:
              path: /health
              port: 8000
            initialDelaySeconds: 5
            periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: analytics-api
  namespace: analytics-lab
spec:
  selector:
    app: analytics-api
  ports:
    - port: 80
      targetPort: 8000
  type: ClusterIP
```

Por que `imagePullPolicy: IfNotPresent`: coherente con imagen cargada en kind (no hay pull remoto).

---

## Fase A.6 - Aplicar y validar

```bash
kubectl apply -f labs/03-k8s-basico/trabajo/k8s/manifests/
kubectl -n analytics-lab get pods,svc
kubectl -n analytics-lab wait --for=condition=ready pod -l app=postgres --timeout=120s
kubectl -n analytics-lab wait --for=condition=ready pod -l app=analytics-api --timeout=120s
```

Port-forward para probar desde tu terminal del Codespace:

```bash
kubectl -n analytics-lab port-forward svc/analytics-api 8080:80
curl -sS http://127.0.0.1:8080/health
curl -sS http://127.0.0.1:8080/db/ping
```

**Resultado esperado**: `{"status":"ok"}` y `{"db":1}`.

**Si falla**:

- `ImagePullBackOff`: olvidaste `kind load docker-image` o el nombre/tag no coincide.
- `CrashLoopBackOff` en API: revisa logs `kubectl -n analytics-lab logs deploy/analytics-api`.
- `db/ping` falla: Postgres no listo o `DATABASE_URL` incorrecta.

---

## Fase A.7 - Escalar replicas

```bash
kubectl -n analytics-lab scale deploy/analytics-api --replicas=3
kubectl -n analytics-lab get pods -l app=analytics-api
```

Por que: entrenar el modelo mental de **Deployment** (conjunto de Pods homogeneos).

---

## Fase B - Kustomize: `base` + `overlays/local`

### Por que pasar a Kustomize

Con YAML sueltos, cuando tienes **dev/staging/prod** terminas duplicando archivos gigantes. Kustomize permite:

- `base/`: lo comun (Deployments, Services).
- `overlays/local`: variaciones (replicas, tags de imagen, labels, patches).

`kubectl` incluye Kustomize: aplica con `-k`.

### Paso B.1 - Copia logica de manifiestos a `base/`

**Crea** los mismos contenidos en `labs/03-k8s-basico/trabajo/k8s/base/` (puedes copiar los ficheros de `manifests/` y renombrar `00-namespace` a `namespace.yaml`, etc.).

**Crea** `labs/03-k8s-basico/trabajo/k8s/base/kustomization.yaml`:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - namespace.yaml
  - postgres-secret.yaml
  - postgres.yaml
  - api-config.yaml
  - api.yaml
commonLabels:
  course: docker-k8s-101
```

Por que **no** ponemos `namespace:` a nivel de Kustomization aqui: evitamos efectos secundarios con el recurso `Namespace` (cluster-scoped); mantenemos `metadata.namespace: analytics-lab` en cada objeto namespaced, como en la Fase A.

Por que `commonLabels`: etiquetado consistente para observabilidad y seleccion; en overlay puedes anadir mas.

Ajusta nombres de ficheros en `base/` para coincidir con `resources:` (copia/pega desde Fase A renombrando).

### Paso B.2 - Overlay `local` (replicas + imagen registry opcional)

**Crea** `labs/03-k8s-basico/trabajo/k8s/overlays/local/kustomization.yaml`:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - ../../base
replicas:
  - name: analytics-api
    count: 2
# Si usas GHCR, descomenta y ajusta TU_USUARIO:
# images:
#   - name: analytics-api
#     newName: ghcr.io/TU_USUARIO/docker-k8s-101/analytics-api
#     newTag: "0.1.0"
patches:
  - patch: |-
      - op: replace
        path: /spec/template/spec/containers/0/imagePullPolicy
        value: Always
    target:
      kind: Deployment
      name: analytics-api
```

Por que `replicas` en overlay: en local quieres 2-3 replicas para ver balanceo; en “minikube de bajos recursos” podrias bajar a 1 en otro overlay.

**Aplica overlay**:

```bash
kubectl apply -k labs/03-k8s-basico/trabajo/k8s/overlays/local
kubectl -n analytics-lab get pods
```

---

## Limpieza del namespace (opcional)

```bash
kubectl delete namespace analytics-lab
```

---

## Entregables

- [ ] Fase A desplegada: health y db ping OK via port-forward.
- [ ] Escalado de `analytics-api` probado.
- [ ] Fase B aplicada con overlay y replicas distintas.

---

## Navegacion

- [Anterior: Capitulo 3 - Docker para analitica](../02-docker-analitica/README.md)
- [Siguiente: Capitulo 5 - Kubernetes para analitica](../04-k8s-analitica/README.md)
