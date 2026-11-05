---
name: diagnostico-entorno
description: >-
  Levanta, diagnostica y verifica el estado de los contenedores Docker del proyecto
  (backend Laravel 13, nginx, MySQL 8.4 y frontend Vue 3). Úsalo cuando el usuario
  pida iniciar el entorno, revisar errores de conexión, verificar el proxy /api o
  comprobar que los servicios estén operativos.
---

# Diagnóstico y Puesta en Marcha del Entorno Docker

Sigue estos pasos desde el directorio `infra/` para verificar o reparar el entorno de desarrollo del Grupo 6.

## Pasos

1. **Verificar archivos `.env`**:
   Comprueba que existan `infra/.env` y `../backend/.env`. Si alguno falta, cópialo desde su respectivo `.env.example`.

2. **Verificar estado de los contenedores**:
   ```powershell
   docker compose ps
   ```
   Los 4 contenedores (`app_backend`, `app_nginx`, `app_db`, `app_frontend`) deben estar en estado `Up` (y `app_db` como `healthy`).

3. **Levantar servicios si están detenidos**:
   ```powershell
   docker compose up -d
   ```

4. **Comprobar salud de la API y del Proxy**:
   - Verificar healthcheck de Laravel vía Nginx (`http://localhost:8000/up` debe devolver HTTP 200).
   - Verificar que el proxy de Vite hacia Nginx y Sanctum responda (`http://localhost:5173/api/user` sin token debe devolver HTTP 401 en JSON).

5. **Revisar logs en caso de fallo**:
   ```powershell
   docker compose logs --tail=50 backend nginx frontend db
   ```
