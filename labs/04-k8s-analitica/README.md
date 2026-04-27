# Capitulo 5/6 - Kubernetes para analitica (Modulo 5 del temario)

**Tiempo orientativo**: ~4h. Trabajas **batch** con **Job** y **CronJob**, separas configuracion no sensible (**ConfigMap**) de credenciales (**Secret**) y conectas con el patron de datos del Capitulo 3.

## Objetivo

- Ejecutar un ETL **una vez** (Job) y **en horario** (CronJob).
- Externalizar parametros sin rebuild de imagen (ConfigMap).
- Mantener credenciales fuera del YAML en claro cuando proceda (Secret + `kubectl`).

## Navegacion rapida

- [Anterior: Capitulo 4 - Kubernetes basico](../03-k8s-basico/README.md)
- [Siguiente: Capitulo 6 - Proyecto final guiado](../05-proyecto-final/README.md)

## Prerrequisitos

- Capitulo 4 desplegado con Postgres accesible como `postgres.analytics-lab.svc` (o el namespace que hayas usado).
- Imagen del ETL construida localmente.

Construye y carga la imagen del job (desde el stack del Capitulo 3):

```bash
cd labs/02-docker-analitica/trabajo/stack-analitica
docker build -t analytics-etl:0.1.0 ./etl
kind load docker-image analytics-etl:0.1.0 --name k8s-101
```

Por que otra imagen: el batch tiene **ciclo de vida distinto** al servidor HTTP; en K8s eso suele traducirse en **Job** en lugar de Deployment.

---

## Estructura de archivos

```
labs/04-k8s-analitica/trabajo/k8s-batch/
  00-configmap-etl.yaml
  01-secret-etl-db.yaml
  02-job-etl-once.yaml
  03-cronjob-etl.yaml
```

---

## Paso 1 - ConfigMap con parametros del batch

**Crea** `labs/04-k8s-analitica/trabajo/k8s-batch/00-configmap-etl.yaml`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: etl-config
  namespace: analytics-lab
data:
  ETL_MODE: "batch"
  LOG_LEVEL: "info"
```

Por que ConfigMap: valores **no secretos** que pueden cambiar por entorno (dev vs prod) sin reconstruir la imagen.

**Ampliacion pedagogica**: tu `etl.py` del Capitulo 3 puede leer `ETL_MODE` con `os.getenv` y cambiar comportamiento; si no quieres tocar codigo aun, el ConfigMap igualmente queda como practica de montaje.

---

## Paso 2 - Secret con URL de base de datos para el Job

**Opcion A (YAML en repo, solo clase)**: **Crea** `labs/04-k8s-analitica/trabajo/k8s-batch/01-secret-etl-db.yaml`:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: etl-db
  namespace: analytics-lab
type: Opaque
stringData:
  DATABASE_URL: postgresql+psycopg://analytics:analytics@postgres:5432/analytics
```

**Opcion B (recomendada en empresas)**: crear el Secret sin archivo en git:

```bash
kubectl -n analytics-lab create secret generic etl-db \
  --from-literal=DATABASE_URL='postgresql+psycopg://analytics:analytics@postgres:5432/analytics' \
  --dry-run=client -o yaml | kubectl apply -f -
```

Por que: reduce riesgo de filtrar credenciales en repositorios.

---

## Paso 3 - Job (ejecucion unica)

**Crea** `labs/04-k8s-analitica/trabajo/k8s-batch/02-job-etl-once.yaml`:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: etl-once
  namespace: analytics-lab
spec:
  backoffLimit: 2
  template:
    spec:
      restartPolicy: Never
      containers:
        - name: etl
          image: analytics-etl:0.1.0
          imagePullPolicy: IfNotPresent
          envFrom:
            - secretRef:
                name: etl-db
            - configMapRef:
                name: etl-config
```

Por que `restartPolicy: Never`: un Job no debe “reiniciar en bucle” como un servidor; si falla, Kubernetes respeta `backoffLimit`.

Aplicar y ver logs:

```bash
kubectl apply -f labs/04-k8s-analitica/trabajo/k8s-batch/02-job-etl-once.yaml
kubectl -n analytics-lab get jobs,pods
kubectl -n analytics-lab logs job/etl-once
```

**Resultado esperado**: log `ETL OK` (si tu `etl.py` del Capitulo 3 sigue imprimiendo eso).

**Si falla**:

- `ImagePullBackOff`: falta `kind load docker-image analytics-etl:0.1.0`.
- error SQL: Postgres no esta o la URL no resuelve (`postgres` debe ser el Service del mismo namespace).

---

## Paso 4 - CronJob (ejecucion periodica)

**Crea** `labs/04-k8s-analitica/trabajo/k8s-batch/03-cronjob-etl.yaml`:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: etl-cron
  namespace: analytics-lab
spec:
  schedule: "*/5 * * * *"
  concurrencyPolicy: Forbid
  successfulJobsHistoryLimit: 1
  failedJobsHistoryLimit: 3
  jobTemplate:
    spec:
      backoffLimit: 1
      template:
        spec:
          restartPolicy: OnFailure
          containers:
            - name: etl
              image: analytics-etl:0.1.0
              imagePullPolicy: IfNotPresent
              envFrom:
                - secretRef:
                    name: etl-db
                - configMapRef:
                    name: etl-config
```

Por que `concurrencyPolicy: Forbid`: en ETL, dos ejecuciones solapadas pueden duplicar cargas o bloquear tablas; para el curso es un buen habito.

Por que `*/5 * * * *`: cada 5 minutos (ajusta en clase si quieres ver ejecucion mas rapida; `* * * * *` es cada minuto pero puede saturar logs).

```bash
kubectl apply -f labs/04-k8s-analitica/trabajo/k8s-batch/03-cronjob-etl.yaml
kubectl -n analytics-lab get cronjobs
kubectl -n analytics-lab get jobs --watch
```

Disparo manual desde CronJob (patron operativo):

```bash
kubectl -n analytics-lab create job --from=cronjob/etl-cron etl-manual-$(date +%s)
kubectl -n analytics-lab logs job/etl-manual-XXXX
```

---

## Paso 5 - (Opcional) Patch en overlay Kustomize

Si ya tienes `overlays/local` del Capitulo 4, puedes anadir estos manifiestos al `base` o crear `overlays/batch` que referencie los mismos recursos; lo importante pedagogico es: **misma app**, distinto **controlador** (Deployment vs Job/CronJob).

---

## Limpieza

```bash
kubectl -n analytics-lab delete cronjob/etl-cron job/etl-once --ignore-not-found
kubectl -n analytics-lab delete configmap/etl-config secret/etl-db --ignore-not-found
```

---

## Entregables

- [ ] Job `etl-once` completa con logs correctos.
- [ ] CronJob creado; observas Jobs hijos en marcha.
- [ ] ConfigMap montado en pods del batch (verifica con `kubectl describe pod`).

---

## Navegacion

- [Anterior: Capitulo 4 - Kubernetes basico](../03-k8s-basico/README.md)
- [Siguiente: Capitulo 6 - Proyecto final guiado](../05-proyecto-final/README.md)
