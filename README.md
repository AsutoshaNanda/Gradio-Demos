
# Gradio-Demos

A collection of **Gradio** examples showcasing how to build interactive web interfaces for various LLMs (OpenAI, Anthropic Claude, Google Gemini) and how to handle streaming responses.  
The repo includes a Jupyter notebook (`Gradio Demo.ipynb`) that walks through the setup, API initialization, and multiple Gradio interfaces, each illustrating a different use‑case.

## Table of Contents

- [Overview](#overview)  
- [Prerequisites](#prerequisites)  
- [Setup & Configuration](#setup--configuration)  
- [Notebook Walk‑through](#notebook-walk-through)  
  - [1️⃣ Environment & API Keys](#1️⃣-environment--api-keys)  
  - [2️⃣ Simple Text‑to‑Text Interface](#2️⃣-simple-text-to-text-interface)  
  - [3️⃣ OpenAI Chat Interface](#3️⃣-openai-chat-interface)  
  - [4️⃣ Streaming Responses (OpenAI, Claude, Gemini)](#4️⃣-streaming-responses-openai-claude-gemini)  
  - [5️⃣ Multi‑Model Selector](#5️⃣-multi-model-selector)  
- [Running the Interfaces Locally](#running-the-interfaces-locally)  
- [Deploying to Gradio Cloud / Hugging Face Spaces](#deploying-to-gradio-cloud--hugging-face-spaces)  
- [Extending the Demo](#extending-the-demo)  
- [License](#license)  

---

## Overview

Gradio is a Python library that turns functions into shareable web apps with **zero front‑end code**.  
This repo demonstrates:

| Feature | Description |
| ------- | ----------- |
| **Basic UI** | Textbox → Upper‑case conversion (`shout`). |
| **OpenAI Chat** | Simple chat with `gpt‑4o‑mini`. |
| **Streaming** | Real‑time token streaming for OpenAI, Anthropic Claude, and Google Gemini. |
| **Model Selector** | Single UI that lets the user pick a model (GPT, Claude, Gemini) and streams the response accordingly. |
| **Deployment Ready** | Each interface is launched with `share=True` for instant public URLs, and instructions are provided for permanent deployment. |

---

## Prerequisites

- **Python ≥ 3.9**
- `pip install -r requirements.txt` *(see the `requirements.txt` snippet below)*
- API keys for the services you wish to use:
  - `OPENAI_API_KEY`
  - `ANTHROPIC_API_KEY`
  - `GOOGLE_API_KEY`

These keys should be placed in a `.env` file at the project root (or exported as environment variables).

```dotenv
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...
GOOGLE_API_KEY=AIzaSy...
```

---

## Setup & Configuration

1. **Clone the repository**  
   ```bash
   git clone https://github.com/AsutoshaNanda/Gradio-Demos.git
   cd Gradio-Demos
   ```

2. **Install dependencies**  
   ```bash
   pip install -r requirements.txt
   ```

   *Typical `requirements.txt`*  

   ```text
   gradio
   openai
   anthropic
   google-generativeai
   python-dotenv
   beautifulsoup4   # used in the notebook for ancillary tasks
   ```

3. **Load environment variables** (the notebook does this automatically with `load_dotenv()`).

---

## Notebook Walk‑through

The notebook `Gradio Demo.ipynb` is organized into logical sections. Below is a high‑level summary of each cell group.

### 1️⃣ Environment & API Keys
- Imports libraries, loads `.env`, and prints a short confirmation that each key is present (truncating the value for security).

### 2️⃣ Simple Text‑to‑Text Interface
```python
def shout(text):
    print(f"The text printed is :{text}")
    return text.upper()
```
- Exposes `shout` via `gr.Interface(fn=shout, inputs='textbox', outputs='textbox')`.

### 3️⃣ OpenAI Chat Interface
- `message_gpt(prompt)` calls `openai.chat.completions.create` with a system prompt and returns the assistant’s reply.
- UI: Textbox → Textbox.

### 4️⃣ Streaming Responses
#### OpenAI Streaming
```python
def stream_gpt(prompt):
    stream = openai.chat.completions.create(
        messages=[{'role':'user','content':prompt},
                  {'role':'system','content':system_prompt}],
        model='gpt-4o-mini',
        stream=True
    )
    for chunk in stream:
        # accumulate and yield incremental text
```

#### Anthropic Claude Streaming
- Uses `claude.messages.stream` and yields accumulated text.

#### Google Gemini Streaming
- Utilizes `google.generativeai.GenerativeModel(...).generate_content(..., stream=True)`.

### 5️⃣ Multi‑Model Selector
```python
def stream_model(prompt, model):
    if model == 'GPT':
        result = stream_gpt(prompt)
    elif model == 'Claude':
        result = stream_claude(prompt)
    elif model == 'Gemini':
        result = stream_google(prompt)
    else:
        raise ValueError("Unknown Model")
    yield from result
```
- UI: Textbox + Dropdown → Markdown output, allowing the user to switch between the three back‑ends on the fly.

---

## Running the Interfaces Locally

Inside the notebook, each interface is launched with:

```python
gr.Interface(...).launch(share=True)
```

- **Local URL**: `http://127.0.0.1:<port>`
- **Public share URL**: Provided by Gradio’s tunneling service (valid for 1 week).

You can also run the interfaces as standalone Python scripts by extracting the relevant function and launch block into a `.py` file.

---

## Deploying to Gradio Cloud / Hugging Face Spaces

1. **Gradio Cloud** (recommended for quick public hosting)  
   ```bash
   gradio deploy path/to/your_script.py
   ```

2. **Hugging Face Spaces**  
   - Create a new Space (type “Gradio”).  
   - Push the repo (or the specific script) to the Space repository.  
   - Add your API keys as *Secrets* in the Space settings.  

Both options give you a permanent URL without the 1‑week expiry limitation.

---

## Extending the Demo

- **Add new models**: Implement a wrapper similar to `stream_gpt` and extend `stream_model`.  
- **Custom UI components**: Gradio supports sliders, audio, images, etc. Replace the textbox with any component you need.  
- **Authentication**: Use Gradio’s `auth` parameter to protect your demo.  
- **Stateful interactions**: Leverage Gradio’s `State` component to keep conversation history.

---

## License

This repository is provided under the **MIT License** – feel free to adapt, remix, and deploy the examples in your own projects.

---

### Quick Start (One‑liner)

```bash
git clone https://github.com/AsutoshaNanda/Gradio-Demos.git && cd Gradio-Demos && pip install -r requirements.txt && python -c "import gradio as gr; from demo import shout; gr.Interface(fn=shout, inputs='textbox', outputs='textbox').launch()"
```

Enjoy building interactive AI demos with Gradio!
