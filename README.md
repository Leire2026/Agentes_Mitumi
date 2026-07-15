# MITÜMI · Backstage — Sistema de agentes para la gestión de eventos

Proyecto de gestión integral de eventos · julio 2026

Sistema de **seis agentes de inteligencia artificial** para la gestión integral de eventos
empresariales, con un gateway común como punto de entrada único, un backend de datos de
apoyo y despliegue reproducible en local y en la nube. La IA **propone y el humano valida**:
ningún agente escribe en la base de datos ni ejecuta acciones con efecto real sin supervisión.

> **Repositorios** · Oficial y desplegable:
> [`ngalparsoro/despliegue_agentes`](https://github.com/ngalparsoro/despliegue_agentes) ·
> Desarrollo (histórico):
> [`ngalparsoro/Agentes_Eventos`](https://github.com/ngalparsoro/Agentes_Eventos)

---

## Arquitectura

```text
                        FRONTEND (React)
                              │  HTTP/JSON
                              ▼
                   ┌─────────────────────┐
                   │  GATEWAY   :5003    │  ← única puerta de entrada
                   │  /agentes/<nombre>/ │     salud agregada · /docs
                   └─────────┬───────────┘
      ┌──────────┬───────────┼───────────┬──────────┐
      ▼          ▼           ▼           ▼          ▼
   Lumen      Operis       Jano       Vigil      Garum
   :5001      :5002        :8001      :8000    (por ciclos)
   chat     briefings   transporte  concursos   correos
      │          │                                  │
      ▼          ▼                                  ▼
   ┌──────────────────────┐          ┌───────────────────────┐
   │ PostgreSQL (Neon)    │◄─────────│ BACKEND DE DATOS :5004│◄── Hermes (bot
   │ rol de SOLO lectura  │          │ (lectura para agentes)│    de Telegram)
   └──────────────────────┘          └───────────────────────┘

   Escrituras reales: SOLO a través del backend de la aplicación, con validación humana.
```

## Los seis agentes

| Agente | Función | Servicio | Documentación |
|---|---|---|---|
| **Lumen** | Copiloto conversacional: consulta eventos, ponentes y logística en lenguaje natural, con memoria por sesión | Flask :5001 | [`agentes/Lumen_buscador/`](agentes/Lumen_buscador/README.md) |
| **Operis** | Autocompletado de formularios: extrae JSON estructurado de briefings (texto o archivo `.txt`/`.pdf`/`.docx`, con OCR para escaneados) | Flask :5002 | [`agentes/Operis_autocompletado/`](agentes/Operis_autocompletado/README.md) |
| **Jano** | Propuestas de transporte y hotel para ponentes, con informes PDF (con y sin precios) | Flask :8001 | [`agentes/Jano_transporte/`](agentes/Jano_transporte/README.md) |
| **Vigil** | Monitorización de concursos públicos de eventos: histórico filtrable, calendario y pliegos | Flask :8000 | [`agentes/Vigil_busquedaconcursos/`](agentes/Vigil_busquedaconcursos/README.md) |
| **Hermes** | Bot de Telegram para ponentes: agenda, logística y documentación con datos reales | Bot (sin HTTP) | [`agentes/Hermes_telegram/`](agentes/Hermes_telegram/README.md) |
| **Garum** | Gestor de correo por ciclos: clasifica, redacta borradores en Gmail y nunca envía | Ciclos vía gateway | [`agentes/Garum_gestorcorreos/`](agentes/Garum_gestorcorreos/README.md) |

Piezas de integración: el **gateway** (`gateway/`, [README](gateway/README.md)) y el
**backend de datos para agentes** (`backend/`, [README](backend/README.md)). El contrato completo de rutas, con ejemplos
reales de entrada y salida, está en la documentación del repositorio oficial.

## Estructura de la entrega

```text
├── agentes/            # los seis agentes (código + README cada uno)
├── backend/            # backend de datos para agentes (FastAPI :5004)
├── gateway/            # punto de entrada único (FastAPI :5003)
├── docs/               # memoria técnica, estudio económico y sistema de diseño
├── arrancar_todo.sh    # levanta todo el sistema en local
├── comprobar_salud.sh  # smoke test: /health de cada pieza
├── arrancar_render.sh  # arranque dentro del contenedor (nube)
├── Dockerfile          # imagen única del sistema (con OCR)
├── render.yaml         # blueprint del despliegue en Render
└── .env.example        # plantilla de configuración común
```

## Puesta en marcha (local)

Requiere **Python 3.10+** (en macOS, el del sistema es 3.9: usar el de Homebrew) y las
dependencias de cada servicio:

```bash
for req in $(find . -name "requirements*.txt"); do pip install -r "$req"; done
cp .env.example .env      # rellenar DATABASE_URL (rol de solo lectura) y GROQ_API_KEY
./arrancar_todo.sh        # todos los servicios (añade --con-hermes para el bot)
./comprobar_salud.sh      # ✓/✗ por servicio + salud agregada del gateway
```

El frontend (u otra herramienta) consume todo en `http://localhost:5003` — Swagger en `/docs`.

## Despliegue en la nube

Un único contenedor Docker con todo el sistema, publicado en Render mediante el blueprint:

```bash
docker build -t mitumi-agentes . && docker run -p 5003:5003 -e PORT=5003 \
  -e DATABASE_URL="..." -e GROQ_API_KEY="..." mitumi-agentes
```

En Render: *New → Blueprint* sobre el repositorio oficial; `render.yaml` declara el servicio
(runtime Docker, healthcheck `/salud`, autodeploy en cada push) y las claves se introducen en
su panel de secretos. Limitaciones del plan gratuito y detalle completo: sección de despliegue
de la memoria técnica.

## Documentación (`docs/`)

- **Memoria técnica** — el documento completo del proyecto: contexto, arquitectura,
  base de datos, aplicación web, los seis agentes, integración, seguridad, despliegue,
  costes y conclusiones.
- **Estudio económico** — coste de inferencia por agente, escenarios y retorno estimado.
- **Sistema de diseño** — la guía SCSS de la interfaz web.

## Principios del sistema

1. **Lectura directa, escritura mediada**: los agentes leen PostgreSQL con un rol que
   físicamente no puede escribir; toda escritura pasa por el backend de la aplicación.
2. **Validación humana**: cualquier propuesta con efecto real (comunicaciones, reservas,
   cambios de datos) se marca `requiere_validacion_humana` y espera aprobación.
3. **Contratos comunes**: `GET /health` uniforme, errores siempre en JSON
   (`{"error": true, "codigo": ..., "mensaje": ...}`) y JSON como formato único de intercambio.
4. **Secretos fuera del código**: solo plantillas `.env.example` en el repositorio;
   las credenciales viven en el `.env` local o en el gestor de secretos de la plataforma.
