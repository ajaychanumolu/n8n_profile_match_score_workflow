# 🚀 LinkedIn Profile Screening Automation (n8n Workflow)

This `n8n` workflow automates the process of screening LinkedIn profiles against a job description, scoring each candidate using AI, and storing results in Google Sheets.



## 📌 Overview

This workflow allows you to:
- Collect job descriptions and file details via a form.
- Scrape LinkedIn profile data from URLs using Apify.
- Summarize profiles and compare them with the job description using AI.
- Score and categorize candidates based on their match.
- Save results to different Google Sheets based on match score.

---

## 📥 Input (Form Submission)

The workflow starts with a form that collects:
- **Job Description** (text)
- **File URL** (Google Sheets link containing profile URLs)
- **Sheet Name** (the name of the sheet inside the file)

---

## 🛠️ Workflow Breakdown

### 1. **Setup Sheets**
- Creates sheets:
  - `JD`
  - `profile_data`
  - `profile_match_score`
  - `>80`
  - `>90`
- Appends the job description to the `JD` sheet.

### 2. **Read & Loop LinkedIn URLs**
- Reads URLs from the specified sheet.
- Loops through each profile and verifies the URL.

### 3. **Scrape Profile Data**
- Calls Apify’s LinkedIn scraper API using the profile URLs.
- Appends detailed profile data into the `profile_data` sheet.

### 4. **Summarize Each Profile**
- Summarizes profile fields (e.g., name, experience, education, skills).
- Transforms JSON into readable summary text using a JavaScript code node.

### 5. **AI Evaluation**
- Compares each profile summary with the job description using a LangChain AI agent (Google Gemini).
- Extracted Fields:
  - `Name`
  - `Experience`
  - `Profile_match_score` (0–100)
  - `Suggested` (Yes/No)
  - `LinkedIn_url`
  - `Modules`
  - `Primary Skill`
  - `Location`
  - `Modules Match` (Yes/No)

### 6. **Categorize Results**
- Appends all profiles to `profile_match_score`.
- Filters and stores:
  - Profiles with score ≥ 80 → `>80`
  - Profiles with score ≥ 90 → `>90`


## 🔗 External Tools & APIs

| Tool                     | Purpose                      |
|--------------------------|------------------------------|
| **Google Sheets**        | Read/write candidate data    |
| **Apify API**            | Scrape LinkedIn profile data |
| **LangChain AI Agent**   | Evaluate profiles using AI   |
| **JavaScript Code Node** | Format and summarize profiles|


## 📤 Output

| Sheet Name         | Description                             |
|--------------------|-----------------------------------------|
| `JD`               | Contains the submitted job description  |
| `profile_data`     | Raw scraped LinkedIn profile data       |
| `profile_match_score` | AI-evaluated candidate scores       |
| `>80`              | Candidates with match score ≥ 80        |
| `>90`              | Candidates with match score ≥ 90        |



