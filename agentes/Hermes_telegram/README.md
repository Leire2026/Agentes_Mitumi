
# Documentación del Agente: Hermes (agente de ponentes por Telegram)

> **Autor:** Equipo de Data Science
> **Estado:** 🟢 MVP funcional

## Nota importante

Este agente nunca ejecuta acciones directamente sobre la base de datos ni envía comunicaciones sin permiso explícito de configuración. Analiza la información, propone acciones y devuelve una respuesta estructurada para su validación.

# 1. Resumen ejecutivo

| Campo | Valor |
|---|---|
| Nombre | Hermes (agente_telegram_ponentes) |
| Propósito | Gestionar las consultas y documentación de los ponentes de un evento a través de Telegram. |
| Fase | Preparación, seguimiento y soporte durante el evento. |
| Interfaz | Bot de Telegram (long polling); el frontend no lo llama |
| Modelo LLM | Groq (llama-3.1, configurable vía `.env`) |
| Framework | API compatible OpenAI (cliente HTTP propio) |
| Fuente de datos | PostgreSQL/Neon con rol de solo lectura |
| Criticidad | Media |
| Estado | MVP funcional |

# 2. Estructura interna

```text
Hermes_telegram/
├── servicio.py            # bucle principal del bot (long polling)
├── config/
│   ├── settings.py        # variables de entorno y parámetros
│   ├── permisos.py        # permisos explícitos (enviar, avisar admin…)
│   └── fuentes.py         # rutas de datos
├── src/
│   ├── agente.py          # lógica del agente
│   ├── herramientas.py    # consultas a la BD (con permisos)
│   ├── memoria.py         # estado por usuario (evento activo, solicitudes)
│   ├── funciones.py · schemas.py · validaciones.py
├── integrations/
│   ├── telegram.py        # API de Telegram (mensajes, botones, documentos)
│   ├── database.py        # lectura de PostgreSQL/Neon
│   └── llm.py             # llamadas al modelo de lenguaje
├── prompts/               # prompt de sistema, análisis y validación
├── data/
│   ├── estado/            # vinculación telegram↔ponente y estado por usuario
│   └── documentos_prueba/ # documentos de ejemplo que el bot puede enviar
├── requirements.txt
└── .env.example
```

# 3. Propósito y límites

## Capacidades
- Resolver consultas de ponentes sobre hoteles, vuelos, taxis, agenda, lugar y documentación de sus eventos, con datos reales de la base de datos.
- Ofrecer botones rápidos (vuelo, hotel, taxi, lugar, urgencia, selección de evento).
- Recibir documentos del ponente y registrar los pendientes.
- Escalar urgencias e incidencias al administrador con propuesta estructurada.

## Limitaciones
- No modifica la base de datos (rol de solo lectura).
- No envía mensajes si los permisos de configuración no lo autorizan.
- No reserva hoteles ni transportes.

# 4. Inicio rápido

```bash
cp .env.example .env   # TELEGRAM_BOT_TOKEN, LLM_API_KEY (Groq) y DATABASE_URL
pip install -r requirements.txt
python servicio.py
```

Desde el sistema completo: `./arrancar_todo.sh --con-hermes` (solo debe existir **una** instancia del bot a la vez).

## Vinculación Telegram ↔ ponente

El bot identifica al ponente que escribe mediante `data/estado/mapeo_telegram_ponentes.json` (`telegram_user_id` → id del ponente en la base de datos). Este mapeo local es el mecanismo vigente mientras la tabla `ponentes` no incorpore la columna `telegram_user_id`; una vez migrada, la vinculación pasará a resolverse en la propia base de datos.

# 5. Lógica de decisión

1. Lee los updates de Telegram (long polling).
2. Identifica al ponente por su vinculación y la intención del mensaje o botón.
3. Consulta la base de datos cuando es necesario (eventos activos, logística, documentos).
4. Construye el contexto y genera la respuesta mediante el LLM.
5. Valida el formato y los permisos.
6. Responde al ponente y/o escala al administrador, siempre de forma estructurada.

# 6. Modos de fallo

| Fallo | Recuperación |
|---|---|
| Error de red de Telegram | Se registra y se reintenta en el siguiente ciclo (el bucle está protegido) |
| Error procesando un mensaje | Se registra y se continúa con el resto; no tumba el servicio |
| Información incompleta | Solicitar datos al usuario |
| Usuario sin vinculación | Se informa y no se expone información de ningún ponente |
| Error de herramienta/BD | Registrar el error y responder de forma controlada |

# 7. Observabilidad

- Trazas por consola (`SHOW_STEPS`) y registros en `logs/`.

# 8. Determinismo

Las consultas a la base de datos son deterministas. Las respuestas del LLM pueden variar según el modelo y la temperatura configurados.

# 9. Contrato interno

## Entrada (payload construido desde cada update de Telegram)

```json
{
  "tipo_peticion": "consulta",
  "datos": { "telegram_chat_id": "…", "texto": "…" },
  "modo": "propuesta"
}
```

## Salida

```json
{
  "ok": true,
  "agente": "agente_telegram_ponentes",
  "resumen": "Consulta resuelta",
  "acciones_propuestas": [],
  "requiere_validacion_humana": true
}
```

# 10. Reglas comunes

- No escribir directamente en BD.
- No enviar comunicaciones sin permiso de configuración.
- Salida siempre estructurada.

# 11. Herramientas

| Herramienta | Función |
|---|---|
| obtener_ponente_por_telegram | Identificar al ponente que escribe |
| obtener_eventos_activos_ponente | Listar sus eventos activos |
| obtener_info_ponente_evento | Logística completa (hotel, viajes, horarios) |
| obtener_documentos_ponente_evento | Documentos disponibles y pendientes |
| registrar_documento_pendiente · crear_incidencia | Registro para validación humana |

# 12. Seguridad

Cumplimiento RGPD, credenciales mediante `.env`, rol de base de datos de solo lectura, permisos explícitos para cada acción con efecto externo y validación de entradas frente a prompt injection.

# 13. Métricas

- Tasa de éxito.
- Latencia.
- Coste por interacción.
- Tasa de derivación a humano.

# 14. Casos de prueba

- Consulta sobre hotel → devuelve la información real del evento activo.
- Falta documentación → genera bloqueo y solicita el documento.
- Usuario sin vincular → mensaje informativo, sin datos de terceros.

# 15. Referencias

- Repositorio del proyecto y contrato de rutas del sistema (`docs/`).
