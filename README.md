# Universo IA 🌌

Agente inteligente que responde preguntas sobre astronomía y el universo, basado en documentación propia.

## 📌 Descripción del proyecto

Universo Ang es un agente conversacional creado como parte del Challenge de Alura/Oracle sobre construcción de agentes inteligentes. El agente utiliza una base documental propia sobre el universo (Big Bang, Sistema Solar, agujeros negros, exploración espacial, glosario astronómico y curiosidades), citando la información contenida en los documentos en lugar de inventar respuestas.

## 🏗️ Arquitectura de la solución

El flujo general del agente es el siguiente:

```
Usuario
   │
   ▼
Pregunta (chat / webhook)
   │
   ▼
   n8n  ──────────────► Cohere (embeddings + LLM)
   │                         │
   ▼                         │
Base documental (PDFs)◄──────┘
   │
   ▼
Respuesta final al usuario
```

1. Los documentos PDF se procesan y dividen en fragmentos.
2. Cada fragmento se convierte en un embedding mediante Cohere.
3. Cuando el usuario hace una pregunta, el flujo de n8n busca el fragmento más relevante.
4. Ese fragmento se envía a Cohere junto con la pregunta para generar la respuesta final.
5. La aplicación está desplegada en la nube (OCI) para estar disponible públicamente.

## 🛠️ Tecnologías y herramientas utilizadas

- **n8n** – orquestación del flujo del agente (recepción de preguntas, búsqueda de información, generación de respuestas).
- **Cohere** – modelo de lenguaje utilizado para generar embeddings y respuestas.
- **OCI (Oracle Cloud Infrastructure)** – despliegue de la aplicación en la nube.
- **GitHub** – control de versiones y entrega del proyecto.
- **PDF** – formato de la base documental utilizada como fuente de información.

## 📄 Documentación utilizada (carpeta `documentos/`)

| Archivo | Contenido |
|---|---|
| `Base_de_Conocimiento_del_Universo.pdf` | Documento principal: origen del universo, galaxias, estrellas, agujeros negros, exploración espacial, entre otros. |
| `FAQ_Universo.pdf` | Preguntas frecuentes sobre astronomía. |
| `Guia_Sistema_Solar.pdf` | Información detallada del Sol, los 8 planetas y otros cuerpos del Sistema Solar. |
| `Guia_Exploracion_Espacial.pdf` | Agencias espaciales y misiones históricas relevantes. |
| `Glosario_Astronomico.pdf` | Definiciones de términos astronómicos clave. |
| `Curiosidades_del_Universo.pdf` | Datos curiosos sobre el universo. |

## ▶️ Cómo ejecutar el proyecto

1. Clonar este repositorio.
2. Importar el archivo `workflow-universo.json` en una instancia de n8n (local o en la nube).
3. Configurar las credenciales de Cohere dentro de n8n (API key propia, no incluida en este repositorio).
4. Cargar los documentos de la carpeta `documentos/` en el nodo correspondiente del flujo.
5. Activar el flujo (webhook) y realizar preguntas al agente a través del chat o del endpoint generado.

## 💬 Ejemplos de preguntas que el agente puede responder

- ¿Qué es un agujero negro?
- ¿Cuántos planetas tiene el Sistema Solar?
- ¿Qué es la materia oscura?
- ¿Qué descubrió el telescopio James Webb?
- ¿Cuál es el planeta más grande?
- ¿Qué diferencia hay entre un meteoro y un meteorito?

## 🤖 Ejemplos de respuestas generadas por el agente

> **Pregunta:** ¿Qué es un agujero negro?
> **Respuesta:** Un agujero negro es una región del espacio-tiempo con una gravedad tan intensa que ni siquiera la luz puede escapar de ella una vez que cruza el horizonte de eventos. Se forma generalmente tras el colapso de una estrella masiva.

> **Pregunta:** ¿Cuál es el planeta más grande del Sistema Solar?
> **Respuesta:** Júpiter es el planeta más grande del Sistema Solar, con un diámetro 11 veces mayor que el de la Tierra.

*(Estas respuestas se actualizarán con capturas reales una vez que el agente esté desplegado y en funcionamiento).*

## ☁️ Despliegue

- **URL pública:** _(pendiente — se agregará al desplegar en OCI)_
- **Evidencia visual:** ver carpeta `screenshots/`

## 👤 Autor

Proyecto desarrollado como parte del Challenge de Agentes Inteligentes (Alura / Oracle Next Education).
