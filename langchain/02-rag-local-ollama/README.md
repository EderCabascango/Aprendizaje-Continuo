# RAG local con LangChain + Ollama

Adaptación 100% local (sin APIs de pago) de un lab de LangChain, usando
Ollama como servidor de modelos.

## Qué cubre
- Chat models, roles de mensajes, prompt templates
- Salida estructurada con Pydantic
- Document loaders, text splitters, embeddings locales
- Vector store (Chroma) + RAG con LCEL
- Memoria conversacional
- Chains secuenciales
- Tools y agentes (tool calling nativo)

## Requisitos
- [Ollama](https://ollama.com) instalado
- `ollama pull llama3.2`
- `ollama pull nomic-embed-text`

## Hallazgos clave
- RAG por similarity search falla en preguntas de resumen global; funciona
  bien en preguntas puntuales.
- El tamaño de chunk afecta mucho la calidad del retrieval.
- El tool-calling con un modelo pequeño (3B) no es 100% consistente entre
  corridas.
