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

# 📥 Input (Form Submission) – n8n Workflow

This workflow begins with an **n8n Form node** which collects essential input from the recruiter or user. It captures three key fields that are used throughout the rest of the workflow to fetch and process candidate data.

---

## 📝 1. Job Description (Text Field)

- **Purpose**: Text input where the recruiter provides a detailed job description for the open position.
- **Contents**: Should include job title, required skills, experience, location, certifications, responsibilities, etc.
- **Usage**: This text is processed using Natural Language Processing (NLP) techniques to extract relevant keywords and compare them with the skills and experiences of candidate profiles.

> **Example**:  
> “We are looking for a Senior Data Scientist with 5+ years of experience in Python, Machine Learning, and Cloud (AWS/GCP). Must have strong communication skills and experience working with business teams.”

---

## 📄 2. File URL (Google Sheets Link)

- **Purpose**: The URL of a Google Sheets file that contains candidate profile data, typically collected from LinkedIn or similar sources.
- **Expected Format**: A valid Google Sheets link with appropriate sharing permissions.
- **Usage**: The link is used to fetch the spreadsheet contents using n8n’s Google Sheets integration.

> **Example**:  
> `https://docs.google.com/spreadsheets/d/1A2B3Cxyz1234567/edit#gid=0`

---

## 📑 3. Sheet Name (Text Field)

- **Purpose**: Indicates the specific tab/sheet inside the provided Google Sheets file where the candidate data resides.
- **Why it’s Important**: Google Sheets files may contain multiple sheets. This input ensures that the workflow fetches data from the correct one.
- **Usage**: Passed into the Google Sheets node to target the correct sheet.

> **Example**:  
> `LinkedInProfiles`
- **Note**: The column that contains the candidate URLs in the Google Sheet must be named exactly as **`Profile URL`** (case-sensitive).
---

## 🧩 Summary of Input Fields

| Field             | Description                                     | Used In                                      |
|------------------|-------------------------------------------------|----------------------------------------------|
| Job Description   | Text description of the role to be filled       | NLP-based keyword extraction & similarity    |
| File URL          | Google Sheets link containing profile data      | Dynamic profile data fetching via n8n        |
| Sheet Name        | Name of the sheet/tab with candidate profiles   | Accurate targeting within the spreadsheet    |

---

This input step is the entry point for a smart recruitment automation system that matches job descriptions with potential candidates using AI and NLP.


### 1. 📄 Sheet Initialization

On form submission, the following sheets are created in the connected Google Spreadsheet:

| Sheet Name         | Description                                                |
|--------------------|------------------------------------------------------------|
| `JD`               | Stores the submitted job description and form metadata     |
| `profile_data`     | Contains raw scraped LinkedIn profile data                 |
| `profile_match_score` | AI-evaluated profile scores and insights              |
| `>80`              | Profiles with match score ≥ 80                             |
| `>90`              | Profiles with match score ≥ 90                             |

---

### 2. 🔗 Read & Loop LinkedIn URLs

- Reads all LinkedIn profile URLs from the column named **`Profile URL`** in the sheet specified via the form.
- Loops through each profile one by one.
- **Validation Step**:  
  - A `HTTP GET` request is sent to verify if the URL is valid and accessible.
  - If valid, it proceeds to the next step.

---

### 3. 🔍 Scrape LinkedIn Profile Data

- Uses **Apify’s LinkedIn Scraper API** in a `HTTP request` node to fetch full profile data.
- The returned data is stored in the `profile_data` sheet.

---

### 4. 📝 Summarize Profile Content

- A **JavaScript Code node** processes each profile's raw JSON into a clean, readable summary.
- This includes parsing fields like:
  - Name
  - Experience
  - Education
  - Skills

---

### 5. 🧠 AI-Powered Evaluation

- The job description (from `JD`) and the summarized profile are passed to a **LangChain AI Agent** using **Google Gemini** (`models/gemini-2.0-flash-exp`).
- The AI evaluates the fit between the profile and the job, and returns a **structured output** with the following fields:

  - `Name`  
  - `Experience`  
  - `Profile_match_score` (0–100)  
  - `Suggested` (Yes/No)  
  - `LinkedIn_url`  
  - `Modules`  
  - `Location`

- This structured data is appended to the `profile_match_score` sheet.

---

### 6. 🧹 Categorize Profiles by Score

- All evaluated profiles are passed through **filter nodes**:
  - Profiles with **score ≥ 80** are appended to the `>80` sheet.
  - Profiles with **score ≥ 90** are appended to the `>90` sheet.

---

## 🔧 External Tools & APIs Used

| Tool / Service             | Purpose                                  |
|----------------------------|-------------------------------------------|
| **Google Sheets**          | Store job descriptions and profile data  |
| **Apify API**              | Scrape LinkedIn profiles                 |
| **Google Gemini** | AI-driven evaluation and scoring     |

---

## 📤 Final Output Summary

| Sheet Name            | Contents                                           |
|-----------------------|----------------------------------------------------|
| `JD`                  | Job description and form data                      |
| `profile_data`        | Raw LinkedIn data scraped from profiles            |
| `profile_match_score` | AI-evaluated structured profile insights           |
| `>80`                 | Profiles with match score ≥ 80                     |
| `>90`                 | Profiles with match score ≥ 90                     |

---

## ⚠️ Notes

- ✅ Make sure the **`Profile URL`** column exists and contains valid LinkedIn URLs.
- 🔐 Ensure that your API keys for **Apify** and **Google Gemini API** are correctly configured.
- ✨ A well-formatted and detailed job description will improve AI match accuracy.



