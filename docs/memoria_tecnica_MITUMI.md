# Memoria técnica del proyecto MITÜMI

## Sistema inteligente para la gestión integral de eventos

**Proyecto multidisciplinar de desarrollo Full Stack y Data Science & IA**  
> Esta memoria consolida la documentación funcional y técnica de la aplicación web, la preparación de datos y los agentes de inteligencia artificial desarrollados para MITÜMI. Los datos utilizados durante el desarrollo proceden de fuentes públicas o han sido generados con fines de prueba; no constituyen la base operativa real de MITÜMI.

---

## Resumen ejecutivo

MITÜMI es una solución digital orientada a centralizar la gestión integral de eventos empresariales. El proyecto responde a la necesidad de reunir en una única plataforma la información que interviene en la planificación y ejecución de un evento: clientes, ponentes, espacios, salas, ponencias, presupuestos, documentación y estados de seguimiento.

La solución combina una aplicación web corporativa de tipo ERP con seis agentes de inteligencia artificial especializados. La aplicación ofrece una interfaz para consultar y administrar la información, mientras que los agentes asisten en procesos documentales, consultas internas, detección de oportunidades, planificación de viajes, coordinación logística y gestión del correo electrónico.

La plataforma se ha desarrollado mediante una arquitectura cliente-servidor. El frontend es una aplicación SPA construida con React y Vite; el backend expone una API REST desarrollada con Node.js y Express; y la persistencia se realiza en una base de datos PostgreSQL gestionada mediante Prisma ORM y alojada en Neon. La autenticación combina Firebase Authentication con sesiones JWT, y los archivos se almacenan mediante Cloudinary. La aplicación está preparada para su despliegue en Vercel.

La capa inteligente está compuesta por los agentes `operis`, `lumen`, `vigil`, `jano`, `hermes` y `garum`. Estos componentes emplean modelos de lenguaje, recuperación de información, datos estructurados y herramientas externas. Los agentes se han desarrollado como MVP independientes y presentan distintos grados de conexión con la aplicación. Por ello, esta memoria diferencia entre capacidades implementadas, puntos de integración preparados y funcionalidades previstas para una fase posterior.

El resultado es una arquitectura funcional y extensible que demuestra cómo una aplicación de gestión puede complementarse con asistentes especializados para reducir tareas repetitivas, mejorar el acceso a la información y mantener la supervisión humana sobre las decisiones sensibles.

---

## 1. Introducción

La organización de eventos requiere coordinar múltiples tareas, recursos y participantes. Cuando la información se encuentra repartida entre hojas de cálculo, documentos, correos electrónicos y aplicaciones independientes, aumentan los tiempos de gestión, la duplicidad de datos y el riesgo de errores. Esta fragmentación también dificulta conocer el estado real de un evento y mantener una comunicación coherente con clientes, proveedores y ponentes.

MITÜMI se plantea como una plataforma única para organizar y consultar la información relacionada con los eventos. El sistema permite gestionar clientes, eventos, ponentes, ponencias, espacios, salas, presupuestos, usuarios y documentación, proporcionando una visión centralizada de los procesos operativos.

Como elemento diferencial, la plataforma incorpora agentes de inteligencia artificial orientados a tareas concretas. Estos agentes pueden interpretar solicitudes en lenguaje natural, consultar documentos o datos, utilizar herramientas y producir respuestas estructuradas. Su finalidad no es sustituir el criterio profesional, sino asistir al equipo de MITÜMI en actividades repetitivas o intensivas en información.

El proyecto se desarrolló de manera multidisciplinar entre las áreas de desarrollo Full Stack y Data Science & IA. La memoria se organiza por componentes del producto, evitando presentar ambas áreas como soluciones separadas.

### 1.1. Objetivo general

Diseñar e implementar una solución digital integrada que centralice la gestión de eventos empresariales y automatice procesos operativos mediante agentes de inteligencia artificial, garantizando la trazabilidad de la información y la supervisión humana de las acciones sensibles.

### 1.2. Objetivos específicos

1. Identificar y recopilar fuentes de información relevantes para construir un conjunto inicial de datos de prueba.
2. Depurar, homogeneizar y validar los datos antes de incorporarlos al sistema.
3. Diseñar una base de datos relacional que represente las entidades y relaciones del dominio.
4. Implementar una API REST segura y reutilizable para gestionar los recursos de la aplicación.
5. Desarrollar una interfaz web responsive que facilite la consulta y administración de eventos.
6. Implementar agentes especializados para automatizar tareas documentales, informativas y de comunicación.
7. Definir contratos y puntos de integración entre la aplicación, los agentes y los servicios externos.
8. Proteger las operaciones mediante autenticación, autorización, validaciones y límites de actuación.
9. Verificar el funcionamiento de los componentes mediante pruebas técnicas y funcionales.
10. Diseñar una arquitectura mantenible que permita incorporar nuevas funcionalidades y agentes.

