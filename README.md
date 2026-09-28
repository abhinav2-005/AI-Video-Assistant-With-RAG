# 🎬 AI Video Assistant with RAG

An AI-powered video and meeting intelligence application that transforms long-form videos or meeting recordings into useful, structured information.

The application accepts a **YouTube URL or local media file**, processes the audio, generates a transcript, summarizes the content, extracts important meeting information, and provides a **RAG-powered chat interface** for asking questions about the video.

---

## ✨ Features

* 🎥 **YouTube & Local File Support**

  * Provide a YouTube URL or local video/audio file.
  * YouTube audio is downloaded and converted to WAV format.
  * Local media files can also be converted and processed.

* 🎙️ **Audio Processing**

  * Converts audio into a standard format.
  * Resamples audio to **16 kHz mono**.
  * Splits long recordings into manageable chunks for processing.

* 📝 **Automatic Transcription**

  * Converts the processed audio into a complete transcript.
  * Supports:

    * English
    * Hinglish

* 🏷️ **AI Title Generation**

  * Automatically generates a meaningful title based on the transcript.

* 📋 **Meeting Summarization**

  * Produces a concise summary of the complete conversation.

* ✅ **Action Item Extraction**

  * Identifies tasks discussed during the meeting.
  * Extracts:

    * Task description
    * Responsible person
    * Deadline, when mentioned

* 🔑 **Key Decision Extraction**

  * Identifies important decisions made during the meeting.

* ❓ **Open Question Extraction**

  * Finds unresolved questions and topics requiring follow-up.

* 🧠 **RAG-based Question Answering**

  * Ask questions directly about the processed video.
  * The RAG pipeline uses the transcript as the knowledge source.
  * Enables contextual conversations instead of relying only on the LLM's general knowledge.

* 💬 **Interactive Chat**

  * Maintains chat history during the session.
  * Ask multiple questions about the same meeting.
  * Clear the conversation whenever required.

* 🎨 **Modern Streamlit Interface**

  * Dark-themed UI.
  * Pipeline progress indicators.
  * Separate sections for transcript, summary, action items, decisions, and questions.
  * Integrated conversational interface.

---

## 🏗️ Architecture

```text
                    ┌─────────────────────┐
                    │   YouTube URL /     │
                    │    Local File       │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Audio Processing  │
                    │  Download / Convert │
                    │      / Chunk        │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │    Transcription    │
                    │                     │
                    │ English / Hinglish  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │     Transcript      │
                    └──────────┬──────────┘
                               │
                ┌──────────────┼──────────────┐
                │              │              │
                ▼              ▼              ▼
        ┌──────────────┐ ┌──────────────┐ ┌──────────────┐
        │ Summarization│ │ Information  │ │ RAG Pipeline │
        │              │ │ Extraction   │ │              │
        └──────┬───────┘ └──────┬───────┘ └──────┬───────┘
               │                │                │
               ▼                ▼                ▼
          Summary       Actions / Decisions   Chat / Q&A
                         / Questions
```

---

## 🔄 Processing Pipeline

The application processes an input through the following stages:

### 1. Audio Processing

For YouTube URLs, the application downloads the best available audio and converts it to WAV.

Local audio/video files are also converted to WAV when necessary.

The audio is converted to:

```text
Mono
16 kHz
WAV
```

Long recordings are divided into smaller chunks before transcription.

---

### 2. Transcription

The audio chunks are passed through the transcription pipeline to generate the complete transcript.

The application provides language selection for:

```text
English
Hinglish
```

---

### 3. AI Analysis

Once the transcript is available, the application performs multiple analysis tasks:

```text
Transcript
    │
    ├──► Generate Title
    │
    ├──► Generate Summary
    │
    ├──► Extract Action Items
    │
    ├──► Extract Key Decisions
    │
    └──► Extract Open Questions
```

The extraction prompts are designed specifically for meeting analysis, including task ownership and deadlines for action items.

---

### 4. RAG Pipeline

The transcript is also passed to the RAG engine.

```text
Transcript
     │
     ▼
Knowledge Base
     │
     ▼
Retriever
     │
     ▼
Relevant Context
     │
     ▼
LLM
     │
     ▼
Answer
```

This allows users to ask questions such as:

```text
What were the main decisions?

Who was assigned the database task?

What problems were discussed?

What are the unresolved questions?

What did the team decide about the project?
```

The answers are generated using the context of the processed meeting.

---

## 🛠️ Tech Stack

### Frontend / UI

* Python
* Streamlit
* HTML
* CSS

### AI / LLM

* LangChain
* Groq
* Mistral AI support
* LLM-based summarization
* LLM-based information extraction

The current implementation uses **Groq's `openai/gpt-oss-120b` model**, while Mistral support is also present in the code.

### RAG

* LangChain
* Retrieval-Augmented Generation
* Transcript-based question answering

