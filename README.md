# 📓 Aprendizaje Continuo — AI/ML Learning Log

Registro continuo de labs, experimentos y notas técnicas mientras aprendo IA/ML, LangChain,
MLOps y herramientas relacionadas, con foco en **ejecutar todo localmente** cuando es posible
(Ollama, modelos open source).

Cada lab vive en su propia carpeta, con un notebook (con las salidas guardadas), su README,
sus dependencias y las **conclusiones, incluidas las limitaciones**: lo que salió mal también
se documenta.

## 🗂️ Labs

| # | Lab | Qué se aprende | Stack |
|---|---|---|---|
| 01 | [RAG local para documentos privados](./langchain/01-rag-documentos-privados-ollama/) | Indexing, retrieval, alucinaciones y cómo detectarlas, comparación de modelos, memoria, agente | LangChain · Ollama · ChromaDB |
| 02 | [LangChain 100% local con Ollama](./langchain/02-rag-local-ollama/) | Chat models, prompts, salida estructurada, RAG con LCEL, memoria, chains, tools y agentes | LangChain · Ollama · ChromaDB |

## 🧱 Estructura del repositorio

```text
Aprendizaje-Continuo/
├── README.md
├── .gitignore
├── _template/               # plantilla y checklist para cada lab nuevo
│   └── README.md
└── langchain/
    ├── 01-rag-documentos-privados-ollama/
    └── 02-rag-local-ollama/
```

Convención: una carpeta por lab, `NN-tema-herramienta`, con el notebook del mismo nombre.
Al empezar un lab nuevo, copia [`_template/README.md`](./_template/README.md) y sigue su checklist.

## ▶️ Cómo ejecutar un lab

1. Instala [Ollama](https://ollama.com/download) y descarga los modelos que indique el README del lab.
2. Crea un entorno virtual e instala las dependencias de esa carpeta:
   ```bash
   python -m venv venv
   # Windows: .\venv\Scripts\Activate.ps1   |   macOS/Linux: source venv/bin/activate
   pip install -r requirements.txt
   ```
3. Abre el notebook y **selecciona el kernel del `venv`** (en VS Code: *Select Kernel*).

## 🛠️ Stack usado

Python 3.12 · LangChain · Ollama · ChromaDB
