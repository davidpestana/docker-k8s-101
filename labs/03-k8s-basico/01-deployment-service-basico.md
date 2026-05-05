# Deployment y Service basico

## Objetivo

Entender los dos recursos clave para desplegar una app en Kubernetes:

- `Deployment`: declara el estado deseado de tus Pods.
- `Service`: expone esos Pods de forma estable dentro (y opcionalmente fuera) del cluster.

## Conceptos base (explicado simple)

### 1) Pod

Un `Pod` es la unidad minima ejecutable en Kubernetes. En la practica, suele contener un solo contenedor de la app.

Problema: los Pods son efimeros (pueden morir y recrearse con otra IP).

### 2) Deployment

El `Deployment` resuelve ese problema declarando:

- Qué imagen ejecutar.
- Cuantas replicas quieres.
- Qué estrategia usar para actualizar.

Kubernetes compara el estado real con el declarado y reconcilia automaticamente.

### 3) Service

El `Service` crea una IP/DNS estable para llegar a los Pods, aunque cambien.

En este modulo trabajamos con:

- `ClusterIP` (acceso interno estable).
- `NodePort` (acceso desde fuera del cluster local para pruebas).

## Modelo mental recomendado

Piensa en tres capas:

1. `Deployment` crea y mantiene Pods.
2. `Service` selecciona Pods por etiquetas (`labels`).
3. Cliente consume el `Service`, no el Pod directo.

Si no recuerdas nada mas: **el selector del Service debe coincidir con las labels del Pod**.

## Demo minima (declarativa)

```bash
kubectl apply -f labs/03-k8s-basico/trabajo/k8s/manifests/
kubectl -n k8s-basics get deploy,pods,svc
```

## Qué debes observar

- El `Deployment` alcanza replicas disponibles.
- El `Service` muestra endpoints asociados.
- Si borras un Pod, el `Deployment` lo repone.

## Continuar

- [02-imagenes-kind-y-registry.md](02-imagenes-kind-y-registry.md)

## Navegacion del libro

- [Anterior](README.md)
- [Siguiente](02-imagenes-kind-y-registry.md)
