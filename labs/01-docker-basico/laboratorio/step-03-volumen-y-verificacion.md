# Step 03 - Volumen y verificación final

## Tarea

Generar `exportar.py` y persistir CSV en `datos/`:

```bash
cd labs/01-docker-basico/trabajo/mi-analitica
docker run --rm -v "$PWD/datos:/datos" mi-analitica:1.0 python -u app/exportar.py
ls -la datos
```

## Validación final

`salida.csv` existe y es legible desde el host.

## Continuar

- [Ir al siguiente módulo](../../02-docker-analitica/README.md)
