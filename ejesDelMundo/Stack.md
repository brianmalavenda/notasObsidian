
- **Next.js:** Renderizado híbrido (SSR/SSG) ideal para contenido editorial
- **NestJS:** Arquitectura modular para flujos complejos
- **WordPress como CMS:** Usa la REST API para gestión de contenido
- **TipTap:** Editor colaborativo en tiempo real
### Recursos Adicionales

1. **Autenticación de roles:** Usar NextAuth.js con adaptador para NestJS
2. **Generación de PDF:**
    - PDFKit (para creación programática)
    - Puppeteer (para convertir HTML a PDF)
3. **API WordPress:**
    - Paquete oficial: `@wordpress/api`
    - Endpoints: `/wp-json/wp/v2/posts`
4. **Text-to-Speech:**
    - ElevenLabs (calidad profesional)
    - AWS Polly (económico)
### Recomendaciones Finales

1. **Gestión de estado:** Usar Zustand para estados complejos de UI
2. **Colaboración en tiempo real:** Implementar Socket.io para edición concurrente
3. **Deploy inicial:** Vercel (Next.js) + Render.com (NestJS)
4. **Caché:** Redis para almacenar resultados de scraping
5. **Monitorización:** Sentry para errores frontend/backend


Este stack te proporciona:
- Escalabilidad para múltiples editores
- Mantenibilidad del código
- Rendimiento óptimo para contenido dinámico
- Integración perfecta con WordPress como CMS