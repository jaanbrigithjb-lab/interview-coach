# Interview Coach

Interview Coach compares a resume with a job description, estimates role alignment, and generates a tailored technical interview. It runs as a Streamlit app and can be deployed to Streamlit Community Cloud.

## Features

- Upload one PDF, DOCX, or TXT resume (up to 10 MB).
- Compare resume evidence with a job description.
- For a low match, show why, practical improvements, and technical skills to learn.
- For a match, choose 5, 10, or 15 tailored questions.
- Listen to generated questions with browser speech synthesis.
- Type answers or record English answers for transcription.
- Review answer correctness, per-answer feedback, improvement advice, technical skills, and response time.
- Returning to the browser tab automatically generates a completely new set of questions and clears the old answers.
- Right-click, cut, copy, and paste are blocked with a warning, except copy/paste and context menu in the job description field.
- Uploaded resumes are processed in memory and are not intentionally saved by the application.

Eligibility is an AI-generated practice estimate based on role-related resume evidence. It is not a hiring decision or a guarantee of job eligibility.

## Technology

- Python 3.10+ and Streamlit
- Groq's OpenAI-compatible API for resume matching, question generation, answer evaluation, and speech recognition
- `pypdf` and `python-docx` for resume text extraction
- Browser speech synthesis for reading interview questions aloud

## Run locally with Streamlit

1. Install dependencies:

   ```bash
   python -m pip install -r requirements.txt
   ```

2. Provide `GROQ_API_KEY` in your shell environment or in a local `.env` file.

3. Launch Streamlit:

   ```bash
   streamlit run streamlit_app.py
   ```

4. Open the local URL printed by Streamlit (usually <http://localhost:8501>).

## Deploy to Streamlit Community Cloud

1. Push this project to a GitHub repository. Do not commit `.env`, API keys, or other credentials.
2. Sign in to <https://share.streamlit.io/> with GitHub and select **Create app**.
3. Choose the repository and branch, and set **Main file path** to `streamlit_app.py`.
4. In the app's **Advanced settings → Secrets**, add:

   ```toml
   GROQ_API_KEY = "your_groq_api_key"
   ```

5. Deploy. Streamlit installs the dependencies from `requirements.txt`.

The app reads the API key from Streamlit secrets, environment variables, or a local `.env` file. Never place secrets in `streamlit_app.py`, the frontend, or a public repository.

## Interview workflow

1. Upload a resume and enter at least 20 characters of job description. The job description supports copy and paste.
2. Choose 5, 10, or 15 questions and start analysis.
3. If the resume has sufficient relevant alignment, answer each question by typing or recording English speech, then save the answer.
4. Finish the interview to review answer evaluations and feedback.
5. If the resume is not a close enough match, review the gaps, improvement suggestions, and skills-to-learn guidance.

Switching away from and back to the interview browser tab automatically requests a new question set. The previous answers are cleared because they belong to the previous questions. Use the visible manual refresh button to request a new set without switching tabs.

PDFs must contain extractable text; scanned or image-only PDFs are not OCR-processed. DOC and image resume files are not supported. Speech recording requires browser microphone permission and English is enforced based on transcription language detection.

## Privacy and limitations

Resume text, the job description, and submitted interview answers are sent to the configured Groq API to generate results. English voice recordings are also sent to Groq for transcription. The application processes uploads in memory and does not intentionally save resumes or interview sessions. Do not upload confidential material unless you are comfortable with the configured provider's data handling.
