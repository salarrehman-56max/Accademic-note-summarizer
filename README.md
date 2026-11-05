# AI-Powered Academic Notes Summarizer

A locally hosted Flask web application that transforms student-provided lecture notes and PDF documents into structured study material using the Groq API. It generates a main summary, key concepts, definitions, important points, three sets of exam questions, quick revision notes, and key takeaways.

> **Grounding:** The model is instructed to use only the submitted material, avoid unsupported facts, and identify information that is missing. AI output should still be checked against the original academic source.

## Features

- Paste lecture notes or upload a selectable-text PDF.
- Optionally combine pasted notes and a PDF in one request.
- Short Summary, Detailed Summary, and Exam Preparation Notes styles.
- English and Urdu output options.
- Seven organized result sections.
- Exactly five short-answer, five conceptual, and five long-answer question slots.
- Copy the generated study guide or download it as a `.txt` file.
- Responsive interface for desktop, tablet, and mobile.
- In-memory request processing; no application database or permanent upload storage.
- Groq API key is read server-side from `.env` and is never sent to browser JavaScript.

## Technology

- Python 3.10+
- Flask
- Groq Python SDK
- Groq-hosted language model (default: `llama-3.3-70b-versatile`)
- PyMuPDF (`fitz`) for PDF text extraction
- python-dotenv
- HTML5, CSS3, JavaScript, Bootstrap 5

## Project structure

```text
academic-notes-summarizer/
├── app.py
├── requirements.txt
├── .env.example
├── .gitignore
├── README.md
├── templates/
│   └── index.html
├── static/
│   ├── style.css
│   └── script.js
└── uploads/
    └── .gitkeep
```

The `uploads/` directory is included to match the requested project layout. Uploaded PDFs are processed directly from memory and are not written to that directory.

## Requirements

- Windows 10/11, macOS, or Linux
- Python 3.10 or newer
- Internet access while calling Groq
- A Groq API key from [Groq Console](https://console.groq.com/keys)

Check Python on Windows:

```powershell
py --version
```

## Windows PowerShell installation

Open PowerShell in the project directory and run:

### 1. Create and activate a virtual environment

```powershell
py -m venv venv
venv\Scripts\activate
```

If PowerShell blocks activation, run this command for the current terminal session, then activate again:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
venv\Scripts\activate
```

### 2. Install dependencies

```powershell
py -m pip install --upgrade pip
py -m pip install -r requirements.txt
```

### 3. Configure the Groq API key

Copy the example environment file:

```powershell
Copy-Item .env.example .env
```

Open `.env` in a text editor and replace the placeholder:

```dotenv
GROQ_API_KEY=your_actual_groq_api_key
GROQ_MODEL=llama-3.3-70b-versatile
FLASK_DEBUG=false
```

Do not add quotes around the key unless required by your environment. Do not share `.env`, commit it to source control, or include it in a ZIP shared with others. `.gitignore` excludes it.

### 4. Run the application

```powershell
py app.py
```

Open this address in your browser:

**http://127.0.0.1:5000**

Press `Ctrl+C` in PowerShell to stop the development server.

## How to use

1. Choose **Paste notes** and enter academic content, or choose **Upload PDF** and select a PDF.
2. Choose a summary style and output language.
3. Click **Generate study material**.
4. Wait for the AI request to finish.
5. Review the seven result sections.
6. Use **Copy summary** or **Download .txt** to keep a local copy.
7. Use **Generate again** to rerun the current input with the selected options, or **Clear all** to reset the workspace.

You may paste notes and upload a PDF in the same request. The backend combines both sources.

## API endpoint

`POST /api/summarize` accepts `multipart/form-data`:

| Field | Values |
|---|---|
| `notes` | Optional pasted text |
| `pdf` | Optional PDF file |
| `style` | `short`, `detailed`, or `exam` |
| `language` | `English` or `Urdu` |

At least one of `notes` or `pdf` is required. A successful response includes a `result` object with the seven study sections and a `source_name`. Errors return JSON with an `error` message and an appropriate HTTP status code.

## Limits and document notes

- Maximum request size: 12 MB.
- Maximum extracted source text: 100,000 characters.
- Only PDF files are accepted in the upload field.
- Password-protected PDFs are rejected.
- The initial version extracts selectable text. Scanned PDFs containing only page images require OCR before upload.
- The model's context and output limits may constrain very long documents. Split especially large materials into chapters or sections.
- The application does not store a history or database record. The browser displays results in the current page session.

## Common errors and solutions

| Error | Solution |
|---|---|
| `GROQ_API_KEY is not configured` | Create `.env` from `.env.example` and set `GROQ_API_KEY`. Restart Flask after editing it. |
| Groq rejected the API key | Check that the key is copied correctly, active, and has no extra spaces. |
| Rate limit reached | Wait and retry, or check your Groq account limits. |
| Could not connect to Groq | Check internet access, firewall, proxy, and Groq service availability. |
| No selectable text was found | The PDF may be scanned. OCR it first or paste the text manually. |
| Invalid PDF | Re-export the document as a valid PDF and try again. |
| Upload too large | Use a PDF under 12 MB. |
| Port 5000 is already in use | Stop the other local server or change the port in the `app.run()` call. |
| `py` is not recognized | Install Python from python.org and enable the Python launcher / PATH option. |

## Security and privacy notes

- Keep the API key exclusively in the server-side `.env` file.
- Never place `GROQ_API_KEY` in HTML, CSS, or JavaScript.
- Do not enable Flask debug mode on a public or production deployment.
- Uploaded files are read into memory; they are not saved to disk by this application.
- Content is sent to Groq for model inference. Review Groq's current terms and privacy documentation before submitting confidential, personal, or institution-restricted material.
- This is a local development starter, not a hardened public deployment. Production use should add authentication, CSRF protection, request throttling, HTTPS, monitoring, and deployment-specific security controls.

## Testing and verification

A basic offline syntax check can be run from the project folder:

```powershell
py -m compileall app.py
```

The PDF extractor can be tested independently with a small text-based PDF. End-to-end summarization requires a valid `GROQ_API_KEY` and internet connectivity. No fake model response is bundled: when the key is absent or the API fails, the application displays an error instead of pretending a summary was generated.

For an API smoke test, start the app and submit a short paragraph through the browser. Verify that the response contains all seven sections and that the copy and download actions work.

## Customization

Set `GROQ_MODEL` in `.env` to another model currently available to your Groq account. The default model can change as provider availability evolves. The model must support the JSON response format used by the backend.

---

Built for students who want to spend less time reorganizing notes and more time understanding them.