### 1.3. Alcance

El proyecto comprende el diseño y desarrollo de una aplicación web corporativa destinada a centralizar y optimizar la gestión integral de eventos empresariales. La solución abarca la administración de clientes, ponentes, eventos, espacios, salas, ponencias, presupuestos, usuarios y archivos asociados.

El alcance incluye:

- Recopilación, limpieza y preparación de datos iniciales procedentes de fuentes públicas o generados para pruebas.
- Diseño de un esquema relacional formado por 10 tablas, sus relaciones, restricciones, migraciones y población inicial.
- Desarrollo de una API REST con 50 endpoints documentados, autenticación, autorización por roles, validación de entradas y manejo centralizado de errores.
- Implementación de infraestructura para almacenar archivos en Cloudinary.
- Desarrollo de una aplicación SPA con más de 15 vistas, rutas protegidas, contexto global de autenticación, componentes reutilizables y diseño responsive.
- Implementación de seis agentes de IA especializados: Operis, Lumen, Vigil, Jano, Hermes y Garum.
- Uso de recuperación aumentada por generación, cuando el caso de uso requiere consultar documentos o conocimiento específico.
- Conexión con fuentes de datos, correo y servicios externos de consulta.
- Definición de mecanismos de comunicación entre la aplicación y los agentes.
- Pruebas funcionales de la API y casos de validación propios de los agentes.

La infraestructura del backend y los agentes se encuentra desarrollada en forma de MVP. Parte de los CRUD del frontend y algunos puntos de integración con los agentes estaban pendientes de conexión en el momento de redactar la documentación original. En consecuencia, no todas las capacidades descritas constituyen un flujo productivo de extremo a extremo.

### 1.4. Limitaciones

- Los datos de desarrollo no representan información operativa real de MITÜMI.
- Los seis agentes presentan un estado MVP y se distribuyen como componentes independientes.
- Algunas integraciones se encuentran diseñadas o preparadas, pero no desplegadas como un sistema único.
- La disponibilidad, el coste y la calidad de las respuestas dependen de modelos y servicios externos.
- Los resultados producidos por IA pueden requerir revisión y no deben considerarse decisiones definitivas.
- Las acciones con impacto operativo, económico o comunicativo requieren validación humana.
- No se dispone en los documentos originales de métricas homogéneas de precisión, cobertura o rendimiento para todos los agentes.

---

## 2. Metodología y fases de trabajo

El proyecto se organizó en cuatro fases relacionadas entre sí.

### 2.1. Identificación de fuentes y recopilación de datos

Se localizaron fuentes públicas relevantes para crear información inicial sobre espacios, salas, clientes, eventos, ponentes, estados, presupuestos y usuarios. Los datos se trasladaron a hojas de cálculo y archivos CSV para su revisión, normalización y posterior carga.

### 2.2. Diseño e implementación de la base de datos

A partir de los requisitos funcionales se definieron las entidades, relaciones, restricciones e identificadores. El modelo se implementó en PostgreSQL mediante Prisma ORM, incluyendo migraciones y un proceso de población inicial.

### 2.3. Diseño y desarrollo de la aplicación web

Se desarrollaron el backend REST y la aplicación SPA. El trabajo incluyó autenticación, autorización, operaciones CRUD, manejo de errores, subida de archivos, rutas protegidas, componentes reutilizables y adaptación responsive.

### 2.4. Desarrollo e integración de agentes de IA

Se identificaron tareas manuales o intensivas en información susceptibles de ser asistidas mediante IA. Cada agente se diseñó para un dominio limitado, con entradas y salidas definidas, herramientas específicas y restricciones de seguridad.

---

## 3. Análisis funcional

### 3.1. Usuarios y roles

La plataforma está orientada principalmente al personal encargado de administrar eventos. La autenticación permite identificar al usuario y la autorización limita determinadas operaciones según su rol. El administrador dispone de acceso a las áreas principales del sistema y a la interfaz de consulta de agentes.

### 3.2. Funcionalidades principales

- Alta, consulta, actualización y eliminación de recursos del dominio.
- Consulta del estado general y del histórico de eventos.
- Asociación de ponentes, ponencias, espacios y salas.
- Gestión de presupuestos y documentación relacionada.
- Autenticación de usuarios y protección de rutas.
- Carga de imágenes, currículos, presentaciones y archivos de viaje.
- Consulta en lenguaje natural sobre información interna.
- Extracción de datos desde briefings y documentos.
- Seguimiento de oportunidades y licitaciones.
- Asistencia en viajes y comunicaciones logísticas.
- Clasificación y preparación de respuestas de correo.

### 3.3. Requisitos no funcionales

