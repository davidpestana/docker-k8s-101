# Capitulo 1/6 - Fundamentos y prework tecnico

Alineado al **Modulo 1 (2h)** y al inicio del **Modulo 2** del temario: primero ideas claras, luego instalacion validada paso a paso.

## Flujo de este capitulo (sin bifurcaciones)

1. Explicacion teorica
2. Demo del profesor
3. Practica guiada
4. Siguiente capitulo

**Siguiente directo**: [Capitulo 2/6 - Docker basico](../01-docker-basico/README.md)

---

## Parte A - Fundamentos (sin comandos, ~2h con tu explicacion)

### A.1 Que es un contenedor (y que no es)

Un **contenedor** es un proceso (o conjunto de procesos) aislado que comparte el kernel del sistema operativo del host, pero con su propio sistema de archivos raiz, usuarios, red y variables de entorno empaquetados en una **imagen**.

No es una maquina virtual completa: **no hay otro kernel**. Por eso son ligeros y rapidos.

### A.2 Contenedor frente a maquina virtual

| VM | Contenedor |
|----|------------|
| Hipervisor + kernel invitado | Comparte kernel del host |
| Arranque lento, mas RAM/CPU | Arranque rapido, menos overhead |
| Muy aislado | Aislado a nivel de proceso + namespaces/cgroups |

En analitica, los contenedores resuelven sobre todo **reproducibilidad** y **portabilidad** del entorno (librerias, version de Python, drivers).

### A.3 Problemas reales en analitica

- **"En mi maquina funciona"**: distinta version de Python, de `pandas`, del sistema.
- **Dependencias nativas** dificiles de instalar en Windows/macOS/Linux mezclados.
- **Entregas**: un notebook sin entorno declarado no es reproducible.

Docker resuelve esto empaquetando **codigo + dependencias + sistema minimo** en una imagen versionada.

### A.4 Docker y Kubernetes en una frase cada uno

- **Docker**: construir, ejecutar y distribuir **aplicaciones en contenedores** en una sola maquina (o en varias con orquestacion externa).
- **Kubernetes (K8s)**: orquestador que decide **donde** corren los contenedores, **cuantas** replicas hay, **como** se exponen por red y **como** se reinician si fallan.

Este curso es de **uso**: tu construyes imagen, despliegas y consumes el cluster; no administracion de cluster.

### A.5 Mapa mental del resto del libro

1. **Capitulo 2**: una sola app en Docker (imagen, volumen, red).
2. **Capitulo 3**: varios servicios con Compose (API + BD + ETL).
3. **Capitulo 4**: mismo servicio en K8s; imagen en registry; manifiestos; **Kustomize** base + overlay.
4. **Capitulo 5**: batch (Job/CronJob), configuracion (ConfigMap/Secret).
5. **Capitulo 6**: cierre end-to-end con buenas practicas de versionado.

Checkpoint mental: si la Parte A esta clara, continua con la Parte B.

---

## Parte B - Prework tecnico (instalacion y validacion)

### Paso B.1 - Verificar Docker y Compose

Por que: Docker es la base sobre la que `kind` crea nodos Kubernetes como contenedores.

```bash
docker --version
docker compose version
docker ps
```

**Resultado esperado**: los tres comandos responden sin error.

**Si falla**: en Codespaces, confirma que el devcontainer tiene Docker-in-Docker; recrea el Codespace si hiciste cambios manuales en el socket.

### Paso B.2 - Instalar kubectl

Por que: `kubectl` es el cliente para hablar con la API de Kubernetes.

```bash
curl -LO "https://dl.k8s.io/release/$(curl -Ls https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client
```

**Resultado esperado**: ves la version del cliente.

### Paso B.3 - Instalar kind

Por que: cluster K8s local sin proveedor cloud; los nodos son contenedores.

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.23.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
kind --version
```

### Paso B.4 - Instalar Helm

Por que: en capitulos avanzados puedes instalar charts; aqui solo comprobamos que el entorno es estandar.

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
helm version
```

### Paso B.5 - Crear cluster local con kind

```bash
kind create cluster --name k8s-101
kubectl cluster-info
kubectl get nodes
```

**Resultado esperado**: al menos un nodo `Ready`.

**Si falla**: `docker ps` debe mostrar contenedores de kind; si el nombre de cluster ya existe, usa `kind delete cluster --name k8s-101` y repite.

### Paso B.6 - Test rapido de despliegue

```bash
kubectl create deployment hello-nginx --image=nginx:1.27
kubectl expose deployment hello-nginx --port=80 --target-port=80 --type=ClusterIP
kubectl get deploy,pods,svc
```

### Paso B.7 - Limpieza antes del Capitulo 2

```bash
kubectl delete service hello-nginx
kubectl delete deployment hello-nginx
```

---

## Comprobacion final (checklist)

- [ ] Parte A entendida (contenedor vs VM, problemas de analitica, rol de Docker y K8s).
- [ ] Docker y Compose operativos.
- [ ] kubectl, kind y helm instalados.
- [ ] Cluster `k8s-101` creado y nodo `Ready`.
- [ ] Test `hello-nginx` creado y borrado.

---

## Siguiente

- [Capitulo 2/6 - Docker basico](../01-docker-basico/README.md)
