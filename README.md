# 🚚 Innovatech Chile — Sistema de Gestión de Despachos

> Proyecto Semestral ISY1101 · Introducción a Herramientas DevOps · DUOC UC · 2026

Plataforma web desarrollada para la gestión de despachos y órdenes de compra de **Innovatech Chile**, desplegada en infraestructura cloud AWS con automatización CI/CD completa mediante GitHub Actions.

---

## 👤 Integrante

| Nombre | GitHub |
|--------|--------|
| Diego Navarrete | [@DiegonavarreteDUOC](https://github.com/DiegonavarreteDUOC) |

---

## 🛠️ Tecnologías Utilizadas

### Frontend
- ⚛️ **React** + **Vite**
- 🎨 **TailwindCSS**
- 📡 **Axios**
- 🔄 **React Hook Form**
- 🌐 **Nginx** (Reverse Proxy)
- 🐳 **Docker**

### Backend
- ☕ **Java 17**
- 🍃 **Spring Boot 3**
- 🔧 **Maven**
- 🗄️ **Spring Data JPA**
- 📄 **Swagger / OpenAPI 3**
- 🐳 **Docker**

### Base de Datos
- 🐬 **MySQL 8.0** (contenedor Docker)

### DevOps / Cloud
- ☁️ **AWS EC2** (3 instancias en VPC)
- 🐙 **GitHub Actions** (CI/CD)
- 🐳 **Docker Hub**
- 🐧 **Amazon Linux 2023**

---

## 🏗️ Arquitectura del Sistema

```
Internet
    │
    ▼
EC2-Front (IP Pública)
├── Nginx Reverse Proxy (Puerto 80)
│   ├── /api/v1/despachos → EC2-Backend:8081
│   └── /api/v1/ventas    → EC2-Backend:8082
│
EC2-Backend (IP Privada: 10.0.2.83)
├── back-despachos (Puerto 8081) ─────┐
└── back-ventas    (Puerto 8082) ─────┤
                                      │
EC2-Data (IP Privada: 10.0.2.196)     │
└── mysql-db (Puerto 3306) ←──────────┘
```

- **Frontend público** expuesto únicamente por el puerto 80
- **Backend desplegado en subnet privada** sin acceso directo desde internet
- **Base de datos en subnet privada** solo accesible desde el backend
- **Comunicación Frontend → Backend** mediante proxy reverso Nginx
- **Despliegue automatizado** con CI/CD al hacer push en la rama `deploy`

---

## 📁 Estructura del Proyecto

```
innovatech-frontend/
├── src/
│   ├── componentes/
│   │   ├── CrudAdmin/
│   │   │   ├── TableDespachos.jsx    # Tabla de órdenes de despacho
│   │   │   ├── TableCompras.jsx      # Tabla de órdenes de compra
│   │   │   ├── FormDespacho.jsx      # Formulario crear despacho
│   │   │   ├── FormCierreDespacho.jsx# Formulario cerrar despacho
│   │   │   └── Modal.jsx
│   │   └── Layouts/
│   │       ├── Header.jsx
│   │       └── Footer.jsx
│   └── Routes/
├── default.conf.template             # Configuración Nginx + Reverse Proxy
├── Dockerfile                        # Multi-stage build
├── .github/
│   └── workflows/
│       └── deploy-frontend.yml       # Pipeline CI/CD
└── docker-compose.yml
```

---

## 🐳 Dockerización

### Build de la imagen
```bash
docker build -t diego991/innovatech-frontend:latest .
```

### Ejecutar contenedor
```bash
docker run -d \
  --name front-despacho \
  --restart always \
  -p 80:80 \
  -e BACKEND_IP=10.0.2.83 \
  diego991/innovatech-frontend:latest
```

---

## ♾️ CI/CD con GitHub Actions

El proyecto implementa integración y despliegue continuo mediante **GitHub Actions**. El pipeline se activa automáticamente al hacer push en la rama `deploy`.

### Flujo automatizado:
1. 🔨 **Build** — Construcción de la imagen Docker
2. 📦 **Push** — Publicación en Docker Hub (`diego991/innovatech-frontend`)
3. 🚀 **Deploy** — Conexión SSH al EC2-Front y reinicio del contenedor

### GitHub Secrets requeridos:
| Secret | Descripción |
|--------|-------------|
| `DOCKER_USERNAME` | Usuario de Docker Hub |
| `DOCKER_PASSWORD` | Token de Docker Hub |
| `EC2_FRONT_IP` | IP pública del EC2-Front (⚠️ cambia al reiniciar el Lab) |
| `SSH_PRIVATE_KEY` | Clave privada SSH para conectarse al EC2 |

> ⚠️ **Importante:** La IP pública de EC2-Front cambia cada vez que se reinicia el Lab de AWS Academy. Actualiza el secret `EC2_FRONT_IP` antes de cada sesión.

---

## 🔗 URLs del Proyecto

| Servicio | URL |
|----------|-----|
| Frontend | `http://<EC2_FRONT_IP>` |
| API Despachos | `http://<EC2_FRONT_IP>/api/v1/despachos` |
| API Ventas | `http://<EC2_FRONT_IP>/api/v1/ventas` |

---

## 📡 Endpoints Principales (Backend Despachos - Puerto 8081)

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| `GET` | `/api/v1/despachos` | Obtener todos los despachos |
| `GET` | `/api/v1/despachos/{id}` | Obtener despacho por ID |
| `POST` | `/api/v1/despachos` | Crear nuevo despacho |
| `PUT` | `/api/v1/despachos/{id}` | Actualizar despacho |
| `DELETE` | `/api/v1/despachos/{id}` | Eliminar despacho |

---

## 🔒 Seguridad

- Backend **privado**, sin acceso público directo
- Frontend expuesto **únicamente por puerto 80**
- Variables sensibles manejadas con **GitHub Secrets**
- No existen credenciales hardcodeadas en el código

---

## 📋 Repositorios Relacionados

| Repositorio | Descripción |
|-------------|-------------|
| [innovatech-frontend](https://github.com/DiegonavarreteDUOC/innovatech-frontend) | Este repositorio (React + Nginx) |
| [innovatech-back-despachos](https://github.com/DiegonavarreteDUOC/innovatech-back-despachos) | Backend Spring Boot despachos |
| [innovatech-back-ventas](https://github.com/DiegonavarreteDUOC/innovatech-back-ventas) | Backend Spring Boot ventas |

---

## 🏫 Contexto Académico

> **Asignatura:** ISY1101 — Introducción a Herramientas DevOps  
> **Institución:** DUOC UC  
> **Año:** 2026  
> **Evaluación:** EP2 — Proyecto Semestral
