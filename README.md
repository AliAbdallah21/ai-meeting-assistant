# AI Meeting Assistant

An AI-powered assistant that:
1. Transcribes meeting audio using Whisper.
2. Cleans up financial product terminology.
3. Generates professional meeting minutes.
4. Produces an actionable task list with deadlines.
5. Provides a web interface via Gradio.

---

## 🚀 Features
- **Speech-to-Text**: Uses `Whisper Tiny` for fast transcription.
- **Financial Term Normalization**: Automatically expands acronyms like `401k → 401(k) retirement savings plan`.
- **Meeting Minutes & Tasks**: Summarizes discussions and creates task lists.
- **Web Interface**: Simple Gradio UI for uploading audio files and downloading outputs.

---

## 📦 Installation
```bash
pip install transformers torch gradio langchain langchain-community langchain-openai pydantic
```
---


## 🔑 Setup
Store your OpenAI API key in Colab’s userdata:
```bash
from google.colab import userdata
client = OpenAI(api_key=userdata.get('OPENAI_API_KEY'))
```
---
## ▶️ Usage

### 1. Run the Application
```bash
python ytbot.py
```

### 2. Gradio Interface
This section sets up a simple web-based interface using **Gradio**.  
The interface allows users to input a product description and receive an AI-generated response.

***Code:***
```python
import gradio as gr

demo = gr.Interface(
    fn=product_assistant,
    inputs=gr.Textbox(lines=2, placeholder="Describe your product here..."),
    outputs="text",
    title="Product Assistant",
    description="Ask the assistant about your product idea."
)

demo.launch()
```



