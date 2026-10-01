# Frontend de Invest Lavalleja

Aplicación autónoma: portal Astro en `portal/`, cliente React/Vite de Gianna y administración en `src/`, Dockerfile y Nginx. No contiene credenciales del modelo ni SMTP.

[Arquitectura y flujos del sistema](https://github.com/NebyX1/invest-lavalleja-rag-chat/blob/main/Arquitectura.md): compilación, navegación, chat, estado local y conexión con la API.

Este repositorio contiene exclusivamente el frontend, situado en la raíz para que Docker y Coolify lo construyan directamente. El backend se despliega como otro servicio.

## Estructura

- `portal/`: sitio editorial Astro, islas React, catálogo, recursos y pruebas.
- `src/`: cliente React/Vite de Gianna y panel administrativo.
- `public/` y `scripts/`: recursos del cliente y ensamblado de ambos builds.
- `nginx/`: rutas, acceso al iframe, proxy y configuración de ejecución.
- `Dockerfile` y `compose.yaml`: contenedor independiente del frontend.

## Compilación

```sh
npm ci
npm ci --prefix portal
npm run build
docker build -t invest-frontend .
```

## Conexión y despliegue

Copiar `.env.example` a `.env` para la configuración local de Compose, o definir las variables en Coolify. `VITE_API_URL`, `PUBLIC_SITE_URL` y `PUBLIC_INDEXING` son argumentos de compilación. `BACKEND_URL` y `TRUSTED_PROXY_CIDR` se usan al ejecutar Nginx.

- API del mismo origen: `VITE_API_URL` vacío; Nginx envía `/api/` a `BACKEND_URL`.
- CORS directo: `VITE_API_URL` contiene el origen HTTPS del backend, sin `/api`.
- El chat se abre únicamente desde `/gianna/`; el panel está en `/admin`.
- El historial se guarda en el navegador. Los cupos se validan en el backend.

En Coolify, seleccionar Dockerfile, directorio base `/`, Dockerfile `/Dockerfile`, puerto interno **80** y healthcheck **/health**. Configurar `BACKEND_URL` con una dirección accesible desde el contenedor del frontend. El valor de ejemplo `http://backend:8010` exige que exista un servicio con ese nombre en la misma red.

El backend debe permitir el origen público del portal mediante CORS y reconocer la red de los proxies de confianza. Los secretos de Ollama, SMTP y administración se configuran exclusivamente en el backend.

El contexto Docker es la raíz de este repositorio. Ver la [guía de despliegue y administración](https://github.com/NebyX1/invest-lavalleja-rag-chat/blob/main/Admin-y-Coolify.md) y el [informe de validación de la integración](https://github.com/NebyX1/invest-lavalleja-rag-chat/blob/main/PRODUCTION-VALIDATION.md).