### Audio / Video Processing

* yt-dlp
* Pydub
* FFmpeg

The audio processing pipeline downloads YouTube audio, converts media to WAV, normalizes it to 16 kHz mono, and chunks long recordings into smaller segments.

### Configuration

* Python-dotenv
* Environment variables for API keys

---

## 📁 Project Structure

```text
AI-Video-Assistant-With-RAG/
│
├── app.py
│
├── core/
│   ├── transcriber.py
│   ├── summarize.py
│   ├── extractor.py
│   └── rag_engine.py
│
├── utils/
│   └── audio_processor.py
│
├── downloads/
│
├── .env
├── .gitignore
├── requirements.txt
└── README.md
```

> The exact filenames can be adjusted if your repository uses different module names.

---

## ⚙️ Installation

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/AI-Video-Assistant-With-RAG.git
cd AI-Video-Assistant-With-RAG
```

### 2. Create a virtual environment

```bash
python -m venv venv
```

Activate it on Windows:

```bash
venv\Scripts\activate
```

On Linux/macOS:

```bash
source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

Make sure **FFmpeg** is installed and available in your system PATH because audio conversion relies on it.

---

## 🔐 Environment Variables

Create a `.env` file in the project root.

```env
GROQ_API_KEY=your_groq_api_key
MISTRAL_API_KEY=your_mistral_api_key
```

The application loads environment variables using `python-dotenv`.

Only configure the API keys required by the LLM/transcription components you are using.

**Never commit your `.env` file to GitHub.**

Add it to `.gitignore`:

```text
.env
venv/
__pycache__/
downloads/
*.wav
```

---

## ▶️ Running the Application

Start the Streamlit application:

```bash
streamlit run app.py
```

Then open the local Streamlit URL shown in your terminal.

---

## 🚀 How to Use

### Step 1 — Provide an Input

Enter either:

```text
YouTube URL
```

or

```text
Local video/audio file path
```

### Step 2 — Select Language

Choose:

```text
English
```

or

```text
Hinglish
```

### Step 3 — Analyse

Click:

```text
⚡ Analyse
```

The application runs the complete pipeline:

```text
Audio Processing
       ↓
Transcription
       ↓
Title Generation
       ↓
Summarisation
       ↓
Information Extraction
       ↓
RAG Engine
```

### Step 4 — Explore Results

After processing, you can view:

* Generated title
* Summary
* Full transcript
* Action items
* Key decisions
* Open questions

### Step 5 — Chat With the Video

Use the chat section to ask questions about the processed meeting.

---

## 🧠 LLM Pipeline

The project uses LangChain's runnable pipeline to construct the LLM workflow.

The current implementation creates a prompt → LLM → string parser chain, with Groq configured as the active LLM provider.

For example, action-item extraction uses a meeting-analysis prompt that asks the model to identify the task, owner, and deadline.

Similar extraction pipelines are used for key decisions and unresolved questions.

---

## 📊 Output

For a processed meeting, the application produces:

```text
Session Title
       │
       ├── Summary
       │
       ├── Full Transcript
       │
       ├── Action Items
       │
       ├── Key Decisions
       │
       ├── Open Questions
       │
       └── RAG Chat
```

---

## 🎯 Use Cases

This project can be used for:

* Meeting analysis
* Lecture summarization
* YouTube educational content
* Project discussions
* Team meetings
* Interview recordings
* Technical presentations
* Long-form video analysis

Instead of manually watching an entire recording, users can extract the important information and directly ask questions about its contents.

---

## 🔮 Future Improvements

Possible extensions include:

* Speaker identification
* Timestamp-based transcript navigation
* Persistent vector database storage
* Multi-video knowledge bases
* Document + video RAG
* Export summaries to PDF/Markdown
* Meeting-to-task integration
* Automatic meeting minutes generation
* Better multilingual transcription
* Source citations for RAG responses
* Authentication and user-specific meeting history

---

## 📸 Screenshots

### Main Interface

<img width="1916" height="872" alt="AI Video Assistant Interface" src="https://github.com/user-attachments/assets/3552db82-4175-4fc6-aa2d-843106a869f5" />

### Analysis / Results

<img width="1917" height="882" alt="AI Video Assistant Results" src="https://github.com/user-attachments/assets/c83dfc24-5605-4c54-91f8-540d8bf2e66b" />

---

## 📌 Project Highlights

This project demonstrates practical implementation of:

* Generative AI
* Large Language Models
* LangChain
* Retrieval-Augmented Generation
* Prompt Engineering
* Audio Processing
* Speech-to-Text pipelines
* Text Summarization
* Information Extraction
* Streamlit application development
* AI-powered conversational interfaces

---

## 👨‍💻 Author

**Raavi Abhinav**

Built as an AI/Generative AI project exploring practical applications of LLMs, RAG, audio processing, and meeting intelligence.