- **Seguridad:** autenticación, autorización por roles, validación de entradas, secretos fuera del repositorio y permisos mínimos.
- **Mantenibilidad:** separación por capas, componentes reutilizables y configuración mediante variables de entorno.
- **Interoperabilidad:** intercambio de información mediante HTTP y JSON.
- **Usabilidad:** navegación coherente y diseño adaptado a distintos tamaños de pantalla.
- **Trazabilidad:** conservación de fuentes y resultados en los flujos donde se procesan documentos o comunicaciones.
- **Escalabilidad:** separación entre frontend, API, base de datos, almacenamiento y agentes.
- **Supervisión:** revisión humana antes de acciones con consecuencias económicas, contractuales o comunicativas.

---

## 4. Arquitectura general

### 4.1. Componentes

```text
Usuario
  |
  v
Frontend SPA: React + Vite + React Router
  |
  | HTTPS / JSON
  v
Backend REST: Node.js + Express
  |-- Autenticación: Firebase + JWT
  |-- Persistencia: Prisma ORM -> PostgreSQL/Neon
  |-- Archivos: Cloudinary
  `-- Puntos de integración con agentes
        |-- Operis
        |-- Lumen
        |-- Vigil
        |-- Jano
        |-- Hermes
        `-- Garum
```

Los agentes no comparten necesariamente una única implementación técnica. Algunos exponen una API HTTP, otros pueden ejecutarse mediante línea de comandos y otros utilizan conectores propios. La arquitectura objetivo prevé homogeneizar su acceso a través de contratos JSON y una capa de integración controlada.

### 4.2. Arquitectura del backend

El backend sigue una separación por capas:

```text
Ruta -> validación -> controlador -> servicio -> Prisma ORM -> PostgreSQL
```

Las rutas definen los métodos y recursos HTTP. Las validaciones comprueban los datos de entrada. Los controladores coordinan cada petición, mientras que los servicios contienen la lógica de negocio y el acceso a datos. Prisma traduce las operaciones a consultas sobre PostgreSQL.

### 4.3. Arquitectura de los agentes

Aunque cada agente responde a un caso de uso diferente, la arquitectura conceptual incluye:

1. Recepción y validación de una solicitud.
2. Interpretación de la intención del usuario.
3. Recuperación de contexto desde datos, documentos o memoria temporal.
4. Selección y uso de herramientas autorizadas.
5. Generación de una respuesta estructurada.
6. Validación de formato, permisos y restricciones.
7. Revisión humana cuando el flujo lo requiere.

Los agentes no se consideran sistemas de aprendizaje autónomo. Su mejora se realiza mediante evaluación de resultados, ajustes de instrucciones, actualización de fuentes y modificación controlada de los flujos.

### 4.4. Tecnologías principales

| Área | Tecnologías |
|---|---|
| Frontend | React, Vite, React Router, SASS, Firebase cliente |
| Backend | Node.js, Express, Prisma ORM, express-validator |
| Datos | PostgreSQL, Neon, migraciones y seed con Prisma |
| Autenticación | Firebase Authentication, Firebase Admin y JWT |
| Archivos | Multer y Cloudinary |
| Despliegue | Vercel y Neon |
| Agentes | Python, APIs HTTP/Flask, modelos de lenguaje, RAG y conectores externos, según el agente |

---

## 5. Fuentes y preparación de datos

Para las entidades relacionadas con espacios y salas se utilizó principalmente el portal público Basque Events. La recopilación se limitó geográficamente al País Vasco, de acuerdo con el ámbito del cliente.

Para completar información de clientes, estados, eventos, ponentes, presupuestos y usuarios se consultaron fuentes públicas y especializadas como SPRI, Turismo Euskadi, Convention Bureaus, eInforma y guías profesionales de gestión de eventos.

El proceso seguido fue:

```text
Selección de fuentes -> recopilación -> normalización -> validación -> CSV -> carga inicial
```

Cuando un valor no pudo comprobarse mediante una fuente fiable, se conservó como nulo para evitar incorporar información inventada. Los conjuntos generados contienen cantidades distintas de registros según la disponibilidad de cada entidad, con un rango documentado de entre 3 y 120 registros.

Los datos se utilizaron exclusivamente para desarrollo y demostración. Antes de un uso real deberán sustituirse o validarse con información autorizada por MITÜMI y someterse a una revisión de protección de datos.

---

## 6. Base de datos

La persistencia utiliza PostgreSQL y Prisma ORM. El esquema documentado contiene 10 tablas y emplea identificadores UUID v7. Entre las entidades principales se encuentran clientes, eventos, ponencias, ponentes, espacios, salas, estados, presupuestos y usuarios, además de la relación necesaria entre eventos y ponentes.

### 6.1. Relaciones principales

- Un cliente puede estar asociado a varios eventos.
- Un evento se relaciona con su estado y con los recursos necesarios para su organización.
- Un evento puede incluir varias ponencias y varios ponentes.
- Un espacio puede contener varias salas.
- Los presupuestos se relacionan con el evento correspondiente.
- Los usuarios representan las identidades autorizadas para operar en la plataforma.

