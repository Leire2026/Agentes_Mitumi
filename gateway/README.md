# Gateway — punto de entrada único del sistema de agentes

> **Estado:** 🟢 Funcional
> **Servicio:** FastAPI · puerto **5003**

## Nota importante

El gateway no contiene lógica de negocio: es la **única puerta de entrada** para el
frontend. Enruta cada petición al agente correspondiente por HTTP, de modo que los
agentes siguen siendo servicios independientes (sin colisiones de dependencias) y
pueden sustituirse sin que el frontend cambie una línea.

# 1. Resumen ejecutivo

| Campo | Valor |
|---|---|
| Nombre | Gateway de agentes |
| Propósito | Una sola URL y contratos comunes para consumir los seis agentes |
| Tecnología | FastAPI + httpx (proxy asíncrono, cliente HTTP reutilizado) |
| Documentación interactiva | `GET /docs` (Swagger autogenerado) |
| Salud agregada | `GET /salud` — estado de todas las piezas, consultado en paralelo |

# 2. Rutas

| Ruta | Qué hace |
|---|---|
| `GET /` | Información: agentes registrados y cómo consumirlos |
| `GET /salud` | Salud de agentes y backend de datos en una sola llamada |
| `POST /agentes/lumen/chat` · `/chat/reset` | Proxy → Lumen (chat con memoria por sesión) |
| `POST /agentes/operis/autocompletar` | Proxy → Operis (briefings; texto o archivo) |
| `GET/POST /agentes/jano/...` | Proxy → Jano (búsqueda, informes PDF) |
| `GET/POST /agentes/vigil/...` | Proxy → Vigil (concursos, ejecuciones, ICS, pliegos) |
| `POST /agentes/garum/ciclos` | Lanza un ciclo de Garum en segundo plano (202 + id; **409** si ya hay uno) |
| `GET /agentes/garum/ciclos/{id}` | Progreso del ciclo: en marcha / terminado / error |
| `POST /chat` · `/chat/reset` · `/autocompletar` | Alias de compatibilidad en la raíz |

# 3. Contrato de errores

Formato común en todo el sistema:

```json
{ "error": true, "codigo": "AGENTE_CAIDO", "mensaje": "El agente 'jano' no responde…" }
```

Códigos propios: `AGENTE_CAIDO` (502) · `RUTA_NO_ENCONTRADA` (404) · `CICLO_EN_MARCHA` (409).

# 4. Ejecución

```bash
pip install -r requirements.txt
python app.py                 # http://localhost:5003 · Swagger en /docs
```

En el sistema completo lo levanta `../arrancar_todo.sh`; dentro del contenedor Docker es
el único puerto expuesto (los agentes quedan detrás, en la interfaz local).

# 5. Diseño

- **Proxy, no monolito**: se descartó cargar los agentes en un solo proceso porque sus
  paquetes internos colisionan (`src/`, `config/`); por HTTP cada uno conserva su entorno.
- **Cliente HTTP único reutilizado** (pool de conexiones) y salud consultada **en paralelo**.
- **Garum por ciclos**: al no ser un servidor residente, el gateway lanza su `main.py`
  como subproceso, guarda el estado por identificador y rechaza ciclos simultáneos.
- Sin autenticación (fase de demostración): añadirla —clave por cabecera y límite de
  peticiones— es la primera evolución prevista.
