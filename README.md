# 🤖 AI-Powered ETL Automation with n8n

An n8n workflow that takes a messy CSV file, cleans every record with an AI model, and exports a clean, standardized CSV. No manual editing needed.

Tested on **50 student records** full of real-world errors: misspelled cities, broken emails, mixed date formats, invalid phone numbers and impossible marks.

![Workflow canvas](screenshots/workflow-canvas.png)

---

## 🔄 How It Works

```
CSV Upload → Extract from File → Basic LLM Chain → Code in JavaScript → Convert to File
                                        ↑
                               Google Gemini Chat Model
```

| Step | Node | What it does |
|------|------|--------------|
| 1 | **CSV Upload** (Form Trigger) | Opens a form to upload the CSV file |
| 2 | **Extract from File** | Splits the CSV into one item per row (50 items) |
| 3 | **Basic LLM Chain** + **Gemini Chat Model** | Cleans and standardizes each record, returns JSON |
| 4 | **Code in JavaScript** | Parses the AI's text response back into structured rows |
| 5 | **Convert to File** | Combines all rows into one clean CSV file |

---

## ✨ What It Fixes

| Field | Before | After |
|-------|--------|-------|
| Name | `  arun kumar ` | `Arun Kumar` |
| Email | `ARUN.KUMAR@GMAIL.COM` | `arun.kumar@gmail.com` |
| Email (invalid) | `ajay.kumar@` | *(empty)* |
| Phone | `+91-9876543212` | `9876543212` |
| City | `Chenai`, `Bangalor`, `kolkatta` | `Chennai`, `Bengaluru`, `Kolkata` |
| Date of birth | `14/07/2008`, `12 Jan 2008` | `2008-07-14`, `2008-01-12` |
| Date (impossible) | `2008-02-30` | *(empty)* |
| Grade | `10th`, `ten`, `X` | `10` |
| Marks | `150`, `-5`, `eighty` | *(empty)* |

![Before vs after](screenshots/before-after.png)

---

## 📁 Project Structure

```
├── workflow/
│   └── ETL_AI_AUTOMATION.json     # n8n workflow export
├── sample-data/
│   ├── students_dirty.csv         # Input: 50 records with errors
│   └── cleaned_students.csv       # Output: cleaned records
├── screenshots/
│   ├── workflow-canvas.png
│   └── before-after.png
├── README.md
└── .gitignore
```

---

## 🚀 Setup

### 1. Import the workflow
In n8n, go to **Workflows → ⋯ → Import from File** and select `workflow/ETL_AI_AUTOMATION.json`.

### 2. Get a free Gemini API key
1. Go to [Google AI Studio](https://aistudio.google.com) and sign in.
2. Click **Get API key → Create API key** and copy it.

### 3. Add the credential in n8n
Open the **Google Gemini Chat Model** node, choose **Credential → Create new**, paste your key and save. Pick a stable **Flash-Lite** model (avoid `preview` models, which often return 503 errors under load).

### 4. Check the binary field name
The **Input Binary Field** in *Extract from File* must exactly match the file field name from the form (see the **Binary** tab of the CSV Upload node output).

### 5. Run it
Click **Execute workflow**, upload `sample-data/students_dirty.csv`, then download the result from the **Binary** tab of the *Convert to File* node.

---

## ⚠️ Notes

### Free-tier rate limit
Gemini's free tier allows about **15 requests per minute**. To stay under it, in the **Basic LLM Chain** node:
- **Options → Batch Processing:** Batch Size `1`, Delay Between Batches `4500` ms
- **Settings → Retry On Fail:** on, Wait Between Tries `20000` ms

Processing 50 rows takes about 4 minutes.

### Using a different AI model
Only one node needs to change. Replace the **Google Gemini Chat Model** with **Anthropic Chat Model** (Claude) or **OpenAI Chat Model** and add that provider's API key. The rest of the workflow stays the same.

> Note: a Claude.ai or ChatGPT subscription does **not** include API access. You need a separate API key from the provider's developer console.

---

## 🛠️ Tech Stack

- [n8n](https://n8n.io) – workflow automation
- [Google Gemini](https://ai.google.dev) – LLM for data cleaning
- JavaScript – response parsing

---

## 🙏 Acknowledgements

Built while learning AI automation with **Social Eagle**.
