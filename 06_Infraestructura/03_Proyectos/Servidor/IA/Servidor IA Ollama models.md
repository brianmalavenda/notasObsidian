tipo::
tags:: #ia #ollama #opencode #servidor 
fecha:: 2026-08-14
links::
links_externos::

### Cómo funciona la conexión

Ollama expone una API compatible con OpenAI en localhost:11434, y OpenCode se comunica con ella vía el endpoint /v1 — no la API nativa de Ollama. Eso significa que tu VM no necesita nada adicional para servir a OpenCode: el mismo Ollama que vas a usar para DeepSeek-OCR ya sirve como backend de código. Lo único que cambia es **a qué red escucha** el servicio. [CloudVyn](https://www.cloudvyn.com/blog/how-to-use-opencode-with-ollama)

#### 1. Exponer Ollama más allá de localhost

Por defecto Ollama solo escucha en `127.0.0.1`, así que si no tocás nada, tu PC de administración no lo va a poder alcanzar. En la VM:

bash

```bash
sudo systemctl edit ollama
```

Agregá:

```ini
[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
```

```bash
sudo systemctl restart ollama
```

Esto lo abre a toda la interfaz de red de la VM, no solo localhost. Dado que ya tenés VLANs segmentadas (ADMIN en la 17, DMZ en la 37), yo pondría esta VM en tu VLAN ADMIN — es tráfico interno de desarrollo, no algo que quieras exponer a Internet ni siquiera pasar por NPM/Cloudflare.

#### 2. Configurar OpenCode en tu PC de administración

El formato de configuración usa un provider con adaptador OpenAI-compatible, apuntando a la IP de tu VM en vez de localhost: [Ollama](https://docs.ollama.com/integrations/opencode)

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "ollama-vm": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Ollama VM homelab",
      "options": {
        "baseURL": "http://192.168.17.X:11434/v1"
      },
      "models": {
        "deepseek-ocr": {}
      }
    }
  }
}
```

Ese archivo va en `~/.config/opencode/opencode.json` en tu PC. Después corrés `/models` dentro de OpenCode y elegís el modelo de tu VM.

---
#### Referencias

