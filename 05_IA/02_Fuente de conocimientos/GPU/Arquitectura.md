tipo::
tags::
fecha:: 2026-09-24
links::
links_externos::

#### pytorch

Depende de la versión de pytorch el soporte de arquitectura tipo CUDA disponible para poder correr modelos sobre GPUs determinadas. Por ejemplo, en mi TESLA P4 que tiene una arquitectura tipo PASCAL `sm_61`la versión de pytorch más reciente compiladas para CUDA 12.6 por defecto no contiene a CUDA 11.8 que sí soportaba `sm_61`**PyTorch 2.5.1 (o 2.4.1)**.

~~~bash
uv run python -c "import torch; print('CUDA disponible:', torch.cuda.is_available()); print('Archs soportadas:', torch.cuda.get_arch_list()); print('Dispositivo:', torch.cuda.get_device_name(0))"
~~~

---
#### Referencias

