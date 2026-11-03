# Infra – Entorno Docker del Sistema de Gestión Logística e Inventario

Orquestación con Docker Compose del proyecto del **Grupo 6 – Ingeniería de Software 2026-II (UNA Puno)**.

| Servicio | Imagen | Puerto local |
|---|---|---|
| `backend` | PHP 8.4-FPM (Laravel 13) | – |
| `nginx` | nginx:stable-alpine | http://localhost:8000 |
| `db` | mysql:8.4 | localhost:3307 |
| `frontend` | node:26-alpine (Vite) | http://localhost:5173 |

## 1. Clonar los repos uno al lado del otro
```powershell
mkdir Proyecto_ingenieria-software; cd Proyecto_ingenieria-software
git clone https://github.com/wdominic6/inventario-electronico-infra.git infra
git clone https://github.com/wdominic6/inventario-electronico-backend.git backend
git clone https://github.com/wdominic6/inventario-electronico-frontend.git frontend
git clone https://github.com/wdominic6/inventario-electronico-docs.git docs
```
Resultado:
```
Proyecto_ingenieria-software/
├── infra/      ← ejecuta docker compose desde aquí
├── backend/
├── frontend/
└── docs/
```

## 2. Primera vez
```powershell
cd infra
Copy-Item .env.example .env
Copy-Item ..\backend\.env.example ..\backend\.env
docker compose up -d --build
docker compose exec backend composer install
docker compose exec backend php artisan key:generate
docker compose exec backend php artisan migrate
```

## 3. Uso diario
```powershell
docker compose up -d        # levantar
docker compose logs -f      # ver logs
docker compose down         # detener (los datos de MySQL se conservan en el volumen db_data)
docker compose down -v      # detener y BORRAR la base de datos
```

## Comprobación rápida
- http://localhost:8000/up debe responder **200**.
- http://localhost:5173 muestra la app Vue.
- http://localhost:5173/api/user debe responder **401** en JSON (el proxy hacia la API funciona).

## Variables (`.env`)
Ver `.env.example`. Los `DB_*` deben coincidir con los de `backend/.env`. Si clonaste los repos con otros nombres de carpeta, ajusta `BACKEND_PATH` y `FRONTEND_PATH`.