### 6.2. Integridad y migraciones

El esquema utiliza claves primarias, claves foráneas, restricciones de unicidad, campos obligatorios e índices. Las migraciones se gestionan con Prisma y se aplican mediante comandos diferenciados para desarrollo y despliegue. La población inicial respeta el orden de dependencias entre entidades.

### 6.3. Acceso de los agentes

El modelo objetivo prioriza el acceso a través de la API REST. Sin embargo, algunos MVP contemplan consultas directas de solo lectura a PostgreSQL cuando necesitan recuperar contexto. Esta excepción debe limitarse mediante credenciales específicas, permisos mínimos y registro de consultas. Ningún agente debería modificar directamente información operativa sin pasar por validaciones y autorización.

---

## 7. Desarrollo de la aplicación

### 7.1. Backend y API REST

El backend se implementó con Node.js y Express. La API documenta 50 endpoints agrupados en salud del sistema, autenticación, eventos, clientes, espacios, ponentes, salas, ponencias, estados, usuarios, presupuestos y subida de archivos.

Las respuestas utilizan una estructura JSON común basada en los campos `ok`, `data`, `message` y, cuando corresponde, `meta`. El manejo centralizado de errores traduce fallos de validación, autenticación, autorización, recursos inexistentes y restricciones de base de datos a códigos HTTP coherentes.

### 7.2. Autenticación y autorización

El frontend utiliza Firebase Authentication para el acceso con Google. El backend verifica la identidad y genera una sesión JWT, almacenada mediante cookie. Los middlewares comprueban la existencia y validez de la sesión, y la autorización limita rutas según los roles permitidos.

### 7.3. Gestión de archivos

Multer procesa las cargas y Cloudinary almacena los archivos. La infraestructura está planteada para imágenes de ponentes, currículos, presentaciones y documentos de viaje. Antes de un despliegue productivo deben verificarse límites de tamaño, tipos MIME, análisis de contenido y política de conservación.

### 7.4. Frontend

El frontend se construyó como SPA con React, Vite y React Router. Incluye más de 15 vistas, rutas protegidas, contexto global de autenticación, componentes reutilizables y estilos con SASS y metodología BEM.

La interfaz contiene páginas para los recursos principales y componentes destinados a la interacción con agentes. En el momento reflejado por la memoria original, la autenticación y parte del consumo de la API estaban conectados, mientras que varios CRUD y rutas de subida permanecían pendientes de integración completa.

### 7.5. Sistema de diseño

La interfaz se apoya en un sistema de diseño propio, documentado de forma independiente (`sistema-diseno.md`, en esta misma carpeta), que garantiza la coherencia visual de todas las vistas:

- **Arquitectura SCSS por capas**: un punto de entrada único (`style.scss`) que importa, en orden, el reset, las variables, los mixins, la tipografía y la estructura base, de modo que cualquier componente nuevo hereda las mismas reglas.
- **Variables centralizadas**: la paleta de colores corporativa, la escala tipográfica, el sistema de espaciado y los radios de borde se definen una sola vez; cambiar la identidad visual es cambiar un archivo.
- **Mixins reutilizables** para los patrones repetidos (composición flex y grid, media queries y tipografía), que evitan duplicar código en los componentes.
- **Diseño responsive** con breakpoints definidos como variables, siguiendo un enfoque adaptado a móvil, tablet y escritorio.
- **Metodología BEM** en el nombrado de clases, alineada con la organización por componentes de React.

Este sistema es el que permite que páginas construidas por distintas personas —incluida la página de interacción con los agentes— compartan aspecto y comportamiento sin coordinación manual.

---

## 8. Agentes de inteligencia artificial

### 8.1. Operis: extracción y actualización documental

`operis` transforma briefings en formatos TXT, PDF o DOCX en una propuesta JSON normalizada. Extrae información del evento, cliente, ponentes y notas operativas; puede fusionarla con el estado anterior y detectar campos pendientes.

El agente conserva los datos no modificados y evita completar mediante suposiciones aquello que no aparece en la fuente. Puede consultar PostgreSQL en modo de solo lectura para recuperar el estado previo. El resultado nunca se incorpora automáticamente: requiere validación humana antes de actualizar la aplicación.

**Flujo principal:**

```text
Texto libre o documento
    → extracción de campos mediante el modelo de lenguaje
    → normalización según el contrato V2
    → generación de un JSON estructurado
    → precarga del formulario de la plataforma
    → revisión y validación humana
```

Cuando el contenido procede de un documento escaneado, el agente dispone de un mecanismo OCR de respaldo antes de efectuar la extracción. El identificador del evento es obligatorio en el contrato V2.

