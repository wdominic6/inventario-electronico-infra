# Infraestructura y Orquestación – Sistema Web de Gestión Logística e Inventario (Grupo 6)

Este repositorio (`inventario-electronico-infra`) orquesta con Docker Compose los servicios del **Sistema Web de Gestión Logística e Inventario de Productos Electrónicos** (Grupo 6 – Ingeniería de Software 2026-II, UNA Puno).

## Mapa de Repositorios del Proyecto
El proyecto completo está dividido en 4 repositorios independientes clonados al mismo nivel:
- `../backend` (`inventario-electronico-backend`): API REST en Laravel 13 + PHP 8.4 + Sanctum.
- `../frontend` (`inventario-electronico-frontend`): SPA en Vue 3.5 + Pinia 4 + Vue Router 5 + Vite 8.
- `.` (`inventario-electronico-infra`): Docker Compose (`backend`, `nginx`, `db` MySQL 8.4, `frontend`).
- `../docs` (`inventario-electronico-docs`): Planificación, informes técnicos (hitos semanas 3, 8, 13, 17), UML y prototipos.

> **Espacio de trabajo Multi-Root**: Abre `infra/grupo6-inventario.code-workspace` en Antigravity / VS Code para cargar los 4 repositorios simultáneamente con sus respectivas reglas y skills de `.agents/`.

## Servicios en `compose.yaml`
| Servicio | Contenedor | Imagen / Base | Puerto Host → Contenedor | Propósito |
| :--- | :--- | :--- | :--- | :--- |
| `backend` | `app_backend` | PHP 8.4-FPM (`../backend/Dockerfile`) | Interno (`:9000` FastCGI) | Ejecuta Laravel 13 |
| `nginx` | `app_nginx` | `nginx:stable-alpine` | `http://localhost:8000` → `:80` | Servidor web / punto de entrada HTTP a la API |
| `db` | `app_db` | `mysql:8.4` | `localhost:3307` → `:3306` | Base de datos relacional transaccional (InnoDB) |
| `frontend` | `app_frontend` | `node:26-alpine` (`../frontend/Dockerfile`) | `http://localhost:5173` → `:5173` | Servidor de desarrollo Vite con proxy `/api` → `http://nginx` |

## Reglas Críticas para el Agente
1. **Todos los comandos de PHP/Composer/Artisan** deben ejecutarse dentro del contenedor `backend` desde `infra/`:
   ```powershell
   docker compose exec backend php artisan <comando>
   docker compose exec backend composer <comando>
   ```
2. **Prohibido destruir datos sin permiso**: Nunca ejecutes `docker compose down -v` ni `php artisan migrate:fresh` sin autorización explícita del usuario, ya que borran el volumen `db_data` de MySQL 8.4.
3. **Respeta las fronteras del equipo**: Antes de tocar archivos en `../backend` o `../frontend`, revisa las reglas de propiedad de archivos por integrante en `../backend/.agents/rules/aislamiento-equipo-backend.md` y `../frontend/.agents/rules/aislamiento-equipo-frontend.md`.
