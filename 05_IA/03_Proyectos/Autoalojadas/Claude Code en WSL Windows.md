tipo::
tags::
fecha:: 2026-03-19
links::
links_externos::

Vamos a instalar claude code y ollama en la virtualización de linux en Windows para poder ejecutar modelos de inteligencia artificial de manera que podamos revisar código e infraestructura de los proyectos que tenemos dentro de la carpeta Repositorios en la virtualización.

Para eso, primero instalamos [Claude Code](https://code.claude.com/docs/es/setup) desde allí podemos supervisar estos pasos pero vamos a seguir esta guía personalizada.

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

Una vez hecho esto, la guía dice que navegues a otra carpeta directamente pero primero lo que vamos a hacer es agregar el path del ejecutable a las rutas del sistema, o mejor dicho a las variables de ambiente donde allí estan las rutas a las aplicaciones. El sistema no encuentra el ejecutable si no se encuentra allí, por eso puede que surja un error donde aclare eso 

>[!ERROR]
claude se instaló (`~/.local/bin`) pero no está en el `PATH`

para resolver eso ejecutamos la siguiente línea de código


```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc && source ~/.bashrc
```

y verificamos que se haya hecho bien haciendo un ```echo $PATH``` donde mostraría la ruta de instalación de claude.

Una vez hecho esto descargamos [Ollama]([https://ollama.com/download/linux](https://docs.ollama.com/linux)) . Lo que hay que hacer el pegar el comando que aparece allí en la consola.

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

para verficar que esta ok, hacemos esta consulta ```curl http://localhost:11434/api/tags``` para verificar que Ollama este activo. Debe responder con una lista vacía de modelos instalados.

Luego vamos a **integrar** Ollama con Claude Code. Antes de ver la documentación de la integración https://docs.ollama.com/integrations/claude-code vamos a movernos de carpeta al directorio con el que vamos a trabajar y configurar nuestro claude.

Una vez alli en mi workdirectoy vamos a configurar a claude para conectarse con ollama seteando unas variables de su configuración. 

```bash
export ANTHROPIC_AUTH_TOKEN=ollama
export ANTHROPIC_API_KEY=""
export ANTHROPIC_BASE_URL=http://localhost:11434
```

el problema con esto es que una vez que cierre la terminal esto se borrará, por lo tanto debemos ejecutarlo de la siguiente manera

```bash
echo 'export ANTHROPIC_AUTH_TOKEN=ollama' >> ~/.bashrc
echo 'export ANTHROPIC_API_KEY=""' >> ~/.bashrc
echo 'export ANTHROPIC_BASE_URL=http://localhost:11434' >> ~/.bashrc
source ~/.bashrc
```

Luego podemos ver la guia de Ollama más arriba o coninuar más directamente con estos pasos. En mi workdirectoy ejecutamos ```ollama launch claude``` y allí me apareceran modelos para utilizar. Vamos a seleccionar uno chico adaptado a los recursos de nuestro equipo o podemos seleccionar un modelo con la palabra *cloud* al final que son aquellos que se ejecutan directamente en los servidores de Anthropic.

>[!NOTA]
>Si seleccionamos un modelo "cloud" nos va a pedir que naveguemos a una ruta de conexion con Ollama donde nos authenticaremos para validar la interacción con la API del servicio

Cuando seleccionamos un modelo que es muy grande para nuestro equipo o directamente nos avisará el propio cliente de claude que no es posible ejecutarlo porque no disponemos de RAM suficiente o lo ejecutará pero tardará varios minutos en responder haciendolo inutil para trabajar.

Si no estamos utilizando el servicio de Ollama, es mejor mantenerlo parado y deshabilitado hasta su uso.

```bash
sudo systemctl status ollama # nos dira "enable"
sudo systemctl stop ollama
sudo systemctl disable ollama
```

Cuando querramos iniciarlo simplemente ejecutamos: 

```bash
sudo systemctl status ollama # nos dira "disable"
ollama serve # inicia el servidor
sudo systemctl enable ollama
sudo systemctl status ollama # debe indicar "enable"
```

---
#### Referencias

