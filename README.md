
# AI Meeting Assistant

An AI-powered meeting assistant that:
- Transcribes meeting audio using Whisper (transformers).
- Normalizes financial product terminology (e.g., “401k” → “401(k) retirement savings plan”).
- Generates structured **Meeting Minutes** and a **Task List** using an OpenAI chat model via LangChain.
- Exposes a simple web UI with Gradio for upload and download.

---

## Features

- Upload an English audio file of a meeting.
- Automatic speech-to-text using the `openai/whisper-tiny.en` model from `transformers`.
- Financial terms normalization (acronyms expanded and standardized).
- Meeting minutes and tasks generated from the adjusted transcript.
- Downloadable `.txt` file with results.

---

## Requirements

- Python 3.10+
- Packages:
  - torch
  - transformers
  - gradio
  - langchain
  - langchain-community
  - langchain-openai
  - pydantic  (note: the original code used `ydantic` which is a typo)

If you are on Google Colab, you can install packages using `!pip install ...` within a cell.

---

## OpenAI API Key Setup (Colab)

Store your OpenAI API key securely in Colab secrets so the code can read it:

1. In a Colab cell, run:
   - `from google.colab import userdata`
   - `userdata.put("OPENAI_API_KEY", "your_api_key_here")`
2. The code retrieves it later with:
   - `userdata.get("OPENAI_API_KEY")`

---

## How It Works (Pipeline Overview)

1. **Transcription**
   - Uses `transformers.pipeline` with the model `openai/whisper-tiny.en` to transcribe the uploaded audio.
2. **Cleaning**
   - A utility function strips non-ASCII characters from the raw transcript for consistent downstream processing.
3. **Financial Terminology Normalization**
   - A custom function sends the transcript to the OpenAI Chat Completions API with specific instructions:
     - Convert spoken numbers for financial products to numeric forms with the full product name in parentheses.
     - Expand common acronyms and keep context sensitivity (e.g., “LTV” may be “Loan to Value” or “Lifetime Value” based on context).
   - Returns an adjusted transcript.
4. **Minutes + Tasks Generation**
   - A LangChain `ChatPromptTemplate` formats a prompt that asks the model to produce:
     - Key points discussed
     - Decisions made
     - Actionable task list (assignees and deadlines)
   - The pipeline is built from:
     - `RunnablePassthrough()` → supplies the transcript as the `context`
     - `prompt` → renders the template
     - `ChatOpenAI` model call
     - `StrOutputParser()` → converts model output to a string
5. **Output**
   - The generated minutes and tasks are written to `meeting_minutes_and_tasks.txt` and also returned to the UI.

---

## Running the App

This project is designed to be run in Google Colab (where `userdata` secrets are available). The main steps are:
1. Install the required packages in a Colab cell.
2. Add your OpenAI API key to Colab secrets.
3. Run the provided cells in order:
   - Imports
   - OpenAI client and `ChatOpenAI` setup
   - Helper functions (`remove_non_ascii`, `product_assistant`)
   - LangChain prompt/chain setup
   - Transcription function (`transcript_audio`)
   - Gradio interface construction and `launch`
4. Open the interface, upload an audio file, and wait for the transcript and minutes to be generated.
5. Download the `.txt` file using the file output component.

**Note on local runs:** The current code references `google.colab.userdata`. If you intend to run locally, replace that with environment variables (e.g., `os.getenv("OPENAI_API_KEY")`) and pass the key into both the OpenAI client and the LangChain `ChatOpenAI` initializer.

---

## Key Files and Functions

- `remove_non_ascii(text)`
  - Removes non-ASCII characters for cleaner processing.
- `product_assistant(ascii_transcript)`
  - Sends the transcript to an OpenAI model with strict instructions to normalize financial product terminology and acronyms. Returns an adjusted transcript.
- `template` and `prompt` (LangChain)
  - Provides a structured format for **Meeting Minutes** and **Task List**.
- `chain`
  - A composed LangChain pipeline: passthrough → template → model → string output.
- `transcript_audio(audio_file)`
  - End-to-end orchestrator:
    - Transcribes audio with Whisper
    - Cleans the text
    - Normalizes finance terms
    - Generates minutes and tasks
    - Saves and returns the result
- Gradio interface
  - Defines the UI elements (audio upload, text output, file download)
  - Launches a web server on `0.0.0.0:5002`

---

## Example Output (Structure)

Meeting Minutes
- Key points discussed (e.g., leverage strategy, Tier 1 capital ratio, revenue forecast, IPO plan)
- Decisions made

Task List
- Actionable items with assignees and deadlines

---

## Notes and Tips

- **Model Choices:** `openai/whisper-tiny.en` is fast and lightweight, but for higher accuracy you could switch to a larger Whisper model.
- **Determinism:** `ChatOpenAI(..., temperature=0)` makes meeting notes more reproducible.
- **Throughput:** Long audio may benefit from chunking and batching; `chunk_length_s=30` and `batch_size=8` are set accordingly.
- **Context Sensitivity:** Acronyms like “LTV” are context-dependent; the prompt instructs the model to infer meaning from surrounding text.

---

## FAQ

**Q: Can I run this outside Colab?**  
A: Yes. Replace any `google.colab.userdata` references with `os.environ` or a config file for your API key.

**Q: How do I change the OpenAI model?**  
A: Update the model name in both places: the direct OpenAI client (`client.chat.completions.create`) and the LangChain `ChatOpenAI` initializer.

**Q: The generated minutes are too brief.**  
A: Adjust the prompt template with more explicit headings or ask for more detail (e.g., “include budget figures, owners, and risk items”).

---

## Contact

👤 **Ali Abdallah**  
📧 Email: aliabdalla2110@gmail.com  
🔗 GitHub: [AliAbdallah21](https://github.com/AliAbdallah21)

---

