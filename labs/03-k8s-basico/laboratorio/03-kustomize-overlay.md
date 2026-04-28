# Step 03 - Aplicar overlay Kustomize

## Objetivo del step

Aplicar configuración por entorno usando `base` + `overlays/local` y validar diferencias sin duplicar manifiestos.

## Fundamento del step

Este step no es solo ejecución de comandos: su objetivo es construir criterio técnico. Cada acción busca evitar errores frecuentes de entorno, de configuración o de integración entre servicios. Antes de avanzar, asegúrate de entender qué problema resuelve cada bloque.

## Ejecución guiada

### 1) Verificar estructura Kustomize

```bash
ls -R labs/03-k8s-basico/trabajo/k8s
```

### 2) Aplicar overlay local

```bash
kubectl apply -k labs/03-k8s-basico/trabajo/k8s/overlays/local
kubectl -n analytics-lab get pods
```

### 3) Verificar ajuste esperado (réplicas)

```bash
kubectl -n analytics-lab get deploy analytics-api -o yaml | rg "replicas:"
```

## Qué validas y qué debes ver

- Overlay aplicado sin errores.
- Réplicas y/o parches del overlay reflejados en el deployment.

## Errores comunes

- Ruta incorrecta en `resources` del overlay.
- `kustomization.yaml` con sintaxis inválida.

## Reto

Crea un segundo overlay `dev` con 1 sola réplica para `analytics-api`.

## Solución del reto

1) Crear `overlays/dev/kustomization.yaml` apuntando a `../../base`.
2) Definir:

```yaml
replicas:
  - name: analytics-api
    count: 1
```

3) Aplicar:

```bash
kubectl apply -k labs/03-k8s-basico/trabajo/k8s/overlays/dev
```

## Navegacion del libro

- [Anterior](02-deploy-y-validar.md)
- [Siguiente](../../04-k8s-analitica/README.md)