**Estado:** MVP.  
**Interfaz documentada:** línea de comandos y API Flask.  
**Permisos:** lectura y generación de propuestas, sin aprobación de presupuestos ni cambios operativos.

### 8.2. Lumen: consulta de información interna

`lumen` responde preguntas en lenguaje natural sobre la información disponible en MITÜMI. Recupera datos relevantes, aplica restricciones de seguridad y devuelve una respuesta JSON estructurada.

Está diseñado como agente de consulta. No modifica registros ni ejecuta acciones sobre presupuestos, fechas, reservas, viajes o proveedores. Su futura integración debe centralizar el acceso y aplicar los mismos permisos que la aplicación.

**Flujo principal:**

```text
Pregunta del usuario
    → interpretación de la consulta mediante el modelo de lenguaje
    → recuperación del contexto del esquema mediante RAG
    → consulta a Neon con un rol de solo lectura
    → generación de una respuesta en lenguaje natural con datos reales
```

El agente conserva memoria de la conversación para admitir consultas encadenadas y dispone de un comando de reinicio para eliminar el contexto acumulado.

**Estado:** MVP.  
**Función:** consulta y recuperación de información.  
**Permisos:** solo lectura.

### 8.3. Vigil: monitorización de concursos públicos

`vigil` consulta fuentes de licitaciones públicas, filtra oportunidades relevantes y prepara resultados para su revisión. Su finalidad es reducir el trabajo manual de búsqueda y facilitar el seguimiento de convocatorias relacionadas con la actividad de MITÜMI.

El flujo contempla recopilación, normalización, clasificación, eliminación de duplicados y publicación de resultados estructurados. La periodicidad, las fuentes definitivas y el canal de alertas deben configurarse antes de un uso productivo.

**Flujo principal:**

```text
Ejecución diaria del proceso de recopilación
    → consulta de la Plataforma de Contratación Pública de Euskadi
    → filtrado con un modelo de lenguaje según relevancia
    → detección de concursos nuevos o modificados
    → generación de un JSON y un archivo .ics por concurso
    → almacenamiento del histórico en SQLite
    → publicación en la sección «Concursos Públicos»
```

El flujo incorpora un semáforo de urgencia calculado según los días hábiles restantes y un etiquetado temático que facilita la distribución de la revisión. En el despliegue Docker documentado no se ejecuta el scraping en vivo porque la imagen no incorpora los navegadores de Playwright.

**Estado:** MVP.  
**Modo:** lectura, clasificación y generación de resultados.  
**Límite:** no presenta ofertas ni compromete a la empresa.

### 8.4. Jano: búsqueda y planificación de viajes

`jano` asiste en la búsqueda de alternativas de transporte y alojamiento para ponentes. A partir de los requisitos del viaje, consulta servicios disponibles y devuelve opciones estructuradas mediante una API HTTP.

Los precios y la disponibilidad pueden cambiar, por lo que deben verificarse en el proveedor antes de tomar una decisión. El agente no realiza compras ni reservas de forma autónoma.

**Flujo principal:**

```text
Formulario con los datos del ponente y del evento
    → solicitud POST al endpoint /buscar
    → búsqueda de hotel, vuelo o tren, taxi y coche de alquiler
    → generación de sugerencias estructuradas en JSON
    → creación de un PDF para el ponente sin precios
    → creación de un PDF interno para MITÜMI con precios
    → revisión humana de las alternativas
```

El agente funciona como un servicio de búsqueda bajo demanda y no necesita acceso directo a la base de datos. Los enlaces proporcionados permiten continuar el proceso en el proveedor correspondiente.

**Estado:** MVP.  
**Modo:** consulta y comparación.  
**Supervisión:** obligatoria antes de cualquier reserva o pago.

### 8.5. Hermes: coordinación logística con ponentes

`hermes` ayuda a preparar comunicaciones relacionadas con la participación de ponentes: solicitud de información, confirmaciones, recordatorios y coordinación logística. El agente utiliza datos estructurados del evento y genera mensajes ajustados al contexto.

El contrato de entrada y salida permite integrarlo con otros componentes. Las comunicaciones deben respetar los permisos, el consentimiento, la privacidad y las reglas de estilo. El envío definitivo requiere control humano salvo que se defina expresamente un flujo automatizado autorizado.

**Flujo principal:**

```text
Mensaje recibido mediante Telegram
    → identificación del ponente por telegram_user_id
    → uso de un mapeo local como mecanismo de respaldo
    → consulta de eventos y datos logísticos en Neon
    → construcción de una respuesta contextualizada
    → envío de la respuesta al chat de Telegram
```

Hermes se ejecuta como un proceso residente y no expone una interfaz HTTP propia. El uso del mapeo local permite mantener el funcionamiento hasta que el identificador de Telegram esté disponible de forma definitiva en el esquema de la base de datos.

