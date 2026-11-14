# 📚 Local PDF Chatbot (RAG)

Ask questions about any PDF and get answers grounded in its content. It is built with **retrieval-augmented generation (RAG)** and runs **entirely on your own computer** using open-source models: no paid API keys, and your documents never leave your machine.

**Tools:** Python · LangChain · Ollama (Qwen, Llama 3) · ChromaDB · Hugging Face sentence-transformers · Gradio

---

## How it works

```
PDF ──► split into chunks ──► embed (all-MiniLM-L6-v2) ──► Chroma vector store
                                                                │
Question ──► embed ──► retrieve most relevant chunks ◄──────────┘
                                │
                                ▼
                     local LLM (Qwen 4B via Ollama) ──► answer
```

1. **Load:** the PDF is read page by page with `PyPDFLoader`.
2. **Chunk:** the text is split into overlapping chunks so that context isn't cut mid-sentence.
3. **Embed and store:** each chunk becomes a vector stored in ChromaDB.
4. **Retrieve and answer:** for each question, the most similar chunks are passed to the LLM as context, so answers are based on the document rather than the model's general knowledge.

## Two versions

| File | What it is |
|---|---|
| `app.py` | A **web app** (Gradio): upload a PDF, ask a question, get an answer. Uses Qwen 4B, which is lightweight enough for an ordinary laptop. |
| `pdf_bot.ipynb` | A **notebook walkthrough** of each RAG step, with a *conversational* chain that remembers earlier questions so you can ask follow-ups. Uses Llama 3 8B and a persistent vector store. |

## Run it

1. Install [Ollama](https://ollama.com) and pull the models:
   ```bash
   ollama pull qwen:4b      # for app.py
   ollama pull llama3:8b    # for the notebook
   ```
2. Install the Python packages:
   ```bash
   pip install -r requirements.txt
   ```
3. Start the web app:
   ```bash
   python app.py
   ```
   Then open http://localhost:7863, upload a PDF and ask away.

   Or open `pdf_bot.ipynb` and set `PDF_PATH` to your PDF.

## Why local models?
Running the LLM locally means **no per-request cost and no data sent to third parties**, which matters for confidential documents like contracts, reports and internal policies. The trade-off is speed and answer quality compared with large hosted models, which is why the app uses a small model that runs well on ordinary hardware.

## Possible improvements
- Show the source page for each answer
- Cache the vector store per uploaded file instead of rebuilding it for every question
- Support several PDFs at once
