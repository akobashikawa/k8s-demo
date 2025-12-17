# k8s-demo (docker-compose)

Este repositorio contiene una demo con dos servicios que se orquestan con Docker Compose:

- `springboot-demo` — backend Spring Boot (puerto 8080)
- `astro-vue-demo` — frontend Astro+Vue (servido por nginx en el contenedor)

Archivos clave:

- `docker-compose.yaml` — define los servicios y la red `appnet`.
- `springboot-demo/` — proyecto Spring Boot (build con su Dockerfile).
- `astro-vue-demo/` — frontend Astro (build estático en imagen nginx).

Requisitos

- Docker / Docker Compose (o una alternativa compatible como `nerdctl`).

Levantar los servicios (desde la raíz del repo):

```bash
cd d:\studio\backend\microservices\kubernetes\k8s-demo
docker compose up --build
```

Puertos expuestos

- Backend: http://localhost:8080
- Frontend: http://localhost:3000  (el contenedor nginx sirve en su puerto 80; se publica en el host como 3000)

Notas importantes

- El frontend se construye durante la fase de `docker build` (imagen estática). El `Dockerfile` del frontend copia `dist/` a nginx.
- Para incrustar la URL de la API en la build del frontend se usa el build-arg `PUBLIC_API_BASE_URL`. `docker-compose.yaml` ya lo configura a `http://springboot-demo:8080` para que la build apunte al servicio backend en la red de Compose.
- Si cambias la URL de la API en la configuración del frontend, reconstruye la imagen:

```bash
docker compose build --no-cache astro-vue-demo
docker compose up -d
```

- Si prefieres inyectar la URL en tiempo de ejecución (sin rebuild), hay que cambiar la estrategia: usar un script de arranque en la imagen nginx que reemplace tokens en `index.html` según variables de entorno.

Apagar y limpiar

```bash
docker compose down
```

¿Quieres que añada un ejemplo de `.env`, un `docker-compose.override.yaml` para desarrollo, o el script de arranque para configuración en tiempo de ejecución del frontend? 
