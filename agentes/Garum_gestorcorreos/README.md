# Garum — Gestor Inteligente de Correos

> **Estado:** MVP funcional
> **Proyecto:** MITÜMI
> **Tipo de agente:** gestión de correo electrónico por ciclos

---

# Índice

1. Introducción
2. Resumen ejecutivo
3. Objetivo
4. Arquitectura general
5. Funcionalidades
6. Qué NO hace
7. Flujo de un ciclo
8. Estructura del proyecto
9. Componentes
10. Variables de entorno
11. Instalación y ejecución
12. Gestión de errores
13. Seguridad
14. Estructura de salida
15. Limitaciones actuales

---

# 1. Introducción

Garum es el agente encargado de la gestión del correo electrónico dentro de la plataforma MITÜMI. Su finalidad es reducir el tiempo dedicado a revisar la bandeja de entrada: clasifica automáticamente los mensajes no leídos, genera borradores de respuesta directamente en Gmail y registra cada resultado de forma estructurada.

El agente funciona **por ciclos**: cada ejecución procesa un lote de correos y termina. No es un servidor residente; se dispara a mano o a través del gateway del sistema. Todas las respuestas que produce son **borradores** — el envío automático está desactivado por diseño y la validación humana es siempre el último paso.

---

# 2. Resumen ejecutivo

| Campo | Valor |
|--------|-------|
| Nombre | Garum (gestor de correos) |
| Proyecto | MITÜMI |
| Lenguaje | Python |
| Estado | MVP funcional |
| Canal principal | Gmail, a través de **Composio** |
| Modelo IA | LLM con API compatible OpenAI (por defecto **Groq**) |
| Persistencia | SQLite (memoria del agente) + JSON (salidas) |
| Punto de entrada | `main.py` (un ciclo por ejecución) |
| Disparo remoto | `POST /agentes/garum/ciclos` en el gateway |

---

# 3. Objetivo

Automatizar el tratamiento de los correos recibidos manteniendo al usuario en el proceso de decisión. En cada ciclo el agente puede:

- Leer los correos no leídos de la bandeja (según el filtro configurado).
- Clasificarlos con un modelo de lenguaje y un umbral de confianza.
- Generar borradores de respuesta en el propio hilo de Gmail.
- Marcar como leídos los mensajes procesados (si está permitido).
- Registrar cada resultado en SQLite y en JSON para su trazabilidad.

---

# 4. Arquitectura general

```text
              Gmail (vía Composio)
                       │
                  main.py  ← un ciclo por ejecución
                       │
                  src/agente.py
          ┌────────────┼────────────┐
          │            │            │
     tools.py       llm.py     memoria.py
  (acciones Gmail  (clasifica   (SQLite: qué se
   controladas)     y redacta)   procesó ya)
          │            │            │
          └────────────┼────────────┘
                       │
        Borrador en Gmail + salida JSON
```

Detalle de diseño: el LLM **no ejecuta herramientas**. Las acciones sobre Gmail están catalogadas en `tools.py` y las invoca siempre el código Python, con permisos explícitos por configuración. El modelo solo clasifica y redacta.

---

# 5. Funcionalidades

## Lectura de Gmail
Consulta los correos no leídos mediante Composio, aplicando el filtro `GMAIL_QUERY` y el límite `MAX_EMAILS_PER_RUN` por ciclo.

## Clasificación inteligente
Cada mensaje se clasifica con el LLM siguiendo `prompts/prompt_clasificacion.txt`; solo se actúa si la confianza supera `CLASSIFICATION_MIN_CONFIDENCE`.

## Generación de borradores
Cuando el correo requiere contestación, redacta una propuesta (`prompts/prompt_redaccion.txt`) y la deja como **borrador en el hilo de Gmail** (`ALLOW_CREATE_DRAFTS`).

## Memoria de proceso
SQLite (`data/gestor_correos_mitumi.db`) registra qué correos ya se han tratado, evitando reprocesarlos en ciclos posteriores.

## Registro de actividad
Cada ciclo deja su resultado estructurado en `outputs/respuestas_json/`.

---

# 6. Qué NO hace

- **No envía correos** (`ALLOW_EMAIL_SEND=False`, decisión de diseño).
- No elimina mensajes.
- No modifica la base de datos del proyecto.
- No ejecuta acciones fuera del catálogo controlado de `tools.py`.
- No toma decisiones críticas sin supervisión humana.

---

# 7. Flujo de un ciclo

```text
Inicio del ciclo (main.py o gateway)
      │
Lectura de no leídos (Composio · GMAIL_QUERY)
      │
¿Ya procesado? ──sí──► se omite (memoria SQLite)
      │ no
Clasificación (LLM + umbral de confianza)
      │
¿Requiere respuesta?
      │ sí
Redacción del borrador → creado en el hilo de Gmail
      │
Marcar como leído (si ALLOW_MARK_AS_READ)
      │
Registro en SQLite + salida JSON
      │
Fin del ciclo (resultado por consola / gateway)
```

