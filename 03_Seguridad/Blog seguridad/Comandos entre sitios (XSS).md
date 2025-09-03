tipo:: [[Seguridad Web]] 
tags:: #seguridad/web/script_injecting 
fecha:: 2025-08-29
links::
links_externos::


El XSS es una vulnerabilidad que permite a un atacante **inyectar y ejecutar código malicioso (generalmente JavaScript)** en el contexto de una página web vulnerable, que luego es ejecutado por el navegador de otras víctimas. El ataque explota la confianza que el navegador tiene en el contenido recibido del sitio web 17.

#### 📝 **Ejemplo Práctico de Prueba (Para Aprendizaje)**

Imagina un blog que permite a los usuarios dejar comentarios. Un comentario normal se vería así:

html

<p>¡Excelente artículo! Estoy de acuerdo.</p>

Un atacante podría aprovechar un campo de comentario que no sanitiza la entrada para inyectar código. En vez de un comentario legítimo, enviaría:

html

<script>alert('XSS Prueba de Concepto');</script>

Si el sitio es vulnerable y no sanitiza el input, este script se almacenaría en la base de datos. **Cada vez que un usuario visite la página de comentarios, el script `alert` se ejecutará en su navegador**, mostrando una ventana emergente 37.

**Para practicar:** Puedes buscar "XSS challenges" online o usar laboratorios intencionadamente vulnerables como los de **Web Security Academy de PortSwigger** 7 o **Bright Security** 3. Estos entornos seguros están diseñados para que pruebes inyecciones como esta sin dañar sistemas reales.

#### ⚠️ **Impacto Real**

No es solo about mostrar alerts. Un ataque real podría robar cookies de sesión, suplantar identidades, redirigir a sitios maliciosos o incluso instalar malware 5. Por ejemplo, el famoso ataque a **British Airways** en 2018 robó datos de 380,000 tarjetas de crédito explotando un XSS en un script de Feedify 3.

#### 🛡️ **¿Cómo Prevenirlo?**

- **Sanitizar y Validar toda Entrada del Usuario:** Filtrar caracteres peligrosos como `<`, `>`, `&`, `"`, `'` y `/` en el servidor.
    
- **Codificar la Salida (Output Encoding):** Asegurarse de que cualquier dato que se renderice en el navegador (como un comentario) sea tratado como texto, no como código HTML o JS. Muchos frameworks modernos lo hacen automáticamente 9.
    
- **Utilizar Headers de Seguridad:** Implementar una **Política de Seguridad de Contenido (CSP)** para restringir qué scripts pueden ejecutarse en la página 79.

---
#### Referencias

