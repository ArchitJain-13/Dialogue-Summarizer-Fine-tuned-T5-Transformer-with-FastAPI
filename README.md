# 📝 Dialogue Summarizer — Fine-tuned T5 with FastAPI

A web app that turns chat-style conversations into short, abstractive summaries. A **T5-small** model is fine-tuned on the **SAMSum** dialogue dataset with Hugging Face Transformers, then served through a **FastAPI** REST API with a simple HTML/JavaScript front end.
                                                              

---

## 🚀 Features

- Abstractive dialogue summarization using a fine-tuned T5-small model
- REST API endpoint (`POST /summarize/`) with Pydantic request validation
- Clean web UI: paste text, click **Summarize**, see the result
- Loading state and error handling in the front end
- Automatic device selection: Apple MPS, CUDA, or CPU
- Full training pipeline included in a Jupyter notebook

---

## 🧰 Tech Stack

| Layer | Tools |
|-------|-------|
| Model | T5-small (Hugging Face Transformers), PyTorch |
| Data | SAMSum dataset, Pandas |
| Backend | FastAPI, Pydantic, Uvicorn, Jinja2 |
| Frontend | HTML, CSS, JavaScript (Fetch API) |

---

## 📂 Project Structure

```
dialogue-summarizer/
├── text_summarizer.ipynb   # Data prep, fine-tuning, saving the model
├── app.py                  # FastAPI app (API + serves the UI)
├── index.html              # Web UI
├── requirements.txt        # Python dependencies
├── .gitignore
└── README.md
```

> The fine-tuned model folder `saved_summary_model/` is **not** stored in this repo because of its size (see [Get the Model](#-get-the-model)).

---

## 🔄 Workflow

1. **Load data** — SAMSum train and validation CSVs (`id`, `dialogue`, `summary`)
2. **Sample** — random subset of 4,000 training and 500 validation examples (`random_state=42`)
3. **Clean text** — normalize line breaks and whitespace, strip HTML tags, lowercase
4. **Tokenize** — dialogues to 512 tokens, summaries to 150 tokens (padding + truncation)
5. **Fine-tune** — `T5ForConditionalGeneration` with the Hugging Face `Trainer`
6. **Save** — model and tokenizer saved to `./saved_summary_model`
7. **Serve** — FastAPI loads the saved model and exposes the summarization endpoint

---

## ⚙️ Training Configuration

| Setting | Value |
|---------|-------|
| Base model | `t5-small` |
| Training samples | 4,000 |
| Validation samples | 500 |
| Epochs | 6 |
| Batch size (train / eval) | 8 / 8 |
| Weight decay | 0.01 |
| Warmup steps | 500 |
| Max input length | 512 tokens |
| Max summary length | 150 tokens |
| Decoding | Beam search, 4 beams, early stopping |

---

## 🛠️ Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/ArchitJain-13/dialogue-summarizer.git
   cd dialogue-summarizer
   ```

2. **Create a virtual environment**
   ```bash
   python -m venv venv
   source venv/bin/activate        # Windows: venv\Scripts\activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

---

## 📦 Get the Model

The trained model is not included in the repo. Pick one option:

**Option A: Train it yourself**
1. Download the SAMSum dataset as CSV files (`samsum-train.csv`, `samsum-validation.csv`) and place them in the project root.
2. Run all cells in `text_summarizer.ipynb`.
3. The notebook saves the model to `./saved_summary_model`.

**Option B: Download a published copy**
If the model is hosted on the Hugging Face Hub, download it into `./saved_summary_model`.

---

## ▶️ Run the App

```bash
uvicorn app:app --reload
```

Open **http://127.0.0.1:8000** in your browser, paste a conversation, and click **Summarize**.

Interactive API docs are available at **http://127.0.0.1:8000/docs**.

---

## 🔌 API Usage

**Endpoint:** `POST /summarize/`

**Request**
```json
{
  "dialogue": "Amanda: I baked cookies. Do you want some?\nJerry: Sure!\nAmanda: I'll bring you tomorrow :-)"
}
```

**cURL**
```bash
curl -X POST http://127.0.0.1:8000/summarize/ \
  -H "Content-Type: application/json" \
  -d '{"dialogue": "Amanda: I baked cookies. Do you want some?\nJerry: Sure!\nAmanda: I will bring you tomorrow."}'
```

**Response**
```json
{
  "summary": "..."
}
```

---

## 💡 Example

Given a long multi-speaker discussion about the growth of AI, covering adoption, bias, regulation and explainability, the model returned:

> ai systems are becoming more capable due to advances in deep learning and access to large datasets. experts highlight the importance of responsible ai development, including data privacy, security, and long-term societal impact.

---

## ⚠️ Limitations

- Output is lowercase because the same cleaning step (including lowercasing) is applied at training and inference time.
- Inputs longer than 512 tokens are truncated.
- Fine-tuned on a 4,000-example subset of SAMSum with T5-small, so quality is limited on very long or non-conversational text.
- No quantitative evaluation (e.g. ROUGE) has been added yet.

---

## 🔮 Future Improvements

- [ ] Evaluate with ROUGE-1 / ROUGE-2 / ROUGE-L on the validation set
- [ ] Train on the full SAMSum dataset and try `t5-base`
- [ ] Preserve original casing in summaries
- [ ] Add chunking for inputs longer than 512 tokens
- [ ] Dockerize and deploy (Hugging Face Spaces / Render)

---

## 🙌 Acknowledgements

- [Hugging Face Transformers](https://huggingface.co/docs/transformers)
- [T5: Text-to-Text Transfer Transformer](https://arxiv.org/abs/1910.10683)
- [SAMSum Corpus](https://arxiv.org/abs/1911.12237)
- [FastAPI](https://fastapi.tiangolo.com/)

---

## 👤 Author

**Archit Jain**
[GitHub](https://github.com/ArchitJain-13) · [LinkedIn](https://linkedin.com/in/architjain1302)