**Estado:** MVP.  
**Función:** generación y coordinación de comunicaciones.  
**Supervisión:** necesaria en comunicaciones externas.

### 8.6. Garum: clasificación y gestión del correo

`garum` constituye la capa de asistencia para la gestión de correo. Puede leer mensajes autorizados, clasificarlos, consultar la disponibilidad en Google Calendar, generar borradores de respuesta y emitir notificaciones mediante Telegram. La conexión con Gmail se realiza a través de Composio.

Las salidas estructuradas y el estado de los ciclos se registran en SQLite para facilitar la revisión y la auditoría. El agente no envía correos de forma autónoma, por lo que el usuario mantiene el control de la acción final. Aunque la documentación previa menciona un RAG histórico, esta funcionalidad no forma parte de la implementación actual.

**Flujo principal:**

```text
Solicitud POST a /agentes/garum/ciclos
    → respuesta HTTP 202 e inicio del ciclo
    → lectura y análisis de correos nuevos
    → clasificación de los mensajes
    → consulta de calendario cuando resulte necesaria
    → propuesta de respuesta y notificación por Telegram
    → registro del proceso y sus resultados en SQLite
    → revisión y validación humana
```

El diseño por ciclos permite auditar cada ejecución y evita procesamientos simultáneos. Cuando ya existe un ciclo en curso, el sistema responde con un conflicto HTTP 409.

**Estado:** MVP.  
**Integraciones:** Gmail mediante Composio, Google Calendar, Telegram y SQLite.  
**Supervisión:** obligatoria antes del envío.

---

## 9. Backend de Data: la capa de integración

La integración utiliza JSON como formato común y HTTP como mecanismo de comunicación. Para evitar que el frontend tuviera que conocer seis servicios distintos, el equipo de Data construyó una capa propia de integración —el **Backend de Data**— compuesta por dos piezas:

**El gateway (FastAPI).** Punto de entrada único para el frontend: todos los agentes se consumen bajo una misma URL con rutas normalizadas `/agentes/<nombre>/...`. Funciona como proxy HTTP, de modo que cada agente sigue siendo un servicio independiente (sin colisiones de dependencias entre ellos) y puede sustituirse o actualizarse sin que el frontend cambie una línea. Además del enrutado, el gateway aporta:

- **Salud agregada** (`GET /salud`): estado de todas las piezas del sistema en una sola llamada, consultado en paralelo.
- **Gestión de ciclos** para el agente de correo (que no es un servidor residente): lanza cada ciclo en segundo plano, devuelve un identificador para consultar el progreso y rechaza ejecuciones simultáneas con HTTP 409.
- **Contrato de errores común** `{"error": true, "codigo": ..., "mensaje": ...}` con códigos propios (agente caído 502, ruta desconocida 404, ciclo en marcha 409), y documentación interactiva generada automáticamente (`/docs`).

**El backend de datos para agentes (FastAPI).** API de lectura que sirve datos de PostgreSQL (ponentes, eventos y logística de ponencias) a los agentes que no exponen HTTP, como el bot de Telegram o el gestor de correo. Lee siempre con el rol de solo lectura y simula las escrituras en memoria local, manteniendo la regla de que ningún agente escribe en la base de datos.

La capa se completa con una **configuración unificada**: un único archivo de entorno para todo el sistema, un script de arranque que levanta los servicios con sus puertos correctos y un test de humo que verifica el `GET /health` —uniforme en todos los servicios— de cada pieza.

| Agente | Estado | Interfaz | Fuente principal | Acción sensible |
|---|---|---|---|---|
| Operis | MVP | HTTP vía gateway | Documentos y PostgreSQL | Actualización de datos |
| Lumen | MVP | HTTP vía gateway | PostgreSQL / contexto interno | Ninguna; solo consulta |
| Vigil | MVP | HTTP vía gateway | Fuentes públicas | Publicación de alertas |
| Jano | MVP | HTTP vía gateway | Servicios de viajes | Reserva o pago |
| Hermes | MVP | Telegram (consume el backend de datos) | Datos logísticos | Envío de comunicaciones |
| Garum | MVP | Ciclos vía gateway | Gmail y RAG | Envío de correo |

Las acciones sensibles se canalizan mediante aprobación humana; en una evolución productiva, la pasarela añadiría autenticación por clave y auditoría centralizada.

---

## 10. Seguridad, privacidad y supervisión

La solución aplica o prevé las siguientes medidas:

- Autenticación de usuarios mediante Firebase.
- Sesiones JWT verificadas en el backend.
- Autorización por roles.
- Validación y saneamiento de entradas.
- Variables de entorno para credenciales y secretos.
- Acceso de solo lectura para agentes de consulta.
- Respuestas estructuradas y validación antes de devolver resultados.
- Supervisión humana antes de modificar datos, enviar comunicaciones, reservar viajes o asumir compromisos económicos.
- Conservación de las fuentes consultadas cuando el flujo necesita trazabilidad.

