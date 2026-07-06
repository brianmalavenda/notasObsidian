tipo::
tags::
fecha:: 2026-07-02
links::
links_externos::

### Detectamos un uso del 80% de RAM asignada a la VM-APP y hicimos algunas revisiones

```bash
free -h
total       usado       libre    búf/caché    disponible
5.8Gi       1.3Gi       1.6Gi      3.1Gi         4.4Gi
```

Lo que importa acá es la columna **"disponible" (4.4Gi)**, no "usado". El **"búf/caché" de 3.1GB es memoria que Linux usa para cachear archivos leídos del disco** (page cache) — la toma porque está libre y no la está usando nadie más, pero la **libera instantáneamente** apenas una aplicación la necesita. No es memoria "ocupada" en el sentido de que le falte RAM al sistema.

De hecho, el **uso real de procesos es de apenas 1.3GB**. Eso es sanísimo para lo que estás corriendo.

**Lo que probablemente te confundió:** el gráfico de resumen de Proxmox (el que ves en el dashboard del nodo/VM) suele mostrar `usado + buff/caché` como "memoria usada" total, lo cual infla visualmente el número y da la sensación de que estás quedándote sin RAM cuando en realidad no es así. Es un problema clásico de interpretación, no técnico.

Tu VM está en un estado saludable: **1.3GB de uso real sobre 5.8GB totales**, con margen de sobra para tus 50 usuarios. El "problema" que veías era un efecto de cómo Proxmox reporta la memoria en el dashboard, no un leak ni sobreconsumo real. Los cambios que hiciste en AdGuard (sacar blocklists duplicadas) igual valieron la pena — bajaste de 1.45GB a 423MB en ese contenedor puntual, que es una mejora real aunque no fuera la causa del "4.5GB" que veías.

---
#### Referencias

