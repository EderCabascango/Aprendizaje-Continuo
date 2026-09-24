# 🔒 RAG local para documentos privados (LangChain + Ollama)

Adaptación **100% local** del lab de IBM / Skills Network *"Summarize Private Documents
Using RAG, LangChain, and LLMs"*. En vez de watsonx.ai (que solo funciona dentro del entorno
web del curso), todo corre en tu máquina con [Ollama](https://ollama.com/): con documentos
propios, nada sale de tu equipo.

📓 **Notebook:** [`01-rag-documentos-privados-ollama.ipynb`](./01-rag-documentos-privados-ollama.ipynb)
— incluye las **salidas de una ejecución real**, así que se puede leer en GitHub sin correr nada.

## Qué cubre

- **Indexing:** `TextLoader`, `CharacterTextSplitter`, `OllamaEmbeddings`, `Chroma`.
- **Retrieval + generación:** `RetrievalQA`, `PromptTemplate`, `return_source_documents`.
- **Alucinaciones y cómo detectarlas:** prompt estricto, verificación contra el texto fuente,
  comparación `llama3.2` (3B) vs `qwen2.5:7b`.
- **Memoria conversacional:** `ConversationalRetrievalChain` + `ConversationBufferMemory`.
- **Agente** conversacional en consola.
- **Ejercicios:** segundo documento y devolución de fuentes.

## Requisitos

- [Ollama](https://ollama.com/download) instalado y corriendo.
- Modelos:
  ```bash
  ollama pull llama3.2          # LLM pequeño (3B)
  ollama pull qwen2.5:7b        # LLM mediano (~4.7 GB; con 16 GB de RAM corre bien)
  ollama pull nomic-embed-text  # embeddings
  ```
- Python **3.12** (se prefirió sobre 3.14 por compatibilidad de dependencias; no se probó 3.14).

## Cómo ejecutarlo

**Windows (PowerShell):**
```powershell
python -m venv venv            # o: py -3.12 -m venv venv
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

**macOS / Linux:**
```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Abre el notebook y **selecciona el kernel del `venv`** (en VS Code: *Select Kernel → Python
Environments → venv*). Ejecuta de arriba abajo; antes de repetir la ejecución completa,
**reinicia el kernel** (si no, Chroma duplica los chunks).

## Estado de los ejercicios

| Ejercicio | Estado |
|---|---|
| 1. Trabajar con otro documento (`stateOfUnion.txt`) | ✅ Completo |
| 2. Devolver la fuente de cada respuesta | ✅ Completo |
| 3. Probar otro LLM | 🟡 Cubierto en su idea: comparación local `llama3.2` vs `qwen2.5:7b` (no se usó Mistral en watsonx) |

## Cambios frente al lab original

| Original (IBM) | Esta versión | Motivo |
|---|---|---|
| `WatsonxLLM` con `project_id="skills-network"` | `OllamaLLM` (`llama3.2` → `qwen2.5:7b`) | El proyecto solo existe en el entorno del curso |
| `HuggingFaceEmbeddings()` | `OllamaEmbeddings("nomic-embed-text")` | Evita instalar PyTorch; **embeddings distintos** → ranking no idéntico |
| `return_message=True` | `return_messages=True` + `output_key="answer"` | `return_message` no es el nombre del parámetro |
| Historial manual (`history.append(...)`) | La memoria lo gestiona | Menos código y menos errores |
| `qa({...})` | `qa.invoke({...})` | Forma actual de invocar chains |
| `wget.download(...)` directo | Con `os.path.exists` antes | `wget` crea `archivo (1).txt` si ya existe |

## Hallazgos clave

*(Evidencia y celdas en la Sección 9 del notebook.)*

1. Sin contexto, el modelo responde con seguridad y se equivoca ("RAG" → "Red de Ayuda y Rescate").
2. Con un modelo de 3B **el prompt no basta**: ante una pregunta cuya respuesta no está en el
   documento, `llama3.2` respondió sobre un tema cercano y llegó a inventar un detalle.
3. Con el resto igual, `qwen2.5:7b` respondió *"The document does not mention this."* en ambos documentos.
4. El retriever devuelve siempre k=4 chunks: en el caso revisado solo 1 de 4 era útil.
5. `CharacterTextSplitter` solo corta en `\n\n`: `chunk_size` es un objetivo, no un límite
   (7 de 16 chunks superaron los 1000 caracteres en el primer documento).
6. La memoria resuelve pronombres en preguntas de seguimiento, pero `ConversationBufferMemory` está deprecada.
7. Hay que verificar también la verificación: `"eat" in texto` dio un falso positivo; con `\b` dio 0.

> ⚠️ **Limitaciones:** una pregunta trampa por documento y una corrida por configuración (no es un
> benchmark); en la comparación cambian a la vez el tamaño y la familia del modelo; documentos pequeños.

## Problemas encontrados y cómo se resolvieron

| Problema | Causa | Solución |
|---|---|---|
| `ModuleNotFoundError: No module named 'wget'` en VS Code | El kernel apuntaba a Miniconda / Python global, no al `venv` | Imprimir `sys.executable` y elegir el kernel del `venv` |
| `pip install ... \` falla en PowerShell | `\` no continúa la línea en PowerShell | Comando en una sola línea (o usar `` ` `` al final de cada línea) |
| Chunks de 1600–2000 caracteres con `chunk_size=1000` | El splitter solo corta en `\n\n` | Entenderlo; siguiente paso: `RecursiveCharacterTextSplitter` |
| Chunks duplicados al re-ejecutar | `Chroma.from_documents` vuelve a insertar en la misma colección | Reiniciar el kernel antes de repetir |
| `LangChainDeprecationWarning` en `ConversationBufferMemory` | Clase obsoleta (retirada prevista en LangChain 2.0) | Funciona hoy; migrar a `create_agent` con checkpointing |

## Estructura de la carpeta

```text
01-rag-documentos-privados-ollama/
├── 01-rag-documentos-privados-ollama.ipynb
├── README.md
├── requirements.txt
└── .gitignore        # ignora los .txt de ejemplo (el notebook los descarga)
```

## Créditos

Lab original: **IBM / Skills Network** — *Summarize Private Documents Using RAG, LangChain, and LLMs*.
Este repositorio no incluye el notebook original ni los datos de ejemplo; el notebook los
descarga desde el bucket del curso al ejecutarse.
