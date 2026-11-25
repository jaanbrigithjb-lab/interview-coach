# Interview Coach

Interview Coach is an AI-assisted resume screening and technical interview practice application built with Streamlit and LangChain. It compares a candidate's resume with a job description, generates role-specific interview questions, and evaluates written answers.

This project provides practice guidance only. Its match score and interview evaluation are not hiring decisions or guarantees of employment.

## Highlights

- Extract text from PDF, DOCX, and TXT resumes.
- Compare resume evidence against a job description.
- Produce a transparent match score and candidate summary.
- Identify matched requirements, gaps, improvement areas, and skills to learn.
- Generate 5, 10, or 15 tailored technical questions.
- Create fresh question sets without repeating recent questions.
- Time and save written interview answers.
- Evaluate every answer for correctness, feedback, improvement, and related skills.
- Process resume uploads in memory without intentionally persisting them.

## Technology Stack

| Component | Purpose |
|---|---|
| Python 3.10+ | Application runtime |
| Streamlit | Web interface and application state |
| LangChain | AI invocation and message orchestration |
| LangChain OpenAI integration | Groq-compatible chat model integration |
| Groq OpenAI-compatible API | Resume analysis, question generation, and answer evaluation |
| `openai/gpt-oss-20b` | Default chat model configured in the application |
| `pypdf` | PDF text extraction |
| `python-docx` | DOCX text and table extraction |

AI responses are requested as JSON and validated by the application before they are shown.

## Project Structure

```text
.
├── streamlit_app.py   # UI, LangChain calls, document parsing, and interview logic
├── requirements.txt   # Python dependencies
└── README.md          # Project documentation
```

## Prerequisites

- Python 3.10 or newer
- A Groq API key
- Internet access for Groq API requests

## Local Setup

1. Clone the repository and enter the project directory.

   ```bash
   git clone <repository-url>
   cd GPT_AI
   ```

2. Create and activate a virtual environment.

   ```bash
   python -m venv .venv
   source .venv/bin/activate
   ```

   On Windows:

   ```powershell
   py -m venv .venv
   .venv\Scripts\activate
   ```

3. Install dependencies.

   ```bash
   python -m pip install --upgrade pip
   python -m pip install -r requirements.txt
   ```

4. Configure `GROQ_API_KEY`.

   Create a `.env` file in the project root:

   ```dotenv
   GROQ_API_KEY=your_groq_api_key
   ```

   Alternatively, export it in the shell:

   ```bash
   export GROQ_API_KEY="your_groq_api_key"
   ```

   PowerShell:

   ```powershell
   $env:GROQ_API_KEY="your_groq_api_key"
   ```

5. Start the application.

   ```bash
   streamlit run streamlit_app.py
   ```

6. Open the local URL reported by Streamlit, normally `http://localhost:8501`.

## Deployment

### Streamlit Community Cloud

1. Push the project to a GitHub repository.
2. Create a new Streamlit Community Cloud app.
3. Set the main file to `streamlit_app.py`.
4. Add this secret in the app's advanced settings:

   ```toml
   GROQ_API_KEY = "your_groq_api_key"
   ```

5. Deploy the app. Community Cloud installs packages from `requirements.txt`.

Do not commit `.env`, API keys, or generated credentials.

## Configuration

| Name | Required | Description |
|---|---:|---|
| `GROQ_API_KEY` | Yes | API key for the Groq OpenAI-compatible endpoint |

The application looks for the key in this order:

1. The `GROQ_API_KEY` environment variable
2. Streamlit secrets
3. A local `.env` file in the project root

The default model and other limits are constants near the top of `streamlit_app.py`.

## How the Application Works

1. **Resume ingestion**
   - Accepts PDF, DOCX, or UTF-8 TXT files up to 10 MB.
   - Limits PDF input to 100 pages and resume text to 100,000 characters.
   - Rejects encrypted PDFs, unreadable documents, unsupported formats, and binary TXT files.

2. **Role analysis**
   - Sends the resume and job description through LangChain chat messages.
   - Receives structured JSON containing eligibility, alignment, requirements, gaps, and advice.
   - Validates required fields and value ranges before updating the interface.

3. **Interview generation**
   - Requests the selected number of distinct questions for the role.
   - Checks replacement questions against generated and previous question sets.

4. **Written interview**
   - Collects one written answer per question.
   - Records the time from the first answer edit until the answer is saved.

5. **Answer evaluation**
   - Sends the role, candidate summary, questions, saved answers, and response times to LangChain.
   - Returns per-answer correctness and feedback plus overall improvement guidance.

## LangChain Integration

LangChain is used by `request_json()` to construct system and human messages and invoke `ChatOpenAI` against Groq's OpenAI-compatible endpoint. The generated response is parsed as JSON and passed through application-level validators.

```python
llm = ChatOpenAI(
    model="openai/gpt-oss-20b",
    base_url="https://api.groq.com/openai/v1",
    api_key=groq_api_key,
    temperature=0.2,
)
```

The JSON response format is set in LangChain's provider-specific model arguments. This keeps prompts and response validation in one application layer.

## Interview Workflow

1. Upload a resume and enter at least 20 characters of job description.
2. Select 5, 10, or 15 questions.
3. Start the analysis.
4. Review the match score, matched requirements, and gaps.
5. Enter each answer in the text area and save it.
6. Finish the interview to review the evaluation.

Generating a fresh question set replaces the existing questions and clears the previous answers. Switching away from the browser tab and returning also requests a replacement set for an eligible interview.

## Supported Resume Formats

| Format | Support |
|---|---|
| PDF | Text-based PDFs only |
| DOCX | Paragraph and table text |
| TXT | UTF-8 text |

Scanned or image-only PDFs are not OCR-processed. DOC and image files are not supported. Password-protected PDFs cannot be read.

## Privacy and Limitations

Resume text, the job description, and interview answers are sent to Groq to produce AI responses. The application does not intentionally save uploaded resumes or interview sessions to disk, but provider data handling remains subject to the Groq account and service terms.

Do not upload confidential information unless you are authorized and comfortable with the configured provider's data handling.

Additional limitations:

- A match estimate is based only on supplied text.
- AI-generated evaluations may be incomplete or incorrect.
- Very large or unusually formatted documents may lose text during extraction.
- The quality of questions and feedback depends on the selected model and prompt.

## Troubleshooting

### Missing API key

Confirm that `GROQ_API_KEY` is set in the environment, Streamlit secrets, or local `.env`.

### Invalid JSON response

Retry the action. Temporary model output or provider failures can produce an invalid response.

### Resume text was not found

Use a text-based PDF, DOCX, or TXT. Scanned PDFs require a separate OCR step before upload.

### Dependency installation fails

Upgrade pip and retry:

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```