Cuando el ciclo se lanza desde el gateway, este devuelve un identificador para consultar el progreso y rechaza con HTTP 409 el lanzamiento de un segundo ciclo mientras hay uno en marcha.

---

# 8. Estructura del proyecto

```text
Garum_gestorcorreos/
├── data/
│   └── gestor_correos_mitumi.db      # memoria SQLite del agente
├── outputs/
│   └── respuestas_json/              # resultado estructurado de cada ciclo
├── prompts/
│   ├── prompt_clasificacion.txt
│   ├── prompt_redaccion.txt
│   └── reglas_comunes.txt
├── src/
│   ├── agente.py       # flujo principal del ciclo
│   ├── gmail.py        # conexión con Composio y funciones de Gmail
│   ├── llm.py          # comunicación con el modelo de lenguaje
│   ├── memoria.py      # memoria SQLite (correos ya procesados)
│   ├── tools.py        # catálogo de acciones controladas por Python
│   ├── funciones.py    # utilidades comunes
│   ├── parametros.py   # configuración centralizada
│   └── prompts.py      # carga de prompts
├── main.py             # punto de entrada: ejecuta UN ciclo
├── requirements.txt
└── .env.example
```

---

# 9. Componentes

| Módulo | Responsabilidad |
|---|---|
| `main.py` | Inicializa memoria, prompts y tools, y ejecuta un ciclo completo |
| `src/agente.py` | Orquesta el ciclo: lectura, clasificación, redacción, registro |
| `src/gmail.py` | Toda la interacción con Gmail a través de Composio |
| `src/llm.py` | Llamadas al LLM (API compatible OpenAI; Groq por defecto) |
| `src/memoria.py` | SQLite: evita reprocesar correos y guarda estados |
| `src/tools.py` | Catálogo interno de acciones; el LLM no las ejecuta |
| `src/parametros.py` | Lee la configuración del `.env` y la centraliza |

---

# 10. Variables de entorno

Copiar `.env.example` como `.env` y completar:

```text
LLM_API_KEY=            # clave del LLM (Groq) — obligatoria
LLM_BASE_URL=https://api.groq.com/openai/v1
LLM_MODEL=llama-3.1-8b-instant
CLASSIFICATION_MIN_CONFIDENCE=0.80

COMPOSIO_API_KEY=       # acceso a Gmail vía Composio — obligatoria
COMPOSIO_USER_ID=
GMAIL_QUERY=is:unread in:inbox newer_than:7d
MAX_EMAILS_PER_RUN=5

ALLOW_CREATE_DRAFTS=True
REQUIRE_THREAD_FOR_DRAFT=True
ALLOW_MARK_AS_READ=True
ALLOW_EMAIL_SEND=False   # el envío automático está desactivado por diseño

SHOW_STEPS=True
```

Sin `COMPOSIO_API_KEY` o `LLM_API_KEY` el ciclo termina con un error controlado que indica qué falta. Las credenciales nunca se almacenan en el código ni en el repositorio.

---

# 11. Instalación y ejecución

```bash
cd agentes/Garum_gestorcorreos
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env    # y completar credenciales
python main.py          # ejecuta UN ciclo y muestra el resultado
```

Desde el sistema integrado, el ciclo se lanza con `POST /agentes/garum/ciclos` en el gateway y se consulta con `GET /agentes/garum/ciclos/{id_ciclo}`.

---

# 12. Gestión de errores

| Error | Acción realizada |
|---------|-----------------|
| Falta una credencial (`.env`) | El ciclo devuelve un resultado controlado indicando la variable ausente |
| Error de acceso a Gmail/Composio | Se registra y el ciclo termina con estado de error |
| Error del modelo LLM | Se genera un resultado de error controlado, sin respuestas incompletas |
| Correo ya procesado | Se omite gracias a la memoria SQLite |
| Error al guardar la salida | Se registra la incidencia sin corromper lo ya almacenado |

---

# 13. Seguridad

- Credenciales exclusivamente en variables de entorno (`.env`, fuera de git).
- El envío de correo está **deshabilitado por configuración**; solo borradores.
- El LLM no ejecuta herramientas: las acciones las controla el código.
- Acceso a Gmail delegado en Composio, sin gestionar OAuth propio.
- Registro estructurado de cada ciclo para trazabilidad y auditoría.

---

# 14. Estructura de salida

Cada ciclo genera un resultado estructurado, por consola y en `outputs/respuestas_json/`:

```json
{
  "ok": true,
  "estado": "completado",
  "resultados": [
    {
      "correo": "…",
      "clasificacion": "…",
      "borrador_creado": true,
      "requiere_revision": true
    }
  ]
}
```

---

# 15. Limitaciones actuales

- Depende de la disponibilidad de Composio y del proveedor del LLM.
- Procesa un máximo de `MAX_EMAILS_PER_RUN` correos por ciclo.
- No envía respuestas ni elimina correos (por diseño).
- La clasificación depende del umbral de confianza configurado.

Evolución prevista: soporte de más proveedores de correo, priorización automática y panel de revisión de borradores integrado en la plataforma.