Antes de tratar datos reales deben definirse formalmente la base de legitimación, la política de conservación y eliminación, el consentimiento para comunicaciones, el registro de accesos y las medidas frente a prompt injection o documentos maliciosos incorporados a RAG.

---

## 11. Pruebas y resultados

### 11.1. Aplicación

El backend utiliza el módulo nativo `node --test`. La estrategia documentada contempla pruebas de servicios, autenticación, autorización, validaciones y endpoints. Las pruebas comprueban códigos HTTP, formato de respuestas, rechazo de datos inválidos e integridad referencial.

El frontend dispone de flujos funcionales de autenticación, navegación y componentes principales. La documentación original no aporta una cifra consolidada de cobertura ni evidencia homogénea de pruebas automatizadas del frontend.

### 11.2. Resultados obtenidos

Los principales resultados acreditados por la documentación son:

1. Un modelo relacional con migraciones y datos iniciales.
2. Una API REST de 50 endpoints con autenticación, autorización y validación.
3. Una SPA responsive con más de 15 vistas y rutas protegidas.
4. Integración con Firebase, Cloudinary y PostgreSQL en la nube.
5. Seis agentes especializados entregados como MVP.
6. Contratos estructurados y puntos de integración definidos para varios agentes.
7. Aplicación de supervisión humana en operaciones sensibles.
8. Una arquitectura extensible preparada para completar la integración entre la plataforma y los agentes.

Estos resultados no implican que todos los flujos estén conectados en producción. La integración completa y su evaluación cuantitativa constituyen el principal trabajo pendiente.

---

## 12. Despliegue y operación

El sistema se despliega en tres piezas independientes:

- **Aplicación web**: el frontend se publica en Vercel; el backend Express se despliega como función serverless en la misma plataforma. Durante la instalación se genera el cliente Prisma y en el despliegue se aplican las migraciones pendientes. PostgreSQL se aloja en Neon.
- **Sistema de agentes**: los seis agentes, el gateway y el backend de datos se despliegan de forma **unificada en un único contenedor Docker** publicado en Render. El `Dockerfile` del repositorio empaqueta todos los servicios (incluidas las dependencias de OCR para los documentos escaneados que procesa el agente de autocompletado) y dentro del contenedor se comunican por la interfaz local, exponiendo únicamente el gateway. Un *blueprint* (`render.yaml`) declara el servicio: se construye automáticamente en cada push al repositorio, la plataforma vigila su salud mediante el endpoint agregado `/salud` y lo reinicia si deja de responder.
- **Ejecución local**: para desarrollo y demostraciones sin nube, un script de arranque levanta todos los servicios con un solo comando y un test de humo verifica la salud de cada pieza. El mismo contenedor puede construirse y ejecutarse en cualquier máquina con Docker, lo que hace el despliegue reproducible.

Las variables sensibles (cadena de conexión de solo lectura, claves de modelos de lenguaje, token del bot) se almacenan exclusivamente en archivos locales ignorados por Git o en el gestor de secretos de la plataforma; el repositorio solo contiene plantillas de ejemplo sin valores reales.

Limitaciones operativas conocidas del plan gratuito de alojamiento: el servicio se suspende tras periodos de inactividad (con un arranque en frío de en torno a un minuto) y la imagen no incluye los navegadores necesarios para el rastreo en vivo de licitaciones, que se ejecuta en local. Una versión productiva añadiría un plan sin suspensión, gestión centralizada de logs, métricas, reintentos y alertas.

---

## 13. Costes de ejecución

El sistema genera costes asociados a:

- Consumo de modelos de lenguaje.
- Ejecución y alojamiento de agentes.
- Base de datos PostgreSQL.
- Almacenamiento de archivos.
- Despliegue de frontend y backend.
- APIs de correo, búsqueda, viajes u otras fuentes externas.
- Monitorización y mantenimiento.

El proyecto dispone de un estudio económico parametrizable (`Estudio_Economico_Agentes_final.xlsx`, en esta misma carpeta) que estima el coste de inferencia por agente a partir del volumen de consultas y los tokens medios por petición. En el escenario de referencia (uso diario continuado, mismo modelo de lenguaje para los seis agentes), el resultado es:

| Agente | Consultas/mes | Coste mensual | Coste anual | Coste por consulta |
|---|---:|---:|---:|---:|
| Lumen (consultas) | 1.500 | 3,28 € | 39,35 € | 0,0022 € |
| Garum (gestor de correos) | 1.650 | 1,82 € | 21,84 € | 0,0011 € |
| Jano (viajes) | 450 | 0,73 € | 8,72 € | 0,0016 € |
| Hermes (Telegram) | 600 | 0,53 € | 6,38 € | 0,0009 € |
| Operis (autocompletado) | 300 | 0,29 € | 3,47 € | 0,0010 € |
| Vigil (concursos) | 60 | 0,05 € | 0,60 € | 0,0008 € |
| **Total inferencia** | **4.560** | **6,70 €** | **80,36 €** | — |

