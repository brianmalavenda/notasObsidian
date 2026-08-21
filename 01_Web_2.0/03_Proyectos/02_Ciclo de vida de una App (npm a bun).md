tipo::
tags::
fecha:: 2026-07-28
links::
links_externos::

Este recorrido esta hecho como ejemplo con la App de Soberanía alimentaria donde tiene como reverse proxy Caddyfile, por eso nombramos quizas a esta funcionalidad. La utilización de **BUN** en vez de NPM o PNPM o YARM es por la velocidad y la utilidad del "todo en uno" que ofrece este framework.
### 1. Gestión de Dependencias (Instalar librerías)

Cuando programas, importas librerías (`next`, `react`, `@prisma/client`, etc.). No subes las carpetas pesadas de esas librerías al control de código (Git), solo las declaras en `package.json`.

- **El Paso:** Bajar e instalar los paquetes necesarios para que la app funcione.
    
- **Flujo tradicional:** Ejecutas `npm install` (o `pnpm install`). Se lee el `package.json` y se genera un archivo de bloqueo (`package-lock.json` o `pnpm-lock.yaml`).
    
- **Cómo lo hace este proyecto:** Ejecutas **`bun install`**. Bun lee tu `package.json` y usa su propio motor súper rápido para descargar las dependencias en la carpeta `node_modules` y generar/actualizar el archivo **`bun.lock`**.
    

### 2. Desarrollo Local (Dev Server)

Mientras programas, necesitas ver tus cambios en vivo en el navegador con recarga automática (_Hot Reloading_).

- **El Paso:** Levantar un servidor de desarrollo que interprete TypeScript y JSX al vuelo.
    
- **Flujo tradicional:** Ejecutas `npm run dev`, lo que internamente llama al CLI de Next.js (`next dev`).
    
- **Cómo lo hace este proyecto:** Ejecutas **`bun run dev`** (o `bun dev`). Bun ejecuta el script directamente sin pasar por Node.js o `npx`.
    

### 3. Compilación y Armado del Bundle (_Build_)

El código en TypeScript (`.ts`, `.tsx`), los componentes de React y los esquemas de Prisma no pueden ser leídos directamente por un servidor de producción tal como están. Tienen que transformarse.

- **El Paso:**
    
    1. **Tipado e Interpretación:** Prisma genera su cliente tipado (`prisma generate`).
        
    2. **Transpilación y Bundling:** Se convierte TypeScript/JSX a JavaScript estándar optimizado.
        
    3. **Minificación:** Se remueven espacios, comentarios y se renombran variables para que el código sea lo más pequeño posible.
        
    4. **Empaquetado Standalone:** Gracias a `output: "standalone"`, Next.js rastrea solo el código verdaderamente utilizado y crea el paquete mínimo autocontenido en `.next/standalone`.
        
- **Herramienta:** Ejecutas **`bun run build`** (que corre `next build`). Aquí Next.js se encarga de empaquetar, utilizando Bun como ejecutable base.
    

### 4. Preparación del Entorno de Despliegue (_Deployment / Packaging_)

Una vez compilada la app, debes llevarla al servidor de producción.

- **El Paso:** Enviar el código preparado al servidor (ya sea subiendo la carpeta compilada o construyendo una imagen de Docker).
    
- **Lo clave del `standalone`:** En el servidor **no necesitas copiar todo el proyecto ni volver a ejecutar `bun install`** para descargar gigabytes de paquetes. Solo trasladas:
    
    - La carpeta `.next/standalone`
        
    - La carpeta `.next/static` (estilos, imágenes, scripts del cliente)
        
    - La carpeta `public` (archivos estáticos)
        

### 5. Ejecución (_Runtime / Serving_)

Ahora el código empaquetado debe ponerse a escuchar peticiones en el servidor.

- **El Paso:** Arrancar el proceso del servidor HTTP que responderá a las visitas.
    
- **Flujo tradicional:** Ejecutabas `node .next/standalone/server.js` gestionado por un administrador de procesos como PM2 o Systemd.
    
- **Cómo lo hace este proyecto:** Puedes arrancarlo con Node.js o directamente con Bun ejecutando:
    
    Bash
    
    ```
    bun .next/standalone/server.js
    ```
    
    Esto inicia un proceso Node/Bun escuchando en un puerto interno (por ejemplo, `http://localhost:3000`).
    

### 6. Exposición a Internet (Reverse Proxy)

Tu app ya está corriendo localmente en el puerto 3000 del servidor, pero nadie en internet puede entrar de forma segura (sin HTTPS ni dominio).

- **El Paso:** Recibir las peticiones HTTPS públicas en el puerto 443 del dominio del cliente y reenviarlas al puerto 3000 donde corre tu app.
    
- **Herramienta:** Aquí entra **Caddy** (o NGINX). Caddy intercepta el tráfico entrante de `[https://mi-cliente.com](https://mi-cliente.com)`, valida el certificado SSL automáticamente y le pasa la petición a `localhost:3000`.
    

| **Tarea / Etapa**         | **Herramienta Tradicional** | **En tu proyecto actual**             |
| ------------------------- | --------------------------- | ------------------------------------- |
| **Gestor de paquetes**    | `npm` / `yarn` / `pnpm`     | **Bun** (`bun install`, `bun.lock`)   |
| **Ejecutor de scripts**   | `npx` / `npm run`           | **Bun** (`bunx`, `bun run`)           |
| **Runtime JS (servidor)** | Node.js                     | **Bun** (o Node.js en el `server.js`) |
| **Framework / Bundler**   | Next.js                     | **Next.js 16** (`output: standalone`) |
| **Proxy & SSL**           | NGINX + Certbot             | **Caddy**                             |


---
#### Referencias

