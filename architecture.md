## ⚙️ CAPA 1: CAPTURA

**Objetivo:** recolectar *todo el conocimiento generado naturalmente* dentro del flujo de trabajo, sin pedirle esfuerzo adicional a las personas.
La captura debe ser **invisible, continua y segura**.

---

### 🔹 1.1 Reuniones (Teams, Meet, Zoom)

**Propósito:** registrar las conversaciones donde se toman decisiones críticas.

**Flujo:**

1. Se graba la reunión (Teams o Meet).
2. Un proceso automático extrae el audio/video (via API o integración nativa).
3. Se envía a un servicio de transcripción:

   * **Opción open-source:** [Whisper](https://github.com/openai/whisper) (puede correr en local o Worker).
   * **Opción corporativa:** Microsoft Copilot o Google Duet AI.
4. Se guarda la transcripción cruda (`meeting-2025-10-26.txt`).
5. Se genera una versión resumida (`meeting-2025-10-26-summary.md`) que identifica:

   * Participantes
   * Tópicos
   * Decisiones
   * Compromisos

**Resultado:** cada reunión se convierte en un documento versionado que alimenta el conocimiento colectivo.

---

### 🔹 1.2 Correos electrónicos

**Propósito:** capturar decisiones, aprobaciones y explicaciones clave que quedan en correos.

**Flujo:**

1. Se conecta un **servicio lector de correo** mediante:

   * **Microsoft Graph API** (para M365/Outlook).
   * **Gmail API** (para G Suite).
2. Filtrado por criterios:

   * Palabras clave (ej. “decisión”, “acuerdo”, “aprobado”).
   * Etiquetas o remitentes clave (gerentes, líderes de proyecto).
3. El contenido relevante se extrae (asunto, cuerpo, fecha, remitente).
4. Se guarda en formato estructurado JSON o `.md` (ej: `/docs/emails/2025-10-26-proyecto-cashback.md`).

**Resultado:** el sistema conserva el contexto narrativo de decisiones, sin duplicar toda la bandeja de entrada.

---

### 🔹 1.3 Commits y Pull Requests

**Propósito:** registrar los *porqués técnicos* de los cambios de código.

**Flujo:**

1. Configurar **webhooks** en GitHub o GitLab para detectar cada commit o PR.
2. Cada evento activa un Worker o Action que analiza:

   * Mensaje del commit.
   * Cambios de código (diff).
   * Archivos afectados.
3. Se genera automáticamente un resumen semántico:

   * “Este commit modifica el cálculo de puntos de cashback para incluir redondeo decimal.”
4. Se guarda como mini-bitácora en `/logs/commits/YYYY-MM-DD.json`.

**Resultado:** cada cambio queda documentado con su intención, no solo con el código.

---

### 🔹 1.4 Chats corporativos (Slack, Teams, Discord)

**Propósito:** capturar conocimiento informal (preguntas, acuerdos, alertas).

**Flujo:**

1. Usar **API de Slack o Teams** para escuchar canales específicos (ej. `#proyecto-cashback`).
2. Guardar solo mensajes con contexto decisional (por ejemplo, los que contienen “ok, aprobemos eso” o “queda definido así”).
3. Generar resumen diario automatizado (“daily digest”) que se agrega al repositorio.

**Resultado:** la inteligencia de equipo queda escrita sin esfuerzo.

---

### 🔹 1.5 Sensores, tablets o dispositivos en faenas

**Propósito:** extender el modelo a entornos físicos (minería, mantenimiento, obra civil).

**Flujo:**

1. Tablets o teléfonos graban audio o texto (“Decidimos extender el turno por seguridad”).
2. Un microservicio local (Raspberry Pi, Jetson Nano, gateway MQTT) sincroniza los datos vía Wi-Fi o 4G.
3. Los archivos (audio o texto) se suben al sistema principal para ser procesados (transcripción + resumen).

**Resultado:** las decisiones de terreno se integran con las digitales en una única memoria organizacional.

---

## 🤖 CAPA 2: PROCESAMIENTO INTELIGENTE

**Objetivo:** transformar datos crudos (reuniones, correos, commits) en **conocimiento estructurado** y consultable.

---

### 🔹 2.1 Limpieza y anonimización

* Se eliminan datos personales (nombres, correos, RUT, direcciones).
* Se estandariza formato de fecha, idioma y estructura.
* Se genera metadata (`source`, `author`, `confidence`, `project`).
* Herramientas: `spaCy`, `Presidio` (de Microsoft), o funciones personalizadas en Python/Node.

---

### 🔹 2.2 Resumen semántico (LLM)

* Se usa **GPT-4**, **Claude**, o **Llama 3** para condensar texto largo.
* Se pide al modelo un formato tipo:

  ```yaml
  meeting_date: 2025-10-26
  decisions:
    - Actualizar API de puntos
    - Migrar base de datos a AlloyDB
  risks:
    - Falta de pruebas de rendimiento
  next_steps:
    - Reunión técnica el 29/10
  ```
* Esto crea resúmenes útiles y fáciles de indexar.

---

### 🔹 2.3 Extracción de decisiones

* Algoritmo de análisis semántico detecta frases como “decidimos”, “acordamos”, “queda definido”.
* Cada decisión se guarda con su fuente y fecha:

  ```json
  {
    "id": "DEC-2025-10-26-001",
    "decision": "Implementar nueva fórmula de cashback",
    "source": "meeting-2025-10-26.md",
    "author": "Carlos Farías"
  }
  ```
* Este dataset se convierte en el “ADN” de la memoria organizacional.

---

### 🔹 2.4 Embeddings vectoriales

* Cada texto (reunión, correo, decisión) se convierte en un **vector numérico** mediante embeddings (ej. `text-embedding-3-large`).
* Los vectores se guardan en una **Vector DB** (Chroma, Weaviate, Qdrant).
* Esto permite hacer búsquedas semánticas:

  > “¿Qué decisiones se tomaron sobre seguridad?”
  > “¿Dónde discutimos el redondeo de puntos?”

---

## 🧱 CAPA 3: ALMACENAMIENTO SOBERANO

**Objetivo:** asegurar que toda la información viva dentro del dominio de la empresa, con trazabilidad, versionado y control de acceso.

---

### 🔹 3.1 Vector DB (Chroma / Weaviate / Qdrant)

* Almacena embeddings semánticos para búsquedas inteligentes.
* Puede correr **on-premise o en nube privada**.
* Permite búsquedas aproximadas (kNN) y consultas híbridas (texto + metadata).

**Ejemplo:** buscar “riesgo de seguridad” y obtener fragmentos de documentos, correos y reuniones relacionadas.

---

### 🔹 3.2 Repositorio documental (Git + Markdown)

* Todo el conocimiento procesado se guarda en un repositorio Git:

  * `/meetings/` → transcripciones.
  * `/decisions/` → acuerdos estructurados.
  * `/emails/` → resúmenes de correos.
  * `/logs/` → commits, incidentes, eventos.
* Ventajas:

  * Versionado histórico (quién cambió qué y cuándo).
  * Integración con GitHub Actions para automatizar builds y análisis.
  * Transparencia y trazabilidad total.

---

### 🔹 3.3 Nube privada o híbrida

* Hospeda la Vector DB, los archivos y los modelos IA.
* Ejemplos:

  * **Azure Stack** → integración natural con M365 y Graph API.
  * **GCP Private Service Connect** → alta seguridad y ML nativo.
  * **AWS Outposts** → despliegue local con compatibilidad en nube.

**Objetivo:** soberanía del conocimiento.
Los datos *nunca salen del perímetro corporativo*.

---

## 💬 CAPA 4: INTERACCIÓN Y ASISTENTES

**Objetivo:** ofrecer a cada persona un punto de acceso natural al conocimiento acumulado.

---

### 🔹 4.1 Chat contextual (RAG + memoria)

* Interfaz conversacional que combina:

  * Recuperación semántica (RAG: *Retrieval-Augmented Generation*).
  * Contexto histórico del usuario o rol.
* Ejemplo:

  > Usuario: “¿Por qué decidimos no usar Redis en la versión 2 del backend?”
  > Asistente: “En la reunión del 12/03/2024 se acordó priorizar PostgreSQL por simplicidad de mantenimiento y compatibilidad con AlloyDB.”

---

### 🔹 4.2 Dashboards de conocimiento

* Paneles interactivos que muestran:

  * Decisiones recientes.
  * Temas recurrentes.
  * Riesgos detectados.
  * Evolución de métricas (tiempo medio de decisión, cantidad de reuniones, etc.).
* Se puede construir con **Eleventy + Tailwind + Chart.js** o frameworks ligeros como SvelteKit.

---

### 🔹 4.3 Aplicaciones por rol

* Interfaces personalizadas:

  * **Operario:** alertas, lecciones aprendidas, decisiones locales.
  * **Ingeniero:** trazabilidad de diseño y cambios técnicos.
  * **Gerente:** decisiones estratégicas, riesgos y compromisos.
* En el futuro: integración con **asistentes de voz** o AR (realidad aumentada) para operaciones en terreno.

---

## 🧭 Resultado global

> El sistema transforma el trabajo cotidiano —reuniones, correos, commits y conversaciones— en **conocimiento indexado, consultable y persistente**.
> Todo queda dentro de la nube privada de la organización, accesible mediante asistentes inteligentes que *recuerdan, razonan y explican*.

