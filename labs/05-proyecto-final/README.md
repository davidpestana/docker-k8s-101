# Capitulo 6/6 - Integracion real y proyecto final (Modulo 6 del temario)

**Tiempo orientativo**: ~3h. Cierras el curso con un **flujo completo** alineado al temario: entorno local reproducible, imagenes versionadas, despliegue en kind y reflexion sobre cloud.

## Objetivo

- Repetir el circuito **Dockerfile -> imagen -> (registry opcional) -> Kubernetes**.
- Documentar **por que** cada decision de estructura (carpetas, base/overlays, Secret vs ConfigMap).
- Dejar un mini-proyecto **entregable** en `labs/05-proyecto-final/trabajo/`.

## Explicacion teorica

Lee este bloque con el grupo para fijar conceptos y vocabulario antes de tocar comandos.

## Demo del profesor

El profesor ejecuta el recorrido completo una vez, explica errores tipicos y marca que validar en cada salida.

## Practica guiada

Sigue exactamente los pasos de este capitulo en orden, sin saltar bloques.

## Flujo de este capitulo (sin bifurcaciones)

1. Explicacion teorica
2. Demo del profesor
3. Practica guiada
4. Cierre y entrega

**Final del recorrido**: [Volver al indice del libro](../README.md)

---

## Enunciado del proyecto final (minimo viable)

Construye bajo `labs/05-proyecto-final/trabajo/proyecto-final/` un proyecto con esta forma:

```
proyecto-final/
  README.md                 # tu documentacion breve
  api/                      # copia mejorada del Capitulo 3 (o nuevo endpoint /metrics)
  etl/                      # batch que alimenta la misma BD
  docker-compose.yml        # entorno local completo
  k8s/
    base/                   # Kustomize base (API + Postgres + Service + Secret)
    overlays/
      local/                # replicas, labels, imagen GHCR opcional
      batch/                # Job/CronJob + ConfigMap (puede ser overlay separado)
```

### Requisitos funcionales

1. `docker compose up` levanta **Postgres + API**.
2. Existe al menos un **endpoint** HTTP documentado en tu `README.md` (`/health` minimo).
3. Imagen de API etiquetada **`proyecto-api:0.1.0`** (o semver equivalente).
4. `kind load docker-image proyecto-api:0.1.0 --name k8s-101` y despliegue exitoso con **Kustomize** (`kubectl apply -k ...`).
5. Un **Job** ETL que escriba datos que luego puedas leer via API (puedes anadir `GET /rows` simple) o justificar en README la query manual con `kubectl exec` a Postgres (menos ideal).

### Requisitos de buenas practicas (explicar en tu README)

- **Versionado de imagenes**: prohibido usar solo `latest` en el YAML final; usa tag semver.
- **Separacion ConfigMap/Secret**: no passwords en ConfigMap.
- **Por que Kustomize**: que cambia entre `overlays/local` y otro overlay ficticio `prod` (aunque no despliegues prod).

---

## Guion paso a paso (para que el alumno no se pierda)

### Paso 1 - Copia de partida honesta

Puedes partir del directorio `labs/02-docker-analitica/trabajo/stack-analitica/`; no reinventes todo: el objetivo es **integrar y documentar**, no escribir microservicios.

### Paso 2 - Endurece el contrato de configuracion

- Centraliza variables en `.env` para Compose.
- En Kubernetes, usa **Secret** para cadena de conexion o usuario/clave.

### Paso 3 - Build y prueba local

```bash
docker compose up --build
curl http://localhost:8000/health
```

### Paso 4 - Imagen y kind

```bash
docker build -t proyecto-api:0.1.0 ./api
kind load docker-image proyecto-api:0.1.0 --name k8s-101
```

### Paso 5 - Registry GHCR (opcional pero recomendado como ejercicio)

Documenta en tu README los comandos exactos que usaste:

```bash
echo "$GITHUB_TOKEN" | docker login ghcr.io -u TU_USUARIO --password-stdin
docker tag proyecto-api:0.1.0 ghcr.io/TU_USUARIO/docker-k8s-101/proyecto-api:0.1.0
docker push ghcr.io/TU_USUARIO/docker-k8s-101/proyecto-api:0.1.0
```

Relaciona con EKS/GKE/AKS: el cluster en cloud **descarga** la imagen desde un registry; kind es el atajo local.

### Paso 6 - Kustomize

- `base/`: Deployments, Services, Namespace, Secrets.
- `overlays/local`: replicas, imagen, labels de entorno.

### Paso 7 - Batch

- Job de carga inicial + CronJob de refresco (aunque el CronJob sea cada hora en “produccion ficticia”).

---

## Rubrica rapida (autoevaluacion)

| Criterio | Peso |
|---------|------|
| Compose funcional | 25% |
| Imagen versionada + carga en kind | 25% |
| Kustomize base + overlay | 25% |
| Job/CronJob + Config/Secret | 25% |

---

## Cierre

- [Volver al indice del libro](../README.md)
