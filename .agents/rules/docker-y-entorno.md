# Reglas de Docker, Red y Seguridad del Entorno (`infra`)

## 1. Sincronización de Variables de Entorno
- `infra/.env` controla las credenciales con las que se inicializa el contenedor `app_db` (`MYSQL_DATABASE`, `MYSQL_USER`, `MYSQL_PASSWORD`, `MYSQL_ROOT_PASSWORD`) y las rutas `BACKEND_PATH` / `FRONTEND_PATH`.
- `../backend/.env` debe mantener exactamente los mismos valores en `DB_DATABASE`, `DB_USERNAME`, `DB_PASSWORD`, con `DB_HOST=db` y `DB_PORT=3306` (comunicación interna por la red `app_network`).
- Desde la máquina host (Windows), la conexión externa a MySQL (por ejemplo desde DBeaver o MySQL Workbench) usa `127.0.0.1` y el puerto `3307` (`DB_EXTERNAL_PORT`).
- Nunca subas archivos `.env` reales al repositorio; cualquier nueva variable debe documentarse primero en `.env.example`.

## 2. Comunicación Frontend ↔ Nginx ↔ Backend
- En desarrollo dentro de Docker, el contenedor `app_frontend` define `VITE_PROXY_TARGET=http://nginx`.
- El cliente HTTP del frontend (`../frontend/src/services/api.js`) envía todas las peticiones al prefijo relativo `/api`.
- Vite redirige `/api/*` hacia `http://nginx/api/*`, y Nginx (`nginx/default.conf`) pasa la ejecución PHP a `backend:9000`.
- **Prohibido** hardcodear `http://localhost:8000` en el código fuente de Vue o cambiar los nombres de servicio en `compose.yaml` (`backend`, `nginx`, `db`, `frontend`), ya que rompería la resolución DNS interna de Docker.

## 3. Protección de Volúmenes y Base de Datos
- El volumen `db_data` persiste las tablas y datos de prueba del equipo.
- Está **estrictamente prohibido** ejecutar `docker compose down -v` salvo que el usuario lo pida de forma literal para reiniciar las credenciales de MySQL desde cero.