Aplicando un margen de seguridad del 15 % e IVA, el coste anual de inferencia estimado es de **111,82 €**, al que se suma el alojamiento de la infraestructura (en torno a 14 €/mes en un plan de pago; 0 € en los planes gratuitos usados durante el proyecto, con las limitaciones descritas en la sección 12).

Dos conclusiones del estudio: el coste de la capa de IA es **marginal frente al ahorro estimado en horas de trabajo** (el propio estudio incluye una hoja de retorno con el tiempo manual frente al asistido por agente), y el agente más consultado (el copiloto de consultas) es también el de mayor coste, lo que orienta dónde optimizar primero.

Los parámetros de consumo (consultas diarias y tokens por petición) son estimaciones del equipo pendientes de contraste con mediciones reales de uso; las cifras definitivas dependen del proveedor, modelo, volumen de solicitudes y frecuencia de ejecución, por lo que deben recalcularse antes de una implantación real.

---

## 14. Conclusiones

El desarrollo de MITÜMI ha permitido construir una solución tecnológica integral para la gestión de eventos empresariales, combinando una aplicación web corporativa con una capa de agentes especializados de inteligencia artificial. El resultado centraliza la información operativa y ofrece herramientas específicas para agilizar la consulta de datos, el alta de eventos, la coordinación con ponentes, la planificación de viajes, la detección de licitaciones y la gestión del correo electrónico.

La aplicación se apoya en una arquitectura modular y escalable, formada por una interfaz web responsive, una API REST, una base de datos relacional y servicios externos de autenticación y almacenamiento. Esta separación de responsabilidades facilita el mantenimiento del sistema, protege el acceso a la información y permite que cada componente evolucione de manera controlada. El gateway común, los contratos de intercambio en formato JSON y el acceso de solo lectura de los agentes a la base de datos refuerzan la coherencia y la seguridad de la solución.

Los seis agentes aportan capacidades complementarias que cubren distintas etapas del ciclo de vida de un evento: Vigil contribuye a la captación de oportunidades; Operis agiliza la incorporación de información; Lumen facilita la consulta conversacional; Hermes y Garum apoyan la comunicación; y Jano asiste en la organización logística de los desplazamientos. En los procesos con impacto operativo, económico o comunicativo, la validación humana se mantiene como parte esencial del flujo, garantizando que la automatización actúe como apoyo a la toma de decisiones.

El carácter multidisciplinar del proyecto ha permitido integrar conocimientos de desarrollo Full Stack, ingeniería de datos e inteligencia artificial dentro de un mismo producto. La definición de modelos de datos, APIs, interfaces y flujos especializados demuestra la capacidad del sistema para responder a necesidades reales de organización, trazabilidad y automatización en el ámbito de los eventos.

En conjunto, MITÜMI constituye una base tecnológica sólida y extensible sobre la que pueden incorporarse nuevos procesos, fuentes de información y agentes especializados. El proyecto cumple así su objetivo de transformar tareas dispersas y manuales en una experiencia de gestión centralizada, asistida y orientada a mejorar la eficiencia operativa.

## 15. Mejoras y trabajo futuro

1. Completar la conexión de todas las vistas y operaciones CRUD del frontend.
2. Añadir autenticación y limitación de peticiones a la pasarela única de agentes.
3. Normalizar los contratos de entrada, salida y error.
4. Eliminar accesos directos innecesarios a la base de datos.
5. Añadir monitorización, métricas y alertas al despliegue de agentes existente.
6. Incorporar pruebas de integración de extremo a extremo.
7. Definir métricas comunes de calidad, precisión, latencia y coste.
8. Reforzar la protección frente a prompt injection y fuentes no confiables.
9. Implementar auditoría y trazabilidad centralizadas.
10. Sustituir los datos de prueba por datos autorizados y aplicar una política RGPD completa.
11. Recoger evaluación de usuarios de MITÜMI y priorizar mejoras a partir de su experiencia.
12. Mantener la validación humana en decisiones económicas, contractuales y comunicativas.

---

## 16. Fuentes y referencias

- Basque Events: información pública sobre espacios y salas para eventos.
- SPRI: referencias del ecosistema empresarial vasco.
- Turismo Euskadi y Convention Bureaus: información sobre destinos, sedes y servicios.
- eInforma: referencias empresariales complementarias.
- Documentación oficial de React, Vite, Node.js, Express, Prisma, PostgreSQL, Firebase, Cloudinary, Neon y Vercel.
- Documentación técnica y repositorios internos de los agentes Operis, Lumen, Vigil, Jano, Hermes y Garum.
