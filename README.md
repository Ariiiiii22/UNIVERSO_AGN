# Universo AGN 🌌

Agente inteligente que responde preguntas sobre astronomía y el universo, basado en documentación propia (PDFs), integrado con el bot de Telegram y desplegado usando servicios de Oracle Cloud Infrastructure (OCI).

## 📌 Descripción del proyecto

El agente utiliza una base documental propia sobre el universo (Big Bang, Sistema Solar, agujeros negros, exploración espacial, glosario astronómico y curiosidades) para responder preguntas de los usuarios a través de Telegram, citando la información contenida en los documentos en lugar de inventar respuestas.

## 🏗️ Arquitectura de la solución

El proyecto está compuesto por **dos workflows independientes** en n8n, que se comunican a través de un almacén de vectores compartido (`universo_vector_store`), y por **OCI Object Storage** como fuente de los documentos originales.

```
                  OCI Object Storage
          (bucket: universo-agn-documentos)
                           │
                           ▼
        ┌─────────────────────────────────────┐
        │     WORKFLOW 1: Ingesta-Universo    │
        │  (se ejecuta manualmente una vez    │
        │   o cada vez que cambian los docs)  │
        │                                     │
        │  Descarga PDFs → Extrae texto →     │
        │  Divide en fragmentos → Genera      │
        │  embeddings con Cohere → Guarda     │
        └──────────────────┬──────────────────┘
                           │
                           ▼
                 universo_vector_store
                  (memoria compartida)
                           ▲
                           │
        ┌──────────────────┴────────────────────┐
        │   WORKFLOW 2: Challenge Universo      │
        │      (activo, escucha Telegram)       │
        │                                       │
        │  Usuario pregunta en Telegram →       │
        │  AI Agent busca en el vector store →  │
        │  Cohere genera la respuesta →         │
        │  Se responde al usuario en Telegram   │
        └───────────────────────────────────────┘
```

- `Ingesta-Universo` se encarga de leer los documentos fuente y convertirlos en información consultable (embeddings). Solo necesita ejecutarse cuando se agregan o modifican documentos.
- `Challenge Universo` es el que queda activo, escuchando mensajes de Telegram y respondiendo en tiempo real usando la información ya cargada.

## 🛠️ Tecnologías y herramientas utilizadas

- **n8n** – orquestación de ambos flujos (ingesta de documentos y agente conversacional).
- **Cohere** – modelo de lenguaje utilizado para generar embeddings y las respuestas del agente.
- **Telegram Bot API** – interfaz conversacional para el usuario final.
- **OCI Object Storage** – almacenamiento en la nube de los documentos PDF fuente, con acceso mediante Pre-Authenticated Requests (PAR).
- **GitHub** – control de versiones y entrega del proyecto.
- **PDF** – formato de la base documental utilizada como fuente de información.

## 📄 Documentación utilizada (PDFs en la raíz del repositorio)

| Archivo | Contenido |
|---------|-----------|
| `Base_de_Conocimiento_del_Universo.pdf` | Documento principal: origen del universo, galaxias, estrellas, agujeros negros, exploración espacial, entre otros. |
| `FAQ_Universo.pdf` | Preguntas frecuentes sobre astronomía. |
| `Guia_Sistema_Solar.pdf` | Información detallada del Sol, los 8 planetas y otros cuerpos del Sistema Solar. |
| `Guia_Exploracion_Espacial.pdf` | Agencias espaciales y misiones históricas relevantes. |
| `Glosario_Astronomico.pdf` | Definiciones de términos astronómicos clave. |
| `Curiosidades_del_Universo.pdf` | Datos curiosos sobre el universo. |

Estos mismos documentos están alojados en **OCI Object Storage** (bucket `universo-agn-documentos`), desde donde el flujo de ingesta los descarga usando URLs de acceso pre-autenticado (PAR). Ver evidencia en `screenshots/oci.jpg`.

## 💻 Código fuente (lectura y procesamiento de documentos)

- **`workflow-ingesta-universo.json`** — contiene la lógica completa de lectura: descarga los PDFs desde OCI Object Storage, extrae su texto, lo divide en fragmentos y genera los embeddings con Cohere.
- **`workflow-agente-universo.json`** — contiene la lógica del agente conversacional: recibe preguntas por Telegram, busca en el vector store y genera la respuesta.

Evidencia visual de ambos flujos ejecutándose correctamente: `screenshots/n8n-ingesta.jpg` y `screenshots/n8n-agente.jpg`.

## 🤖 Prueba el agente ahora mismo

El agente ya está **desplegado y activo**. Puedes hablarle directamente en Telegram, sin instalar ni configurar nada:

- **Bot:** [@universoagente_bot](https://t.me/universoagente_bot)
- **Cómo usarlo:** abre el link, presiona "Start" (o envía `/start`), y escríbele cualquier pregunta sobre astronomía o el universo.

## ▶️ Cómo ejecutar el proyecto localmente (opcional)

Si quieres replicar el proyecto desde cero:

1. Clonar este repositorio
2. Importar `workflow-ingesta-universo.json` y `workflow-agente-universo.json` en una instancia de n8n.
3. Configurar las credenciales necesarias dentro de n8n:
   - API key de Cohere.
   - Token de un bot de Telegram propio (creado con @BotFather).
4. En el nodo "Simple Vector Store" de ambos workflows, verificar que el **Memory Key** sea el mismo (`universo_vector_store`) para que compartan la misma información.
5. Ejecutar primero `Ingesta-Universo` para cargar los documentos en el vector store.
6. Activar `Challenge Universo` para que el bot quede escuchando mensajes de Telegram de forma continua.
7. Escribirle al bot en Telegram para probarlo.


## 💬 Ejemplos de preguntas que el agente puede responder

- ¿Cómo inició el universo?
- ¿Qué es un agujero negro?
- ¿Me puedes hablar sobre Mercurio?
- ¿Me puedes hablar sobre Saturno?
- ¿Qué es una nebulosa?
- ¿Cuántos planetas tiene el Sistema Solar?

Capturas de estas conversaciones reales disponibles en `screenshots/universo-agente.jpg` y `screenshots/universo-agente2.jpg`.

## ☁️ Despliegue y evidencia de uso de OCI

El agente está desplegado y accesible públicamente en Telegram: **[@universoagente_bot](https://t.me/universoagente_bot)**.

Como parte del proceso de despliegue, este proyecto integra **OCI Object Storage** del ecosistema de Oracle Cloud Infrastructure:

- Se creó un bucket llamado `universo-agn-documentos` en OCI Object Storage (región Mexico Central - Querétaro).
- Los 6 documentos PDF fuente se alojan ahí.
- Se generaron **Pre-Authenticated Requests (PAR)** por cada archivo, permitiendo que el flujo de ingesta de n8n los descargue de forma segura sin exponer el bucket completo como público.
- El agente conversacional (n8n) está activo y responde en tiempo real a través de Telegram, usando la información indexada desde estos documentos alojados en OCI.
